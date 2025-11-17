# 🟦 CHƯƠNG 07  
# **FORM & INPUT TRONG FLUTTER**  
*(TextField – Form – Validation – Keyboard – Controllers)*

Gần như mọi ứng dụng đều cần nhập dữ liệu:

- đăng nhập  
- đăng ký  
- tạo ghi chú  
- nhập số tiền  
- tìm kiếm  

Vì vậy, hiểu TextField và Form là kỹ năng **bắt buộc** trong Flutter.

---

# 🎯 MỤC TIÊU HỌC TẬP

Sau chương này bạn sẽ:

- Tạo TextField để nhập dữ liệu.  
- Đọc dữ liệu từ TextField bằng controller.  
- Dùng Form + FormState để validate dữ liệu.  
- Hiểu cách xử lý keyboard.  
- Tránh các lỗi cực phổ biến khi nhập liệu.  
- Tạo màn hình đăng nhập đơn giản.

---

# 1. **TextField – widget nhập liệu cơ bản**

Đơn giản nhất:

```dart
TextField()
```

Nhưng trong thực tế, bạn gần như luôn cần controller.

---

# 2. **TextEditingController – lấy dữ liệu từ TextField**

```dart
final TextEditingController nameController = TextEditingController();
```

### Gán vào TextField:

```dart
TextField(
  controller: nameController,
)
```

### Lấy giá trị khi bấm nút:

```dart
print(nameController.text);
```

---

### 🎒 Ví dụ đời sống  
TextEditingController giống như “cái hộp thư” chứa nội dung người dùng nhập vào.  
Bạn mở hộp → lấy ra `.text`.

---

# 3. **Decoration – làm TextField đẹp và dễ dùng**

```dart
TextField(
  decoration: InputDecoration(
    labelText: "Tên",
    hintText: "Nhập tên của bạn",
    prefixIcon: Icon(Icons.person),
    border: OutlineInputBorder(),
  ),
)
```

Các thuộc tính hay dùng:

- labelText  
- hintText  
- prefixIcon / suffixIcon  
- border  
- errorText (validate thủ công)

---

# 4. **Ẩn mật khẩu – obscureText**

```dart
TextField(
  obscureText: true,
  decoration: InputDecoration(
    labelText: "Mật khẩu",
  ),
)
```

---

# 5. **TextField có keyboard phù hợp**

```dart
keyboardType: TextInputType.number
```

Các loại:

- `text`  
- `number`  
- `emailAddress`  
- `phone`  
- `datetime`

---

# 6. **Form & FormState – validation chuyên nghiệp**

### Tại sao cần Form?

- validate nhiều input cùng lúc  
- quản lý lỗi dễ hơn  
- logic gọn hơn  

---

### Ví dụ Form đầy đủ

```dart
final _formKey = GlobalKey<FormState>();
final _emailController = TextEditingController();

Form(
  key: _formKey,
  child: Column(
    children: [
      TextFormField(
        controller: _emailController,
        decoration: const InputDecoration(labelText: "Email"),
        validator: (value) {
          if (value == null || value.isEmpty) {
            return "Email không được để trống";
          }
          if (!value.contains("@")) {
            return "Email không hợp lệ";
          }
          return null; // hợp lệ
        },
      ),
      ElevatedButton(
        onPressed: () {
          if (_formKey.currentState!.validate()) {
            print("Hợp lệ: ${_emailController.text}");
          }
        },
        child: const Text("Gửi"),
      )
    ],
  ),
);
```

---

### 🧠 Giải thích cực rõ

- `Form` = “đơn đăng ký”  
- `validator` = luật kiểm tra  
- `formKey.currentState!.validate()` = kiểm tra toàn bộ input  

---

# 7. **Tắt bàn phím khi chạm ra ngoài**

Sinh viên hay bị lỗi nhập xong → keyboard che UI.

Cách tắt keyboard:

```dart
FocusScope.of(context).unfocus();
```

Cách dùng:

```dart
GestureDetector(
  onTap: () => FocusScope.of(context).unfocus(),
  child: ...
)
```

---

# 8. **Sử dụng TextFormField – phiên bản nâng cấp của TextField**

`TextFormField` = TextField + tích hợp validation.

```dart
TextFormField(
  decoration: InputDecoration(labelText: "Họ tên"),
  validator: (value) {
    if (value == null || value.isEmpty) {
      return "Không được để trống";
    }
    return null;
  },
)
```

---

# 9. **Sai vs Đúng – sinh viên hay lỗi**

## ❌ Sai: Lấy dữ liệu trước khi người dùng nhập

```dart
print(nameController.text);  // luôn trống
```

## ✔ Đúng: Lấy trong onPressed

---

## ❌ Sai: validator không return string lỗi

```dart
validator: (value) {
  if (value!.isEmpty) print("Empty");  // sai hoàn toàn
}
```

## ✔ Đúng:

```dart
return "Không được để trống";
```

---

## ❌ Sai: đặt TextField trong Container có height cố định → tràn

## ✔ Đúng: để TextField tự giãn theo nội dung

---

## ❌ Sai: dùng nhiều TextField mà không tắt keyboard → UI bị che

## ✔ Đúng: dùng GestureDetector để unfocus

---

# 10. **Ví dụ hoàn chỉnh: Form Login**

```dart
class LoginScreen extends StatefulWidget {
  const LoginScreen({super.key});

  @override
  State<LoginScreen> createState() => _LoginScreenState();
}

class _LoginScreenState extends State<LoginScreen> {
  final _formKey = GlobalKey<FormState>();
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Đăng nhập")),
      body: Padding(
        padding: const EdgeInsets.all(20),
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              TextFormField(
                controller: _emailController,
                decoration: const InputDecoration(labelText: "Email"),
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return "Email không được để trống";
                  }
                  if (!value.contains("@")) {
                    return "Email không hợp lệ";
                  }
                  return null;
                },
              ),
              const SizedBox(height: 20),
              TextFormField(
                controller: _passwordController,
                obscureText: true,
                decoration: const InputDecoration(labelText: "Mật khẩu"),
                validator: (value) {
                  if (value == null || value.length < 6) {
                    return "Mật khẩu phải >= 6 ký tự";
                  }
                  return null;
                },
              ),
              const SizedBox(height: 20),
              ElevatedButton(
                onPressed: () {
                  if (_formKey.currentState!.validate()) {
                    print("Đăng nhập thành công!");
                  }
                },
                child: const Text("Đăng nhập"),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

---

# 11. Bài tập thực hành

1. Tạo form đăng ký có: họ tên, email, mật khẩu, nhập lại mật khẩu.  
2. Tạo form nhập thông tin sản phẩm (tên, giá, mô tả).  
3. Tạo search bar bằng TextField + IconButton.  
4. Làm màn hình ghi chú có TextField lớn + nút lưu.  
5. Làm form feedback (rating + message).

---

# 12. Mini Test cuối chương

**Câu 1:** TextEditingController dùng để làm gì?  
→ Lấy dữ liệu từ TextField.

**Câu 2:** validator trả về gì khi có lỗi?  
→ Thông báo lỗi dạng String.

**Câu 3:** Làm sao để validate tất cả field?  
→ `_formKey.currentState!.validate()`.

**Câu 4:** obscureText dùng khi nào?  
→ Ẩn mật khẩu.

**Câu 5:** Làm sao tắt keyboard khi chạm ra ngoài?  
→ `FocusScope.of(context).unfocus()`.

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- TextField nhập dữ liệu → controller lấy dữ liệu.  
- Form + validator = kiểm tra hợp lệ chuyên nghiệp.  
- TextFormField = TextField + validation tích hợp.  
- Keyboard che màn hình? → unfocus.  
- Luôn tách UI và logic để code dễ bảo trì.

---

# 🎉 Kết thúc chương 07  
Tiếp theo: chương cực kỳ quan trọng trong kiến trúc UI:

👉 **Chương 08 – State Management căn bản (setState, lifting state up)**

