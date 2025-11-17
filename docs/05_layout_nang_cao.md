# 🟦 CHƯƠNG 05  
# **LAYOUT NÂNG CAO TRONG FLUTTER**  
*(Expanded – Flexible – Stack – ListView – GridView – Responsive)*

Nếu chương 04 giúp bạn biết “xếp” UI, thì chương này giúp bạn xây **layout chuyên nghiệp**, không lỗi tràn, không bị “đổ bố cục”, và phù hợp cho nhiều kích thước màn hình.

Đây là chương bắt buộc phải nắm vững trước khi làm app thật.

---

# 🎯 MỤC TIÊU HỌC TẬP

Sau chương này bạn có thể:

- Hiểu khi nào dùng Expanded & Flexible.  
- Dùng Stack để chồng widget.  
- Sử dụng ListView để cuộn nội dung.  
- Tạo GridView để hiển thị dạng lưới.  
- Xử lý Overflow (cháy layout).  
- Làm UI phản hồi theo kích thước màn hình (responsive).

---

# 1. **Expanded – GIẢI PHÁP CHO MỌI KIỂU TRÀN MÀN HÌNH**

Trong Column/Row, nếu con chiếm quá nhiều không gian → lỗi OVERFLOW.

### 🧩 Cách sửa: bọc bằng Expanded

```dart
Expanded(
  child: Container(color: Colors.red),
)
```

Expanded chiếm **phần còn lại** của không gian.

---

## Ví dụ

```dart
Column(
  children: [
    Container(height: 100, color: Colors.blue),
    Expanded(
      child: Container(color: Colors.green),
    ),
  ],
);
```

→ Khối xanh lá sẽ tự giãn chiếm toàn bộ phần trống còn lại.

---

### 🎒 Ví dụ đời sống  
Expanded giống như **cái bong bóng** trong hộp → nó tự phồng ra chiếm hết khoảng trống.

---

# 2. **Flexible – Linh hoạt hơn Expanded**

Flexible cũng chiếm không gian, nhưng **không bắt buộc chiếm hết**.

### Ví dụ:

```dart
Flexible(
  flex: 2,
  child: Container(color: Colors.orange),
),
```

`flex` xác định độ ưu tiên phân chia không gian.

---

### Expanded vs Flexible

| Widget | Chiếm toàn bộ không gian còn lại? | Dùng khi |
|--------|---------------------------------|----------|
| Expanded | ✔ Có | khi muốn chiếm hết |
| Flexible | ✘ Không | khi cần linh hoạt theo nội dung |

---

# 3. **Stack – Xếp chồng widget lên nhau**

### Khi nào dùng Stack?

- Banner có chữ phía trên  
- Avatar có nút edit góc dưới  
- Màn hình có nhiều lớp UI  

### Ví dụ:

```dart
Stack(
  children: [
    Image.asset("assets/images/banner.png"),
    Positioned(
      bottom: 20,
      left: 20,
      child: Text("Welcome!", style: TextStyle(fontSize: 24, color: Colors.white)),
    )
  ],
);
```

---

### 🎒 Ví dụ đời sống  
Stack giống như **xếp bánh kem nhiều lớp** → lớp nào cũng nằm trên lớp khác.

---

# 4. **ListView – Cuộn danh sách**

Trong ứng dụng thật, nội dung dài gần như luôn cần cuộn.

### Dùng ListView đơn giản:

```dart
ListView(
  children: const [
    Text("Item 1"),
    Text("Item 2"),
    Text("Item 3"),
  ],
);
```

### ListView.builder (dùng nhiều nhất)

```dart
ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) {
    return Text(items[index]);
  },
);
```

=> Tối ưu cho danh sách dài.

---

# 5. **GridView – Hiển thị dạng lưới**

Dùng cho UI kiểu:

- danh sách sản phẩm  
- bộ sưu tập ảnh  
- chọn icon  

### Ví dụ:

```dart
GridView.count(
  crossAxisCount: 2,
  children: List.generate(4, (i) {
    return Container(
      margin: const EdgeInsets.all(8),
      color: Colors.blue,
      child: Center(child: Text("Item $i")),
    );
  }),
);
```

---

# 6. **SingleChildScrollView – Cuộn 1 màn hình dài**

Dùng cho màn hình form, giới thiệu, profile dài.

```dart
SingleChildScrollView(
  child: Column(
    children: [...],
  ),
);
```

### ⚠ Cẩn thận:
- Không dùng `ListView` lồng `SingleChildScrollView`.  
- Không dùng `SingleChildScrollView` bên trong ListView.  

---

# 7. **Responsive Layout – UI phù hợp mọi màn hình**

Dùng `MediaQuery`:

```dart
double width = MediaQuery.of(context).size.width;

if (width < 400) {
  return Text("Màn nhỏ");
} else {
  return Text("Màn lớn");
}
```

Hoặc:

```dart
LayoutBuilder(
  builder: (context, constraints) {
    if (constraints.maxWidth < 500) {
      return MobileLayout();
    } else {
      return TabletLayout();
    }
  },
);
```

---

# 8. **Sai vs Đúng (dành cho sinh viên hay lỗi)**

## ❌ Sai: Row không đủ chỗ
```
Row(
  children: [
    Text("Tên rất rất dài..."),
    Text("Giá: 1000000đ"),
  ],
);
```
→ Lỗi OVERFLOW

## ✔ Đúng:
```
Expanded(
  child: Text("Tên rất rất dài..."),
)
```

---

## ❌ Sai: List dài nhưng không cuộn
```
Column(
  children: List.generate(100, (i) => Text("Item $i")),
);
```

## ✔ Đúng:
```
ListView.builder(...)
```

---

# 9. **Bài tập thực hành**

1. Tạo màn hình profile có avatar, tên, bio và nút Follow → dùng Column + Row + Padding.  
2. Tạo layout banner dùng Stack (ảnh nền + text).  
3. Tạo danh sách 50 sản phẩm dùng ListView.builder.  
4. Tạo lưới ảnh 3 cột bằng GridView.count.  
5. Làm UI trang giới thiệu công ty bằng SingleChildScrollView.  
6. Tạo hai giao diện Mobile/Tablet bằng LayoutBuilder.

---

# 10. Mini Test cuối chương

**Câu 1:** Widget nào dùng để chiếm không gian còn lại?  
→ Expanded.

**Câu 2:** Khi nào dùng Stack?  
→ Khi muốn xếp widget chồng lên nhau.

**Câu 3:** Dùng widget nào để hiển thị danh sách dài?  
→ ListView.builder.

**Câu 4:** crossAxisCount trong GridView là gì?  
→ số cột.

**Câu 5:** LayoutBuilder dùng để làm gì?  
→ tạo responsive UI.

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- Expanded = chiếm hết chỗ trống.  
- Flexible = chiếm chỗ nhưng không bắt buộc đầy.  
- Stack = chồng UI.  
- ListView = cuộn danh sách dài.  
- GridView = hiển thị dạng lưới.  
- SingleChildScrollView = cuộn 1 màn hình dài.  
- MediaQuery / LayoutBuilder = responsive.  

---

# 🎉 Kết thúc chương 05  
Tiếp theo, bạn sẽ học cách điều hướng giữa các màn hình:

👉 **Chương 06 – Navigation (Điều hướng trong Flutter)**

