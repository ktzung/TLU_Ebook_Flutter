# 🟦 CHƯƠNG 13  
# **ANIMATION CƠ BẢN TRONG FLUTTER**  
*(AnimatedContainer – AnimatedOpacity – Tween – Curves – AnimatedAlign – AnimatedDefaultTextStyle)*

Animation giúp ứng dụng cảm giác **mượt – sống – chuyên nghiệp**.  
Không cần kiến thức khó, chỉ cần nắm đúng các widget animation tích hợp sẵn của Flutter.

Chương này dành cho người mới: chỉ dùng “animation đơn giản nhưng hiệu quả cao”.

---

# 🎯 MỤC TIÊU HỌC TẬP

Sau chương này bạn sẽ:

- Hiểu nguyên lý animation trong Flutter.  
- Dùng AnimatedContainer để animate kích thước, màu sắc, bo góc.  
- Dùng AnimatedOpacity để làm hiệu ứng fade in/out.  
- Dùng Tween animation với AnimationController.  
- Hiểu về Curves (độ mượt của animation).  
- Dùng các AnimatedWidget phổ biến trong UI thực tế.  
- Tạo hiệu ứng nhỏ nhanh và đẹp mắt.

---

# 1. **Animation trong Flutter hoạt động như thế nào?**

Flutter làm animation bằng cách:

1. Thay đổi giá trị nào đó theo thời gian  
2. Vẽ lại UI rất nhanh  
3. Mắt người thấy nó chuyển động mượt mà  

Animation = thay đổi từ *trạng thái A* → *trạng thái B* trong một khoảng thời gian.

---

### 🎒 Ví dụ đời sống  
Animation giống như bạn kéo 1 cánh cửa:

- lúc đầu → cửa đóng  
- sau đó → từ từ mở  
- cuối cùng → mở hoàn toàn  

UI cũng vậy: từ trạng thái ban đầu → chuyển dần sang trạng thái mới.

---

# 2. **AnimatedContainer – widget animation “tất cả trong một”**

Dùng để animate:

- width / height  
- padding / margin  
- màu sắc  
- borderRadius  

### Ví dụ:

```dart
class BoxAnimation extends StatefulWidget {
  const BoxAnimation({super.key});

  @override
  State<BoxAnimation> createState() => _BoxAnimationState();
}

class _BoxAnimationState extends State<BoxAnimation> {
  bool big = false;

  @override
  Widget build(BuildContext context) {
    return AnimatedContainer(
      duration: const Duration(milliseconds: 500),
      width: big ? 200 : 100,
      height: big ? 200 : 100,
      decoration: BoxDecoration(
        color: big ? Colors.blue : Colors.red,
        borderRadius: BorderRadius.circular(big ? 20 : 0),
      ),
      child: TextButton(
        onPressed: () => setState(() => big = !big),
        child: const Text("Nhấn tôi"),
      ),
    );
  }
}
```

→ Kích thước, màu, bo góc thay đổi *mượt* khi nhấn.

---

# 3. **AnimatedOpacity – hiệu ứng Fade In / Fade Out**

```dart
AnimatedOpacity(
  duration: const Duration(milliseconds: 400),
  opacity: isVisible ? 1 : 0,
  child: const Text("Xin chào!"),
);
```

Lý tưởng cho:

- fade chữ  
- fade avatar  
- hiệu ứng xuất hiện ảnh/banner  

---

# 4. **AnimatedAlign – di chuyển widget mượt mà**

```dart
AnimatedAlign(
  duration: const Duration(milliseconds: 500),
  alignment: isLeft ? Alignment.centerLeft : Alignment.centerRight,
  child: const Icon(Icons.circle, size: 40),
);
```

Ứng dụng thực:

- button chuyển trạng thái  
- nút toggle  
- timeline animation  

---

# 5. **AnimatedDefaultTextStyle – đổi style chữ mượt**

```dart
AnimatedDefaultTextStyle(
  duration: const Duration(milliseconds: 400),
  style: TextStyle(
    fontSize: big ? 30 : 18,
    color: big ? Colors.blue : Colors.black,
  ),
  child: const Text("Flutter"),
);
```

---

# 6. **Curves – cách làm animation “mượt có style”**

Curves là *đồ thị tốc độ*: nhanh–chậm–nhanh, hoặc chậm–nhanh–chậm.

Ví dụ phổ biến:

- Curves.easeIn  
- Curves.easeOut  
- Curves.easeInOut  
- Curves.bounceOut  
- Curves.elasticOut  

```dart
AnimatedContainer(
  duration: const Duration(milliseconds: 600),
  curve: Curves.easeInOut,
  ...
);
```

---

# 7. **Tween + AnimationController – animation “toàn quyền kiểm soát”**

Chỉ dùng khi bạn cần animation tùy chỉnh nâng cao.

### Ví dụ Tween basic:

```dart
class TweenDemo extends StatefulWidget {
  const TweenDemo({super.key});

  @override
  State<TweenDemo> createState() => _TweenDemoState();
}

class _TweenDemoState extends State<TweenDemo> with SingleTickerProviderStateMixin {
  late AnimationController controller;
  late Animation<double> opacityAnim;

  @override
  void initState() {
    super.initState();
    controller = AnimationController(
      duration: const Duration(seconds: 1),
      vsync: this,
    );
    opacityAnim = Tween<double>(begin: 0, end: 1).animate(controller);

    controller.forward();
  }

  @override
  void dispose() {
    controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return FadeTransition(
      opacity: opacityAnim,
      child: const Text("Fade với AnimationController"),
    );
  }
}
```

---

# 8. **Sai vs Đúng – các lỗi sinh viên cực hay mắc**

## ❌ Sai: animation không chạy vì thiếu setState

```dart
big = !big;  // UI không biết thay đổi!
```

## ✔ Đúng:

```dart
setState(() => big = !big);
```

---

## ❌ Sai: dùng AnimationController mà quên dispose  
→ *memory leak*

## ✔ Đúng:

```
dispose() {
  controller.dispose();
}
```

---

## ❌ Sai: duration quá dài → không giống animation mà giống lag  
→ 300–600 ms là đẹp nhất  
→ >1500 ms trông “lụt nghề”

---

## ❌ Sai: AnimatedContainer lồng nhau gây xung đột thuộc tính  
→ tách ra rõ ràng

---

# 9. **Ví dụ tổng hợp: Button phóng to thu nhỏ**

```dart
class AnimateButton extends StatefulWidget {
  const AnimateButton({super.key});

  @override
  State<AnimateButton> createState() => _AnimateButtonState();
}

class _AnimateButtonState extends State<AnimateButton> {
  bool big = false;

  @override
  Widget build(BuildContext context) {
    return Center(
      child: AnimatedContainer(
        duration: const Duration(milliseconds: 300),
        curve: Curves.easeInOut,
        width: big ? 200 : 120,
        height: big ? 60 : 40,
        child: ElevatedButton(
          onPressed: () => setState(() => big = !big),
          child: const Text("Nhấn"),
        ),
      ),
    );
  }
}
```

---

# 10. Bài tập thực hành

1. Tạo box đổi màu + đổi kích thước bằng AnimatedContainer.  
2. Tạo nút “Hiện/ẩn” chữ bằng AnimatedOpacity.  
3. Làm slider chuyển vị trí icon bằng AnimatedAlign.  
4. Tạo card sản phẩm khi nhấn sẽ “phóng to” nhẹ (scale effect).  
5. Tạo hiệu ứng fade-in logo khi mở app bằng Tween + AnimationController.

---

# 11. Mini Test cuối chương

**Câu 1:** AnimatedContainer dùng để animate gì?  
→ kích thước, màu, border, padding…

**Câu 2:** opacity = 0 nghĩa là gì?  
→ hoàn toàn trong suốt (ẩn).

**Câu 3:** Curves dùng để làm gì?  
→ điều chỉnh độ mượt/tốc độ animation.

**Câu 4:** AnimationController phải làm gì khi không dùng nữa?  
→ dispose.

**Câu 5:** Tween dùng để làm gì?  
→ chuyển giá trị từ A → B theo thời gian.

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- AnimatedContainer = animation đơn giản mạnh nhất.  
- AnimatedOpacity = fade in/out dễ nhất.  
- AnimatedAlign giúp di chuyển nhẹ nhàng.  
- Curves tạo cảm giác mượt và tự nhiên.  
- Tween + Controller dành cho animation nâng cao.  

---

# 🎉 Kết thúc chương 13  
Tiếp theo là chương giúp app của bạn tương tác với người dùng mạnh mẽ hơn:

👉 **Chương 14 – Gesture & Interaction (Tap, DoubleTap, LongPress, Drag)**

