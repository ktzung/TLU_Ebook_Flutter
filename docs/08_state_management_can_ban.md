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
setState()
↓
build()
↓
dispose()
```

### Giải thích:

- `initState()` → chạy 1 lần khi widget được tạo → dùng để load dữ liệu ban đầu  
- `build()` → chạy nhiều lần → dùng để render UI  
- `dispose()` → chạy khi widget bị huỷ → giải phóng controller, timer  

---

## Ví dụ:

```dart
@override
void initState() {
  super.initState();
  print("Widget được tạo");
}

@override
void dispose() {
  print("Widget bị huỷ");
  super.dispose();
}
```

---

# 7. **Sai vs Đúng – sinh viên hay mắc nhất**

## ❌ Sai: dùng StatefulWidget cho UI không thay đổi  
→ Gánh nặng performance không cần thiết.

## ✔ Đúng: dùng StatelessWidget khi UI tĩnh.

---

## ❌ Sai: setState bên ngoài State class  
→ Flutter crash.

## ✔ Đúng: chỉ dùng setState trong State class.

---

## ❌ Sai: logic ở trong build()  
→ build() được gọi lại rất nhiều lần → chậm.

## ✔ Đúng: logic đặt trong hàm riêng hoặc initState.

---

## ❌ Sai: tạo controller trong build()  
→ lặp vô tận (memory leak)

## ✔ Đúng  

```
initState() {
  controller = TextEditingController();
}
dispose() {
  controller.dispose();
}
```

---

# 8. **Ví dụ hoàn chỉnh: App counter**

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
            ElevatedButton(onPressed: increase, child: const Text("+")),
          ],
        )
      ],
    );
  }
}
```

---

# 9. **Bài tập thực hành**

1. Tạo StatefulWidget quản lý chế độ dark/light (toggle switch).  
2. Tạo UI hiển thị số lượng item trong giỏ hàng + nút tăng/giảm.  
3. Dùng lifting state up:  
   - Parent quản lý số đếm  
   - ChildA hiển thị  
   - ChildB tăng  
   - ChildC giảm  
4. Tạo bộ đếm giờ (timer) bằng initState + dispose.  
5. Tạo app chọn màu nền → click màu nào đổi background.

---

# 10. Mini Test cuối chương

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

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- State = dữ liệu làm thay đổi UI.  
- Stateless vs Stateful = tĩnh vs động.  
- setState = kích hoạt UI rebuild.  
- State nên đặt ở widget cha khi cần chia sẻ.  
- initState & dispose cực quan trọng khi dùng controller/timer.  
- Không viết logic trong build().

---

# 🎉 Kết thúc chương 08  
Tiếp theo, chúng ta học mức “state quản lý chuyên nghiệp”:

👉 **Chương 09 – State Management nâng cao với Provider**

