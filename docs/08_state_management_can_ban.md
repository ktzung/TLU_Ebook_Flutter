# 🟦 CHƯƠNG 08  
# **STATE MANAGEMENT CĂN BẢN**  
*(setState – State – Lifting State Up – Nguyên lý hoạt động của UI Flutter)*

Quản lý state (trạng thái) là **linh hồn của Flutter**.  
Hầu như mọi bug UI của sinh viên đều xuất phát từ… *không hiểu state*.

Chương này sẽ giúp bạn nắm vững nền tảng để sau này học Provider, Riverpod, Bloc trở nên dễ dàng.

---

# 🎯 MỤC TIÊU HỌC TẬP

Sau chương này bạn sẽ:

- Hiểu State là gì và tại sao UI cần state.  
- Xác định lúc nào dùng StatefulWidget / setState.  
- Biết nguyên lý “UI rebuild”.  
- Biết cách truyền state lên (lifting state up).  
- Hiểu vòng đời (lifecycle) của StatefulWidget.  
- Viết được app nhỏ có state đơn giản.

---

# 1. **State là gì?**

**State = dữ liệu ảnh hưởng trực tiếp đến giao diện UI.**

Ví dụ:

- số đếm  
- nội dung TextField  
- danh sách sản phẩm  
- trạng thái loading  
- trạng thái đăng nhập  

Nếu dữ liệu thay đổi → UI phải thay đổi theo.

---

### 🎒 Ví dụ đời sống  
Bạn nhìn một nồi cơm điện:

- Khi đang nấu → đèn màu đỏ  
- Khi chín → đèn chuyển sang vàng  
- Khi giữ ấm → đèn chuyển xanh  

Đèn đổi màu = UI thay đổi theo trạng thái của nồi.

Đó chính là **state**.

---

# 2. **StatefulWidget – nền tảng quản lý state**

Một StatefulWidget gồm 2 phần:

```
StatefulWidget (khung)
↓
State (logic + dữ liệu thay đổi)
```

### Ví dụ cơ bản:

```dart
class CounterApp extends StatefulWidget {
  const CounterApp({super.key});

  @override
  State<CounterApp> createState() => _CounterAppState();
}

class _CounterAppState extends State<CounterApp> {
  int count = 0;

  void increase() {
    setState(() => count++);
  }

  @override
  Widget build(BuildContext context) {
    return Text("Count: $count");
  }
}
```

---

### 🧠 Giải thích cực dễ  
- StatefulWidget = cái "khung"  
- State = dữ liệu + logic thay đổi  
- setState() = báo Flutter: "UI ơi, rebuild lại đi!"

---

# 3. **setState() – vũ khí chính để cập nhật UI**

```dart
setState(() {
  count++;
});
```

Quy tắc:

- phải thay đổi **state bên trong setState**  
- setState luôn trigger UI rebuild  
- build() sẽ được gọi lại **nhiều lần** (hoàn toàn bình thường!)

---

### 🧠 Tại sao cần setState? (Lý thuyết sâu)

Flutter sử dụng **immutability** (bất biến) cho Widget. Điều này có nghĩa:

1. **Widget là immutable** → không thể thay đổi trực tiếp
2. **State là mutable** → có thể thay đổi, nhưng phải thông báo Flutter
3. **setState()** = cách duy nhất để thông báo: "State đã thay đổi, rebuild UI đi!"

**Cơ chế hoạt động:**

```
Thay đổi state → setState() → Flutter đánh dấu widget "dirty" 
→ build() được gọi → UI được rebuild với state mới
```

**Ví dụ minh họa:**

```dart
// ❌ SAI: Thay đổi state nhưng không báo Flutter
void increase() {
  count++;  // State thay đổi nhưng Flutter không biết!
  // UI vẫn hiển thị giá trị cũ
}

// ✅ ĐÚNG: Báo Flutter biết state đã thay đổi
void increase() {
  setState(() {
    count++;  // Flutter biết → rebuild UI
  });
}
```

---

### 🔄 Cơ chế Rebuild của Flutter (Lý thuyết chi tiết)

**Widget Tree vs Element Tree:**

```
Widget Tree (mô tả UI)          Element Tree (thực tế render)
     ↓                                    ↓
CounterApp                          Element(CounterApp)
     ↓                                    ↓
   Text                              Element(Text)
```

**Quá trình rebuild:**

1. **setState() được gọi** → Flutter đánh dấu Element là "dirty"
2. **Flutter so sánh** Widget cũ vs Widget mới (diff algorithm)
3. **Chỉ rebuild phần thay đổi** → tối ưu performance
4. **build() được gọi** → tạo Widget tree mới
5. **Element tree được cập nhật** → UI thay đổi

**Ví dụ minh họa:**

```dart
class _CounterAppState extends State<CounterApp> {
  int count = 0;
  
  void increase() {
    setState(() {
      count++;  // Bước 1: Thay đổi state
    });
    // Bước 2: Flutter đánh dấu dirty
    // Bước 3: build() được gọi tự động
    // Bước 4: UI hiển thị count mới
  }
  
  @override
  Widget build(BuildContext context) {
    print("build() được gọi - count = $count");
    // Hàm này sẽ chạy lại mỗi khi setState() được gọi
    return Text("Count: $count");
  }
}
```

**Lưu ý quan trọng:**

- build() **KHÔNG phải là vấn đề** nếu chạy nhiều lần
- Flutter chỉ **cập nhật phần thay đổi**, không rebuild toàn bộ app
- Performance của Flutter rất tốt nhờ diff algorithm thông minh

---

### ❌ Sai nhất của sinh viên:

```dart
count++;
print(count);
// nhưng không có setState
```

→ UI **KHÔNG cập nhật**.

---

# 4. **Các loại state thường gặp**

1. **Ephemeral state** (local state)  
   - đếm số  
   - bật/tắt nút  
   - trạng thái UI nhỏ  
→ Dùng `setState`.

2. **App-wide state** (global state)  
   - user login  
   - theme  
   - giỏ hàng  
→ Dùng Provider / Riverpod / BLoC (sẽ học ở chương sau).

---

# 5. **Lifting State Up – chia sẻ state giữa các widget**

Đây là kỹ năng sống còn để tránh viết code spaghetti.

Ví dụ:

```
Parent
│
├── ChildA (hiển thị count)
└── ChildB (nút tăng count)
```

→ count phải nằm trong **Parent**, không nằm trong Child.

### 🌟 Ví dụ code:

#### Parent

```dart
class Parent extends StatefulWidget {
  const Parent({super.key});

  @override
  State<Parent> createState() => _ParentState();
}

class _ParentState extends State<Parent> {
  int count = 0;

  void increase() => setState(() => count++);

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        ChildA(count: count),
        ChildB(onIncrease: increase),
      ],
    );
  }
}
```

#### ChildA

```dart
class ChildA extends StatelessWidget {
  final int count;
  const ChildA({required this.count, super.key});

  @override
  Widget build(BuildContext context) {
    return Text("Count: $count");
  }
}
```

#### ChildB

```dart
class ChildB extends StatelessWidget {
  final VoidCallback onIncrease;
  const ChildB({required this.onIncrease, super.key});

  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: onIncrease,
      child: const Text("Tăng"),
    );
  }
}
```

---

### 🎒 Ví dụ đời sống  
State giống như “nồi cơm”.  
Nhiều người ăn thì nồi phải đặt ở phòng bếp (parent),  
không phải ai cũng mang nồi riêng về phòng mình (child).

---

# 6. **Lifecycle của StatefulWidget – hiểu đúng tránh bug**

```
initState()
↓
didChangeDependencies()
↓
build()
↓
[setState() → build()] (lặp lại nhiều lần)
↓
dispose()
```

### Giải thích chi tiết:

#### 1. **initState()** - Khởi tạo một lần

- **Khi nào:** Chạy **1 lần duy nhất** khi widget được tạo
- **Dùng để:**
  - Khởi tạo biến state
  - Tạo controller (TextEditingController, AnimationController, etc.)
  - Subscribe stream, timer
  - Load dữ liệu ban đầu
- **Lưu ý:** KHÔNG gọi setState() trực tiếp trong initState() (dùng Future.microtask nếu cần)

```dart
@override
void initState() {
  super.initState();
  print("Widget được tạo");
  // ✅ ĐÚNG: Tạo controller
  _controller = TextEditingController();
  // ✅ ĐÚNG: Khởi tạo state
  _isLoading = false;
  // ✅ ĐÚNG: Load dữ liệu
  _loadData();
}
```

#### 2. **didChangeDependencies()** - Phụ thuộc thay đổi

- **Khi nào:** Chạy sau initState(), và mỗi khi InheritedWidget thay đổi
- **Dùng để:** Lấy dữ liệu từ context (Theme, MediaQuery, etc.)
- **Lưu ý:** Ít khi cần override, nhưng hữu ích khi cần dữ liệu từ context

```dart
@override
void didChangeDependencies() {
  super.didChangeDependencies();
  // Lấy theme từ context
  final theme = Theme.of(context);
  print("Theme: ${theme.brightness}");
}
```

#### 3. **build()** - Xây dựng UI

- **Khi nào:** Chạy nhiều lần (sau initState, sau setState, sau didUpdateWidget)
- **Dùng để:** Trả về Widget tree
- **Lưu ý:** KHÔNG đặt logic nặng, KHÔNG tạo controller, KHÔNG gọi async operations

```dart
@override
Widget build(BuildContext context) {
  // ✅ ĐÚNG: Chỉ build UI
  return Column(
    children: [
      Text("Count: $count"),
      ElevatedButton(onPressed: increase, child: Text("Tăng")),
    ],
  );
}
```

#### 4. **didUpdateWidget()** - Widget cha thay đổi

- **Khi nào:** Khi widget cha rebuild và truyền widget mới vào
- **Dùng để:** So sánh widget cũ vs mới, cập nhật state nếu cần

```dart
@override
void didUpdateWidget(CounterApp oldWidget) {
  super.didUpdateWidget(oldWidget);
  // So sánh và cập nhật nếu cần
  if (oldWidget.initialValue != widget.initialValue) {
    count = widget.initialValue;
  }
}
```

#### 5. **dispose()** - Dọn dẹp

- **Khi nào:** Chạy 1 lần khi widget bị xóa khỏi tree
- **Dùng để:**
  - Dispose controller
  - Cancel timer, stream subscription
  - Giải phóng tài nguyên
- **Lưu ý:** LUÔN gọi super.dispose() ở cuối

```dart
@override
void dispose() {
  print("Widget bị huỷ");
  // ✅ QUAN TRỌNG: Dispose controller
  _controller.dispose();
  // ✅ QUAN TRỌNG: Cancel timer
  _timer?.cancel();
  super.dispose();
}
```

---

## 🌟 Ví dụ hoàn chỉnh với lifecycle:

```dart
class TimerWidget extends StatefulWidget {
  const TimerWidget({super.key});

  @override
  State<TimerWidget> createState() => _TimerWidgetState();
}

class _TimerWidgetState extends State<TimerWidget> {
  int seconds = 0;
  Timer? _timer;

  @override
  void initState() {
    super.initState();
    print("1. initState() - Widget được tạo");
    // Tạo timer trong initState
    _timer = Timer.periodic(const Duration(seconds: 1), (timer) {
      setState(() {
        seconds++;
      });
    });
  }

  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    print("2. didChangeDependencies() - Phụ thuộc thay đổi");
  }

  @override
  Widget build(BuildContext context) {
    print("3. build() - Xây dựng UI (seconds = $seconds)");
    return Text("Timer: $seconds giây");
  }

  @override
  void dispose() {
    print("4. dispose() - Widget bị huỷ");
    // QUAN TRỌNG: Cancel timer để tránh memory leak
    _timer?.cancel();
    super.dispose();
  }
}
```

**Kết quả console:**
```
1. initState() - Widget được tạo
2. didChangeDependencies() - Phụ thuộc thay đổi
3. build() - Xây dựng UI (seconds = 0)
3. build() - Xây dựng UI (seconds = 1)
3. build() - Xây dựng UI (seconds = 2)
...
4. dispose() - Widget bị huỷ
```

---

# 7. **Sai vs Đúng – sinh viên hay mắc nhất**

## ❌ Sai: dùng StatefulWidget cho UI không thay đổi  
→ Gánh nặng performance không cần thiết.

## ✔ Đúng: dùng StatelessWidget khi UI tĩnh.

```dart
// ❌ SAI: UI không thay đổi nhưng dùng StatefulWidget
class StaticText extends StatefulWidget {
  @override
  State<StaticText> createState() => _StaticTextState();
}
class _StaticTextState extends State<StaticText> {
  @override
  Widget build(BuildContext context) {
    return Text("Hello World"); // Không có state nào!
  }
}

// ✅ ĐÚNG: Dùng StatelessWidget
class StaticText extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Text("Hello World");
  }
}
```

---

## ❌ Sai: setState bên ngoài State class  
→ Flutter crash.

## ✔ Đúng: chỉ dùng setState trong State class.

```dart
// ❌ SAI: setState bên ngoài State class
void someFunction() {
  setState(() { count++; }); // Lỗi!
}

// ✅ ĐÚNG: setState trong State class
class _MyWidgetState extends State<MyWidget> {
  void someFunction() {
    setState(() { count++; }); // OK!
  }
}
```

---

## ❌ Sai: logic ở trong build()  
→ build() được gọi lại rất nhiều lần → chậm.

## ✔ Đúng: logic đặt trong hàm riêng hoặc initState.

```dart
// ❌ SAI: Logic nặng trong build()
@override
Widget build(BuildContext context) {
  // Tính toán phức tạp mỗi lần build() chạy
  final expensiveResult = calculateExpensiveThing();
  return Text("$expensiveResult");
}

// ✅ ĐÚNG: Cache kết quả trong state
class _MyWidgetState extends State<MyWidget> {
  String? _cachedResult;
  
  @override
  void initState() {
    super.initState();
    _cachedResult = calculateExpensiveThing();
  }
  
  @override
  Widget build(BuildContext context) {
    return Text("$_cachedResult");
  }
}
```

---

## ❌ Sai: tạo controller trong build()  
→ lặp vô tận (memory leak)

## ✔ Đúng: Tạo trong initState, dispose trong dispose()

```dart
// ❌ SAI: Tạo controller trong build()
@override
Widget build(BuildContext context) {
  final controller = TextEditingController(); // Tạo mới mỗi lần build!
  return TextField(controller: controller);
  // Controller không được dispose → memory leak!
}

// ✅ ĐÚNG: Tạo trong initState, dispose trong dispose()
class _MyWidgetState extends State<MyWidget> {
  late final TextEditingController _controller;
  
  @override
  void initState() {
    super.initState();
    _controller = TextEditingController(); // Tạo 1 lần
  }
  
  @override
  void dispose() {
    _controller.dispose(); // Giải phóng
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    return TextField(controller: _controller);
  }
}
```

---

## ❌ Sai: Thay đổi state không dùng setState

```dart
// ❌ SAI: Thay đổi state nhưng UI không cập nhật
void increase() {
  count++; // State thay đổi nhưng Flutter không biết!
}

// ✅ ĐÚNG: Dùng setState
void increase() {
  setState(() {
    count++; // Flutter biết → rebuild UI
  });
}
```

---

## ❌ Sai: Gọi setState trong initState trực tiếp

```dart
// ❌ SAI: setState ngay trong initState
@override
void initState() {
  super.initState();
  setState(() {
    count = 10; // Có thể gây lỗi
  });
}

// ✅ ĐÚNG: Dùng Future.microtask nếu cần
@override
void initState() {
  super.initState();
  Future.microtask(() {
    if (mounted) {
      setState(() {
        count = 10;
      });
    }
  });
}
```

---

## ❌ Sai: Quên kiểm tra mounted trước khi setState trong async

```dart
// ❌ SAI: setState sau khi widget đã bị dispose
Future<void> loadData() async {
  await Future.delayed(Duration(seconds: 2));
  setState(() {
    data = "Loaded"; // Có thể lỗi nếu widget đã bị dispose
  });
}

// ✅ ĐÚNG: Kiểm tra mounted
Future<void> loadData() async {
  await Future.delayed(Duration(seconds: 2));
  if (mounted) {
    setState(() {
      data = "Loaded"; // An toàn
    });
  }
}
```

---

## ❌ Sai: State trong child thay vì parent (không lifting state up)

```dart
// ❌ SAI: State ở child, không thể chia sẻ
class Parent extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        ChildA(), // Muốn hiển thị count
        ChildB(), // Muốn tăng count
      ],
    );
  }
}

// ✅ ĐÚNG: State ở parent, truyền xuống children
class Parent extends StatefulWidget {
  @override
  State<Parent> createState() => _ParentState();
}

class _ParentState extends State<Parent> {
  int count = 0;
  
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        ChildA(count: count),
        ChildB(onIncrease: () => setState(() => count++)),
      ],
    );
  }
}
```

---

# 8. **Các ví dụ thực tế đa dạng**

## 8.1. **Ví dụ cơ bản: App Counter**

```dart
class CounterApp extends StatefulWidget {
  const CounterApp({super.key});

  @override
  State<CounterApp> createState() => _CounterAppState();
}

class _CounterAppState extends State<CounterApp> {
  int count = 0;

  void increase() => setState(() => count++);
  void decrease() => setState(() => count--);
  void reset() => setState(() => count = 0);

  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Text("Count: $count", style: const TextStyle(fontSize: 24)),
        const SizedBox(height: 20),
        Row(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(onPressed: decrease, child: const Text("-")),
            const SizedBox(width: 20),
            ElevatedButton(onPressed: reset, child: const Text("Reset")),
            const SizedBox(width: 20),
            ElevatedButton(onPressed: increase, child: const Text("+")),
          ],
        )
      ],
    );
  }
}
```

---

## 8.2. **Ví dụ: Toggle Switch (Dark/Light Mode)**

```dart
class ThemeToggleApp extends StatefulWidget {
  const ThemeToggleApp({super.key});

  @override
  State<ThemeToggleApp> createState() => _ThemeToggleAppState();
}

class _ThemeToggleAppState extends State<ThemeToggleApp> {
  bool isDarkMode = false;

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: isDarkMode 
        ? ThemeData.dark() 
        : ThemeData.light(),
      home: Scaffold(
        appBar: AppBar(
          title: const Text("Theme Toggle"),
        ),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Text(
                isDarkMode ? "Dark Mode" : "Light Mode",
                style: const TextStyle(fontSize: 24),
              ),
              const SizedBox(height: 20),
              Switch(
                value: isDarkMode,
                onChanged: (value) {
                  setState(() {
                    isDarkMode = value;
                  });
                },
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

---

## 8.3. **Ví dụ: Form Validation với TextEditingController**

```dart
class LoginForm extends StatefulWidget {
  const LoginForm({super.key});

  @override
  State<LoginForm> createState() => _LoginFormState();
}

class _LoginFormState extends State<LoginForm> {
  // QUAN TRỌNG: Tạo controller trong State class
  final TextEditingController _emailController = TextEditingController();
  final TextEditingController _passwordController = TextEditingController();
  
  String? _emailError;
  String? _passwordError;
  bool _isLoading = false;

  @override
  void dispose() {
    // QUAN TRỌNG: Dispose controller
    _emailController.dispose();
    _passwordController.dispose();
    super.dispose();
  }

  void _validateAndSubmit() {
    setState(() {
      // Reset errors
      _emailError = null;
      _passwordError = null;
      
      // Validate email
      if (_emailController.text.isEmpty) {
        _emailError = "Email không được để trống";
      } else if (!_emailController.text.contains("@")) {
        _emailError = "Email không hợp lệ";
      }
      
      // Validate password
      if (_passwordController.text.isEmpty) {
        _passwordError = "Mật khẩu không được để trống";
      } else if (_passwordController.text.length < 6) {
        _passwordError = "Mật khẩu phải có ít nhất 6 ký tự";
      }
    });

    // Nếu không có lỗi, submit form
    if (_emailError == null && _passwordError == null) {
      _submitForm();
    }
  }

  void _submitForm() {
    setState(() {
      _isLoading = true;
    });
    
    // Giả lập API call
    Future.delayed(const Duration(seconds: 2), () {
      if (mounted) {
        setState(() {
          _isLoading = false;
        });
        // Hiển thị thông báo thành công
        ScaffoldMessenger.of(context).showSnackBar(
          const SnackBar(content: Text("Đăng nhập thành công!")),
        );
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Đăng nhập")),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            TextField(
              controller: _emailController,
              decoration: InputDecoration(
                labelText: "Email",
                errorText: _emailError,
                border: const OutlineInputBorder(),
              ),
            ),
            const SizedBox(height: 16),
            TextField(
              controller: _passwordController,
              obscureText: true,
              decoration: InputDecoration(
                labelText: "Mật khẩu",
                errorText: _passwordError,
                border: const OutlineInputBorder(),
              ),
            ),
            const SizedBox(height: 24),
            _isLoading
              ? const CircularProgressIndicator()
              : ElevatedButton(
                  onPressed: _validateAndSubmit,
                  child: const Text("Đăng nhập"),
                ),
          ],
        ),
      ),
    );
  }
}
```

---

## 8.4. **Ví dụ: Shopping Cart (Giỏ hàng)**

```dart
class Product {
  final String name;
  final double price;
  
  Product({required this.name, required this.price});
}

class ShoppingCartApp extends StatefulWidget {
  const ShoppingCartApp({super.key});

  @override
  State<ShoppingCartApp> createState() => _ShoppingCartAppState();
}

class _ShoppingCartAppState extends State<ShoppingCartApp> {
  final List<Product> _products = [
    Product(name: "Áo thun", price: 200000),
    Product(name: "Quần jean", price: 500000),
    Product(name: "Giày thể thao", price: 800000),
  ];
  
  final Map<Product, int> _cart = {}; // Product -> Số lượng

  void _addToCart(Product product) {
    setState(() {
      _cart[product] = (_cart[product] ?? 0) + 1;
    });
  }

  void _removeFromCart(Product product) {
    setState(() {
      if (_cart[product] != null) {
        if (_cart[product]! > 1) {
          _cart[product] = _cart[product]! - 1;
        } else {
          _cart.remove(product);
        }
      }
    });
  }

  double _getTotalPrice() {
    double total = 0;
    _cart.forEach((product, quantity) {
      total += product.price * quantity;
    });
    return total;
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Cửa hàng"),
        actions: [
          Stack(
            children: [
              IconButton(
                icon: const Icon(Icons.shopping_cart),
                onPressed: () {
                  // Hiển thị giỏ hàng
                },
              ),
              if (_cart.isNotEmpty)
                Positioned(
                  right: 8,
                  top: 8,
                  child: Container(
                    padding: const EdgeInsets.all(4),
                    decoration: const BoxDecoration(
                      color: Colors.red,
                      shape: BoxShape.circle,
                    ),
                    child: Text(
                      "${_cart.values.reduce((a, b) => a + b)}",
                      style: const TextStyle(
                        color: Colors.white,
                        fontSize: 12,
                      ),
                    ),
                  ),
                ),
            ],
          ),
        ],
      ),
      body: Column(
        children: [
          Expanded(
            child: ListView.builder(
              itemCount: _products.length,
              itemBuilder: (context, index) {
                final product = _products[index];
                final quantity = _cart[product] ?? 0;
                
                return ListTile(
                  title: Text(product.name),
                  subtitle: Text("${product.price.toStringAsFixed(0)} đ"),
                  trailing: Row(
                    mainAxisSize: MainAxisSize.min,
                    children: [
                      if (quantity > 0) ...[
                        IconButton(
                          icon: const Icon(Icons.remove),
                          onPressed: () => _removeFromCart(product),
                        ),
                        Text("$quantity"),
                      ],
                      IconButton(
                        icon: const Icon(Icons.add),
                        onPressed: () => _addToCart(product),
                      ),
                    ],
                  ),
                );
              },
            ),
          ),
          Container(
            padding: const EdgeInsets.all(16),
            decoration: BoxDecoration(
              color: Colors.grey[200],
              boxShadow: [
                BoxShadow(
                  color: Colors.black.withOpacity(0.1),
                  blurRadius: 4,
                ),
              ],
            ),
            child: Row(
              mainAxisAlignment: MainAxisAlignment.spaceBetween,
              children: [
                Text(
                  "Tổng tiền: ${_getTotalPrice().toStringAsFixed(0)} đ",
                  style: const TextStyle(
                    fontSize: 18,
                    fontWeight: FontWeight.bold,
                  ),
                ),
                ElevatedButton(
                  onPressed: _cart.isEmpty ? null : () {
                    // Thanh toán
                  },
                  child: const Text("Thanh toán"),
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }
}
```

---

## 8.5. **Ví dụ: Todo List (Danh sách công việc)**

```dart
class Todo {
  final String id;
  String title;
  bool isCompleted;
  
  Todo({
    required this.id,
    required this.title,
    this.isCompleted = false,
  });
}

class TodoListApp extends StatefulWidget {
  const TodoListApp({super.key});

  @override
  State<TodoListApp> createState() => _TodoListAppState();
}

class _TodoListAppState extends State<TodoListApp> {
  final List<Todo> _todos = [];
  final TextEditingController _todoController = TextEditingController();

  @override
  void dispose() {
    _todoController.dispose();
    super.dispose();
  }

  void _addTodo() {
    if (_todoController.text.trim().isEmpty) return;
    
    setState(() {
      _todos.add(Todo(
        id: DateTime.now().millisecondsSinceEpoch.toString(),
        title: _todoController.text.trim(),
      ));
      _todoController.clear();
    });
  }

  void _toggleTodo(String id) {
    setState(() {
      final todo = _todos.firstWhere((t) => t.id == id);
      todo.isCompleted = !todo.isCompleted;
    });
  }

  void _deleteTodo(String id) {
    setState(() {
      _todos.removeWhere((t) => t.id == id);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Todo List"),
      ),
      body: Column(
        children: [
          Padding(
            padding: const EdgeInsets.all(16.0),
            child: Row(
              children: [
                Expanded(
                  child: TextField(
                    controller: _todoController,
                    decoration: const InputDecoration(
                      hintText: "Nhập công việc mới...",
                      border: OutlineInputBorder(),
                    ),
                    onSubmitted: (_) => _addTodo(),
                  ),
                ),
                const SizedBox(width: 8),
                ElevatedButton(
                  onPressed: _addTodo,
                  child: const Text("Thêm"),
                ),
              ],
            ),
          ),
          Expanded(
            child: _todos.isEmpty
              ? const Center(
                  child: Text(
                    "Chưa có công việc nào",
                    style: TextStyle(fontSize: 18, color: Colors.grey),
                  ),
                )
              : ListView.builder(
                  itemCount: _todos.length,
                  itemBuilder: (context, index) {
                    final todo = _todos[index];
                    return ListTile(
                      leading: Checkbox(
                        value: todo.isCompleted,
                        onChanged: (_) => _toggleTodo(todo.id),
                      ),
                      title: Text(
                        todo.title,
                        style: TextStyle(
                          decoration: todo.isCompleted
                            ? TextDecoration.lineThrough
                            : null,
                          color: todo.isCompleted ? Colors.grey : null,
                        ),
                      ),
                      trailing: IconButton(
                        icon: const Icon(Icons.delete, color: Colors.red),
                        onPressed: () => _deleteTodo(todo.id),
                      ),
                    );
                  },
                ),
          ),
          if (_todos.isNotEmpty)
            Padding(
              padding: const EdgeInsets.all(16.0),
              child: Text(
                "Tổng: ${_todos.length} | "
                "Hoàn thành: ${_todos.where((t) => t.isCompleted).length} | "
                "Chưa làm: ${_todos.where((t) => !t.isCompleted).length}",
                style: const TextStyle(fontSize: 14, color: Colors.grey),
              ),
            ),
        ],
      ),
    );
  }
}
```

---

## 8.6. **Ví dụ: Color Picker (Chọn màu nền)**

```dart
class ColorPickerApp extends StatefulWidget {
  const ColorPickerApp({super.key});

  @override
  State<ColorPickerApp> createState() => _ColorPickerAppState();
}

class _ColorPickerAppState extends State<ColorPickerApp> {
  Color _selectedColor = Colors.white;
  
  final List<Color> _colors = [
    Colors.red,
    Colors.blue,
    Colors.green,
    Colors.yellow,
    Colors.orange,
    Colors.purple,
    Colors.pink,
    Colors.teal,
    Colors.brown,
    Colors.grey,
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Color Picker"),
      ),
      backgroundColor: _selectedColor,
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Container(
              padding: const EdgeInsets.all(20),
              decoration: BoxDecoration(
                color: Colors.white,
                borderRadius: BorderRadius.circular(12),
                boxShadow: [
                  BoxShadow(
                    color: Colors.black.withOpacity(0.1),
                    blurRadius: 10,
                  ),
                ],
              ),
              child: Column(
                children: [
                  const Text(
                    "Chọn màu nền",
                    style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
                  ),
                  const SizedBox(height: 20),
                  Wrap(
                    spacing: 10,
                    runSpacing: 10,
                    children: _colors.map((color) {
                      final isSelected = color == _selectedColor;
                      return GestureDetector(
                        onTap: () {
                          setState(() {
                            _selectedColor = color;
                          });
                        },
                        child: Container(
                          width: 50,
                          height: 50,
                          decoration: BoxDecoration(
                            color: color,
                            shape: BoxShape.circle,
                            border: Border.all(
                              color: isSelected ? Colors.black : Colors.transparent,
                              width: 3,
                            ),
                          ),
                          child: isSelected
                            ? const Icon(Icons.check, color: Colors.white)
                            : null,
                        ),
                      );
                    }).toList(),
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

---

## 8.7. **Ví dụ: Loading State với Conditional Rendering**

```dart
class DataLoaderApp extends StatefulWidget {
  const DataLoaderApp({super.key});

  @override
  State<DataLoaderApp> createState() => _DataLoaderAppState();
}

class _DataLoaderAppState extends State<DataLoaderApp> {
  bool _isLoading = false;
  String? _data;
  String? _error;

  Future<void> _loadData() async {
    setState(() {
      _isLoading = true;
      _error = null;
      _data = null;
    });

    // Giả lập API call
    await Future.delayed(const Duration(seconds: 2));

    // Giả lập: 70% thành công, 30% lỗi
    if (DateTime.now().millisecond % 10 < 7) {
      if (mounted) {
        setState(() {
          _isLoading = false;
          _data = "Dữ liệu đã tải thành công!";
        });
      }
    } else {
      if (mounted) {
        setState(() {
          _isLoading = false;
          _error = "Lỗi khi tải dữ liệu!";
        });
      }
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Data Loader")),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            if (_isLoading)
              const Column(
                children: [
                  CircularProgressIndicator(),
                  SizedBox(height: 16),
                  Text("Đang tải dữ liệu..."),
                ],
              )
            else if (_error != null)
              Column(
                children: [
                  const Icon(Icons.error, color: Colors.red, size: 64),
                  const SizedBox(height: 16),
                  Text(
                    _error!,
                    style: const TextStyle(color: Colors.red, fontSize: 18),
                  ),
                  const SizedBox(height: 16),
                  ElevatedButton(
                    onPressed: _loadData,
                    child: const Text("Thử lại"),
                  ),
                ],
              )
            else if (_data != null)
              Column(
                children: [
                  const Icon(Icons.check_circle, color: Colors.green, size: 64),
                  const SizedBox(height: 16),
                  Text(
                    _data!,
                    style: const TextStyle(fontSize: 18),
                  ),
                  const SizedBox(height: 16),
                  ElevatedButton(
                    onPressed: _loadData,
                    child: const Text("Tải lại"),
                  ),
                ],
              )
            else
              ElevatedButton(
                onPressed: _loadData,
                child: const Text("Tải dữ liệu"),
              ),
          ],
        ),
      ),
    );
  }
}
```

---

# 9. **Performance và Best Practices**

## 9.1. **Khi nào dùng StatelessWidget vs StatefulWidget?**

### StatelessWidget - Dùng khi:
- UI không thay đổi
- Chỉ nhận dữ liệu từ parent qua constructor
- Không có state nội bộ

```dart
// ✅ ĐÚNG: StatelessWidget cho UI tĩnh
class ProductCard extends StatelessWidget {
  final String name;
  final double price;
  
  const ProductCard({
    required this.name,
    required this.price,
    super.key,
  });
  
  @override
  Widget build(BuildContext context) {
    return Card(
      child: ListTile(
        title: Text(name),
        subtitle: Text("${price.toStringAsFixed(0)} đ"),
      ),
    );
  }
}
```

### StatefulWidget - Dùng khi:
- UI thay đổi theo state
- Cần quản lý controller (TextEditingController, AnimationController)
- Cần timer, stream subscription
- Cần form validation

```dart
// ✅ ĐÚNG: StatefulWidget cho UI động
class CounterWidget extends StatefulWidget {
  @override
  State<CounterWidget> createState() => _CounterWidgetState();
}

class _CounterWidgetState extends State<CounterWidget> {
  int count = 0; // State thay đổi
  
  @override
  Widget build(BuildContext context) {
    return Text("Count: $count");
  }
}
```

---

## 9.2. **Tối ưu Performance**

### 1. Tránh rebuild không cần thiết

```dart
// ❌ SAI: Rebuild toàn bộ widget tree
@override
Widget build(BuildContext context) {
  return Column(
    children: [
      ExpensiveWidget(), // Rebuild mỗi lần
      Text("Count: $count"),
    ],
  );
}

// ✅ ĐÚNG: Tách widget không cần rebuild
class _MyWidgetState extends State<MyWidget> {
  int count = 0;
  
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        const ExpensiveWidget(), // const → không rebuild
        Text("Count: $count"),
      ],
    );
  }
}
```

### 2. Sử dụng const constructor

```dart
// ✅ ĐÚNG: const cho widget không thay đổi
const Text("Hello"),
const SizedBox(height: 20),
const Icon(Icons.star),
```

### 3. Cache giá trị tính toán

```dart
// ❌ SAI: Tính toán lại mỗi lần build
@override
Widget build(BuildContext context) {
  final expensiveValue = calculateExpensiveThing();
  return Text("$expensiveValue");
}

// ✅ ĐÚNG: Cache trong state
class _MyWidgetState extends State<MyWidget> {
  String? _cachedValue;
  
  @override
  void initState() {
    super.initState();
    _cachedValue = calculateExpensiveThing();
  }
  
  @override
  Widget build(BuildContext context) {
    return Text("$_cachedValue");
  }
}
```

---

## 9.3. **Best Practices**

### 1. Luôn dispose controller

```dart
@override
void dispose() {
  _controller.dispose();
  _timer?.cancel();
  _subscription?.cancel();
  super.dispose();
}
```

### 2. Kiểm tra mounted trong async

```dart
Future<void> loadData() async {
  await Future.delayed(Duration(seconds: 2));
  if (mounted) {
    setState(() {
      // Cập nhật state
    });
  }
}
```

### 3. Tách logic ra khỏi build()

```dart
// ✅ ĐÚNG: Logic trong method riêng
void _handleButtonPress() {
  // Logic phức tạp
  setState(() {
    // Cập nhật state
  });
}

@override
Widget build(BuildContext context) {
  return ElevatedButton(
    onPressed: _handleButtonPress,
    child: Text("Click"),
  );
}
```

### 4. Sử dụng key khi cần

```dart
// Khi có list động, dùng key để Flutter track đúng widget
ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) {
    return ListTile(
      key: ValueKey(items[index].id), // Giúp Flutter track đúng
      title: Text(items[index].name),
    );
  },
)
```

---

# 10. **Bài tập thực hành**

1. **Tạo StatefulWidget quản lý chế độ dark/light (toggle switch).**  
   → Xem ví dụ 8.2

2. **Tạo UI hiển thị số lượng item trong giỏ hàng + nút tăng/giảm.**  
   → Xem ví dụ 8.4

3. **Dùng lifting state up:**  
   - Parent quản lý số đếm  
   - ChildA hiển thị  
   - ChildB tăng  
   - ChildC giảm  
   → Xem ví dụ phần 5

4. **Tạo bộ đếm giờ (timer) bằng initState + dispose.**  
   → Xem ví dụ lifecycle phần 6

5. **Tạo app chọn màu nền → click màu nào đổi background.**  
   → Xem ví dụ 8.6

6. **Tạo form đăng ký với validation:**
   - Email (phải có @)
   - Mật khẩu (ít nhất 8 ký tự, có chữ hoa, số)
   - Xác nhận mật khẩu (phải khớp)
   - Hiển thị lỗi real-time

7. **Tạo app quản lý danh sách sản phẩm:**
   - Thêm/sửa/xóa sản phẩm
   - Tìm kiếm sản phẩm
   - Lọc theo giá
   - Hiển thị tổng giá trị

8. **Tạo app quiz (câu hỏi trắc nghiệm):**
   - Hiển thị câu hỏi
   - Chọn đáp án
   - Hiển thị kết quả đúng/sai
   - Đếm điểm
   - Nút "Câu tiếp theo"

---

# 11. Mini Test cuối chương

**Câu 1:** State là gì?  
→ Dữ liệu ảnh hưởng trực tiếp đến UI.

**Câu 2:** Tại sao cần setState?  
→ Để thông báo Flutter build lại UI.

**Câu 3:** initState chạy khi nào?  
→ Khi widget được tạo.

**Câu 4:** Lifting state up nghĩa là gì?  
→ Đưa state lên widget cha để chia sẻ cho nhiều widget con.

**Câu 5:** Vì sao không đặt logic trong build()?  
→ build() chạy nhiều lần → chậm app.

**Câu 6:** Tại sao cần dispose controller?  
→ Để tránh memory leak, giải phóng tài nguyên.

**Câu 7:** Khi nào dùng StatelessWidget?  
→ Khi UI không thay đổi, không có state nội bộ.

**Câu 8:** Immutability trong Flutter là gì?  
→ Widget là immutable (bất biến), không thể thay đổi trực tiếp.

**Câu 9:** Tại sao phải kiểm tra mounted trước setState trong async?  
→ Tránh lỗi khi widget đã bị dispose nhưng async operation vẫn chạy.

**Câu 10:** Lifting state up nghĩa là gì?  
→ Đưa state lên widget cha để chia sẻ cho nhiều widget con.

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- **State** = dữ liệu làm thay đổi UI.  
- **Stateless vs Stateful** = tĩnh vs động.  
- **setState()** = kích hoạt UI rebuild.  
- **State nên đặt ở widget cha** khi cần chia sẻ (lifting state up).  
- **initState & dispose** cực quan trọng khi dùng controller/timer.  
- **Không viết logic trong build()** - đặt trong method riêng hoặc initState.  
- **Luôn dispose controller** trong dispose() để tránh memory leak.  
- **Kiểm tra mounted** trước setState trong async operations.  
- **Widget là immutable** - chỉ có thể thay đổi qua setState().  
- **Flutter rebuild thông minh** - chỉ cập nhật phần thay đổi, không rebuild toàn bộ.

---

# 🎉 Kết thúc chương 08  
Tiếp theo, chúng ta học mức “state quản lý chuyên nghiệp”:

👉 **Chương 09 – State Management nâng cao với Provider**

