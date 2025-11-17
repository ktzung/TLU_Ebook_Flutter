# 🟦 CHƯƠNG 03  
# **CẤU TRÚC DỰ ÁN FLUTTER & TỔ CHỨC FILE CHUẨN**  
*(Hiểu đúng cấu trúc dự án = viết code không rối, học Flutter không mệt)*

Sinh viên yếu thường bị “ngộp” khi mở dự án Flutter vì có nhiều thư mục lạ.  
Chương này sẽ giải thích cặn kẽ từng phần, ví dụ hoá, và hướng dẫn cách tổ chức dự án như lập trình viên chuyên nghiệp.

---

# 🎯 MỤC TIÊU HỌC TẬP

Sau chương này, bạn sẽ:

- Hiểu toàn bộ cấu trúc dự án Flutter.  
- Biết file nào quan trọng, file nào không cần đụng đến.  
- Biết tổ chức thư mục theo module để dự án lớn không rối.  
- Biết pubspec.yaml dùng để làm gì.  
- Biết quản lý assets (ảnh, font, icon…).  

---

# 1. **Cấu trúc dự án Flutter – giải thích đơn giản & rõ ràng**

Khi bạn chạy:

```
flutter create my_app
```

Bạn sẽ có cấu trúc:

```
my_app/
│
├── android/
├── ios/
├── web/
├── linux/
├── macos/
├── windows/
│
├── lib/
│   └── main.dart
│
├── test/
├── pubspec.yaml
└── README.md
```

---

# 2. **Giải thích từng thư mục (Hiểu cực nhanh)**

## 🟩 2.1. lib/ — NƠI QUAN TRỌNG NHẤT  
Toàn bộ code Flutter của bạn nằm ở đây.

```
lib/
  main.dart
  screens/
  widgets/
  models/
  services/
  utils/
```

**Sinh viên chỉ cần quan tâm thư mục này.**

---

## 🟩 2.2. android/ — cấu hình build cho Android  
Không cần động vào trừ khi:

- đổi tên package  
- build release  
- cài permission (camera, file, internet)

Tương tự với thư mục **ios/**.

---

## 🟩 2.3. web/ — build app Flutter web  
Chỉ quan tâm nếu bạn muốn deploy web.

---

## 🟩 2.4. test/ — viết test code  
Dùng cho unit test, widget test.

---

## 🟩 2.5. pubspec.yaml — file QUAN TRỌNG bậc nhất

File này quản lý:

- dependencies (thư viện)  
- dev_dependencies  
- phiên bản Flutter  
- assets (ảnh, font)  
- tên app, mô tả  

Ví dụ:

```yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^1.2.0
  provider: ^6.0.0

assets:
  - assets/images/
  - assets/icons/
```

---

# 3. **Hiểu rõ vai trò của main.dart**

`main.dart` là cửa vào của ứng dụng Flutter:

```dart
void main() {
  runApp(const MyApp());
}
```

App bắt đầu chạy từ widget `MyApp`.

Cấu trúc tối thiểu:

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: HomeScreen(),
    );
  }
}
```

---

# 4. **Tổ chức thư mục lib/ theo chuẩn chuyên nghiệp**

Sinh viên thường mắc lỗi **nhét tất cả vào main.dart**.

Để dự án sạch, ta chia như sau:

```
lib/
│
├── main.dart
│
├── screens/          <-- các màn hình
│     ├── home_screen.dart
│     ├── detail_screen.dart
│     └── login_screen.dart
│
├── widgets/          <-- widget tái sử dụng
│     ├── app_button.dart
│     └── user_avatar.dart
│
├── models/           <-- class dữ liệu
│     └── user.dart
│
├── services/         <-- xử lý logic (API, Auth)
│     ├── api_service.dart
│     └── auth_service.dart
│
├── utils/            <-- hàm tiện ích, constants
│     ├── app_colors.dart
│     └── helpers.dart
│
└── data/             <-- json mock, danh sách mẫu
      └── demo_data.dart
```

---

# 5. **Asset management – cách thêm ảnh, font, icon vào Flutter**

Tạo thư mục assets:

```
assets/
  images/
  icons/
  fonts/
```

Chỉnh `pubspec.yaml`:

```yaml
assets:
  - assets/images/
  - assets/icons/
```

Ví dụ dùng ảnh:

```dart
Image.asset("assets/images/banner.png");
```

---

# 6. **Ví dụ minh họa: Một dự án Flutter sạch sẽ**

```
lib/
  main.dart
  screens/
    home_screen.dart
    profile_screen.dart
  widgets/
    profile_card.dart
  models/
    user.dart
  services/
    user_service.dart
  utils/
    app_styles.dart
    app_colors.dart
  data/
    fake_users.dart
```

Mỗi phần chỉ làm đúng nhiệm vụ của nó → code dễ hiểu ngay cả với sinh viên mới.

---

# 7. **Sai vs Đúng (dành cho sinh viên thụ động)**

## ❌ Sai — dự án của sinh viên năm nhất
```
lib/
  main.dart      <-- 2000 dòng
  home.dart
  a.dart
  b.dart
  c.dart
```

## ✔ Đúng — dự án theo chuẩn
```
lib/
  main.dart
  screens/
  widgets/
  models/
  services/
  utils/
```

---

# 8. **Các lỗi thường gặp**

| Lỗi | Nguyên nhân | Cách sửa |
|-----|-------------|----------|
| Asset not found | quên khai trong pubspec.yaml | thêm đường dẫn assets |
| Too many positional arguments | file main.dart quá phình | tách thành screens/widgets |
| Class duplicate | đặt tên file trùng nhau | theo chuẩn snake_case & folder rõ ràng |
| Import sai | file bị ở nhầm folder | kiểm tra đường dẫn lib/... |
| Không chạy được hot reload | sửa file config | chỉ sửa file trong lib/ |

---

# 9. **Bài tập thực hành**

1. Tạo project `flutter_structure_demo`.  
2. Tự chia lại thư mục lib/ theo đúng chuẩn.  
3. Thêm thư mục assets/images và khai báo trong pubspec.yaml.  
4. Tạo 2 screens: HomeScreen, ProfileScreen.  
5. Tạo widget button tái sử dụng có tên: `AppButton`.  
6. Tạo model User có name, avatar, age.

---

# 10. **Mini Test cuối chương**

**Câu 1:** Thư mục nào quan trọng nhất trong Flutter?  
→ `lib/`

**Câu 2:** File quản lý dependencies là gì?  
→ `pubspec.yaml`

**Câu 3:** Tại sao phải tách screens/widgets/models?  
→ Code sạch, dễ sửa, dễ mở rộng.

**Câu 4:** Cách thêm assets vào Flutter?  
→ Tạo thư mục → khai báo trong pubspec.yaml → dùng bằng Image.asset

**Câu 5:** File đầu vào của Flutter app là gì?  
→ main.dart

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- Dự án Flutter = lib/ là trung tâm.  
- Không bao giờ để mọi thứ trong main.dart.  
- Tổ chức thư mục đúng giúp học Flutter dễ gấp 3 lần.  
- pubspec.yaml = trái tim cấu hình dự án.  
- Assets phải được khai báo mới dùng được.  
- Chia module: screens – widgets – models – services – utils.

---

# 🎉 Kết thúc chương 03  
Tiếp theo, bạn sẽ bắt đầu xây giao diện bằng các widget:

👉 **Chương 04 – Widget Cơ Bản (Stateless & Stateful + UI cơ bản)**

