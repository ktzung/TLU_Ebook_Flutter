# 🟦 CHƯƠNG 11  
# **LOCAL STORAGE TRONG FLUTTER**  
*(SharedPreferences – JSON local – File Storage – Lưu dữ liệu offline)*

Ứng dụng thực tế không chỉ chạy khi mở app — nó cần **ghi nhớ thông tin**:

- Lưu tài khoản đăng nhập  
- Lưu token của API  
- Lưu theme dark/light  
- Lưu danh sách ghi chú  
- Lưu giỏ hàng mini  
- Lưu cài đặt người dùng  

Chương này giúp bạn nắm **cách lưu dữ liệu cục bộ (local) đúng chuẩn**, dễ dùng, dùng được ngay.

---

# 🎯 MỤC TIÊU HỌC TẬP

Bạn sẽ biết cách:

- Lưu/lấy dữ liệu bằng SharedPreferences  
- Lưu JSON vào file  
- Đọc/ghi file trong Flutter  
- Hiểu khi nào nên dùng kiểu lưu nào  
- Tránh các lỗi phổ biến (null, future chưa hoàn thành)  
- Tạo ứng dụng mini lưu ghi chú offline

---

# 1. **SharedPreferences – dễ nhất, nhanh nhất**

Dùng để lưu dữ liệu **nhỏ**:

- bool  
- int  
- String  
- List<String>  

Không dùng để lưu dữ liệu lớn.

---

# 2. **Cài package**

Trong pubspec.yaml:

```yaml
dependencies:
  shared_preferences: ^2.2.2
```

Import:

```dart
import 'package:shared_preferences/shared_preferences.dart';
```

---

# 3. **Lưu dữ liệu**

Ví dụ: lưu tên người dùng.

```dart
Future<void> saveName(String name) async {
  final prefs = await SharedPreferences.getInstance();
  await prefs.setString("username", name);
}
```

---

# 4. **Lấy dữ liệu**

```dart
Future<String?> getName() async {
  final prefs = await SharedPreferences.getInstance();
  return prefs.getString("username");
}
```

---

# 5. **Lưu danh sách (List<String>)**

```dart
prefs.setStringList("favs", ["A", "B", "C"]);
```

Lấy lại:

```dart
prefs.getStringList("favs");
```

---

### 🎒 Ví dụ đời sống  
SharedPreferences giống như **ngăn kéo nhỏ** cạnh bàn học —  
chỉ để được vài món quan trọng: giấy note, tấm thẻ, vài món đồ lặt vặt.

Không để vali to vào đó!

---

# 6. **Ví dụ hoàn chỉnh: Lưu trạng thái theme Light/Dark**

```dart
class ThemeProvider extends ChangeNotifier {
  bool isDark = false;

  Future<void> loadTheme() async {
    final prefs = await SharedPreferences.getInstance();
    isDark = prefs.getBool("theme") ?? false;
    notifyListeners();
  }

  Future<void> toggleTheme() async {
    isDark = !isDark;
    final prefs = await SharedPreferences.getInstance();
    await prefs.setBool("theme", isDark);
    notifyListeners();
  }
}
```

---

# 7. **Lưu File – khi dữ liệu lớn hơn**

Dùng khi:

- lưu danh sách ghi chú  
- lưu nội dung JSON  
- lưu dữ liệu cỡ vừa  

Thư viện dùng:  
`dart:io` (⚠ Flutter Web không hỗ trợ)

---

## Lấy thư mục lưu file (application directory)

```dart
import 'dart:io';
import 'package:path_provider/path_provider.dart';

Future<String> getFilePath() async {
  final dir = await getApplicationDocumentsDirectory();
  return "${dir.path}/notes.json";
}
```

---

## Ghi file:

```dart
Future<void> writeFile(String content) async {
  final path = await getFilePath();
  final file = File(path);
  await file.writeAsString(content);
}
```

---

## Đọc file:

```dart
Future<String> readFile() async {
  final path = await getFilePath();
  final file = File(path);
  return await file.readAsString();
}
```

---

# 8. **Lưu JSON vào file**

Ví dụ: danh sách ghi chú:

```dart
List<Map<String, dynamic>> notes = [
  {"title": "Học Flutter", "done": false},
  {"title": "Mua trà sữa", "done": true},
];
```

### Ghi:

```dart
writeFile(jsonEncode(notes));
```

### Đọc:

```dart
final jsonStr = await readFile();
final data = jsonDecode(jsonStr);
```

---

# 9. **Khi nào dùng SharedPreferences? Khi nào dùng file?**

| Trường hợp | Nên dùng |
|------------|----------|
| Lưu token | SharedPreferences |
| Lưu cài đặt | SharedPreferences |
| Lưu danh sách nhỏ | SharedPreferences |
| Lưu dữ liệu nhiều dòng | File |
| Lưu JSON size lớn | File |
| Lưu tài liệu, text dài | File |
| Lưu database quan hệ | Sqflite (sẽ học chương sau) |

---

# 10. **Sai vs Đúng (lỗi sinh viên hay gặp)**

## ❌ Sai: quên await → dữ liệu chưa lưu

```dart
prefs.setString("key", value); // Không await!
```

## ✔ Đúng:

```dart
await prefs.setString("key", value);
```

---

## ❌ Sai: lưu object vào SharedPreferences

```
prefs.setString("user", userObject); // sai
```

## ✔ Đúng:

```dart
prefs.setString("user", jsonEncode(userObject));
```

---

## ❌ Sai: viết file trong build()  
→ build chạy liên tục → app lag

## ✔ Đúng: viết file trong hàm riêng, gọi từ button hoặc initState

---

## ❌ Sai: quên import path_provider  
→ không lấy được directory

---

# 11. **Ví dụ hoàn chỉnh: Mini App ghi chú offline**

```
lib/
  services/
    local_service.dart
```

### local_service.dart

```dart
class LocalNoteService {
  Future<String> _path() async {
    final dir = await getApplicationDocumentsDirectory();
    return "${dir.path}/notes.json";
  }

  Future<List<dynamic>> loadNotes() async {
    try {
      final file = File(await _path());
      final content = await file.readAsString();
      return jsonDecode(content);
    } catch (e) {
      return [];
    }
  }

  Future<void> saveNotes(List<dynamic> notes) async {
    final file = File(await _path());
    await file.writeAsString(jsonEncode(notes));
  }
}
```

### UI đơn giản

```dart
class NoteApp extends StatefulWidget {
  const NoteApp({super.key});

  @override
  State<NoteApp> createState() => _NoteAppState();
}

class _NoteAppState extends State<NoteApp> {
  final service = LocalNoteService();
  List<dynamic> notes = [];
  final ctrl = TextEditingController();

  @override
  void initState() {
    super.initState();
    service.loadNotes().then((value) {
      setState(() => notes = value);
    });
  }

  void addNote() async {
    notes.add({"text": ctrl.text, "done": false});
    await service.saveNotes(notes);
    setState(() {});
    ctrl.clear();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Ghi chú offline")),
      body: Column(
        children: [
          TextField(controller: ctrl),
          ElevatedButton(onPressed: addNote, child: const Text("Thêm")),
          Expanded(
            child: ListView.builder(
              itemCount: notes.length,
              itemBuilder: (_, i) => ListTile(
                title: Text(notes[i]["text"]),
              ),
            ),
          )
        ],
      ),
    );
  }
}
```

---

# 12. Bài tập thực hành

1. Tạo app “Ghi nhớ tên người dùng” bằng SharedPreferences.  
2. Tạo app lưu trạng thái dark/light vào SharedPreferences.  
3. Tạo app ghi chú lưu JSON vào file.  
4. Tạo danh sách yêu thích sản phẩm (favorite list) và lưu với SharedPreferences.  
5. Tạo mini app nhập nhật ký, mỗi ngày một đoạn → lưu file.

---

# 13. Mini Test cuối chương

**Câu 1:** SharedPreferences lưu được loại dữ liệu gì?  
→ int, double, bool, String, List<String>.

**Câu 2:** File storage dùng để làm gì?  
→ lưu dữ liệu lớn hoặc dạng JSON.

**Câu 3:** jsonEncode làm gì?  
→ chuyển object → chuỗi JSON.

**Câu 4:** jsonDecode làm gì?  
→ chuyển chuỗi JSON → Map/List.

**Câu 5:** tại sao không gọi writeFile trong build()?  
→ build chạy nhiều lần → lag và phản tác dụng.

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- SharedPreferences = dữ liệu nhỏ, cài đặt.  
- File storage = dữ liệu lớn hơn, JSON.  
- Luôn await thao tác ghi dữ liệu.  
- Không viết file trong build().  
- Lưu đối tượng (object) phải convert JSON.

---

# 🎉 Kết thúc chương 11  
Tiếp theo, bạn sẽ học về **UI nâng cao và Widgets thường dùng**:

👉 **Chương 12 – Widgets Nâng Cao (ListTile, Card, Dialog, Drawer, BottomNav…)**

