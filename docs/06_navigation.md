# 🟦 CHƯƠNG 06  
# **NAVIGATION TRONG FLUTTER**  
*(Điều hướng giữa các màn hình – push, pop, named routes – truyền dữ liệu)*

Một ứng dụng thực tế luôn có nhiều màn hình.  
Bạn không thể nhét tất cả UI vào 1 page được.

Chương này sẽ dạy bạn:

- chuyển màn hình  
- quay lại  
- truyền dữ liệu giữa các màn hình  
- dùng Named Routes  
- quản lý Navigation cho dự án lớn  

---

# 🎯 MỤC TIÊU HỌC TẬP

Sau chương này bạn có thể:

- Điều hướng giữa các màn hình bằng push/pop.  
- Truyền dữ liệu sang màn hình khác.  
- Nhận dữ liệu trả về từ màn hình.  
- Dùng Named Routes để quản lý nhiều màn hình.  
- Biết các lỗi thường gặp khi navigate.

---

# 1. **Cấu trúc dự án nhiều màn hình**

```
lib/
  main.dart
  screens/
    home_screen.dart
    detail_screen.dart
```

Mỗi màn hình là một Widget.

---

# 2. **Navigator.push – chuyển sang màn hình mới**

Ví dụ HomeScreen → DetailScreen:

### 🟩 HomeScreen

```dart
ElevatedButton(
  onPressed: () {
    Navigator.push(
      context,
      MaterialPageRoute(builder: (context) => const DetailScreen()),
    );
  },
  child: const Text("Đi tới màn chi tiết"),
);
```

### 🟩 DetailScreen

```dart
class DetailScreen extends StatelessWidget {
  const DetailScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Chi tiết")),
      body: const Center(child: Text("Detail Page")),
    );
  }
}
```

---

# 3. **Navigator.pop – quay về màn hình trước**

Trong DetailScreen:

```dart
Navigator.pop(context);
```

---

### 🎒 Ví dụ đời sống  
`Navigator.push` giống như **bạn đi vào phòng mới**.  
`Navigator.pop` giống như **bạn bước ra lại phòng cũ**.

Stack push–pop = chồng phòng.

---

# 4. **Truyền dữ liệu sang màn hình khác**

Ví dụ: từ HomeScreen → DetailScreen truyền "sản phẩm".

### 🟩 HomeScreen

```dart
Navigator.push(
  context,
  MaterialPageRoute(
    builder: (context) => DetailScreen(productName: "Laptop X"),
  ),
);
```

### 🟩 DetailScreen

```dart
class DetailScreen extends StatelessWidget {
  final String productName;

  const DetailScreen({super.key, required this.productName});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(productName)),
      body: Center(child: Text("Chi tiết: $productName")),
    );
  }
}
```

---

# 5. **Nhận dữ liệu trả về – giống “result” trong Android**

Ví dụ: màn chọn màu trả về kết quả:

### 🟩 Từ HomeScreen

```dart
final result = await Navigator.push(
  context,
  MaterialPageRoute(builder: (context) => const ColorPickerScreen()),
);

print("Màu đã chọn: $result");
```

### 🟩 Từ ColorPickerScreen

```dart
Navigator.pop(context, "red");
```

---

# 6. **Named Routes – Quản lý navigation cho dự án lớn**

### Đăng ký route trong MaterialApp

```dart
MaterialApp(
  routes: {
    '/': (context) => const HomeScreen(),
    '/detail': (context) => const DetailScreen(),
  },
);
```

### Điều hướng

```dart
Navigator.pushNamed(context, '/detail');
```

### Điều hướng kèm dữ liệu

Không thể truyền trực tiếp → dùng arguments:

```dart
Navigator.pushNamed(
  context,
  '/detail',
  arguments: "Laptop X",
);
```

### Nhận dữ liệu trong màn hình

```dart
@override
Widget build(BuildContext context) {
  final productName = ModalRoute.of(context)!.settings.arguments as String;
  return Text("Sản phẩm: $productName");
}
```

---

# 7. **onGenerateRoute – dành cho ứng dụng lớn và linh hoạt**

```dart
MaterialApp(
  onGenerateRoute: (settings) {
    if (settings.name == '/detail') {
      final data = settings.arguments as String;
      return MaterialPageRoute(
        builder: (context) => DetailScreen(productName: data),
      );
    }
    return MaterialPageRoute(builder: (context) => const HomeScreen());
  },
);
```

---

# 8. **BottomNavigationBar & Navigation nâng cao**  
(giới thiệu để sinh viên biết — sẽ học kỹ ở phần sau)

Bạn có thể tạo navigation kiểu tab:

- Home  
- Profile  
- Settings  

Hoặc dùng:

- go_router  
- AutoRoute  

Nhưng đó là ở phần nâng cao.

---

# 9. **Sai vs Đúng (Sinh viên hay mắc nhất)**

## ❌ Sai: push màn hình vào chính nó → bị loop

```dart
Navigator.push(context,
  MaterialPageRoute(builder: (_) => HomeScreen()));
```

## ✔ Đúng:
Chỉ push sang màn hình mới.

---

## ❌ Sai: quên truyền dữ liệu

```
Navigator.pushNamed(context, '/detail');
```

DetailScreen:

```
final data = ModalRoute.of(context)!.settings.arguments as String; // lỗi
```

## ✔ Đúng:

```
Navigator.pushNamed(context, '/detail', arguments: "abc");
```

---

## ❌ Sai: dùng ListView nhưng lại wrap Container height lớn → overflow  
(Chuyện rất thường xảy ra với sinh viên)

## ✔ Đúng: chỉ để ListView tự chiếm không gian

---

# 10. **Bài tập thực hành**

1. Tạo HomeScreen → nút “Đi tới Chi tiết” → DetailScreen.  
2. Truyền 1 chuỗi (tên sinh viên) sang màn hình chi tiết.  
3. Tạo ColorPickerScreen → trả kết quả về HomeScreen.  
4. Cấu hình named routes cho 3 màn hình (Home, Detail, Profile).  
5. Tạo ứng dụng mini: Danh sách sản phẩm → bấm vào 1 sản phẩm → sang DetailScreen.

---

# 11. Mini Test cuối chương

**Câu 1:** Navigator.push dùng để làm gì?  
→ Chuyển sang màn hình mới.

**Câu 2:** Làm sao để quay lại màn hình trước?  
→ `Navigator.pop(context)`.

**Câu 3:** Muốn truyền dữ liệu sang màn hình khác?  
→ Truyền qua constructor hoặc arguments.

**Câu 4:** Named routes là gì?  
→ Cách định nghĩa đường dẫn cho màn hình để dễ quản lý.

**Câu 5:** onGenerateRoute dùng trong trường hợp nào?  
→ Ứng dụng lớn, cần kiểm soát navigation linh hoạt.

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- push = đi tới màn mới, pop = quay lại.  
- Truyền dữ liệu bằng constructor hoặc arguments.  
- await Navigator.push → nhận dữ liệu trả về.  
- Named routes giúp quản lý nhiều màn hình.  
- onGenerateRoute = tuỳ chỉnh navigation nâng cao.

---

# 🎉 Kết thúc chương 06  
Tiếp theo, bạn sẽ học Form & Input — cốt lõi của mọi app thực tế:

👉 **Chương 07 – Form & Input (TextField, Validation, Keyboard)**

