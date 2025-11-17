# 🟦 CHƯƠNG 02  
# **DART CHO FLUTTER DEVELOPER**  
*(Phiên bản thực chiến – chỉ học những gì Flutter cần)*

Bạn đã học Dart căn bản trong phần trước.  
Nhưng! Khi bước vào Flutter, bạn **không cần tất cả**, mà chỉ cần những phần “ăn liền” giúp build UI nhanh, xử lý state, làm việc với async, và quản lý dữ liệu.

Chương này chọn lọc **Dart tối thiểu nhưng đủ dùng** để bạn làm app Flutter chuyên nghiệp.

---

# 🎯 MỤC TIÊU HỌC TẬP

Sau chương này bạn sẽ:

- Biết những phần Dart nào quan trọng nhất cho Flutter.  
- Hiểu bản chất `StatelessWidget` và `StatefulWidget` nhờ OOP.  
- Sử dụng List/Map với UI.  
- Biết dùng async–await để gọi API và load dữ liệu.  
- Tự tin đọc code Flutter của người khác.  

---

# 1. **Dart – Nền tảng 100% của Flutter**

Bạn viết Flutter = bạn đang viết Dart.  
Bạn hiểu Dart tốt → Flutter vào đầu như uống nước.

Dart trong Flutter chủ yếu dùng cho:

- UI (Widgets)  
- State  
- Xử lý logic  
- Xử lý async (API, Future, Stream)  
- Quản lý data (List, Map, Model class)  

---

# 2. **Biến & Kiểu dữ liệu (rất hay gặp khi viết UI)**

## 📌 var và final  
Flutter dùng `final` rất nhiều.

```dart
final String title = "Hello";
var count = 0;
```

### final dùng khi:
- dữ liệu **không thay đổi**, nhưng lấy được lúc runtime  
(VD: màu, padding, text)

### const dùng khi:
- hằng số compile-time  
(VD: const Text("Hi") trong widget tree)

---

## 📌 List – Map – Cặp dữ liệu quan trọng nhất của Flutter

### List

```dart
List<String> names = ["Huy", "Mai", "An"];
```

Dùng để hiển thị ListView.

### Map

```dart
Map<String, dynamic> user = {
  "name": "Dung",
  "age": 21
};
```

Dùng để xử lý JSON khi gọi API.

---

# 3. **Hàm (function) – đơn giản nhưng dùng suốt**

### Hàm bình thường

```dart
int sum(int a, int b) {
  return a + b;
}
```

### Hàm ngắn gọn (fat arrow)

```dart
int sum2(int a, int b) => a + b;
```

### Callback – cực quan trọng với Buttons

```dart
onPressed: () {
  print("Clicked!");
}
```

---

# 4. **OOP trong Flutter (hiểu đúng để không bị rối)**

UI trong Flutter là **class**.  
Mỗi màn hình là 1 class.  
Mỗi widget cũng là 1 class.

## 📌 StatelessWidget
Dùng khi UI **không thay đổi**.

```dart
class MyBox extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Text("Box");
  }
}
```

## 📌 StatefulWidget
Dùng khi UI **thay đổi theo trạng thái**.

```dart
class Counter extends StatefulWidget {
  @override
  _CounterState createState() => _CounterState();
}

class _CounterState extends State<Counter> {
  int count = 0;

  void increase() {
    setState(() => count++);
  }

  @override
  Widget build(BuildContext context) {
    return Text("$count");
  }
}
```

### 🎒 Ví dụ đời sống  
- Stateless = cái bảng hiệu treo tường, viết rồi giữ nguyên.  
- Stateful = cái nồi cơm điện đang đếm giờ, số phút thay đổi theo thời gian.

---

# 5. **Model class – cực quan trọng khi làm app**

Các app thật luôn tương tác với dữ liệu.  
Model class là cấu trúc mô tả đối tượng.

Ví dụ: User model

```dart
class User {
  String name;
  int age;

  User({required this.name, required this.age});
}
```

Dùng trong API + UI.

---

# 6. **Dart & JSON – kỹ năng sống còn khi dùng API**

API trả về JSON → bạn phải convert sang Dart Map/List.

```dart
import 'dart:convert';

void main() {
  String jsonStr = '{"name":"Tuan","age":20}';
  Map<String, dynamic> data = jsonDecode(jsonStr);

  print(data["name"]); // Tuan
}
```

---

# 7. **Async – Await (phần quan trọng nhất của chương)**

Flutter làm nhiều việc bất đồng bộ:

- đọc file  
- load dữ liệu  
- gọi API  
- chờ animation  
- chờ UI load  

## 📌 Future

```dart
Future<String> fetchData() async {
  return "Hello";
}
```

## 📌 async – await

```dart
void main() async {
  var data = await fetchData();
  print(data);
}
```

---

## 🧠 Hình dung đời sống  
- async giống như việc bạn bấm đặt đồ ăn → callback khi ship tới.  
- await giống như bạn đứng chờ gọi đồ ăn (chặn lại cho đến khi có dữ liệu).

---

## 📌 FutureBuilder – dùng để load API trong UI

```dart
FutureBuilder(
  future: fetchData(),
  builder: (context, snapshot) {
    if (snapshot.connectionState == ConnectionState.waiting) {
      return CircularProgressIndicator();
    }
    return Text(snapshot.data.toString());
  },
);
```

Đây là công cụ bạn sẽ dùng **rất nhiều**.

---

# 8. **Extensions – kỹ thuật viết code nhanh hơn**

```dart
extension IntX on int {
  int doubleUp() => this * 2;
}

void main() {
  print(5.doubleUp()); // 10
}
```

Trong Flutter hay dùng để rút gọn UI.

---

# 9. **Lỗi sinh viên thường gặp**

| Lỗi | Nguyên nhân | Cách sửa |
|-----|-------------|----------|
| Không hiểu async → FutureBuilder lỗi | không biết snapshot có trạng thái | check `snapshot.hasData` |
| Dùng var lung tung | thiếu type → khó debug | dùng kiểu rõ ràng |
| Không dùng `setState` | UI không update | luôn bọc thay đổi state bằng `setState()` |
| Model class sai kiểu JSON | nhầm int/string | in JSON ra xem kỹ |
| Viết code tất cả trong main.dart | lười tách file | chia theo screens/models/widgets |

---

# 10. **Bài tập thực hành**

1. Tạo class `Product` có name, price, description.  
2. Tạo List<Product> và in ra từng item.  
3. Parse chuỗi JSON thành Map và hiển thị ra console.  
4. Viết hàm async chờ 2 giây rồi trả về “Done!”.  
5. Tạo StatefulWidget hiển thị số đếm và nút bấm tăng số.

---

# 11. **Mini Test cuối chương**

**Câu 1:** StatelessWidget dùng khi nào?  
→ Khi UI không thay đổi.

**Câu 2:** Từ khóa giúp UI cập nhật trong StatefulWidget?  
→ `setState()`.

**Câu 3:** Dạng dữ liệu API thường trả về là gì?  
→ JSON: Map, List.

**Câu 4:** async–await dùng để làm gì?  
→ Xử lý bất đồng bộ.

**Câu 5:** Model class dùng để làm gì?  
→ Định nghĩa cấu trúc dữ liệu dùng trong ứng dụng.

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- Flutter = Widget → tất cả đều là class.  
- Dart phải vững: List, Map, async.  
- Stateless = không đổi, Stateful = có trạng thái.  
- FutureBuilder là công cụ quan trọng khi xử lý API.  
- Model class giúp quản lý dữ liệu sạch sẽ.  
- `setState()` = cập nhật giao diện.  

---

# 🎉 Kết thúc chương 02  
Tiếp theo, bạn sẽ học về cấu trúc dự án Flutter – điều cực kỳ quan trọng:

👉 **Chương 03 – Cấu trúc dự án Flutter & Tổ chức file**

