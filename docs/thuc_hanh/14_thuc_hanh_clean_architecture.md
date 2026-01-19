# 🟦 THỰC HÀNH CHƯƠNG 14: CLEAN ARCHITECTURE TRONG FLUTTER

> **📌 DÀNH CHO NGƯỜI ĐÃ CÓ KINH NGHIỆM**
> 
> Bài thực hành này hướng dẫn cách tổ chức code theo Clean Architecture để dự án dễ maintain và scale.

---

## 🎯 MỤC TIÊU

Sau bài này, bạn sẽ:
- ✅ Hiểu nguyên tắc Clean Architecture
- ✅ Tổ chức code theo layers (Presentation, Domain, Data)
- ✅ Tách biệt business logic và UI
- ✅ Xây dựng ứng dụng theo chuẩn Clean Architecture

---

## 📋 CHECKLIST CHUẨN BỊ

Trước khi bắt đầu, đảm bảo bạn có:
- [ ] Kiến thức về Flutter cơ bản
- [ ] Hiểu về State Management (Provider/Bloc)
- [ ] Kiến thức về API và Repository pattern

---

## BÀI TẬP 1: CẤU TRÚC CLEAN ARCHITECTURE

### Mục đích
Hiểu cấu trúc Clean Architecture trong Flutter.

### Yêu cầu

1. **Tạo cấu trúc thư mục:**
```
lib/
├── main.dart
├── core/                    # Core functionality
│   ├── constants/
│   ├── errors/
│   └── utils/
├── features/                # Features (modules)
│   └── products/
│       ├── data/           # Data layer
│       │   ├── datasources/
│       │   ├── models/
│       │   └── repositories/
│       ├── domain/         # Domain layer
│       │   ├── entities/
│       │   ├── repositories/
│       │   └── usecases/
│       └── presentation/   # Presentation layer
│           ├── bloc/
│           ├── pages/
│           └── widgets/
└── injection/              # Dependency injection
```

2. **Tạo file core:**
Tạo `lib/core/constants/api_constants.dart`:
```dart
class ApiConstants {
  static const String baseUrl = 'https://api.example.com';
  static const String productsEndpoint = '/products';
}
```

Tạo `lib/core/errors/failures.dart`:
```dart
abstract class Failure {
  final String message;
  Failure(this.message);
}

class ServerFailure extends Failure {
  ServerFailure(String message) : super(message);
}

class CacheFailure extends Failure {
  CacheFailure(String message) : super(message);
}
```

### Kết quả mong đợi
- Hiểu cấu trúc Clean Architecture
- Tạo được cấu trúc thư mục chuẩn

---

## BÀI TẬP 2: DOMAIN LAYER

### Mục đích
Tạo Domain layer với entities và use cases.

### Yêu cầu

1. **Tạo Entity:**
Tạo `lib/features/products/domain/entities/product.dart`:
```dart
class Product {
  final String id;
  final String name;
  final double price;
  final String? description;
  
  Product({
    required this.id,
    required this.name,
    required this.price,
    this.description,
  });
}
```

2. **Tạo Repository Interface:**
Tạo `lib/features/products/domain/repositories/product_repository.dart`:
```dart
import '../entities/product.dart';
import '../../../../core/errors/failures.dart';

abstract class ProductRepository {
  Future<Either<Failure, List<Product>>> getProducts();
  Future<Either<Failure, Product>> getProduct(String id);
}
```

3. **Tạo Use Case:**
Tạo `lib/features/products/domain/usecases/get_products.dart`:
```dart
import '../entities/product.dart';
import '../repositories/product_repository.dart';
import '../../../../core/errors/failures.dart';

class GetProducts {
  final ProductRepository repository;
  
  GetProducts(this.repository);
  
  Future<Either<Failure, List<Product>>> call() {
    return repository.getProducts();
  }
}
```

### Kết quả mong đợi
- Tạo được Domain layer
- Hiểu về entities, repositories, use cases

---

## BÀI TẬP 3: DATA LAYER

### Mục đích
Tạo Data layer với models, data sources, và repositories.

### Yêu cầu

1. **Tạo Model:**
Tạo `lib/features/products/data/models/product_model.dart`:
```dart
import '../../domain/entities/product.dart';

class ProductModel extends Product {
  ProductModel({
    required String id,
    required String name,
    required double price,
    String? description,
  }) : super(
          id: id,
          name: name,
          price: price,
          description: description,
        );
  
  factory ProductModel.fromJson(Map<String, dynamic> json) {
    return ProductModel(
      id: json['id'] as String,
      name: json['name'] as String,
      price: (json['price'] as num).toDouble(),
      description: json['description'] as String?,
    );
  }
  
  Map<String, dynamic> toJson() {
    return {
      'id': id,
      'name': name,
      'price': price,
      'description': description,
    };
  }
}
```

2. **Tạo Data Source:**
Tạo `lib/features/products/data/datasources/product_remote_data_source.dart`:
```dart
import '../models/product_model.dart';
import '../../../../core/constants/api_constants.dart';
import 'package:http/http.dart' as http;
import 'dart:convert';

abstract class ProductRemoteDataSource {
  Future<List<ProductModel>> getProducts();
}

class ProductRemoteDataSourceImpl implements ProductRemoteDataSource {
  final http.Client client;
  
  ProductRemoteDataSourceImpl({required this.client});
  
  @override
  Future<List<ProductModel>> getProducts() async {
    final response = await client.get(
      Uri.parse('${ApiConstants.baseUrl}${ApiConstants.productsEndpoint}'),
    );
    
    if (response.statusCode == 200) {
      final List<dynamic> jsonList = jsonDecode(response.body);
      return jsonList
          .map((json) => ProductModel.fromJson(json))
          .toList();
    } else {
      throw ServerException('Failed to load products');
    }
  }
}
```

3. **Tạo Repository Implementation:**
Tạo `lib/features/products/data/repositories/product_repository_impl.dart`:
```dart
import '../../domain/entities/product.dart';
import '../../domain/repositories/product_repository.dart';
import '../datasources/product_remote_data_source.dart';
import '../../../../core/errors/failures.dart';

class ProductRepositoryImpl implements ProductRepository {
  final ProductRemoteDataSource remoteDataSource;
  
  ProductRepositoryImpl({required this.remoteDataSource});
  
  @override
  Future<Either<Failure, List<Product>>> getProducts() async {
    try {
      final products = await remoteDataSource.getProducts();
      return Right(products);
    } catch (e) {
      return Left(ServerFailure(e.toString()));
    }
  }
  
  @override
  Future<Either<Failure, Product>> getProduct(String id) async {
    // Implementation
    throw UnimplementedError();
  }
}
```

### Kết quả mong đợi
- Tạo được Data layer
- Hiểu về models, data sources, repositories

---

## BÀI TẬP 4: PRESENTATION LAYER

### Mục đích
Tạo Presentation layer với Bloc và UI.

### Yêu cầu

1. **Tạo Bloc:**
Tạo `lib/features/products/presentation/bloc/product_bloc.dart`:
```dart
import 'package:flutter_bloc/flutter_bloc.dart';
import '../../domain/entities/product.dart';
import '../../domain/usecases/get_products.dart';
import '../../../../core/errors/failures.dart';

// Events
abstract class ProductEvent {}

class LoadProducts extends ProductEvent {}

// States
abstract class ProductState {}

class ProductInitial extends ProductState {}

class ProductLoading extends ProductState {}

class ProductLoaded extends ProductState {
  final List<Product> products;
  ProductLoaded(this.products);
}

class ProductError extends ProductState {
  final String message;
  ProductError(this.message);
}

// Bloc
class ProductBloc extends Bloc<ProductEvent, ProductState> {
  final GetProducts getProducts;
  
  ProductBloc({required this.getProducts}) : super(ProductInitial()) {
    on<LoadProducts>(_onLoadProducts);
  }
  
  Future<void> _onLoadProducts(
    LoadProducts event,
    Emitter<ProductState> emit,
  ) async {
    emit(ProductLoading());
    
    final result = await getProducts();
    
    result.fold(
      (failure) => emit(ProductError(failure.message)),
      (products) => emit(ProductLoaded(products)),
    );
  }
}
```

2. **Tạo UI:**
Tạo `lib/features/products/presentation/pages/product_list_page.dart`:
```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import '../bloc/product_bloc.dart';

class ProductListPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      create: (context) => ProductBloc(
        getProducts: GetProducts(
          // Inject repository
        ),
      )..add(LoadProducts()),
      child: Scaffold(
        appBar: AppBar(title: Text('Products')),
        body: BlocBuilder<ProductBloc, ProductState>(
          builder: (context, state) {
            if (state is ProductLoading) {
              return Center(child: CircularProgressIndicator());
            } else if (state is ProductError) {
              return Center(child: Text('Error: ${state.message}'));
            } else if (state is ProductLoaded) {
              return ListView.builder(
                itemCount: state.products.length,
                itemBuilder: (context, index) {
                  final product = state.products[index];
                  return ListTile(
                    title: Text(product.name),
                    subtitle: Text('\$${product.price}'),
                  );
                },
              );
            }
            return Center(child: Text('No data'));
          },
        ),
      ),
    );
  }
}
```

### Kết quả mong đợi
- Tạo được Presentation layer
- Hiểu về Bloc pattern trong Clean Architecture

---

## BÀI TẬP 5: DEPENDENCY INJECTION

### Mục đích
Thiết lập Dependency Injection để kết nối các layers.

### Yêu cầu

1. **Thêm package:**
```yaml
dependencies:
  get_it: ^7.6.4
```

2. **Tạo injection container:**
Tạo `lib/injection/injection_container.dart`:
```dart
import 'package:get_it/get_it.dart';
import 'package:http/http.dart' as http;
import '../features/products/data/datasources/product_remote_data_source.dart';
import '../features/products/data/repositories/product_repository_impl.dart';
import '../features/products/domain/repositories/product_repository.dart';
import '../features/products/domain/usecases/get_products.dart';
import '../features/products/presentation/bloc/product_bloc.dart';

final sl = GetIt.instance;

Future<void> init() async {
  // Bloc
  sl.registerFactory(
    () => ProductBloc(getProducts: sl()),
  );
  
  // Use cases
  sl.registerLazySingleton(() => GetProducts(sl()));
  
  // Repository
  sl.registerLazySingleton<ProductRepository>(
    () => ProductRepositoryImpl(remoteDataSource: sl()),
  );
  
  // Data sources
  sl.registerLazySingleton<ProductRemoteDataSource>(
    () => ProductRemoteDataSourceImpl(client: sl()),
  );
  
  // External
  sl.registerLazySingleton(() => http.Client());
}
```

3. **Sử dụng trong main.dart:**
```dart
import 'injection/injection_container.dart' as di;

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await di.init();
  runApp(MyApp());
}
```

### Kết quả mong đợi
- Thiết lập được Dependency Injection
- Kết nối được các layers

---

## 📝 CHECKLIST HOÀN THÀNH

Sau khi hoàn thành tất cả bài tập, bạn nên:
- [ ] Hiểu nguyên tắc Clean Architecture
- [ ] Tổ chức code theo layers
- [ ] Tách biệt business logic và UI
- [ ] Xây dựng được ứng dụng theo chuẩn

---

## 🎉 KẾT THÚC

Chúc mừng! Bạn đã hoàn thành bài thực hành Clean Architecture.

👉 **Tiếp theo:** Bài 15 - Testing hoặc các bài nâng cao khác
