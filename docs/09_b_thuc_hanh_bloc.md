# 🟦 THỰC HÀNH CHI TIẾT: BLOC & CUBIT (BÀI 09+)

Tài liệu này giúp bạn thực hành kiến trúc **BLoC (Business Logic Component)**.
Chúng ta sẽ ưu tiên dùng **Cubit** cho các bài cơ bản vì nó gọn nhẹ hơn, sau đó nâng cấp lên **Bloc** cho bài phức tạp.

> **⚠️ BẮT BUỘC:** Hãy gõ code theo từng bước.
> **💡 TƯ DUY:**
> - **Cubit:** Gọi hàm -> Bắn State.
> - **Bloc:** Gửi Event -> Bắn State.
> - **UI:** Dùng `BlocBuilder` để vẽ, `BlocListener` để xử lý sự kiện phụ (SnackBar, Navigate).

---

## 🎯 MỤC TIÊU SẢN PHẨM
1.  **Level 1 (Dễ): Counter Cubit** - *Làm quen Cubit cơ bản.*
2.  **Level 2 (Trung bình): Theme Cubit** - *Quản lý giao diện Sáng/Tối.*
3.  **Level 3 (Khó): Login Bloc** - *Xử lý trạng thái Loading/Success/Failure giả lập.*
4.  **Level 4 (Rất khó): Internet Check** - *Mô phỏng check kết nối mạng (Logic Stream).*

---

## 🛠️ CHUẨN BỊ
1.  Tạo dự án mới:
    ```bash
    flutter create thuc_hanh_bloc
    cd thuc_hanh_bloc
    ```
2.  **Cài đặt thư viện flutter_bloc:**
    Mở `pubspec.yaml`, thêm vào phần `dependencies`:
    ```yaml
    dependencies:
      flutter:
        sdk: flutter
      flutter_bloc: ^8.1.0  # <--- Thư viện quan trọng nhất
      equatable: ^2.0.5     # <--- Giúp so sánh State dễ hơn (tùy chọn nhưng nên dùng)
    ```
    Sau đó chạy lệnh: `flutter pub get`

3.  Setup `main.dart` trống:

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';

// import 'bai1_counter_cubit.dart';

void main() {
  runApp(const MaterialApp(
    home: Scaffold(body: Center(child: Text("SETUP XONG"))),
  ));
}
```

---

## 🟢 LEVEL 1: COUNTER CUBIT (NHẬP MÔN)
**Mục tiêu:** Tăng giảm số đếm dùng Cubit.
**Tư duy:** State là một số nguyên `int`.

### 📝 Hướng dẫn từng bước:

**Bước 1:** Tạo file `lib/cubits/counter_cubit.dart`.

```dart
import 'package:flutter_bloc/flutter_bloc.dart';

// Cubit<int>: State quản lý là kiểu int
class CounterCubit extends Cubit<int> {
  // Khởi tạo giá trị ban đầu là 0
  CounterCubit() : super(0);

  // Logic tăng
  void increment() => emit(state + 1);

  // Logic giảm
  void decrement() => emit(state - 1);
}
```

**Bước 2:** Tạo UI `lib/bai1_counter.dart`.

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'cubits/counter_cubit.dart';

class CounterPage extends StatelessWidget {
  const CounterPage({super.key});

  @override
  Widget build(BuildContext context) {
    // BlocProvider cung cấp Cubit cho nhánh Widget con
    return BlocProvider(
      create: (_) => CounterCubit(),
      child: const CounterView(),
    );
  }
}

class CounterView extends StatelessWidget {
  const CounterView({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Counter Cubit")),
      body: Center(
        // BlocBuilder: Lắng nghe và vẽ lại UI khi state đổi
        child: BlocBuilder<CounterCubit, int>(
          builder: (context, count) {
            return Text(
              '$count',
              style: const TextStyle(fontSize: 50, fontWeight: FontWeight.bold),
            );
          },
        ),
      ),
      floatingActionButton: Column(
        mainAxisAlignment: MainAxisAlignment.end,
        children: [
          FloatingActionButton(
            // context.read: Gọi hàm logic
            onPressed: () => context.read<CounterCubit>().increment(),
            child: const Icon(Icons.add),
          ),
          const SizedBox(height: 10),
          FloatingActionButton(
            onPressed: () => context.read<CounterCubit>().decrement(),
            child: const Icon(Icons.remove),
          ),
        ],
      ),
    );
  }
}
```

> **🧠 Giải thích code:**
> - `emit(newValue)`: Thay thế `setState`. Nó bắn tín hiệu ra ngoài.
> - `BlocProvider`: Phải bao bọc widget muốn dùng Cubit.
> - `BlocBuilder`: Chỉ rebuild đúng cái Text bên trong, không rebuild cả màn hình -> Siêu tối ưu.

---

## 🟡 LEVEL 2: THEME CUBIT (GLOBAL BLOC)
**Mục tiêu:** Áp dụng Cubit cho toàn bộ ứng dụng (chuyển màu Sáng/Tối).

### 📝 Hướng dẫn từng bước:

**Bước 1:** Tạo `lib/cubits/theme_cubit.dart`.

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';

// State ở đây là ThemeData luôn cho tiện
class ThemeCubit extends Cubit<ThemeData> {
  // Mặc định là Light Theme
  ThemeCubit() : super(ThemeData.light());

  void toggleTheme() {
    // Nếu đang là sáng thì chuyển tối, ngược lại
    if (state.brightness == Brightness.light) {
      emit(ThemeData.dark());
    } else {
      emit(ThemeData.light());
    }
  }
}
```

**Bước 2:** Cấu hình `main.dart` để bọc toàn bộ App.

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'cubits/theme_cubit.dart';
import 'bai1_counter.dart'; // Tận dụng lại bài 1

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    // Cung cấp ThemeCubit cho toàn bộ app
    return BlocProvider(
      create: (_) => ThemeCubit(),
      child: BlocBuilder<ThemeCubit, ThemeData>(
        builder: (context, theme) {
          return MaterialApp(
            debugShowCheckedModeBanner: false,
            title: 'Flutter Bloc Demo',
            theme: theme, // Theme thay đổi theo state
            home: const HomePage(),
          );
        },
      ),
    );
  }
}

class HomePage extends StatelessWidget {
  const HomePage({super.key});
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Theme Switcher")),
      body: Center(
        child: ElevatedButton(
          onPressed: () {
            // Gọi hàm toggleTheme
            context.read<ThemeCubit>().toggleTheme();
          },
          child: const Text("Đổi Giao Diện"),
        ),
      ),
    );
  }
}
```

---

## 🟠 LEVEL 3: LOGIN BLOC (XỬ LÝ TRẠNG THÁI PHỨC TẠP)
**Mục tiêu:** Giả lập quá trình đăng nhập.
**Vấn đề:** Đăng nhập có 3 giai đoạn: `Loading` (xoay xoay) -> `Success` (vào nhà) hoặc `Failure` (báo lỗi).
**Tư duy:** Dùng Class state chứ không dùng kiểu nguyên thủy nữa.

### 📝 Hướng dẫn từng bước:

**Bước 1:** Định nghĩa State (`lib/blocs/login/login_state.dart`).

```dart
abstract class LoginState {}

class LoginInitial extends LoginState {}      // Trạng thái ban đầu
class LoginLoading extends LoginState {}      // Đang xử lý
class LoginSuccess extends LoginState {}      // Thành công
class LoginFailure extends LoginState {       // Thất bại
  final String error;
  LoginFailure(this.error);
}
```

**Bước 2:** Viết Logic Cubit (`lib/blocs/login/login_cubit.dart`).

```dart
import 'package:flutter_bloc/flutter_bloc.dart';
import 'login_state.dart';

class LoginCubit extends Cubit<LoginState> {
  LoginCubit() : super(LoginInitial());

  Future<void> login(String username, String password) async {
    // 1. Bắn ra trạng thái Loading
    emit(LoginLoading());

    // 2. Giả lập gọi API mất 2 giây
    await Future.delayed(const Duration(seconds: 2));

    // 3. Kiểm tra kết quả
    if (username == "admin" && password == "123456") {
      emit(LoginSuccess());
    } else {
      emit(LoginFailure("Sai tài khoản hoặc mật khẩu!"));
    }
  }
}
```

**Bước 3:** Tạo giao diện Login (`lib/bai3_login.dart`).

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'blocs/login/login_cubit.dart';
import 'blocs/login/login_state.dart';

class LoginScreen extends StatelessWidget {
  const LoginScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      create: (context) => LoginCubit(),
      child: const LoginForm(), // Tách ra widget con để code gọn
    );
  }
}

class LoginForm extends StatefulWidget {
  const LoginForm({super.key});
  @override
  State<LoginForm> createState() => _LoginFormState();
}

class _LoginFormState extends State<LoginForm> {
  final _userController = TextEditingController();
  final _passController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Đăng Nhập Bloc")),
      body: Padding(
        padding: const EdgeInsets.all(20),
        
        // BlocConsumer = BlocBuilder + BlocListener
        // Vừa vẽ lại UI (builder), vừa lắng nghe sự kiện (listener)
        child: BlocConsumer<LoginCubit, LoginState>(
          listener: (context, state) {
            // Xử lý sự kiện 1 lần (SnackBar, Dialog, Navigate)
            if (state is LoginFailure) {
              ScaffoldMessenger.of(context).showSnackBar(
                SnackBar(content: Text(state.error), backgroundColor: Colors.red),
              );
            } else if (state is LoginSuccess) {
              ScaffoldMessenger.of(context).showSnackBar(
                const SnackBar(content: Text("Đăng nhập thành công!"), backgroundColor: Colors.green),
              );
              // Navigator.pushNamed(context, '/home'); // Chuyển màn hình ở đây
            }
          },
          builder: (context, state) {
            // Vẽ giao diện dựa trên state
            if (state is LoginLoading) {
              return const Center(child: CircularProgressIndicator());
            }

            return Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                const Icon(Icons.person, size: 80, color: Colors.blue),
                const SizedBox(height: 20),
                TextField(
                  controller: _userController,
                  decoration: const InputDecoration(labelText: "Username (admin)", border: OutlineInputBorder()),
                ),
                const SizedBox(height: 10),
                TextField(
                  controller: _passController,
                  obscureText: true,
                  decoration: const InputDecoration(labelText: "Password (123456)", border: OutlineInputBorder()),
                ),
                const SizedBox(height: 20),
                SizedBox(
                  width: double.infinity,
                  height: 50,
                  child: ElevatedButton(
                    onPressed: () {
                      context.read<LoginCubit>().login(
                        _userController.text, 
                        _passController.text
                      );
                    },
                    child: const Text("LOGIN"),
                  ),
                )
              ],
            );
          },
        ),
      ),
    );
  }
}
```

> **🧠 Giải thích code:**
> - `BlocConsumer`: Widget mạnh mẽ nhất. 
>   - `builder`: Dùng để vẽ những thứ **tĩnh** trên màn hình (nút bấm, ô nhập, vòng xoay loading).
>   - `listener`: Dùng để xử lý những thứ **động** chỉ xảy ra 1 lần (Thông báo lỗi, Chuyển trang). Không bao giờ vẽ UI (navigate) trong `builder`.

---

## 🏆 TỔNG KẾT
Bạn đã chạm tay vào **Bloc** - kiến trúc tiêu chuẩn công nghiệp.
- **Cubit** là khởi đầu hoàn hảo: `Function` -> `State`.
- **BlocConsumer** là công cụ đắc lực để vừa vẽ vừa xử lý sự kiện.

Khi làm dự án thực tế, bạn sẽ thấy Bloc giúp code cực kỳ ngăn nắp, dễ mở rộng và đặc biệt là **Debug siêu sướng** (vì biết chính xác dòng logic nào bắn ra state nào).
