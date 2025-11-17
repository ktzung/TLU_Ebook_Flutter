# 🟦 CHƯƠNG 12  
# **WIDGETS NÂNG CAO TRONG FLUTTER**  
*(ListTile – Card – Dialog – Drawer – BottomNavigationBar – SnackBar – AppBar nâng cao)*

Đến chương này, bạn đã có đủ nền tảng để xây một ứng dụng hoàn chỉnh.  
Giờ là lúc học những widget nâng cao nhưng cực kỳ *thực dụng*, dùng trong 90% ứng dụng Flutter hiện nay.

Chương này giúp bạn nâng cấp UI rõ rệt.

---

# 🎯 MỤC TIÊU HỌC TẬP

Sau chương này bạn sẽ:

- Sử dụng ListTile, Card để tạo danh sách đẹp.  
- Hiển thị Dialog, AlertDialog, BottomSheet.  
- Dùng SnackBar để show thông báo.  
- Tạo Drawer (menu bên cạnh).  
- Tạo BottomNavigationBar như ứng dụng chuyên nghiệp.  
- Thao tác với AppBar nâng cao.

---

# 1. **ListTile – widget “đa năng” cho danh sách**

Dùng rất nhiều trong:

- danh sách sản phẩm  
- danh sách người dùng  
- danh sách cài đặt  
- menu chọn  

### Ví dụ:

```dart
ListTile(
  leading: const Icon(Icons.person),
  title: const Text("Nguyễn Văn A"),
  subtitle: const Text("Sinh viên CNTT"),
  trailing: const Icon(Icons.arrow_forward_ios),
  onTap: () {
    print("Clicked!");
  },
);
```

---

# 2. **Card – đóng gói giao diện thành khối đẹp mắt**

Ví dụ:

```dart
Card(
  elevation: 4,
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(12),
  ),
  child: Padding(
    padding: const EdgeInsets.all(16),
    child: Column(
      children: const [
        Text("Sản phẩm X", style: TextStyle(fontSize: 18)),
        SizedBox(height: 8),
        Text("Giá: 1.200.000đ"),
      ],
    ),
  ),
);
```

---

### 🎒 Ví dụ đời sống  
Card giống như "tấm thẻ thông tin" — gọn gàng, rõ ràng, dễ nhìn.

---

# 3. **Divider – đường kẻ ngăn cách**

```dart
Divider(color: Colors.grey[300]);
```

---

# 4. **ListView kết hợp ListTile – danh sách chuyên nghiệp**

```dart
ListView.builder(
  itemCount: users.length,
  itemBuilder: (context, index) {
    final user = users[index];
    return ListTile(
      leading: CircleAvatar(child: Text(user[0])),
      title: Text(user),
      subtitle: const Text("Click để xem chi tiết"),
    );
  },
);
```

---

# 5. **Dialog – hiển thị yêu cầu / cảnh báo**

## AlertDialog

```dart
showDialog(
  context: context,
  builder: (context) {
    return AlertDialog(
      title: const Text("Xác nhận"),
      content: const Text("Bạn có chắc chắn muốn xoá?"),
      actions: [
        TextButton(
          onPressed: () => Navigator.pop(context),
          child: const Text("Huỷ"),
        ),
        ElevatedButton(
          onPressed: () {
            print("Đã xoá!");
            Navigator.pop(context);
          },
          child: const Text("Xoá"),
        ),
      ],
    );
  },
);
```

---

## SimpleDialog

```dart
showDialog(
  context: context,
  builder: (context) => SimpleDialog(
    title: const Text("Chọn màu"),
    children: [
      SimpleDialogOption(
        child: const Text("Đỏ"),
        onPressed: () => Navigator.pop(context, "red"),
      ),
    ],
  ),
);
```

---

# 6. **BottomSheet – menu kéo từ dưới lên**

### ModalBottomSheet:

```dart
showModalBottomSheet(
  context: context,
  builder: (context) {
    return SizedBox(
      height: 200,
      child: Center(child: Text("Nội dung của BottomSheet")),
    );
  },
);
```

---

# 7. **SnackBar – thông báo nhanh**

```dart
ScaffoldMessenger.of(context).showSnackBar(
  const SnackBar(
    content: Text("Đã lưu thành công!"),
    duration: Duration(seconds: 2),
  ),
);
```

---

# 8. **Drawer – menu kéo từ cạnh trái**

Rất phổ biến trong nhiều ứng dụng.

### Scaffold:

```dart
Scaffold(
  appBar: AppBar(title: const Text("Home")),
  drawer: Drawer(
    child: ListView(
      children: const [
        DrawerHeader(
          child: Text("Menu"),
        ),
        ListTile(title: Text("Trang chủ")),
        ListTile(title: Text("Cài đặt")),
      ],
    ),
  ),
);
```

---

# 9. **BottomNavigationBar – tạo ứng dụng có nhiều tab**

Dùng cho:

- tab Home  
- tab Profile  
- tab Settings  
- tab Notification  

### Ví dụ:

```dart
class BottomNavApp extends StatefulWidget {
  const BottomNavApp({super.key});

  @override
  State<BottomNavApp> createState() => _BottomNavAppState();
}

class _BottomNavAppState extends State<BottomNavApp> {
  int index = 0;

  final screens = [
    const Center(child: Text("Home")),
    const Center(child: Text("Profile")),
    const Center(child: Text("Setting")),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: screens[index],
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: index,
        onTap: (i) => setState(() => index = i),
        items: const [
          BottomNavigationBarItem(icon: Icon(Icons.home), label: "Home"),
          BottomNavigationBarItem(icon: Icon(Icons.person), label: "Profile"),
          BottomNavigationBarItem(icon: Icon(Icons.settings), label: "Settings"),
        ],
      ),
    );
  }
}
```

---

# 10. **AppBar nâng cao – thêm actions, search, avatar**

```dart
AppBar(
  title: const Text("Sản phẩm"),
  actions: [
    IconButton(
      icon: const Icon(Icons.search),
      onPressed: () {},
    ),
    IconButton(
      icon: const Icon(Icons.shopping_cart),
      onPressed: () {},
    )
  ],
);
```

---

# 11. **Sai vs Đúng (lỗi sinh viên hay mắc)**

## ❌ Sai: đặt ListView trong Column không Expanded → overflow  
## ✔ Đúng:

```
Expanded(
  child: ListView(...)
)
```

---

## ❌ Sai: show Snackbar bằng Scaffold.of(context) trong bản Flutter mới  
## ✔ Đúng:

```
ScaffoldMessenger.of(context).showSnackBar(...)
```

---

## ❌ Sai: quên Navigator.pop() khi bấm nút trong Dialog  
## ✔ Đúng: luôn đóng dialog trước khi xử lý tiếp

---

## ❌ Sai: BottomNavigationBar không thay đổi tab  
→ quên setState

---

# 12. **Bài tập thực hành**

1. Tạo danh sách người dùng bằng ListTile + ListView.  
2. Tạo Card sản phẩm đẹp (ảnh + tên + giá).  
3. Tạo Dialog xác nhận xóa item.  
4. Làm BottomSheet để chọn màu sắc hoặc theme.  
5. Tạo ứng dụng có 3 tab bằng BottomNavigationBar.  
6. Tạo Drawer menu giống ứng dụng Messenger.

---

# 13. Mini Test cuối chương

**Câu 1:** ListTile dùng để làm gì?  
→ tạo hàng danh sách có icon, text, subtitle.

**Câu 2:** BottomSheet xuất hiện từ đâu?  
→ kéo từ dưới màn hình lên.

**Câu 3:** SnackBar dùng khi nào?  
→ hiển thị thông báo ngắn.

**Câu 4:** Drawer nằm ở đâu?  
→ cạnh trái (hoặc phải) của màn hình.

**Câu 5:** BottomNavigationBar dùng để làm gì?  
→ tạo tab chuyển đổi màn hình.

---

# 📝 Quick Notes (Ghi nhớ nhanh)

- ListTile + Card = giao diện danh sách đẹp và chuyên nghiệp.  
- Dialog = popup xác nhận / cảnh báo.  
- BottomSheet = menu chọn nhanh từ dưới.  
- Snackbar = thông báo “nhẹ nhàng”.  
- Drawer = menu cạnh bên.  
- BottomNavigationBar = nhiều tab trong app.

---

# 🎉 Kết thúc chương 12  
Sẵn sàng để bước sang chương rất quan trọng cho app thực tế:

👉 **Chương 13 – Animation cơ bản (Tween, AnimatedContainer, AnimatedOpacity)**

