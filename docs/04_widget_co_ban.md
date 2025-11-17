# 🟦 CHƯƠNG 04  
# **WIDGET CƠ BẢN TRONG FLUTTER**  
*(StatelessWidget – StatefulWidget – Text – Button – Layout cơ bản)*

Đây là chương quan trọng nhất dành cho người mới.  
Flutter = Widgets.  
Hiểu Widgets = biết Flutter.

Trong chương này, bạn sẽ học cách tạo giao diện bằng những widget cơ bản nhất nhưng dùng *suốt đời*.

---

# 🎯 MỤC TIÊU HỌC TẬP

Sau chương này bạn có thể:

- Hiểu StatelessWidget & StatefulWidget.  
- Xây UI bằng Text, Image, Icon, Button.  
- Sử dụng Column, Row, Center, Container.  
- Biết style cơ bản của các widget.  
- Tránh lỗi thường gặp khi viết UI.  
- Tự xây một màn hình UI đơn giản.

---

# 1. **Widget là gì? (Giải thích dễ nhất)**

Widget = mảnh ghép nhỏ tạo thành giao diện Flutter.

- Text → widget  
- Button → widget  
- Image → widget  
- ListView → widget  
- App → cũng là widget  

Flutter xây dựng toàn bộ UI bằng việc lắp ghép các widget này lại.

---

### 🎒 Ví dụ đời sống  
Widget giống như **LEGO**:  
Bạn ráp nhiều mảnh nhỏ → thành 1 công trình lớn.

---

# 2. StatelessWidget – UI không thay đổi

Dùng khi UI **không có trạng thái**, không cần cập nhật lại.

Ví dụ:  
- tiêu đề  
- banner  
- logo  
- nội dung tĩnh  

### 📌 Ví dụ:

```dart
class HelloText extends StatelessWidget {
  const HelloText({super.key});

  @override
  Widget build(BuildContext context) {
    return const Text(
      "Xin chào Flutter!",
      style: TextStyle(fontSize: 20),
    );
  }
}
```

---

# 3. StatefulWidget – UI thay đổi theo trạng thái

Dùng khi UI **có giá trị thay đổi**, ví dụ:

- Counter (tăng giảm số)  
- Form nhập liệu  
- Switch, Checkbox  
- API loading  

### 📌 Ví dụ:

```dart
class CounterApp extends StatefulWidget {
  const CounterApp({super.key});

  @override
  State<CounterApp> createState() => _CounterAppState();
}

class _CounterAppState extends State<CounterApp> {
  int count = 0;

  void increase() {
    setState(() {
      count++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Text("Count: $count"),
        ElevatedButton(
          onPressed: increase,
          child: const Text("Tăng"),
        )
      ],
    );
  }
}
```

> **Ghi nhớ:** mọi thay đổi UI trong StatefulWidget phải gọi `setState()`.

---

# 4. Các widget cơ bản bạn sẽ dùng suốt đời

## 4.1. Text – hiển thị chữ

```dart
Text(
  "Hello!",
  style: TextStyle(
    fontSize: 24,
    color: Colors.blue,
    fontWeight: FontWeight.bold,
  ),
);
```

---

## 4.2. Image – hiển thị ảnh

### Ảnh trong asset:

```dart
Image.asset("assets/images/banner.png");
```

### Ảnh từ internet:

```dart
Image.network("https://picsum.photos/200");
```

---

## 4.3. Icon

```dart
const Icon(
  Icons.favorite,
  color: Colors.red,
  size: 32,
);
```

---

## 4.4. Button (nút bấm cơ bản)

```dart
ElevatedButton(
  onPressed: () => print("Clicked!"),
  child: const Text("Nhấn tôi"),
);
```

Các loại button khác:

- `TextButton`  
- `OutlinedButton`  
- `IconButton`  
- `FloatingActionButton`  

---

# 5. Các widget bố cục (layout) quan trọng nhất

## 5.1. Center – căn giữa

```dart
Center(
  child: Text("Hello"),
);
```

---

## 5.2. Container – widget “tất cả trong một”

Giúp:

- padding  
- margin  
- background  
- border  
- size  

```dart
Container(
  padding: const EdgeInsets.all(16),
  margin: const EdgeInsets.symmetric(vertical: 20),
  color: Colors.amber,
  child: const Text("Box"),
);
```

---

## 5.3. Row – xếp theo chiều ngang

```dart
Row(
  mainAxisAlignment: MainAxisAlignment.center,
  children: const [
    Icon(Icons.star),
    Text("Hạng VIP"),
  ],
);
```

---

## 5.4. Column – xếp theo chiều dọc

```dart
Column(
  crossAxisAlignment: CrossAxisAlignment.start,
  children: const [
    Text("Tên: Dũng"),
    Text("Nghề: Lập trình viên"),
  ],
);
```

---

## 5.5. SizedBox – tạo khoảng cách

```dart
SizedBox(height: 20)
```

---

### 🎨 Ví dụ minh họa tổng hợp

```dart
Center(
  child: Column(
    mainAxisSize: MainAxisSize.min,
    children: const [
      Text("Xin chào!"),
      SizedBox(height: 16),
      Icon(Icons.flutter_dash, size: 48),
    ],
  ),
);
```

---

# 6. Tạo UI màn hình hoàn chỉnh đơn giản

```dart
class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Home")),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            const Text("Xin chào Flutter!"),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {},
              child: const Text("Nhấn tôi"),
            ),
          ],
        ),
      ),
    );
  }
}
```

---

# 7. Lỗi thường gặp của sinh viên

| Lỗi | Nguyên nhân | Cách sửa |
|-----|-------------|----------|
| “setState() called but nothing changed” | logic sai | đảm bảo biến thay đổi trong setState |
| Text bị tràn màn hình | quên dùng Expanded/Flexible | xem chương Layout nâng cao |
| UI không hiển thị | build() không trả widget | trả về widget, không return null |
| Overflow (chéo màu vàng) | Column/Row không giới hạn | thêm Expanded hoặc đặt height cố định |
| import sai thư mục | tách file lung tung | tổ chức lại project theo chuẩn |

---

# 8. Bài tập thực hành

1. Tạo HomeScreen gồm: Text + Icon + ElevatedButton.  
2. Tạo widget ProfileCard gồm avatar + tên + nút follow.  
3. Tạo StatefulWidget Counter có nút tăng/giảm.  
4. Dùng Row + Column tạo layout danh thiếp cá nhân.  
5. Tạo UI sản phẩm: ảnh + tên + giá + nút mua.

---

# 9. Mini Test cuối chương

**Câu 1:** Widget nào dùng khi UI thay đổi?  
→ StatefulWidget.

**Câu 2:** Muốn cập nhật UI thì dùng hàm gì?  
→ setState().

**Câu 3:** Dùng widget nào để hiển thị ảnh từ file?  
→ Image.asset.

**Câu 4:** Row xếp widget theo hướng nào?  
→ Ngang.

**Câu 5:** Container dùng để làm gì?  
→ Tạo box: padding, margin, background, border, size…

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- Mọi thứ trong Flutter là widget.  
- Stateless = không thay đổi, Stateful = thay đổi theo state.  
- Column và Row là nền tảng của mọi layout.  
- Container là widget “đa năng”.  
- setState() = cập nhật UI.  
- Dùng SizedBox để tạo khoảng cách.

---

# 🎉 Kết thúc chương 04  
Tiếp theo chúng ta nâng cấp khả năng thiết kế UI:

👉 **Chương 05 – Layout Nâng Cao (Expanded, Flexible, Stack, ListView, GridView)**

