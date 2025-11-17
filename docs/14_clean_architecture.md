# 🟦 CHƯƠNG 14  
# **GESTURE & INTERACTION TRONG FLUTTER**  
*(Tap – Double Tap – Long Press – Drag – GestureDetector – InkWell)*

App không chỉ để xem — mà phải **chạm được**, **kéo được**, **vuốt được**, **giữ được**, **nhấn được**, **phóng to/thu nhỏ được**.

Chương này giúp bạn tạo ra ứng dụng tương tác mềm mại và tự nhiên.

---

# 🎯 MỤC TIÊU HỌC TẬP

Sau chương này bạn sẽ:

- Sử dụng GestureDetector để bắt các thao tác chạm.  
- Tạo hiệu ứng tap như ứng dụng thật bằng InkWell.  
- Xử lý các tương tác: tap, double tap, long press, drag.  
- Tạo UI kéo thả (drag & drop).  
- Tạo vùng chạm "mở rộng" cho button nhỏ.  
- Biết lỗi thường gặp và cách tránh.

---

# 1. **GestureDetector – widget bắt mọi loại tương tác**

GestureDetector giống như “cảm biến chạm” của widget.

### Ví dụ đơn giản:

```dart
GestureDetector(
  onTap: () {
    print("Bạn đã chạm!");
  },
  child: Container(
    width: 100,
    height: 100,
    color: Colors.red,
  ),
);
```

---

# 2. **Các loại gesture thường dùng**

### 1. onTap – chạm 1 lần

```dart
onTap: () => print("Tap"),
```

### 2. onDoubleTap – chạm 2 lần

```dart
onDoubleTap: () => print("Double Tap"),
```

### 3. onLongPress – nhấn giữ

```dart
onLongPress: () => print("Long Press"),
```

### 4. onPanUpdate – kéo (drag)

```dart
onPanUpdate: (details) {
  print("dx: ${details.delta.dx}, dy: ${details.delta.dy}");
}
```

### 5. onTapDown – chạm xuống (nhưng chưa nhả)

```dart
onTapDown: (_) => print("Down"),
```

---

### 🎒 Ví dụ đời sống  
GestureDetector giống như **cái công tắc cảm ứng**:  
bạn chạm nhẹ – nó biết, nhấn giữ – nó biết, vuốt – nó biết luôn.

---

# 3. **InkWell – hiệu ứng ripple (gợn sóng) khi bấm**

InkWell là button *ngầm*, hiển thị hiệu ứng “sóng nước” khi bấm, rất đẹp.

### Ví dụ:

```dart
InkWell(
  onTap: () => print("Pressed"),
  child: Container(
    padding: const EdgeInsets.all(16),
    child: const Text("InkWell Button"),
  ),
);
```

---

# 4. **InkResponse – phiên bản nâng cấp của InkWell**

Hỗ trợ hiệu ứng bo tròn tốt hơn:

```dart
InkResponse(
  onTap: () {},
  splashColor: Colors.red,
  child: const Icon(Icons.favorite),
);
```

---

# 5. **GestureDetector vs InkWell – nên dùng cái nào?**

| Tiêu chí | GestureDetector | InkWell |
|---------|------------------|---------|
| Bắt nhiều loại gesture | ✔ Rất mạnh | ✘ chỉ tap |
| Hiệu ứng ripple | ✘ Không có | ✔ Có |
| Hình dáng button | tự custom | dễ dùng |
| Dùng trong MaterialApp | được | ✔ đẹp hơn |

👉 **Nếu chỉ muốn “bấm + hiệu ứng” → dùng InkWell.**  
👉 **Nếu muốn bắt nhiều gesture → dùng GestureDetector.**

---

# 6. **Làm nút bấm có vùng chạm lớn hơn (dành cho nút nhỏ)**

```dart
GestureDetector(
  behavior: HitTestBehavior.opaque,
  onTap: () => print("tap"),
  child: const Padding(
    padding: EdgeInsets.all(20),
    child: Icon(Icons.close),
  ),
);
```

→ tránh lỗi tap “khó bấm”.

---

# 7. **Tạo hiệu ứng kéo (drag) để di chuyển widget**

### Ví dụ: kéo một hình tròn theo tay

```dart
class DragBall extends StatefulWidget {
  const DragBall({super.key});

  @override
  State<DragBall> createState() => _DragBallState();
}

class _DragBallState extends State<DragBall> {
  double x = 100;
  double y = 100;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onPanUpdate: (details) {
        setState(() {
          x += details.delta.dx;
          y += details.delta.dy;
        });
      },
      child: Stack(
        children: [
          Positioned(
            left: x,
            top: y,
            child: const CircleAvatar(radius: 40),
          ),
        ],
      ),
    );
  }
}
```

---

# 8. **Gesture conflict – khi nhiều widget chồng lên nhau**

Sinh viên hay gặp lỗi:

- gesture không hoạt động  
- onTap bị nuốt bởi widget bên dưới  

⚠ Để tránh:

- kiểm tra `behavior: HitTestBehavior.translucent`  
- không lồng nhiều GestureDetector không cần thiết  
- ưu tiên InkWell nếu chỉ cần onTap  

---

# 9. **Sai vs Đúng (lỗi thực tế của sinh viên)**

## ❌ Sai: InkWell không có hiệu ứng  
→ vì widget cha không phải Material

```dart
InkWell(child: Text("Tap"));  // Không ripple
```

## ✔ Đúng:

```dart
Material(
  color: Colors.transparent,
  child: InkWell(
    onTap: () {},
    child: const Text("Tap"),
  ),
);
```

---

## ❌ Sai: GestureDetector bọc Container nhưng không bắt tap  
→ quên set màu hoặc không dùng behavior

```dart
GestureDetector(
  onTap: () {},
  child: Container(),  // quá nhỏ, không có màu
)
```

## ✔ Đúng:

```dart
GestureDetector(
  behavior: HitTestBehavior.opaque,
  onTap: () {},
  child: Container(height: 50),
)
```

---

## ❌ Sai: đặt GestureDetector trong ListView → gesture delay  
→ do ListView ưu tiên cuộn

## ✔ Đúng  
dùng `onTap` hoặc `InkWell` thay vì drag gesture.

---

# 10. **Ví dụ tổng hợp: Card có thể nhấn + ripple + long press**

```dart
Card(
  child: InkWell(
    onTap: () => print("Tap card"),
    onLongPress: () => print("Long press"),
    child: Padding(
      padding: const EdgeInsets.all(20),
      child: Row(
        children: const [
          Icon(Icons.star),
          SizedBox(width: 10),
          Text("Card có tương tác"),
        ],
      ),
    ),
  ),
);
```

---

# 11. Bài tập thực hành

1. Tạo button tùy chỉnh dùng InkWell (ripple).  
2. Tạo widget có thể nhấn giữ (long press) để đổi màu.  
3. Làm widget drag-and-drop (kéo icon trên màn hình).  
4. Tạo danh sách ListTile nhưng mỗi tile có InkWell bao ngoài.  
5. Làm mini game “kéo bóng” bằng GestureDetector.

---

# 12. Mini Test cuối chương

**Câu 1:** GestureDetector dùng để làm gì?  
→ bắt các thao tác chạm, kéo, giữ…

**Câu 2:** InkWell có gì đặc biệt?  
→ hiệu ứng ripple đẹp.

**Câu 3:** Làm sao mở rộng vùng chạm?  
→ dùng `behavior: HitTestBehavior.opaque`.

**Câu 4:** onDoubleTap khác gì onTap?  
→ onDoubleTap kích hoạt khi nhấn nhanh 2 lần.

**Câu 5:** onPanUpdate dùng cho loại gesture nào?  
→ drag (kéo).

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- GestureDetector = bắt mọi gesture.  
- InkWell = tap đẹp + ripple.  
- LongPress = nhấn giữ.  
- DoubleTap = nhấn nhanh 2 lần.  
- Drag = di chuyển widget theo tay.  
- behavior = giúp mở rộng vùng chạm.  

---

# 🎉 Kết thúc chương 14  
Tiếp theo chúng ta sẽ học **chương 15 – Navigation nâng cao với BottomSheet, Dialog, và Routing 2.0 (Giới thiệu)**  
hoặc bạn có thể chuyển sang **chương 15 – Responsive Layout** tuỳ theo giáo trình bạn muốn.

