# 🟦 CHƯƠNG 10  
# **NETWORKING & API TRONG FLUTTER**  
*(HTTP, JSON, Future, async–await, FutureBuilder, fetch API)*

Hầu hết app thật đều phải lấy dữ liệu từ Internet, ví dụ:

- danh sách sản phẩm  
- thông tin thời tiết  
- avatar user  
- dữ liệu từ server  

Chương này sẽ dạy bạn:

- gọi API bằng package `http`  
- xử lý JSON  
- dựng model  
- dùng FutureBuilder để hiển thị dữ liệu  
- tránh lỗi “snapshot null”, “Future not completed”  
- viết 1 mini app fetch dữ liệu hoàn chỉnh

---

# 🎯 MỤC TIÊU HỌC TẬP

Sau chương này bạn có thể:

- Gọi API GET / POST bằng http.  
- Biết dùng async–await đúng cách.  
- Parse JSON từ API thành model Dart.  
- Dùng FutureBuilder hiển thị dữ liệu.  
- Tạo service class tách logic khỏi UI.  
- Xử lý lỗi mạng cơ bản.

---

# 1. **Cài đặt package http**

Trong `pubspec.yaml`:

```yaml
dependencies:
  http: ^1.2.0
```

Import:

```dart
import 'package:http/http.dart' as http;
import 'dart:convert';
```

---

# 2. **Gọi API GET – ví dụ đơn giản**

Ví dụ API miễn phí:  
`https://jsonplaceholder.typicode.com/posts`

### Hàm fetch:

```dart
Future<List<dynamic>> fetchPosts() async {
  final url = Uri.parse("https://jsonplaceholder.typicode.com/posts");
  final response = await http.get(url);

  if (response.statusCode == 200) {
    return jsonDecode(response.body);
  } else {
    throw Exception("Lỗi tải dữ liệu");
  }
}
```

---

# 3. **Gọi API POST – gửi dữ liệu lên server**

```dart
Future<void> createPost() async {
  final url = Uri.parse("https://jsonplaceholder.typicode.com/posts");

  final response = await http.post(
    url,
    body: jsonEncode({
      "title": "Hello",
      "body": "Flutter",
      "userId": 1
    }),
    headers: {"Content-type": "application/json"},
  );

  print(response.body);
}
```

---

# 4. **JSON & model class (rất quan trọng)**

JSON là dạng dữ liệu phổ biến nhất.  
Ví dụ JSON post:

```json
{
  "userId": 1,
  "id": 1,
  "title": "Hello",
  "body": "Nội dung"
}
```

Tạo model:

```dart
class Post {
  final int id;
  final int userId;
  final String title;
  final String body;

  Post({
    required this.id,
    required this.userId,
    required this.title,
    required this.body,
  });

  factory Post.fromJson(Map<String, dynamic> json) {
    return Post(
      id: json["id"],
      userId: json["userId"],
      title: json["title"],
      body: json["body"],
    );
  }
}
```

### Convert JSON → List<Post>

```dart
List<Post> toPosts(List<dynamic> data) {
  return data.map((json) => Post.fromJson(json)).toList();
}
```

---

# 5. **FutureBuilder – cách hiển thị dữ liệu lên UI**

```dart
FutureBuilder(
  future: fetchPosts(),
  builder: (context, snapshot) {
    if (snapshot.connectionState == ConnectionState.waiting) {
      return const Center(child: CircularProgressIndicator());
    }

    if (snapshot.hasError) {
      return const Text("Lỗi tải dữ liệu");
    }

    final posts = snapshot.data as List<dynamic>;

    return ListView.builder(
      itemCount: posts.length,
      itemBuilder: (context, index) {
        return ListTile(
          title: Text(posts[index]["title"]),
          subtitle: Text(posts[index]["body"]),
        );
      },
    );
  },
);
```

---

### 🧠 Mẹo nhớ trạng thái snapshot

| Trạng thái | Ý nghĩa | Dùng để… |
|-----------|--------|----------|
| waiting | đang tải | loading UI |
| hasError | lỗi | hiển thị lỗi |
| hasData | có dữ liệu | build UI |

---

# 6. **Tách riêng API logic vào Folder services/**

```
lib/
  services/
    api_service.dart
```

```dart
class ApiService {
  static Future<List<Post>> getPosts() async {
    final url = Uri.parse("https://jsonplaceholder.typicode.com/posts");
    final response = await http.get(url);
    if (response.statusCode != 200) throw Exception("Lỗi");

    final data = jsonDecode(response.body) as List;
    return data.map((e) => Post.fromJson(e)).toList();
  }
}
```

→ UI chỉ tập trung vào hiển thị, không chứa logic API.

---

# 7. **Xử lý lỗi mạng cơ bản**

```dart
try {
  final posts = await ApiService.getPosts();
} catch (e) {
  print("Có lỗi: $e");
}
```

Hoặc trong UI:

```dart
if (snapshot.hasError) return Text("Lỗi rồi!");
```

---

# 8. **Ví dụ hoàn chỉnh: Màn hình danh sách bài viết**

```dart
class PostScreen extends StatelessWidget {
  const PostScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Danh sách bài viết")),
      body: FutureBuilder(
        future: ApiService.getPosts(),
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.waiting) {
            return const Center(child: CircularProgressIndicator());
          }

          if (snapshot.hasError) {
            return const Center(child: Text("Lỗi tải dữ liệu"));
          }

          final posts = snapshot.data as List<Post>;

          return ListView.builder(
            itemCount: posts.length,
            itemBuilder: (context, index) {
              final p = posts[index];
              return ListTile(
                title: Text(p.title),
                subtitle: Text(p.body),
              );
            },
          );
        },
      ),
    );
  }
}
```

---

# 9. **Sai vs Đúng – lỗi sinh viên thường gặp**

## ❌ Sai: quên await → snapshot không bao giờ hoàn thành

```dart
future: fetchPosts(), // thiếu await trong hàm fetch
```

## ✔ Đúng:

Trong fetch:

```dart
final response = await http.get(url);
```

---

## ❌ Sai: decode sai kiểu JSON

```dart
final data = jsonDecode(response.body) as Map; // sai
```

## ✔ Đúng:

```dart
final data = jsonDecode(response.body) as List;
```

---

## ❌ Sai: viết API logic trong build()

## ✔ Đúng: tách vào service + dùng FutureBuilder

---

## ❌ Sai: gọi phụ thuộc mạng trong initState nhưng không gắn setState

## ✔ Đúng:

→ dùng FutureBuilder hoặc setState đúng cách

---

# 10. Bài tập thực hành

1. Gọi API thời tiết từ link public (OpenWeatherMap).  
2. Làm màn hình danh sách ảnh từ API `picsum.photos`.  
3. Tạo model User và fetch danh sách User từ API.  
4. Tạo Button “Làm mới” và fetch lại dữ liệu.  
5. Tách API logic vào ApiService hoàn chỉnh.  

---

# 11. Mini Test cuối chương

**Câu 1:** http.get trả về gì?  
→ Response.

**Câu 2:** jsonDecode làm gì?  
→ chuyển JSON string → Map/List.

**Câu 3:** FutureBuilder dùng khi nào?  
→ khi muốn hiển thị dữ liệu từ Future/API.

**Câu 4:** notifyListeners có liên quan trong networking không?  
→ Không trực tiếp (chỉ liên quan Provider).

**Câu 5:** POST và GET khác nhau?  
→ GET lấy dữ liệu, POST gửi dữ liệu lên server.

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- http = gọi API, JSON = dữ liệu của API.  
- async–await là nền tảng xử lý mạng.  
- FutureBuilder giúp render UI theo trạng thái Future.  
- Luôn tách API logic vào service.  
- Model class giúp quản lý dữ liệu sạch và rõ ràng.  

---

# 🎉 Kết thúc chương 10  
Tiếp theo: App thật cần lưu dữ liệu cục bộ — đừng để mất khi tắt app.

👉 **Chương 11 – Local Storage (SharedPreferences, File, JSON local)**

