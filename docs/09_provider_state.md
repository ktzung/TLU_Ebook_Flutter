# 🟦 CHƯƠNG 09  
# **STATE MANAGEMENT NÂNG CAO VỚI PROVIDER**  
*(ChangeNotifier – Provider – Consumer – MultiProvider – Tổ chức kiến trúc)*

Khi ứng dụng lớn dần, `setState()` trở nên rối:

- state nằm lung tung  
- truyền xuống quá nhiều widget con  
- khó bảo trì  
- màn hình rebuild quá nhiều  

Đây là lúc **Provider** tỏa sáng.  
Provider là state management đơn giản nhất, gọn nhất và được chính Google khuyến nghị.

---

# 🎯 MỤC TIÊU HỌC TẬP

Sau chương này bạn có thể:

- Hiểu Provider là gì và tại sao cần dùng.  
- Tạo ChangeNotifier để quản lý state.  
- Sử dụng Provider, Consumer, context.watch(), context.read().  
- Áp dụng MultiProvider cho dự án lớn.  
- Chia kiến trúc Model → Provider → UI.  
- Xây một mini app với Provider.

---

# 1. **Provider là gì?**

Provider là “bình chứa state + logic” đặt ở trên cao (root) để mọi widget bên dưới có thể:

- đọc state  
- lắng nghe thay đổi  
- cập nhật UI  

Provider giúp loại bỏ việc truyền state lòng vòng giữa các widget.

---

### 🎒 Ví dụ đời sống  
Provider giống **loa phát thanh của khu dân cư**:

- thông báo được phát 1 lần ở trạm loa  
- mọi nhà đều nghe được  
- không ai phải truyền miệng vòng vòng nữa  

---

# 2. **Cài đặt Provider**

Thêm vào pubspec.yaml:

```yaml
dependencies:
  provider: ^6.0.0
```

---

# 3. **ChangeNotifier – nơi giữ state và logic**

Tạo file:

```
lib/providers/counter_provider.dart
```

```dart
import 'package:flutter/foundation.dart';

class CounterProvider extends ChangeNotifier {
  int count = 0;

  void increase() {
    count++;
    notifyListeners();
  }

  void decrease() {
    count--;
    notifyListeners();
  }
}
```

### Giải thích:

- `count` là state  
- `increase()` thay đổi state  
- `notifyListeners()` báo UI rebuild  

---

# 4. **Khởi tạo Provider trong ứng dụng**

Trong `main.dart`:

```dart
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (_) => CounterProvider(),
      child: const MyApp(),
    ),
  );
}
```

Từ giờ, mọi widget trong MyApp đều truy cập được CounterProvider.

---

# 5. **Đọc state từ Provider**

## Cách 1 — context.watch<T>()  
UI tự rebuild khi giá trị thay đổi.

```dart
final count = context.watch<CounterProvider>().count;
```

---

## Cách 2 — context.read<T>()  
Chỉ gọi hành động, **không rebuild**.

```dart
context.read<CounterProvider>().increase();
```

→ Dùng trong onPressed là tốt nhất.

---

## Cách 3 — Consumer<T>()  
Chỉ rebuild đúng widget chứa Consumer.

```dart
Consumer<CounterProvider>(
  builder: (context, provider, child) {
    return Text("Count: ${provider.count}");
  },
)
```

---

# 6. **Ví dụ hoàn chỉnh: Counter App bằng Provider**

### HomeScreen

```dart
class CounterScreen extends StatelessWidget {
  const CounterScreen({super.key});

  @override
  Widget build(BuildContext context) {
    final count = context.watch<CounterProvider>().count;

    return Scaffold(
      appBar: AppBar(title: const Text("Counter Provider")),
      body: Center(
        child: Text(
          "Count: $count",
          style: const TextStyle(fontSize: 32),
        ),
      ),
      floatingActionButton: Row(
        mainAxisAlignment: MainAxisAlignment.end,
        children: [
          FloatingActionButton(
            onPressed: () => context.read<CounterProvider>().increase(),
            child: const Icon(Icons.add),
          ),
          const SizedBox(width: 20),
          FloatingActionButton(
            onPressed: () => context.read<CounterProvider>().decrease(),
            child: const Icon(Icons.remove),
          ),
        ],
      ),
    );
  }
}
```

---

# 7. Sử dụng MultiProvider – nhiều provider trong ứng dụng lớn

```dart
MultiProvider(
  providers: [
    ChangeNotifierProvider(create: (_) => CounterProvider()),
    ChangeNotifierProvider(create: (_) => UserProvider()),
    ChangeNotifierProvider(create: (_) => CartProvider()),
  ],
  child: const MyApp(),
);
```

---

# 8. **Quy tắc vàng khi dùng Provider**

1. **State đặt ở Provider**, không đặt ở UI.  
2. UI chỉ hiển thị và gọi action (`increase()`, `addToCart()`).  
3. Không thay đổi state bên ngoài Provider.  
4. Không dùng watch trong build nếu không cần.  
5. Consumer dùng khi muốn tối ưu hiệu năng.

---

# 9. **Sai vs Đúng (sinh viên rất hay gặp)**

## ❌ Sai: gọi notifyListeners() trong build()

```dart
build() {
  provider.notifyListeners();  // Đây là tội ác lập trình
}
```

→ vòng lặp vô hạn.

## ✔ Đúng:
Chỉ gọi trong hàm xử lý (increase, decrease,…)

---

## ❌ Sai: dùng watch trong onPressed

```dart
onPressed: () => context.watch<CounterProvider>().increase(),
```

## ✔ Đúng:

```dart
onPressed: () => context.read<CounterProvider>().increase(),
```

---

## ❌ Sai: truyền state thủ công giữa widget con  
→ rối, trùng lặp, khó bảo trì

## ✔ Đúng: để Provider quản lý

---

## ❌ Sai: đặt quá nhiều state trong 1 provider  
→ class phình to

## ✔ Đúng: chia thành nhiều provider nhỏ

---

# 10. **Ví dụ nâng cao: Quản lý giỏ hàng**

### CartProvider

```dart
class CartProvider extends ChangeNotifier {
  List<String> items = [];

  void addItem(String item) {
    items.add(item);
    notifyListeners();
  }

  void removeItem(String item) {
    items.remove(item);
    notifyListeners();
  }

  int get count => items.length;
}
```

### UI hiển thị giỏ hàng

```dart
final cart = context.watch<CartProvider>();

Text("Số lượng: ${cart.count}");
```

### Nút thêm vào giỏ

```dart
onPressed: () => context.read<CartProvider>().addItem("Sản phẩm A")
```

---

# 11. Bài tập thực hành

1. Tạo CounterApp với tăng/giảm-reset bằng Provider.  
2. Tạo TodoApp mini với Provider (danh sách công việc).  
3. Tạo CartApp có thêm/xóa sản phẩm + tính tổng giá.  
4. Tách dự án thành 3 provider: User, Theme, Cart.  
5. Tạo màn hình login → lưu trạng thái user vào Provider.

---

# 12. Mini Test cuối chương

**Câu 1:** ChangeNotifier dùng để làm gì?  
→ quản lý state và notifyListeners.

**Câu 2:** context.watch() dùng để?  
→ lắng nghe state và rebuild UI.

**Câu 3:** context.read() dùng để?  
→ gọi action, không rebuild.

**Câu 4:** Consumer giúp gì?  
→ chỉ rebuild widget bên trong.

**Câu 5:** notifyListeners() dùng khi nào?  
→ khi state thay đổi và muốn cập nhật UI.

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- Provider = state management đơn giản + hiệu quả nhất.  
- ChangeNotifier giữ state + logic.  
- watch() để lắng nghe, read() để hành động.  
- Consumer tối ưu performance.  
- MultiProvider dùng cho ứng dụng lớn.

---

# 🎉 Kết thúc chương 09  
Tiếp theo là “đỉnh cao” trong Flutter cơ bản:

👉 **Chương 10 – Networking & API (http, Future, JSON, FutureBuilder)**

