# 🟦 THỰC HÀNH CHI TIẾT: BLOC + PROVIDER VỚI .NET WEB API
## **Dự án thực tế: Ứng dụng Quản lý Sách (Book Management App)**

> **📌 DÀNH CHO NGƯỜI MỚI BẮT ĐẦU**
> 
> Tài liệu này được viết **cực kỳ chi tiết** để ngay cả người mới cũng có thể:
> - ✅ Hiểu từng bước làm gì và tại sao
> - ✅ Làm theo từng bước một cách dễ dàng
> - ✅ Chạy được kết quả ngay
> - ✅ Biết cách xử lý khi gặp lỗi
>
> **⏱️ Thời gian ước tính:** 4-6 giờ (tùy kinh nghiệm)
> 
> **🎯 Mục tiêu cuối cùng:** Có một app Flutter hoàn chỉnh kết nối với .NET Web API

---

## 🚀 BẮT ĐẦU TỪ ĐÂU? (ĐỌC KỸ PHẦN NÀY!)

### Bước 0: Chuẩn bị công cụ

**Bạn cần có:**
1. ✅ **.NET SDK** (phiên bản 6.0 trở lên)
   - Tải tại: https://dotnet.microsoft.com/download
   - Kiểm tra: Mở Command Prompt/Terminal, gõ `dotnet --version`
   - Phải thấy số phiên bản (ví dụ: `6.0.100`)

2. ✅ **Flutter SDK** (phiên bản 3.0 trở lên)
   - Tải tại: https://flutter.dev/docs/get-started/install
   - Kiểm tra: Mở Command Prompt/Terminal, gõ `flutter --version`
   - Phải thấy số phiên bản

3. ✅ **Visual Studio Code** (hoặc IDE khác)
   - Tải tại: https://code.visualstudio.com/
   - Cài extension: C# (cho .NET), Dart (cho Flutter)

4. ✅ **Postman** (để test API)
   - Tải tại: https://www.postman.com/downloads/

**⚠️ QUAN TRỌNG:** Đảm bảo tất cả đã cài đặt xong trước khi bắt đầu!

---

### Cấu trúc dự án (Tổng quan)

Sau khi hoàn thành, bạn sẽ có **2 dự án**:

```
📁 Workspace của bạn/
├── 📁 BookManagementAPI/          ← .NET Web API (Backend)
│   ├── Models/
│   ├── Data/
│   ├── Controllers/
│   └── Migrations/
│
└── 📁 book_management_app/         ← Flutter App (Frontend)
    └── lib/
        ├── models/
        ├── services/
        ├── providers/
        ├── blocs/
        ├── screens/
        └── widgets/
```

**Luồng hoạt động:**
```
Flutter App (Frontend)
    ↓ (Gửi request)
.NET Web API (Backend)
    ↓ (Xử lý)
SQLite Database
    ↓ (Trả về dữ liệu)
Flutter App (Hiển thị)
```

---

### Lộ trình học tập (Làm theo thứ tự!)

**PHẦN 1:** Xây dựng .NET Web API (Backend)
- Bước 1-2: Tạo project và Model
- Bước 3: Migration và Seeding
- Bước 4: Controller
- Bước 5: Test với Postman

**PHẦN 2:** Xây dựng Flutter App (Frontend)
- Bước 1-2: Setup project và dependencies
- Bước 3-7: Tạo các file code
- Bước 8: Chạy app và test

**PHẦN 3:** Kết nối và test
- Kết nối Flutter với API
- Test toàn bộ tính năng

---

### 💡 TƯ DUY QUAN TRỌNG

Trước khi code, hiểu rõ:

- **Bloc:** Xử lý logic nghiệp vụ phức tạp (API calls, CRUD)
  - Giống như "nhà máy xử lý" - có quy trình rõ ràng
  - Event → Logic → State

- **Provider:** Quản lý state toàn cục (User, Theme, Settings)
  - Giống như "kho hàng chung" - ai cũng dùng được
  - notifyListeners() → UI cập nhật

- **Kết hợp:** Bloc cho features, Provider cho app-wide state

> **⚠️ LƯU Ý:** Đừng vội vàng! Làm từng bước một, test kỹ trước khi sang bước tiếp theo.

---

## 🎯 MỤC TIÊU SẢN PHẨM

Xây dựng ứng dụng **Quản lý Sách** với các tính năng:

1. **Authentication** (Provider) - Đăng nhập/Đăng xuất
2. **Book Management** (Bloc) - Xem danh sách, Thêm, Sửa, Xóa sách
3. **Theme Switcher** (Provider) - Chuyển giao diện Sáng/Tối
4. **API Integration** - Kết nối với .NET Web API backend

---

## 🧠 HIỂU RÕ PROVIDER VÀ BLOC QUA LIÊN TƯỞNG THỰC TẾ

Trước khi bắt đầu code, hãy hiểu rõ **Provider** và **Bloc** là gì qua các ví dụ đời thường:

### 📦 PROVIDER - "Kho Hàng Chung" (Shared Storage)

**Liên tưởng:** Hãy tưởng tượng bạn đang ở một **cửa hàng tiện lợi** (convenience store):

- **Kho hàng (Provider)** là nơi lưu trữ những thứ **ai cũng cần dùng chung**:
  - 💡 **Bóng đèn** (Theme) - Tất cả mọi người trong cửa hàng đều thấy cùng một loại ánh sáng
  - 🔑 **Chìa khóa cửa** (Authentication) - Ai vào cửa hàng cũng cần chìa khóa này
  - 📊 **Bảng giá chung** (Settings) - Mọi nhân viên đều xem cùng một bảng giá

- **Đặc điểm của Provider:**
  - ✅ **Đơn giản:** Chỉ cần `notifyListeners()` là mọi người biết có thay đổi
  - ✅ **Toàn cục:** Mọi widget đều có thể truy cập (như mọi người đều vào được kho)
  - ✅ **Nhẹ:** Không cần cấu trúc phức tạp, chỉ cần get/set

**Ví dụ trong code:**
```dart
// Provider như một cái kho đơn giản
class ThemeProvider with ChangeNotifier {
  bool _isDark = false;
  
  bool get isDark => _isDark;
  
  void toggle() {
    _isDark = !_isDark;
    notifyListeners(); // "Này mọi người, đèn đã đổi rồi!"
  }
}
```

**Khi nào dùng Provider?**
- ✅ Quản lý Theme (Sáng/Tối)
- ✅ Authentication (User đã đăng nhập chưa?)
- ✅ Settings (Cài đặt toàn app)
- ✅ Shopping Cart (Giỏ hàng - nếu đơn giản)

---

### 🏭 BLOC - "Nhà Máy Xử Lý" (Business Logic Factory)

**Liên tưởng:** Hãy tưởng tượng bạn đang ở một **nhà máy sản xuất**:

- **Nhà máy (Bloc)** là nơi xử lý các **quy trình phức tạp**:
  - 📦 **Dây chuyền sản xuất** (CRUD Operations):
    1. Nhận đơn hàng (Event: `CreateBookEvent`)
    2. Kiểm tra nguyên liệu (Loading state)
    3. Sản xuất (Gọi API)
    4. Kiểm tra chất lượng (Success/Error state)
    5. Giao hàng (Emit state mới)
  
  - 🔍 **Quy trình tìm kiếm** (Search):
    1. Nhận từ khóa (Event: `SearchEvent`)
    2. Xử lý tìm kiếm (Loading)
    3. Trả kết quả (Success với danh sách)

- **Đặc điểm của Bloc:**
  - ✅ **Có cấu trúc rõ ràng:** Event → Logic → State
  - ✅ **Theo dõi được:** Biết chính xác event nào gây ra state nào
  - ✅ **Mạnh mẽ:** Xử lý logic phức tạp, nhiều bước
  - ✅ **Dễ test:** Test logic độc lập với UI

**Ví dụ trong code:**
```dart
// Bloc như một nhà máy có quy trình rõ ràng
class BookBloc extends Bloc<BookEvent, BookState> {
  BookBloc() : super(BookInitial()) {
    on<LoadBooksEvent>((event, emit) async {
      emit(BookLoading());           // Bước 1: Báo "Đang làm"
      try {
        final books = await ApiService.getBooks(); // Bước 2: Gọi API
        emit(BookLoaded(books));     // Bước 3: Báo "Xong rồi"
      } catch (e) {
        emit(BookError(e.toString())); // Bước 3: Báo "Lỗi rồi"
      }
    });
  }
}
```

**Khi nào dùng Bloc?**
- ✅ CRUD Operations (Create, Read, Update, Delete)
- ✅ API Calls với nhiều trạng thái (Loading, Success, Error)
- ✅ Search/Filter phức tạp
- ✅ Form validation phức tạp
- ✅ Business logic có nhiều bước

---

### 🎯 SO SÁNH TRỰC QUAN

| Khía cạnh | Provider (Kho Hàng) | Bloc (Nhà Máy) |
|-----------|---------------------|----------------|
| **Độ phức tạp** | Đơn giản (get/set) | Phức tạp (Event → State) |
| **Ví dụ đời thường** | Kho hàng chung | Nhà máy sản xuất |
| **Khi nào dùng** | State toàn cục đơn giản | Logic nghiệp vụ phức tạp |
| **Cấu trúc** | `notifyListeners()` | `Event → Logic → State` |
| **Debug** | Biết method nào gọi | Biết event nào → state nào |
| **Ví dụ trong app** | Theme, Auth, Settings | CRUD, Search, API calls |

---

### 🔄 KẾT HỢP PROVIDER + BLOC TRONG CÙNG APP

**Liên tưởng:** Một **cửa hàng lớn** có cả:
- **Kho hàng (Provider):** Lưu thông tin chung (Theme, User)
- **Nhà máy (Bloc):** Xử lý đơn hàng, sản phẩm (CRUD)

**Trong ứng dụng của chúng ta:**
- **Provider quản lý:**
  - 🔐 Authentication (User đã đăng nhập?)
  - 🎨 Theme (Giao diện sáng hay tối?)
  
- **Bloc quản lý:**
  - 📚 Book Management (CRUD sách)
  - 🔍 Search (Tìm kiếm sách - nếu có)

**Lý do kết hợp:**
- Provider nhẹ, phù hợp cho state đơn giản
- Bloc mạnh, phù hợp cho logic phức tạp
- Mỗi cái làm đúng việc của mình → Code sạch, dễ maintain

---

### 💡 TÓM TẮT NHANH

```
PROVIDER = Kho hàng đơn giản
  → Dùng cho: Theme, Auth, Settings
  → Cách dùng: notifyListeners() khi thay đổi

BLOC = Nhà máy xử lý phức tạp
  → Dùng cho: CRUD, API calls, Search
  → Cách dùng: Event → Logic → State
```

Bây giờ bạn đã hiểu rõ, hãy bắt đầu code! 🚀

---

## 📋 PHẦN 1: THIẾT LẬP .NET WEB API (Backend)

> **⏱️ Thời gian:** 30-45 phút
> 
> **🎯 Mục tiêu:** Tạo một API server có thể nhận request từ Flutter và trả về dữ liệu sách

---

### Bước 1: Tạo .NET Web API Project

**Mục đích:** Tạo một project mới để làm backend server.

**Các bước chi tiết:**

**1.1. Mở Terminal/Command Prompt:**
- Windows: Nhấn `Win + R`, gõ `cmd`, Enter
- Mac/Linux: Mở Terminal

**1.2. Di chuyển đến thư mục bạn muốn tạo project:**
```bash
# Ví dụ: Tạo trong thư mục Desktop
cd Desktop

# Hoặc tạo thư mục mới
mkdir FlutterProjects
cd FlutterProjects
```

**1.3. Tạo project Web API:**
```bash
dotnet new webapi -n BookManagementAPI
```

**Giải thích lệnh:**
- `dotnet new`: Tạo project mới
- `webapi`: Template cho Web API
- `-n BookManagementAPI`: Tên project

**Kết quả mong đợi:**
```
The template "ASP.NET Core Web API" was created successfully.
```

**1.4. Di chuyển vào thư mục project:**
```bash
cd BookManagementAPI
```

**1.5. Kiểm tra project đã tạo:**
```bash
# Xem danh sách file
dir    # Windows
ls     # Mac/Linux
```

**Bạn sẽ thấy:**
```
BookManagementAPI/
├── Controllers/
├── Program.cs
├── appsettings.json
└── BookManagementAPI.csproj
```

**1.6. Thêm package Entity Framework Core:**
```bash
# Package để làm việc với SQLite database
dotnet add package Microsoft.EntityFrameworkCore.Sqlite

# Package để tạo migrations
dotnet add package Microsoft.EntityFrameworkCore.Design
```

**Giải thích:**
- **Entity Framework Core:** Giúp làm việc với database dễ dàng (không cần viết SQL)
- **SQLite:** Database đơn giản, lưu trong file (không cần cài đặt server)

**Kết quả mong đợi:**
```
PackageReference for package 'Microsoft.EntityFrameworkCore.Sqlite' version 'X.X.X' added to the project.
PackageReference for package 'Microsoft.EntityFrameworkCore.Design' version 'X.X.X' added to the project.
```

**1.7. Cài đặt EF Core Tools (nếu chưa có):**
```bash
dotnet tool install --global dotnet-ef
```

**Giải thích:** Tool này giúp tạo migrations (sẽ học ở bước sau).

**✅ KIỂM TRA BƯỚC 1:**
- [ ] Project đã được tạo
- [ ] Đã di chuyển vào thư mục `BookManagementAPI`
- [ ] Đã cài đặt 2 packages
- [ ] Không có lỗi khi chạy lệnh

**❌ Nếu gặp lỗi:**
- **"dotnet: command not found"** → Chưa cài .NET SDK, tải tại https://dotnet.microsoft.com/download
- **"Package not found"** → Kiểm tra kết nối internet, thử lại

### Bước 2: Tạo Model và DbContext

**Mục đích:** 
- **Model:** Định nghĩa cấu trúc dữ liệu của Book (giống như bảng trong database)
- **DbContext:** Kết nối với database, cho phép thao tác dữ liệu

**Giải thích đơn giản:**
- Model = "Bản thiết kế" của một cuốn sách (có gì: title, author, ...)
- DbContext = "Cầu nối" giữa code và database

---

**2.1. Tạo thư mục Models:**

**Cách 1: Dùng Terminal**
```bash
# Trong thư mục BookManagementAPI
mkdir Models
```

**Cách 2: Dùng Visual Studio Code**
- Click phải vào thư mục `BookManagementAPI` trong Explorer
- Chọn "New Folder"
- Đặt tên: `Models`

**2.2. Tạo file `Models/Book.cs`:**

**Cách tạo file:**
- Click phải vào thư mục `Models`
- Chọn "New File"
- Đặt tên: `Book.cs`

**Copy toàn bộ code sau vào file:**

```csharp
namespace BookManagementAPI.Models
{
    public class Book
    {
        public int Id { get; set; }                    // ID tự động tăng
        public string Title { get; set; } = string.Empty;  // Tiêu đề sách
        public string Author { get; set; } = string.Empty; // Tác giả
        public string ISBN { get; set; } = string.Empty;  // Mã ISBN
        public DateTime PublishedDate { get; set; }        // Ngày xuất bản
        public int PageCount { get; set; }                 // Số trang
        public string? Description { get; set; }           // Mô tả (có thể null)
        public DateTime CreatedAt { get; set; } = DateTime.UtcNow; // Ngày tạo
    }
}
```

**Giải thích từng dòng:**
- `public int Id`: ID của sách (số nguyên, tự động tăng)
- `public string Title`: Tiêu đề sách (chuỗi, bắt buộc)
- `= string.Empty`: Giá trị mặc định là chuỗi rỗng
- `public string? Description`: Dấu `?` nghĩa là có thể null (không bắt buộc)
- `DateTime.UtcNow`: Lấy thời gian hiện tại (UTC)

**2.3. Tạo thư mục Data:**

```bash
mkdir Data
```

**2.4. Tạo file `Data/ApplicationDbContext.cs`:**

```csharp
using Microsoft.EntityFrameworkCore;
using BookManagementAPI.Models;

namespace BookManagementAPI.Data
{
    // ApplicationDbContext kế thừa từ DbContext (của EF Core)
    public class ApplicationDbContext : DbContext
    {
        // Constructor: Nhận cấu hình từ bên ngoài
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options) { }

        // DbSet: Đại diện cho bảng Books trong database
        // Có thể dùng để query, thêm, sửa, xóa sách
        public DbSet<Book> Books { get; set; }
    }
}
```

**Giải thích:**
- `DbContext`: Class cơ sở của EF Core để làm việc với database
- `DbSet<Book>`: Đại diện cho bảng `Books` trong database
- `DbContextOptions`: Cấu hình kết nối database (sẽ setup ở bước sau)

**✅ KIỂM TRA BƯỚC 2:**
- [ ] Đã tạo thư mục `Models`
- [ ] Đã tạo file `Models/Book.cs` với code trên
- [ ] Đã tạo thư mục `Data`
- [ ] Đã tạo file `Data/ApplicationDbContext.cs` với code trên
- [ ] Không có lỗi đỏ gạch chân trong code (nếu dùng IDE)

**❌ Nếu gặp lỗi:**
- **"The type or namespace name 'Microsoft' could not be found"** → Chưa restore packages, chạy: `dotnet restore`
- **Lỗi đỏ gạch chân** → Đảm bảo đã copy đúng code, kiểm tra namespace

### Bước 3: Cấu hình DbContext trong `Program.cs`

```csharp
using Microsoft.EntityFrameworkCore;
using BookManagementAPI.Data;
using Microsoft.Extensions.Logging;

var builder = WebApplication.CreateBuilder(args);

// Add services
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// Cấu hình SQLite Database
// Lưu ý: Connection string có thể đặt trong appsettings.json
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlite(builder.Configuration.GetConnectionString("DefaultConnection") 
        ?? "Data Source=books.db"));

// Cấu hình CORS để Flutter app có thể gọi API
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowFlutterApp",
        policy =>
        {
            // Cho phép Flutter app gọi API từ các origin này
            policy.WithOrigins(
                    "http://localhost:3000",      // Web
                    "http://10.0.2.2:5000",       // Android Emulator
                    "http://127.0.0.1:5000"        // iOS Simulator
                  )
                  .AllowAnyHeader()
                  .AllowAnyMethod();
        });
});

var app = builder.Build();

// Configure the HTTP request pipeline
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseCors("AllowFlutterApp");
app.UseAuthorization();
app.MapControllers();

// Áp dụng migrations và seed data khi app khởi động
using (var scope = app.Services.CreateScope())
{
    var services = scope.ServiceProvider;
    var context = services.GetRequiredService<ApplicationDbContext>();
    
    try
    {
        // Áp dụng migrations tự động
        context.Database.Migrate();
        
        // Seed dữ liệu mẫu (chỉ khi database trống)
        SeedData.Initialize(context);
    }
    catch (Exception ex)
    {
        var logger = services.GetRequiredService<ILogger<Program>>();
        logger.LogError(ex, "An error occurred while migrating or seeding the database.");
    }
}

app.Run();
```

---

### Bước 3.1: Tạo Migration (Quan trọng!)

**Migration là gì?**
- Migration giống như **"bản thiết kế"** của database
- Mỗi lần thay đổi Model (thêm cột, xóa bảng...), bạn tạo Migration mới
- Migration giúp **đồng bộ** code Model với database thực tế

**Tại sao cần Migration thay vì EnsureCreated?**
- ✅ **Kiểm soát được:** Biết chính xác database thay đổi gì
- ✅ **Version control:** Có thể rollback nếu cần
- ✅ **Production-ready:** Phù hợp cho môi trường thực tế
- ✅ **Lịch sử:** Biết database đã thay đổi như thế nào

**Các bước tạo Migration:**

**Bước 1:** Cài đặt EF Core Tools (nếu chưa có)
```bash
dotnet tool install --global dotnet-ef
```

**Bước 2:** Tạo Migration đầu tiên
```bash
# Tạo migration với tên "InitialCreate"
dotnet ef migrations add InitialCreate

# Kết quả: Tạo thư mục Migrations/ với các file migration
```

**Bước 3:** Xem Migration đã tạo
Sau khi chạy lệnh trên, bạn sẽ thấy:
```
Migrations/
├── 20240115120000_InitialCreate.cs    # File migration
└── ApplicationDbContextModelSnapshot.cs # Snapshot của model hiện tại
```

**File Migration được tạo (`Migrations/XXXXXX_InitialCreate.cs`):**
```csharp
using Microsoft.EntityFrameworkCore.Migrations;

#nullable disable

namespace BookManagementAPI.Migrations
{
    /// <inheritdoc />
    public partial class InitialCreate : Migration
    {
        /// <inheritdoc />
        protected override void Up(MigrationBuilder migrationBuilder)
        {
            migrationBuilder.CreateTable(
                name: "Books",
                columns: table => new
                {
                    Id = table.Column<int>(type: "INTEGER", nullable: false)
                        .Annotation("Sqlite:Autoincrement", true),
                    Title = table.Column<string>(type: "TEXT", nullable: false),
                    Author = table.Column<string>(type: "TEXT", nullable: false),
                    ISBN = table.Column<string>(type: "TEXT", nullable: false),
                    PublishedDate = table.Column<DateTime>(type: "TEXT", nullable: false),
                    PageCount = table.Column<int>(type: "INTEGER", nullable: false),
                    Description = table.Column<string>(type: "TEXT", nullable: true),
                    CreatedAt = table.Column<DateTime>(type: "TEXT", nullable: false)
                },
                constraints: table =>
                {
                    table.PrimaryKey("PK_Books", x => x.Id);
                });
        }

        /// <inheritdoc />
        protected override void Down(MigrationBuilder migrationBuilder)
        {
            migrationBuilder.DropTable(
                name: "Books");
        }
    }
}
```

**Giải thích Migration:**
- `Up()`: Chạy khi **áp dụng** migration (tạo bảng, thêm cột...)
- `Down()`: Chạy khi **rollback** migration (xóa bảng, xóa cột...)

**Bước 4:** Áp dụng Migration vào Database
```bash
# Áp dụng tất cả migrations chưa được áp dụng
dotnet ef database update

# Hoặc áp dụng migration cụ thể
dotnet ef database update InitialCreate
```

**Kết quả:** Database `books.db` được tạo với bảng `Books` và các cột đã định nghĩa.

---

### Bước 3.2: Tạo Migration mới khi thay đổi Model

**Ví dụ:** Bạn muốn thêm cột `Price` vào bảng `Books`:

**Bước 1:** Sửa Model
```csharp
public class Book
{
    // ... các properties cũ ...
    public decimal Price { get; set; }  // ← Thêm property mới
}
```

**Bước 2:** Tạo Migration mới
```bash
dotnet ef migrations add AddPriceToBook
```

**Bước 3:** Xem Migration mới
File `Migrations/XXXXXX_AddPriceToBook.cs`:
```csharp
public partial class AddPriceToBook : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.AddColumn<decimal>(
            name: "Price",
            table: "Books",
            type: "TEXT",
            nullable: false,
            defaultValue: 0m);
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropColumn(
            name: "Price",
            table: "Books");
    }
}
```

**Bước 4:** Áp dụng Migration
```bash
dotnet ef database update
```

**Kết quả:** Bảng `Books` có thêm cột `Price`.

---

### Bước 3.3: Các lệnh Migration hữu ích

```bash
# Xem danh sách migrations
dotnet ef migrations list

# Xem migration chưa được áp dụng
dotnet ef migrations list --context ApplicationDbContext

# Xóa migration cuối cùng (chưa apply)
dotnet ef migrations remove

# Rollback về migration trước đó
dotnet ef database update PreviousMigrationName

# Tạo script SQL từ migrations (để deploy)
dotnet ef migrations script -o migration.sql
```

---

### Bước 4: Tạo Seeding Data (Dữ liệu mẫu)

**Seeding là gì?**
- Seeding giống như **"trồng hạt giống"** - tạo dữ liệu mẫu ban đầu
- Giúp test app ngay mà không cần nhập tay từng dữ liệu
- Chỉ chạy khi database trống (tránh duplicate)

**Tạo file `Data/SeedData.cs`:**

```csharp
using BookManagementAPI.Models;

namespace BookManagementAPI.Data
{
    public static class SeedData
    {
        public static void Initialize(ApplicationDbContext context)
        {
            // Kiểm tra xem đã có dữ liệu chưa
            if (context.Books.Any())
            {
                return; // Database đã có dữ liệu, không seed nữa
            }

            // Tạo danh sách sách mẫu
            var books = new Book[]
            {
                new Book
                {
                    Title = "Flutter Complete Guide",
                    Author = "John Doe",
                    ISBN = "978-0123456789",
                    PublishedDate = new DateTime(2023, 1, 15),
                    PageCount = 500,
                    Description = "Hướng dẫn toàn diện về Flutter framework",
                    CreatedAt = DateTime.UtcNow
                },
                new Book
                {
                    Title = "C# Programming Mastery",
                    Author = "Jane Smith",
                    ISBN = "978-0987654321",
                    PublishedDate = new DateTime(2023, 3, 20),
                    PageCount = 650,
                    Description = "Từ cơ bản đến nâng cao về C# và .NET",
                    CreatedAt = DateTime.UtcNow
                },
                new Book
                {
                    Title = "Clean Architecture",
                    Author = "Robert C. Martin",
                    ISBN = "978-0134494166",
                    PublishedDate = new DateTime(2017, 9, 20),
                    PageCount = 432,
                    Description = "Kiến trúc phần mềm sạch và dễ bảo trì",
                    CreatedAt = DateTime.UtcNow
                },
                new Book
                {
                    Title = "Design Patterns",
                    Author = "Gang of Four",
                    ISBN = "978-0201633610",
                    PublishedDate = new DateTime(1994, 10, 21),
                    PageCount = 395,
                    Description = "Các mẫu thiết kế trong lập trình hướng đối tượng",
                    CreatedAt = DateTime.UtcNow
                },
                new Book
                {
                    Title = "Entity Framework Core in Action",
                    Author = "Jon P Smith",
                    ISBN = "978-1617294563",
                    PublishedDate = new DateTime(2021, 2, 15),
                    PageCount = 520,
                    Description = "Hướng dẫn sử dụng EF Core hiệu quả",
                    CreatedAt = DateTime.UtcNow
                }
            };

            // Thêm vào database
            context.Books.AddRange(books);
            context.SaveChanges();

            Console.WriteLine("✅ Seeded {0} books to database.", books.Length);
        }
    }
}
```

**Lưu ý:** File `SeedData.cs` đã được gọi trong `Program.cs` ở bước 3.

**Kết quả:** Khi chạy app lần đầu, database sẽ tự động có 5 cuốn sách mẫu để test.

---

### Bước 4.1: Seeding nâng cao (Tùy chọn)

Nếu muốn seeding phức tạp hơn (ví dụ: random data):

```csharp
public static void Initialize(ApplicationDbContext context)
{
    if (context.Books.Any())
    {
        return;
    }

    var random = new Random();
    var authors = new[] { "John Doe", "Jane Smith", "Bob Johnson", "Alice Williams" };
    var titles = new[] 
    { 
        "Flutter Guide", "C# Mastery", "Clean Code", 
        "Design Patterns", "EF Core Tutorial" 
    };

    var books = Enumerable.Range(1, 10).Select(index => new Book
    {
        Title = $"{titles[random.Next(titles.Length)]} {index}",
        Author = authors[random.Next(authors.Length)],
        ISBN = $"978-{random.Next(1000000000, 9999999999)}",
        PublishedDate = DateTime.UtcNow.AddDays(-random.Next(365, 1095)),
        PageCount = random.Next(200, 800),
        Description = $"Mô tả cho sách số {index}",
        CreatedAt = DateTime.UtcNow
    }).ToArray();

    context.Books.AddRange(books);
    context.SaveChanges();
}
```

### Bước 4: Tạo Controller

**File: `Controllers/BooksController.cs`**

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using BookManagementAPI.Data;
using BookManagementAPI.Models;

namespace BookManagementAPI.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class BooksController : ControllerBase
    {
        private readonly ApplicationDbContext _context;

        public BooksController(ApplicationDbContext context)
        {
            _context = context;
        }

        // GET: api/books
        [HttpGet]
        public async Task<ActionResult<IEnumerable<Book>>> GetBooks()
        {
            return await _context.Books.ToListAsync();
        }

        // GET: api/books/5
        [HttpGet("{id}")]
        public async Task<ActionResult<Book>> GetBook(int id)
        {
            var book = await _context.Books.FindAsync(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        // POST: api/books
        [HttpPost]
        public async Task<ActionResult<Book>> CreateBook(Book book)
        {
            book.CreatedAt = DateTime.UtcNow;
            _context.Books.Add(book);
            await _context.SaveChangesAsync();

            return CreatedAtAction(nameof(GetBook), new { id = book.Id }, book);
        }

        // PUT: api/books/5
        [HttpPut("{id}")]
        public async Task<IActionResult> UpdateBook(int id, Book book)
        {
            if (id != book.Id)
            {
                return BadRequest();
            }

            _context.Entry(book).State = EntityState.Modified;

            try
            {
                await _context.SaveChangesAsync();
            }
            catch (DbUpdateConcurrencyException)
            {
                if (!BookExists(id))
                {
                    return NotFound();
                }
                else
                {
                    throw;
                }
            }

            return NoContent();
        }

        // DELETE: api/books/5
        [HttpDelete("{id}")]
        public async Task<IActionResult> DeleteBook(int id)
        {
            var book = await _context.Books.FindAsync(id);
            if (book == null)
            {
                return NotFound();
            }

            _context.Books.Remove(book);
            await _context.SaveChangesAsync();

            return NoContent();
        }

        private bool BookExists(int id)
        {
            return _context.Books.Any(e => e.Id == id);
        }
    }
}
```

### Bước 5: Chạy API và Kiểm tra

**Mục đích:** Khởi động API server và kiểm tra xem có hoạt động không.

**5.1. Chạy API:**

Trong Terminal, đảm bảo đang ở thư mục `BookManagementAPI`, sau đó chạy:

```bash
dotnet run
```

**Kết quả mong đợi:**
```
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5000
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
```

**⚠️ QUAN TRỌNG:** 
- API đang chạy tại: `http://localhost:5000` (HTTP) hoặc `https://localhost:5001` (HTTPS)
- **ĐỪNG ĐÓNG Terminal này!** API sẽ dừng nếu bạn đóng.
- Để dừng API: Nhấn `Ctrl + C` trong Terminal

**5.2. Kiểm tra API bằng Browser (Nhanh nhất):**

Mở browser, truy cập:
```
http://localhost:5000/api/books
```

**Kết quả mong đợi:**
- Nếu đã seed data: Thấy JSON array chứa các cuốn sách
- Nếu chưa seed: Thấy `[]` (mảng rỗng)
- Nếu lỗi: Thấy thông báo lỗi

**5.3. Kiểm tra Swagger UI (Nếu có):**

Nếu bạn thấy Swagger được bật, truy cập:
```
http://localhost:5000/swagger
```

**Swagger là gì?**
- Giao diện web để test API trực tiếp
- Không cần Postman, test ngay trên browser
- Xem được tất cả endpoints có sẵn

**✅ KIỂM TRA BƯỚC 5:**
- [ ] API đã chạy (không có lỗi trong Terminal)
- [ ] Truy cập `http://localhost:5000/api/books` thấy kết quả (JSON hoặc mảng rỗng)
- [ ] Không có lỗi 500 Internal Server Error

**❌ Nếu gặp lỗi:**

**Lỗi: "Unable to connect"**
- → API chưa chạy, kiểm tra Terminal
- → Port bị chiếm, thử đổi port trong `launchSettings.json`

**Lỗi: "500 Internal Server Error"**
- → Database chưa được tạo, chạy lại: `dotnet ef database update`
- → Migration chưa được áp dụng

**Lỗi: "Cannot find file"**
- → Đảm bảo đang ở đúng thư mục `BookManagementAPI`
- → Chạy `dotnet restore` trước

---

### 📝 TÓM TẮT PHẦN 1 (.NET Web API)

**Bạn đã hoàn thành:**
- ✅ Tạo project .NET Web API
- ✅ Tạo Model Book
- ✅ Tạo DbContext
- ✅ Cấu hình Migration
- ✅ Seed dữ liệu mẫu
- ✅ Tạo Controller với CRUD operations
- ✅ Chạy và test API

**Kết quả:**
- API server đang chạy tại `http://localhost:5000`
- Có thể test bằng browser hoặc Postman
- Database đã có dữ liệu mẫu (5 cuốn sách)

**Bước tiếp theo:** Xây dựng Flutter App để kết nối với API này!

---

## 📱 PHẦN 2: THIẾT LẬP FLUTTER APP (Frontend)

> **⏱️ Thời gian:** 2-3 giờ
> 
> **🎯 Mục tiêu:** Tạo ứng dụng Flutter có thể kết nối với .NET Web API và hiển thị dữ liệu

**⚠️ QUAN TRỌNG:** 
- Đảm bảo .NET API đang chạy (Phần 1)
- Flutter SDK đã được cài đặt
- Có thể dùng Android Studio, VS Code, hoặc bất kỳ IDE nào

---

### Bước 1: Tạo Flutter Project

**Mục đích:** Tạo một project Flutter mới.

**1.1. Mở Terminal/Command Prompt:**

**1.2. Di chuyển đến thư mục bạn muốn (khác với thư mục API):**
```bash
# Ví dụ: Tạo trong cùng thư mục với API
cd Desktop/FlutterProjects  # Hoặc thư mục bạn muốn
```

**1.3. Tạo Flutter project:**
```bash
flutter create book_management_app
```

**Giải thích:**
- `flutter create`: Lệnh tạo project mới
- `book_management_app`: Tên project (có thể đổi tên khác)

**Kết quả mong đợi:**
```
Creating project book_management_app...
[✓] Flutter project created successfully!
```

**1.4. Di chuyển vào thư mục project:**
```bash
cd book_management_app
```

**1.5. Kiểm tra project:**
```bash
# Xem cấu trúc thư mục
dir    # Windows
ls     # Mac/Linux
```

**Bạn sẽ thấy:**
```
book_management_app/
├── lib/
│   └── main.dart
├── pubspec.yaml
├── android/
├── ios/
└── ...
```

**✅ KIỂM TRA BƯỚC 1:**
- [ ] Project đã được tạo
- [ ] Đã di chuyển vào thư mục `book_management_app`
- [ ] Thấy file `lib/main.dart` và `pubspec.yaml`

**❌ Nếu gặp lỗi:**
- **"flutter: command not found"** → Chưa cài Flutter SDK, thêm vào PATH
- **"Flutter SDK not found"** → Cài đặt Flutter và thêm vào PATH

---

### Bước 2: Cài đặt Dependencies (Thư viện cần thiết)

**Mục đích:** Cài đặt các package cần thiết cho app.

**2.1. Mở file `pubspec.yaml`:**

**Cách mở:**
- Dùng VS Code: Click vào file `pubspec.yaml` trong Explorer
- Hoặc dùng Notepad/Text Editor bất kỳ

**2.2. Tìm phần `dependencies:` và sửa như sau:**

**Tìm dòng này:**
```yaml
dependencies:
  flutter:
    sdk: flutter
```

**Thay thế bằng (hoặc thêm vào):**
```yaml
dependencies:
  flutter:
    sdk: flutter
  
  # State Management - Quản lý state
  flutter_bloc: ^8.1.0      # Bloc pattern
  equatable: ^2.0.5         # So sánh objects
  provider: ^6.1.0          # Provider pattern
  
  # HTTP & JSON - Gọi API và xử lý JSON
  http: ^1.2.0              # Gọi HTTP requests
  
  # UI - Giao diện
  intl: ^0.19.0             # Format ngày tháng
```

**Giải thích từng package:**
- `flutter_bloc`: Quản lý state với Bloc pattern (cho CRUD operations)
- `equatable`: Giúp so sánh objects dễ dàng (cho Bloc states)
- `provider`: Quản lý state toàn cục (cho Auth, Theme)
- `http`: Gọi API (GET, POST, PUT, DELETE)
- `intl`: Format ngày tháng đẹp (ví dụ: 15/01/2024)

**⚠️ LƯU Ý:** 
- `dart:convert` KHÔNG cần thêm vào `pubspec.yaml` (đã có sẵn trong Dart SDK)
- Chỉ cần `import 'dart:convert';` trong code

**2.3. Lưu file `pubspec.yaml`**

**2.4. Cài đặt packages:**
```bash
flutter pub get
```

**Kết quả mong đợi:**
```
Running "flutter pub get" in book_management_app...
Resolving dependencies...
Got dependencies!
```

**✅ KIỂM TRA BƯỚC 2:**
- [ ] Đã sửa file `pubspec.yaml`
- [ ] Đã chạy `flutter pub get`
- [ ] Không có lỗi (thấy "Got dependencies!")

**❌ Nếu gặp lỗi:**
- **"Could not find package"** → Kiểm tra tên package, version
- **"Version solving failed"** → Thử version thấp hơn (ví dụ: `^7.0.0`)

---

### Bước 3: Tạo Cấu trúc Thư mục

**Mục đích:** Tổ chức code theo cấu trúc rõ ràng, dễ quản lý.

**3.1. Tạo các thư mục:**

**Cách 1: Dùng Terminal (Nhanh nhất)**
```bash
# Trong thư mục book_management_app
mkdir lib\models
mkdir lib\services
mkdir lib\providers
mkdir lib\blocs
mkdir lib\blocs\book
mkdir lib\screens
mkdir lib\widgets
```

**Cách 2: Dùng VS Code**
- Click phải vào thư mục `lib`
- Chọn "New Folder"
- Tạo từng thư mục một

**3.2. Cấu trúc thư mục cuối cùng:**

```
lib/
├── main.dart                    ← File chính (đã có sẵn)
├── models/                      ← Định nghĩa dữ liệu
│   └── book.dart
├── services/                    ← Gọi API
│   └── api_service.dart
├── providers/                   ← State toàn cục (Auth, Theme)
│   ├── auth_provider.dart
│   └── theme_provider.dart
├── blocs/                       ← State management phức tạp
│   └── book/
│       ├── book_event.dart
│       ├── book_state.dart
│       └── book_bloc.dart
├── screens/                     ← Các màn hình
│   ├── login_screen.dart
│   ├── home_screen.dart
│   ├── book_list_screen.dart
│   └── book_form_screen.dart
└── widgets/                     ← Widgets tái sử dụng
    └── book_card.dart
```

**Giải thích cấu trúc:**
- **models/**: Định nghĩa cấu trúc dữ liệu (Book class)
- **services/**: Logic gọi API (tách biệt khỏi UI)
- **providers/**: State toàn cục (Auth, Theme)
- **blocs/**: State management phức tạp (CRUD operations)
- **screens/**: Các màn hình của app
- **widgets/**: Widgets có thể tái sử dụng

**✅ KIỂM TRA BƯỚC 3:**
- [ ] Đã tạo tất cả các thư mục
- [ ] Cấu trúc giống như trên

**Bước tiếp theo:** Bắt đầu tạo các file code!

---

## 📦 PHẦN 3: XÂY DỰNG FLUTTER APP

> **⏱️ Thời gian:** 1.5-2 giờ
> 
> **🎯 Mục tiêu:** Tạo tất cả các file code cần thiết

**⚠️ LƯU Ý:** 
- Làm theo thứ tự từng bước
- Copy code chính xác (đặc biệt là dấu ngoặc, dấu chấm phẩy)
- Test sau mỗi bước (nếu có thể)

---

### Bước 1: Tạo Model (`models/book.dart`)

**Mục đích:** Định nghĩa cấu trúc dữ liệu Book trong Flutter (giống với Model trong .NET API).

**1.1. Tạo file `lib/models/book.dart`:**

**Cách tạo:**
- Click phải vào thư mục `lib/models`
- Chọn "New File"
- Đặt tên: `book.dart`

**1.2. Copy toàn bộ code sau vào file:**

```dart
import 'dart:convert';  // Để chuyển đổi JSON

// Class Book: Định nghĩa cấu trúc dữ liệu của một cuốn sách
class Book {
  // Các thuộc tính (properties) của Book
  final int id;                    // ID của sách
  final String title;              // Tiêu đề
  final String author;             // Tác giả
  final String isbn;               // Mã ISBN
  final DateTime publishedDate;    // Ngày xuất bản
  final int pageCount;             // Số trang
  final String? description;       // Mô tả (có thể null)
  final DateTime createdAt;        // Ngày tạo

  // Constructor: Tạo object Book từ các tham số
  Book({
    required this.id,              // required: Bắt buộc phải có
    required this.title,
    required this.author,
    required this.isbn,
    required this.publishedDate,
    required this.pageCount,
    this.description,              // Không có required: Có thể bỏ qua
    required this.createdAt,
  });

  // fromJson: Chuyển từ JSON (từ API) sang Dart Object
  // Ví dụ: {"id": 1, "title": "Flutter Guide"} → Book object
  factory Book.fromJson(Map<String, dynamic> json) {
    return Book(
      id: json['id'] as int,  // Lấy giá trị 'id' từ JSON, ép kiểu thành int
      title: json['title'] as String,
      author: json['author'] as String,
      isbn: json['isbn'] as String,
      // DateTime.parse: Chuyển chuỗi "2024-01-15T00:00:00Z" thành DateTime
      publishedDate: DateTime.parse(json['publishedDate'] as String),
      pageCount: json['pageCount'] as int,
      description: json['description'] as String?,  // Có thể null
      createdAt: DateTime.parse(json['createdAt'] as String),
    );
  }

  // toJson: Chuyển từ Dart Object sang JSON (để gửi lên API)
  // Ví dụ: Book object → {"id": 1, "title": "Flutter Guide"}
  Map<String, dynamic> toJson() {
    return {
      'id': id,
      'title': title,
      'author': author,
      'isbn': isbn,
      // toIso8601String: Chuyển DateTime thành chuỗi ISO format
      'publishedDate': publishedDate.toIso8601String(),
      'pageCount': pageCount,
      'description': description,
      'createdAt': createdAt.toIso8601String(),
    };
  }

  // copyWith: Tạo bản copy với một số thay đổi (dùng khi update)
  // Ví dụ: book.copyWith(title: "New Title") → Book mới với title mới
  Book copyWith({
    int? id,
    String? title,
    String? author,
    String? isbn,
    DateTime? publishedDate,
    int? pageCount,
    String? description,
    DateTime? createdAt,
  }) {
    return Book(
      // id ?? this.id: Nếu id không được truyền vào, dùng id cũ
      id: id ?? this.id,
      title: title ?? this.title,
      author: author ?? this.author,
      isbn: isbn ?? this.isbn,
      publishedDate: publishedDate ?? this.publishedDate,
      pageCount: pageCount ?? this.pageCount,
      description: description ?? this.description,
      createdAt: createdAt ?? this.createdAt,
    );
  }
}
```

**Giải thích các khái niệm:**

1. **`final`**: Giá trị không thể thay đổi sau khi khởi tạo (immutable)
2. **`String?`**: Có thể là String hoặc null (nullable)
3. **`required`**: Tham số bắt buộc khi tạo object
4. **`factory`**: Constructor đặc biệt, có thể trả về instance mới hoặc instance đã có
5. **`as int`**: Ép kiểu (cast) sang int
6. **`??`**: Toán tử null-coalescing, nếu bên trái null thì dùng bên phải

**✅ KIỂM TRA BƯỚC 1:**
- [ ] Đã tạo file `lib/models/book.dart`
- [ ] Đã copy code trên vào file
- [ ] Không có lỗi đỏ gạch chân (nếu dùng IDE)
- [ ] Code được format đẹp (dùng Format Document trong VS Code)

**❌ Nếu gặp lỗi:**
- **"Undefined name 'dart:convert'"** → Không cần import, đã có sẵn trong Dart SDK
- **Lỗi đỏ gạch chân** → Kiểm tra lại dấu ngoặc, dấu chấm phẩy
- **"Expected identifier"** → Kiểm tra lại cú pháp, có thể thiếu dấu `,` hoặc `;`

### Bước 2: Tạo API Service (`services/api_service.dart`)

```dart
import 'dart:io';
import 'package:flutter/foundation.dart';
import 'package:http/http.dart' as http;
import 'dart:convert';
import '../models/book.dart';

class ApiService {
  // Tự động chọn URL dựa trên platform
  static String get baseUrl {
    if (kIsWeb) {
      return 'http://localhost:5000/api';
    }
    
    // Android Emulator dùng 10.0.2.2 để trỏ về localhost của máy
    if (Platform.isAndroid) {
      return 'http://10.0.2.2:5000/api';
    }
    
    // iOS Simulator dùng localhost
    return 'http://localhost:5000/api';
  }

  static Map<String, String> get _headers => {
    'Content-Type': 'application/json; charset=UTF-8',
    'Accept': 'application/json',
  };

  // GET: Lấy danh sách sách
  static Future<List<Book>> getBooks() async {
    try {
      final url = Uri.parse('$baseUrl/books');
      final response = await http.get(url, headers: _headers).timeout(
        const Duration(seconds: 10),
      );

      if (response.statusCode == 200) {
        final List<dynamic> data = jsonDecode(response.body);
        return data.map((json) => Book.fromJson(json)).toList();
      } else {
        throw Exception('Failed to load books: ${response.statusCode}');
      }
    } on SocketException {
      throw Exception('No internet connection');
    } on TimeoutException {
      throw Exception('Request timeout');
    } catch (e) {
      throw Exception('Error: $e');
    }
  }

  // GET: Lấy 1 cuốn sách theo ID
  static Future<Book> getBook(int id) async {
    try {
      final url = Uri.parse('$baseUrl/books/$id');
      final response = await http.get(url, headers: _headers).timeout(
        const Duration(seconds: 10),
      );

      if (response.statusCode == 200) {
        return Book.fromJson(jsonDecode(response.body));
      } else if (response.statusCode == 404) {
        throw Exception('Book not found');
      } else {
        throw Exception('Failed to load book: ${response.statusCode}');
      }
    } catch (e) {
      throw Exception('Error: $e');
    }
  }

  // POST: Tạo sách mới
  static Future<Book> createBook(Book book) async {
    try {
      final url = Uri.parse('$baseUrl/books');
      final response = await http.post(
        url,
        headers: _headers,
        body: jsonEncode(book.toJson()),
      ).timeout(const Duration(seconds: 10));

      if (response.statusCode == 201) {
        return Book.fromJson(jsonDecode(response.body));
      } else {
        throw Exception('Failed to create book: ${response.statusCode}');
      }
    } catch (e) {
      throw Exception('Error: $e');
    }
  }

  // PUT: Cập nhật sách
  static Future<Book> updateBook(Book book) async {
    try {
      final url = Uri.parse('$baseUrl/books/${book.id}');
      final response = await http.put(
        url,
        headers: _headers,
        body: jsonEncode(book.toJson()),
      ).timeout(const Duration(seconds: 10));

      if (response.statusCode == 204 || response.statusCode == 200) {
        return book; // Server trả về NoContent, dùng book hiện tại
      } else {
        throw Exception('Failed to update book: ${response.statusCode}');
      }
    } catch (e) {
      throw Exception('Error: $e');
    }
  }

  // DELETE: Xóa sách
  static Future<void> deleteBook(int id) async {
    try {
      final url = Uri.parse('$baseUrl/books/$id');
      final response = await http.delete(url, headers: _headers).timeout(
        const Duration(seconds: 10),
      );

      if (response.statusCode != 204 && response.statusCode != 200) {
        throw Exception('Failed to delete book: ${response.statusCode}');
      }
    } catch (e) {
      throw Exception('Error: $e');
    }
  }
}
```

### Bước 3: Tạo Provider cho Authentication (`providers/auth_provider.dart`)

```dart
import 'package:flutter/foundation.dart';

class AuthProvider with ChangeNotifier {
  String? _token;
  String? _username;
  bool _isAuthenticated = false;

  String? get token => _token;
  String? get username => _username;
  bool get isAuthenticated => _isAuthenticated;

  // Đăng nhập (giả lập - trong thực tế sẽ gọi API)
  Future<bool> login(String username, String password) async {
    // Giả lập delay API call
    await Future.delayed(const Duration(seconds: 1));

    // Kiểm tra đơn giản (trong thực tế sẽ gọi API)
    if (username == 'admin' && password == '123456') {
      _token = 'fake_token_${DateTime.now().millisecondsSinceEpoch}';
      _username = username;
      _isAuthenticated = true;
      notifyListeners();
      return true;
    }

    return false;
  }

  // Đăng xuất
  void logout() {
    _token = null;
    _username = null;
    _isAuthenticated = false;
    notifyListeners();
  }
}
```

### Bước 4: Tạo Provider cho Theme (`providers/theme_provider.dart`)

```dart
import 'package:flutter/material.dart';

class ThemeProvider with ChangeNotifier {
  ThemeMode _themeMode = ThemeMode.light;

  ThemeMode get themeMode => _themeMode;
  bool get isDarkMode => _themeMode == ThemeMode.dark;

  void toggleTheme() {
    _themeMode = _themeMode == ThemeMode.light
        ? ThemeMode.dark
        : ThemeMode.light;
    notifyListeners();
  }

  void setTheme(ThemeMode mode) {
    _themeMode = mode;
    notifyListeners();
  }
}
```

### Bước 5: Tạo Bloc cho Book Management

**File: `blocs/book/book_event.dart`**

```dart
import 'package:equatable/equatable.dart';
import '../../models/book.dart';

abstract class BookEvent extends Equatable {
  const BookEvent();

  @override
  List<Object?> get props => [];
}

// Lấy danh sách sách
class LoadBooksEvent extends BookEvent {
  const LoadBooksEvent();
}

// Làm mới danh sách (pull to refresh)
class RefreshBooksEvent extends BookEvent {
  const RefreshBooksEvent();
}

// Tạo sách mới
class CreateBookEvent extends BookEvent {
  final Book book;
  const CreateBookEvent(this.book);

  @override
  List<Object?> get props => [book];
}

// Cập nhật sách
class UpdateBookEvent extends BookEvent {
  final Book book;
  const UpdateBookEvent(this.book);

  @override
  List<Object?> get props => [book];
}

// Xóa sách
class DeleteBookEvent extends BookEvent {
  final int bookId;
  const DeleteBookEvent(this.bookId);

  @override
  List<Object?> get props => [bookId];
}
```

**File: `blocs/book/book_state.dart`**

```dart
import 'package:equatable/equatable.dart';
import '../../models/book.dart';

abstract class BookState extends Equatable {
  const BookState();

  @override
  List<Object?> get props => [];
}

// Trạng thái ban đầu
class BookInitial extends BookState {
  const BookInitial();
}

// Đang tải dữ liệu
class BookLoading extends BookState {
  const BookLoading();
}

// Tải thành công
class BookLoaded extends BookState {
  final List<Book> books;
  const BookLoaded(this.books);

  @override
  List<Object?> get props => [books];
}

// Tải thất bại
class BookError extends BookState {
  final String message;
  const BookError(this.message);

  @override
  List<Object?> get props => [message];
}

// Đang tạo sách
class BookCreating extends BookState {
  const BookCreating();
}

// Tạo thành công
class BookCreated extends BookState {
  final Book book;
  const BookCreated(this.book);

  @override
  List<Object?> get props => [book];
}

// Đang cập nhật sách
class BookUpdating extends BookState {
  const BookUpdating();
}

// Cập nhật thành công
class BookUpdated extends BookState {
  final Book book;
  const BookUpdated(this.book);

  @override
  List<Object?> get props => [book];
}

// Đang xóa sách
class BookDeleting extends BookState {
  const BookDeleting();
}

// Xóa thành công
class BookDeleted extends BookState {
  final int bookId;
  const BookDeleted(this.bookId);

  @override
  List<Object?> get props => [bookId];
}
```

**File: `blocs/book/book_bloc.dart`**

```dart
import 'package:flutter_bloc/flutter_bloc.dart';
import '../../models/book.dart';
import '../../services/api_service.dart';
import 'book_event.dart';
import 'book_state.dart';

class BookBloc extends Bloc<BookEvent, BookState> {
  BookBloc() : super(const BookInitial()) {
    // Đăng ký xử lý các events
    on<LoadBooksEvent>(_onLoadBooks);
    on<RefreshBooksEvent>(_onRefreshBooks);
    on<CreateBookEvent>(_onCreateBook);
    on<UpdateBookEvent>(_onUpdateBook);
    on<DeleteBookEvent>(_onDeleteBook);
  }

  // Xử lý Load Books
  Future<void> _onLoadBooks(
    LoadBooksEvent event,
    Emit<BookState> emit,
  ) async {
    emit(const BookLoading());
    try {
      final books = await ApiService.getBooks();
      emit(BookLoaded(books));
    } catch (e) {
      emit(BookError(e.toString()));
    }
  }

  // Xử lý Refresh Books (pull to refresh)
  Future<void> _onRefreshBooks(
    RefreshBooksEvent event,
    Emit<BookState> emit,
  ) async {
    // Giữ nguyên state hiện tại (không hiển thị loading)
    try {
      final books = await ApiService.getBooks();
      emit(BookLoaded(books));
    } catch (e) {
      emit(BookError(e.toString()));
    }
  }

  // Xử lý Create Book
  Future<void> _onCreateBook(
    CreateBookEvent event,
    Emit<BookState> emit,
  ) async {
    emit(const BookCreating());
    try {
      final createdBook = await ApiService.createBook(event.book);
      emit(BookCreated(createdBook));
      
      // Sau khi tạo thành công, load lại danh sách
      final books = await ApiService.getBooks();
      emit(BookLoaded(books));
    } catch (e) {
      emit(BookError(e.toString()));
    }
  }

  // Xử lý Update Book
  Future<void> _onUpdateBook(
    UpdateBookEvent event,
    Emit<BookState> emit,
  ) async {
    emit(const BookUpdating());
    try {
      await ApiService.updateBook(event.book);
      emit(BookUpdated(event.book));
      
      // Sau khi cập nhật thành công, load lại danh sách
      final books = await ApiService.getBooks();
      emit(BookLoaded(books));
    } catch (e) {
      emit(BookError(e.toString()));
    }
  }

  // Xử lý Delete Book
  Future<void> _onDeleteBook(
    DeleteBookEvent event,
    Emit<BookState> emit,
  ) async {
    emit(const BookDeleting());
    try {
      await ApiService.deleteBook(event.bookId);
      emit(BookDeleted(event.bookId));
      
      // Sau khi xóa thành công, load lại danh sách
      final books = await ApiService.getBooks();
      emit(BookLoaded(books));
    } catch (e) {
      emit(BookError(e.toString()));
    }
  }
}
```

### Bước 6: Tạo UI Screens

**File: `screens/login_screen.dart`**

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../providers/auth_provider.dart';
import 'home_screen.dart';

class LoginScreen extends StatefulWidget {
  const LoginScreen({super.key});

  @override
  State<LoginScreen> createState() => _LoginScreenState();
}

class _LoginScreenState extends State<LoginScreen> {
  final _usernameController = TextEditingController();
  final _passwordController = TextEditingController();
  bool _isLoading = false;
  String? _errorMessage;

  @override
  void dispose() {
    _usernameController.dispose();
    _passwordController.dispose();
    super.dispose();
  }

  Future<void> _handleLogin() async {
    setState(() {
      _isLoading = true;
      _errorMessage = null;
    });

    final authProvider = context.read<AuthProvider>();
    final success = await authProvider.login(
      _usernameController.text,
      _passwordController.text,
    );

    if (!mounted) return;

    setState(() {
      _isLoading = false;
    });

    if (success) {
      Navigator.of(context).pushReplacement(
        MaterialPageRoute(builder: (_) => const HomeScreen()),
      );
    } else {
      setState(() {
        _errorMessage = 'Sai tên đăng nhập hoặc mật khẩu';
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Đăng Nhập'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Icon(
              Icons.book,
              size: 80,
              color: Colors.blue,
            ),
            const SizedBox(height: 40),
            TextField(
              controller: _usernameController,
              decoration: const InputDecoration(
                labelText: 'Tên đăng nhập',
                hintText: 'admin',
                border: OutlineInputBorder(),
                prefixIcon: Icon(Icons.person),
              ),
            ),
            const SizedBox(height: 16),
            TextField(
              controller: _passwordController,
              obscureText: true,
              decoration: const InputDecoration(
                labelText: 'Mật khẩu',
                hintText: '123456',
                border: OutlineInputBorder(),
                prefixIcon: Icon(Icons.lock),
              ),
            ),
            if (_errorMessage != null) ...[
              const SizedBox(height: 16),
              Text(
                _errorMessage!,
                style: const TextStyle(color: Colors.red),
              ),
            ],
            const SizedBox(height: 24),
            SizedBox(
              width: double.infinity,
              height: 50,
              child: ElevatedButton(
                onPressed: _isLoading ? null : _handleLogin,
                child: _isLoading
                    ? const CircularProgressIndicator()
                    : const Text('Đăng Nhập'),
              ),
            ),
            const SizedBox(height: 16),
            const Text(
              'Thông tin đăng nhập:\nUsername: admin\nPassword: 123456',
              textAlign: TextAlign.center,
              style: TextStyle(color: Colors.grey),
            ),
          ],
        ),
      ),
    );
  }
}
```

**File: `screens/home_screen.dart`**

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../providers/auth_provider.dart';
import '../providers/theme_provider.dart';
import 'book_list_screen.dart';

class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    final authProvider = context.watch<AuthProvider>();
    final themeProvider = context.watch<ThemeProvider>();

    return Scaffold(
      appBar: AppBar(
        title: const Text('Quản Lý Sách'),
        actions: [
          // Nút chuyển theme
          IconButton(
            icon: Icon(
              themeProvider.isDarkMode
                  ? Icons.light_mode
                  : Icons.dark_mode,
            ),
            onPressed: () => themeProvider.toggleTheme(),
            tooltip: 'Chuyển giao diện',
          ),
          // Menu đăng xuất
          PopupMenuButton(
            itemBuilder: (context) => [
              PopupMenuItem(
                child: Text('Xin chào: ${authProvider.username}'),
                enabled: false,
              ),
              const PopupMenuDivider(),
              PopupMenuItem(
                child: const Text('Đăng xuất'),
                onTap: () {
                  Future.delayed(
                    Duration.zero,
                    () {
                      authProvider.logout();
                      Navigator.of(context).pushNamedAndRemoveUntil(
                        '/login',
                        (route) => false,
                      );
                    },
                  );
                },
              ),
            ],
          ),
        ],
      ),
      body: const BookListScreen(),
    );
  }
}
```

**File: `screens/book_list_screen.dart`**

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import '../blocs/book/book_bloc.dart';
import '../blocs/book/book_event.dart';
import '../blocs/book/book_state.dart';
import '../models/book.dart';
import '../widgets/book_card.dart';
import 'book_form_screen.dart';

class BookListScreen extends StatelessWidget {
  const BookListScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      create: (context) => BookBloc()..add(const LoadBooksEvent()),
      child: const BookListView(),
    );
  }
}

class BookListView extends StatelessWidget {
  const BookListView({super.key});

  @override
  Widget build(BuildContext context) {
    return BlocConsumer<BookBloc, BookState>(
      listener: (context, state) {
        // Xử lý các sự kiện một lần (SnackBar, Dialog, Navigate)
        if (state is BookError) {
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(
              content: Text('Lỗi: ${state.message}'),
              backgroundColor: Colors.red,
            ),
          );
        } else if (state is BookCreated) {
          ScaffoldMessenger.of(context).showSnackBar(
            const SnackBar(
              content: Text('Thêm sách thành công!'),
              backgroundColor: Colors.green,
            ),
          );
        } else if (state is BookUpdated) {
          ScaffoldMessenger.of(context).showSnackBar(
            const SnackBar(
              content: Text('Cập nhật sách thành công!'),
              backgroundColor: Colors.green,
            ),
          );
        } else if (state is BookDeleted) {
          ScaffoldMessenger.of(context).showSnackBar(
            const SnackBar(
              content: Text('Xóa sách thành công!'),
              backgroundColor: Colors.green,
            ),
          );
        }
      },
      builder: (context, state) {
        // Vẽ UI dựa trên state
        if (state is BookLoading) {
          return const Center(child: CircularProgressIndicator());
        }

        if (state is BookError) {
          return Center(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                const Icon(Icons.error_outline, size: 64, color: Colors.red),
                const SizedBox(height: 16),
                Text('Lỗi: ${state.message}'),
                const SizedBox(height: 16),
                ElevatedButton(
                  onPressed: () {
                    context.read<BookBloc>().add(const LoadBooksEvent());
                  },
                  child: const Text('Thử lại'),
                ),
              ],
            ),
          );
        }

        if (state is BookLoaded) {
          final books = state.books;

          if (books.isEmpty) {
            return Center(
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  const Icon(Icons.book_outlined, size: 64, color: Colors.grey),
                  const SizedBox(height: 16),
                  const Text('Chưa có sách nào'),
                  const SizedBox(height: 16),
                  ElevatedButton.icon(
                    onPressed: () {
                      Navigator.of(context).push(
                        MaterialPageRoute(
                          builder: (_) => const BookFormScreen(),
                        ),
                      );
                    },
                    icon: const Icon(Icons.add),
                    label: const Text('Thêm sách đầu tiên'),
                  ),
                ],
              ),
            );
          }

          return RefreshIndicator(
            onRefresh: () async {
              context.read<BookBloc>().add(const RefreshBooksEvent());
            },
            child: ListView.builder(
              padding: const EdgeInsets.all(8),
              itemCount: books.length,
              itemBuilder: (context, index) {
                final book = books[index];
                return BookCard(book: book);
              },
            ),
          );
        }

        // BookInitial hoặc các state khác
        return const Center(child: Text('Khởi tạo...'));
      },
    );
  }
}
```

**File: `screens/book_form_screen.dart`**

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:intl/intl.dart';
import '../blocs/book/book_bloc.dart';
import '../blocs/book/book_event.dart';
import '../models/book.dart';

class BookFormScreen extends StatefulWidget {
  final Book? book; // Nếu có thì là edit, không có thì là create

  const BookFormScreen({super.key, this.book});

  @override
  State<BookFormScreen> createState() => _BookFormScreenState();
}

class _BookFormScreenState extends State<BookFormScreen> {
  final _formKey = GlobalKey<FormState>();
  final _titleController = TextEditingController();
  final _authorController = TextEditingController();
  final _isbnController = TextEditingController();
  final _pageCountController = TextEditingController();
  final _descriptionController = TextEditingController();
  DateTime _selectedDate = DateTime.now();
  bool _isLoading = false;

  @override
  void initState() {
    super.initState();
    // Nếu là edit mode, điền dữ liệu vào form
    if (widget.book != null) {
      _titleController.text = widget.book!.title;
      _authorController.text = widget.book!.author;
      _isbnController.text = widget.book!.isbn;
      _pageCountController.text = widget.book!.pageCount.toString();
      _descriptionController.text = widget.book!.description ?? '';
      _selectedDate = widget.book!.publishedDate;
    }
  }

  @override
  void dispose() {
    _titleController.dispose();
    _authorController.dispose();
    _isbnController.dispose();
    _pageCountController.dispose();
    _descriptionController.dispose();
    super.dispose();
  }

  Future<void> _selectDate() async {
    final picked = await showDatePicker(
      context: context,
      initialDate: _selectedDate,
      firstDate: DateTime(1900),
      lastDate: DateTime.now(),
    );
    if (picked != null && picked != _selectedDate) {
      setState(() {
        _selectedDate = picked;
      });
    }
  }

  void _handleSubmit() {
    if (_formKey.currentState!.validate()) {
      setState(() {
        _isLoading = true;
      });

      final book = Book(
        id: widget.book?.id ?? 0,
        title: _titleController.text,
        author: _authorController.text,
        isbn: _isbnController.text,
        publishedDate: _selectedDate,
        pageCount: int.parse(_pageCountController.text),
        description: _descriptionController.text.isEmpty
            ? null
            : _descriptionController.text,
        createdAt: widget.book?.createdAt ?? DateTime.now(),
      );

      if (widget.book == null) {
        // Create
        context.read<BookBloc>().add(CreateBookEvent(book));
      } else {
        // Update
        context.read<BookBloc>().add(UpdateBookEvent(book));
      }

      // Đợi một chút để Bloc xử lý, sau đó quay lại
      Future.delayed(const Duration(milliseconds: 500), () {
        if (mounted) {
          Navigator.of(context).pop();
        }
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.book == null ? 'Thêm Sách' : 'Sửa Sách'),
      ),
      body: BlocListener<BookBloc, BookState>(
        listener: (context, state) {
          if (state is BookError) {
            setState(() {
              _isLoading = false;
            });
            ScaffoldMessenger.of(context).showSnackBar(
              SnackBar(
                content: Text('Lỗi: ${state.message}'),
                backgroundColor: Colors.red,
              ),
            );
          }
        },
        child: SingleChildScrollView(
          padding: const EdgeInsets.all(16),
          child: Form(
            key: _formKey,
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: [
                TextFormField(
                  controller: _titleController,
                  decoration: const InputDecoration(
                    labelText: 'Tiêu đề *',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Vui lòng nhập tiêu đề';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 16),
                TextFormField(
                  controller: _authorController,
                  decoration: const InputDecoration(
                    labelText: 'Tác giả *',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Vui lòng nhập tác giả';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 16),
                TextFormField(
                  controller: _isbnController,
                  decoration: const InputDecoration(
                    labelText: 'ISBN *',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Vui lòng nhập ISBN';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 16),
                TextFormField(
                  controller: _pageCountController,
                  decoration: const InputDecoration(
                    labelText: 'Số trang *',
                    border: OutlineInputBorder(),
                  ),
                  keyboardType: TextInputType.number,
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Vui lòng nhập số trang';
                    }
                    if (int.tryParse(value) == null) {
                      return 'Vui lòng nhập số hợp lệ';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 16),
                InkWell(
                  onTap: _selectDate,
                  child: InputDecorator(
                    decoration: const InputDecoration(
                      labelText: 'Ngày xuất bản *',
                      border: OutlineInputBorder(),
                      suffixIcon: Icon(Icons.calendar_today),
                    ),
                    child: Text(
                      DateFormat('dd/MM/yyyy').format(_selectedDate),
                    ),
                  ),
                ),
                const SizedBox(height: 16),
                TextFormField(
                  controller: _descriptionController,
                  decoration: const InputDecoration(
                    labelText: 'Mô tả',
                    border: OutlineInputBorder(),
                  ),
                  maxLines: 3,
                ),
                const SizedBox(height: 24),
                SizedBox(
                  height: 50,
                  child: ElevatedButton(
                    onPressed: _isLoading ? null : _handleSubmit,
                    child: _isLoading
                        ? const CircularProgressIndicator()
                        : Text(widget.book == null ? 'Thêm Sách' : 'Cập Nhật'),
                  ),
                ),
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

**File: `widgets/book_card.dart`**

```dart
import 'package:flutter/material.dart';
import 'package:intl/intl.dart';
import '../models/book.dart';
import '../screens/book_form_screen.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import '../blocs/book/book_bloc.dart';
import '../blocs/book/book_event.dart';

class BookCard extends StatelessWidget {
  final Book book;

  const BookCard({super.key, required this.book});

  void _showDeleteDialog(BuildContext context) {
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: const Text('Xác nhận xóa'),
        content: Text('Bạn có chắc muốn xóa sách "${book.title}"?'),
        actions: [
          TextButton(
            onPressed: () => Navigator.of(context).pop(),
            child: const Text('Hủy'),
          ),
          TextButton(
            onPressed: () {
              context.read<BookBloc>().add(DeleteBookEvent(book.id));
              Navigator.of(context).pop();
            },
            child: const Text('Xóa', style: TextStyle(color: Colors.red)),
          ),
        ],
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Card(
      margin: const EdgeInsets.symmetric(horizontal: 8, vertical: 4),
      child: ListTile(
        leading: const Icon(Icons.book, size: 40),
        title: Text(
          book.title,
          style: const TextStyle(fontWeight: FontWeight.bold),
        ),
        subtitle: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text('Tác giả: ${book.author}'),
            Text('ISBN: ${book.isbn}'),
            Text(
              'Xuất bản: ${DateFormat('dd/MM/yyyy').format(book.publishedDate)}',
            ),
            Text('Số trang: ${book.pageCount}'),
          ],
        ),
        isThreeLine: true,
        trailing: PopupMenuButton(
          itemBuilder: (context) => [
            PopupMenuItem(
              child: const Row(
                children: [
                  Icon(Icons.edit, size: 20),
                  SizedBox(width: 8),
                  Text('Sửa'),
                ],
              ),
              onTap: () {
                Future.delayed(
                  Duration.zero,
                  () {
                    Navigator.of(context).push(
                      MaterialPageRoute(
                        builder: (_) => BookFormScreen(book: book),
                      ),
                    );
                  },
                );
              },
            ),
            PopupMenuItem(
              child: const Row(
                children: [
                  Icon(Icons.delete, size: 20, color: Colors.red),
                  SizedBox(width: 8),
                  Text('Xóa', style: TextStyle(color: Colors.red)),
                ],
              ),
              onTap: () {
                Future.delayed(
                  Duration.zero,
                  () => _showDeleteDialog(context),
                );
              },
            ),
          ],
        ),
      ),
    );
  }
}
```

### Bước 7: Cấu hình Main App (`main.dart`)

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:provider/provider.dart';
import 'providers/auth_provider.dart';
import 'providers/theme_provider.dart';
import 'screens/login_screen.dart';
import 'screens/home_screen.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MultiProvider(
      providers: [
        // Provider cho Authentication (toàn cục)
        ChangeNotifierProvider(create: (_) => AuthProvider()),
        // Provider cho Theme (toàn cục)
        ChangeNotifierProvider(create: (_) => ThemeProvider()),
      ],
      child: Consumer<ThemeProvider>(
        builder: (context, themeProvider, child) {
          return MaterialApp(
            title: 'Quản Lý Sách',
            debugShowCheckedModeBanner: false,
            theme: ThemeData.light(),
            darkTheme: ThemeData.dark(),
            themeMode: themeProvider.themeMode,
            // Routes
            initialRoute: '/login',
            routes: {
              '/login': (context) => const LoginScreen(),
              '/home': (context) => const HomeScreen(),
            },
            // BlocObserver để debug (tùy chọn)
            builder: (context, child) {
              return BlocObserverProvider(
                child: child!,
              );
            },
          );
        },
      ),
    );
  }
}

// BlocObserver để log các events và states (hữu ích khi debug)
class BlocObserverProvider extends StatelessWidget {
  final Widget child;

  const BlocObserverProvider({super.key, required this.child});

  @override
  Widget build(BuildContext context) {
    return child;
  }
}

// Uncomment để bật BlocObserver (log tất cả events/states)
/*
class SimpleBlocObserver extends BlocObserver {
  @override
  void onChange(BlocBase bloc, Change change) {
    super.onChange(bloc, change);
    print('${bloc.runtimeType} $change');
  }

  @override
  void onError(BlocBase bloc, Object error, StackTrace stackTrace) {
    print('${bloc.runtimeType} $error');
    super.onError(bloc, error, stackTrace);
  }
}

// Thêm vào main():
void main() {
  Bloc.observer = SimpleBlocObserver();
  runApp(const MyApp());
}
*/
```

---

## 🧪 PHẦN 4: TESTING & DEBUGGING

### 📮 HƯỚNG DẪN KIỂM THỬ API VỚI POSTMAN (CHI TIẾT)

**Postman là gì?**
- Postman là công cụ giúp test API **không cần viết code**
- Giống như "trình duyệt" nhưng dành cho API
- Cho phép gửi request và xem response một cách trực quan

**Cài đặt Postman:**
1. Tải về: https://www.postman.com/downloads/
2. Đăng ký tài khoản miễn phí (hoặc dùng không cần đăng ký)
3. Mở Postman

---

### 🎯 BƯỚC 1: CHUẨN BỊ

**1.1. Khởi động .NET Web API:**
```bash
cd BookManagementAPI
dotnet run
```

**Kết quả:** API chạy tại `http://localhost:5000` hoặc `https://localhost:5001`

**1.2. Kiểm tra API đang chạy:**
- Mở browser: `http://localhost:5000/swagger` (nếu có Swagger)
- Hoặc: `http://localhost:5000/api/books` (sẽ thấy JSON hoặc lỗi CORS - bình thường)

---

### 📋 BƯỚC 2: TEST GET - LẤY DANH SÁCH SÁCH

**Mục đích:** Kiểm tra API có trả về danh sách sách không (sau khi seeding).

**Các bước trong Postman:**

1. **Tạo Request mới:**
   - Click nút **"New"** → Chọn **"HTTP Request"**
   - Đặt tên: `GET All Books`

2. **Cấu hình Request:**
   - **Method:** Chọn `GET` (dropdown bên trái)
   - **URL:** Nhập `http://localhost:5000/api/books`
   - **Headers:** Không cần (GET không cần header đặc biệt)

3. **Gửi Request:**
   - Click nút **"Send"** (màu xanh)
   - Đợi response

4. **Kiểm tra Response:**
   - **Status:** Phải là `200 OK` (màu xanh)
   - **Body:** Phải thấy JSON array chứa các cuốn sách đã seed

**Response mẫu (200 OK):**
```json
[
  {
    "id": 1,
    "title": "Flutter Complete Guide",
    "author": "John Doe",
    "isbn": "978-0123456789",
    "publishedDate": "2023-01-15T00:00:00Z",
    "pageCount": 500,
    "description": "Hướng dẫn toàn diện về Flutter framework",
    "createdAt": "2024-01-15T10:00:00Z"
  },
  {
    "id": 2,
    "title": "C# Programming Mastery",
    "author": "Jane Smith",
    "isbn": "978-0987654321",
    "publishedDate": "2023-03-20T00:00:00Z",
    "pageCount": 650,
    "description": "Từ cơ bản đến nâng cao về C# và .NET",
    "createdAt": "2024-01-15T10:00:00Z"
  }
  // ... các sách khác
]
```

**Nếu lỗi:**
- **404 Not Found:** Kiểm tra URL có đúng không
- **500 Internal Server Error:** Kiểm tra database đã được tạo chưa
- **CORS Error:** Bình thường khi test từ browser, Postman không bị CORS

---

### 📝 BƯỚC 3: TEST GET - LẤY 1 CUỐN SÁCH THEO ID

**Mục đích:** Kiểm tra API có trả về đúng sách theo ID không.

**Các bước:**

1. **Tạo Request mới:** `GET Book By ID`

2. **Cấu hình:**
   - **Method:** `GET`
   - **URL:** `http://localhost:5000/api/books/1` (ID = 1)
   - **Headers:** Không cần

3. **Gửi và kiểm tra:**
   - **Status:** `200 OK`
   - **Body:** JSON object của 1 cuốn sách

**Response mẫu (200 OK):**
```json
{
  "id": 1,
  "title": "Flutter Complete Guide",
  "author": "John Doe",
  "isbn": "978-0123456789",
  "publishedDate": "2023-01-15T00:00:00Z",
  "pageCount": 500,
  "description": "Hướng dẫn toàn diện về Flutter framework",
  "createdAt": "2024-01-15T10:00:00Z"
}
```

**Test trường hợp lỗi:**
- **URL:** `http://localhost:5000/api/books/999` (ID không tồn tại)
- **Expected:** Status `404 Not Found`

---

### ➕ BƯỚC 4: TEST POST - TẠO SÁCH MỚI

**Mục đích:** Kiểm tra API có tạo sách mới thành công không.

**Các bước:**

1. **Tạo Request mới:** `POST Create Book`

2. **Cấu hình:**
   - **Method:** Chọn `POST`
   - **URL:** `http://localhost:5000/api/books`

3. **Thiết lập Headers:**
   - Tab **"Headers"**
   - Thêm header:
     - **Key:** `Content-Type`
     - **Value:** `application/json`
   - (Postman có thể tự thêm, nhưng nên kiểm tra)

4. **Thiết lập Body:**
   - Tab **"Body"**
   - Chọn **"raw"**
   - Dropdown bên phải: Chọn **"JSON"**
   - Nhập JSON:

```json
{
  "title": "Dart Programming Language",
  "author": "Alice Developer",
  "isbn": "978-1122334455",
  "publishedDate": "2024-01-20T00:00:00Z",
  "pageCount": 350,
  "description": "Học Dart từ cơ bản đến nâng cao"
}
```

**Lưu ý:** Không cần gửi `id` và `createdAt` (server tự tạo).

5. **Gửi Request:**
   - Click **"Send"**

6. **Kiểm tra Response:**
   - **Status:** Phải là `201 Created` (màu xanh lá)
   - **Body:** JSON object của sách vừa tạo (có `id` mới)

**Response mẫu (201 Created):**
```json
{
  "id": 6,
  "title": "Dart Programming Language",
  "author": "Alice Developer",
  "isbn": "978-1122334455",
  "publishedDate": "2024-01-20T00:00:00Z",
  "pageCount": 350,
  "description": "Học Dart từ cơ bản đến nâng cao",
  "createdAt": "2024-01-20T12:00:00Z"
}
```

**Test validation (nếu có):**
- Gửi request thiếu field bắt buộc (ví dụ: không có `title`)
- **Expected:** Status `400 Bad Request`

---

### ✏️ BƯỚC 5: TEST PUT - CẬP NHẬT SÁCH

**Mục đích:** Kiểm tra API có cập nhật sách thành công không.

**Các bước:**

1. **Tạo Request mới:** `PUT Update Book`

2. **Cấu hình:**
   - **Method:** `PUT`
   - **URL:** `http://localhost:5000/api/books/1` (ID = 1)

3. **Headers:**
   - **Content-Type:** `application/json`

4. **Body (JSON):**
```json
{
  "id": 1,
  "title": "Flutter Complete Guide - Updated Edition",
  "author": "John Doe",
  "isbn": "978-0123456789",
  "publishedDate": "2023-01-15T00:00:00Z",
  "pageCount": 550,
  "description": "Hướng dẫn toàn diện về Flutter framework - Phiên bản cập nhật",
  "createdAt": "2024-01-15T10:00:00Z"
}
```

**Lưu ý:** Phải gửi **đầy đủ** tất cả fields (kể cả `id` và `createdAt`).

5. **Gửi và kiểm tra:**
   - **Status:** `204 No Content` hoặc `200 OK`
   - **Body:** Có thể rỗng (204) hoặc trả về object đã update (200)

**Verify:** Gửi lại GET request để xem sách đã được cập nhật chưa.

---

### 🗑️ BƯỚC 6: TEST DELETE - XÓA SÁCH

**Mục đích:** Kiểm tra API có xóa sách thành công không.

**Các bước:**

1. **Tạo Request mới:** `DELETE Book`

2. **Cấu hình:**
   - **Method:** `DELETE`
   - **URL:** `http://localhost:5000/api/books/1` (ID = 1)
   - **Headers:** Không cần
   - **Body:** Không cần

3. **Gửi và kiểm tra:**
   - **Status:** `204 No Content` (thành công)
   - **Body:** Rỗng

**Verify:** Gửi lại GET request với ID vừa xóa → Phải trả về `404 Not Found`.

---

### 📚 BƯỚC 7: TỔ CHỨC REQUESTS TRONG COLLECTION

**Collection là gì?**
- Giống như "thư mục" chứa nhiều requests
- Giúp tổ chức và quản lý requests dễ dàng
- Có thể export/import để chia sẻ

**Tạo Collection:**

1. **Tạo Collection:**
   - Click **"New"** → **"Collection"**
   - Đặt tên: `Book Management API`

2. **Thêm Requests vào Collection:**
   - Kéo thả các requests đã tạo vào collection
   - Hoặc: Tạo request mới → Chọn collection khi lưu

3. **Sắp xếp:**
   - GET All Books
   - GET Book By ID
   - POST Create Book
   - PUT Update Book
   - DELETE Book

**Lợi ích:**
- ✅ Dễ tìm kiếm
- ✅ Có thể chạy tất cả requests cùng lúc (Run Collection)
- ✅ Có thể set biến môi trường (Environment Variables)

---

### 🔧 BƯỚC 8: SỬ DỤNG ENVIRONMENT VARIABLES (NÂNG CAO)

**Environment Variables là gì?**
- Biến để lưu giá trị dùng chung (như base URL)
- Tránh phải sửa URL ở nhiều nơi

**Cách dùng:**

1. **Tạo Environment:**
   - Click icon **"Environments"** (bên trái)
   - Click **"+"** để tạo mới
   - Đặt tên: `Local Development`

2. **Thêm biến:**
   - **Variable:** `base_url`
   - **Initial Value:** `http://localhost:5000`
   - **Current Value:** `http://localhost:5000`

3. **Sử dụng trong Request:**
   - URL: `{{base_url}}/api/books`
   - Postman sẽ thay `{{base_url}}` bằng giá trị thực tế

**Lợi ích:**
- ✅ Dễ chuyển đổi giữa môi trường (Local, Dev, Production)
- ✅ Không cần sửa từng request

---

### 📊 BƯỚC 9: KIỂM TRA RESPONSE TIME

**Response Time là gì?**
- Thời gian từ khi gửi request đến khi nhận response
- Giúp đánh giá hiệu suất API

**Cách xem:**
- Sau khi gửi request, xem tab **"Time"** trong response
- Hoặc xem ở góc dưới bên phải (ví dụ: `123 ms`)

**Benchmark:**
- ✅ < 100ms: Rất nhanh
- ✅ 100-500ms: Tốt
- ⚠️ 500-1000ms: Chấp nhận được
- ❌ > 1000ms: Cần tối ưu

---

### 🐛 BƯỚC 10: XỬ LÝ CÁC LỖI THƯỜNG GẶP

**1. Lỗi 500 Internal Server Error:**
- **Nguyên nhân:** Database chưa được tạo hoặc migration chưa chạy
- **Giải pháp:** 
  ```bash
  dotnet ef database update
  ```

**2. Lỗi 400 Bad Request:**
- **Nguyên nhân:** JSON format sai hoặc thiếu field bắt buộc
- **Giải pháp:** Kiểm tra lại JSON body, đảm bảo đúng format

**3. Lỗi 404 Not Found:**
- **Nguyên nhân:** URL sai hoặc ID không tồn tại
- **Giải pháp:** Kiểm tra lại URL và ID

**4. Lỗi Connection Refused:**
- **Nguyên nhân:** API chưa chạy hoặc port sai
- **Giải pháp:** 
  ```bash
  dotnet run
  ```
  Kiểm tra port trong console output

**5. Lỗi CORS (khi test từ browser):**
- **Nguyên nhân:** Browser chặn request từ origin khác
- **Giải pháp:** Postman không bị CORS, hoặc sửa CORS policy trong `Program.cs`

---

### ✅ CHECKLIST KIỂM THỬ

Trước khi tích hợp với Flutter, đảm bảo đã test:

- [ ] GET All Books → 200 OK, trả về danh sách
- [ ] GET Book By ID (có tồn tại) → 200 OK
- [ ] GET Book By ID (không tồn tại) → 404 Not Found
- [ ] POST Create Book → 201 Created
- [ ] POST Create Book (thiếu field) → 400 Bad Request
- [ ] PUT Update Book → 204/200 OK
- [ ] PUT Update Book (ID không khớp) → 400 Bad Request
- [ ] DELETE Book → 204 No Content
- [ ] DELETE Book (không tồn tại) → 404 Not Found
- [ ] Response time < 500ms

---

### 🎯 TÓM TẮT QUY TRÌNH TEST

```
1. Khởi động API (dotnet run)
2. Test GET để xem dữ liệu seed
3. Test POST để tạo mới
4. Test GET lại để verify
5. Test PUT để cập nhật
6. Test DELETE để xóa
7. Test các trường hợp lỗi
8. Verify response time
9. ✅ Sẵn sàng tích hợp Flutter!
```

### Debug Flutter App

**Kiểm tra kết nối API:**

1. **Android Emulator:** Dùng `10.0.2.2:5000`
2. **iOS Simulator:** Dùng `localhost:5000`
3. **Web:** Dùng `localhost:5000`

**Xem logs Bloc:**

Uncomment `SimpleBlocObserver` trong `main.dart` để xem tất cả events và states.

**Xử lý lỗi CORS:**

Nếu gặp lỗi CORS khi test trên web, sửa `Program.cs` trong .NET API:

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowFlutterApp",
        policy =>
        {
            policy.AllowAnyOrigin()  // Cho phép mọi origin (chỉ dùng khi dev)
                  .AllowAnyHeader()
                  .AllowAnyMethod();
        });
});
```

---

## 🎓 PHẦN 5: PHÂN TÍCH KIẾN TRÚC

### Tại sao dùng Bloc cho Book Management?

1. **Logic phức tạp:** CRUD operations có nhiều trạng thái (Loading, Success, Error)
2. **Theo dõi được:** Biết chính xác event nào gây ra state nào
3. **Dễ test:** Có thể test logic độc lập với UI
4. **Scalable:** Dễ mở rộng thêm features (search, filter, pagination)

### Tại sao dùng Provider cho Auth & Theme?

1. **State toàn cục:** Cần truy cập từ nhiều màn hình
2. **Logic đơn giản:** Chỉ cần get/set, không cần event phức tạp
3. **Nhẹ:** Provider nhẹ hơn Bloc cho use case đơn giản

### Luồng dữ liệu (Data Flow)

```
[UI Screen]
    ↓ (User Action)
[Bloc Event] (hoặc Provider method)
    ↓
[API Service]
    ↓
[.NET Web API]
    ↓ (Response)
[Bloc State] (hoặc Provider notifyListeners)
    ↓
[UI Rebuild]
```

### So sánh Bloc vs Provider

| Đặc điểm | Bloc | Provider |
|---------|------|----------|
| **Phức tạp** | Cao (Event → State) | Thấp (Method → notifyListeners) |
| **Use case** | Business logic phức tạp | State toàn cục đơn giản |
| **Debug** | Rất tốt (biết event nào) | Tốt (biết method nào) |
| **Learning curve** | Cao hơn | Thấp hơn |
| **Ví dụ** | CRUD, Search, Filter | Auth, Theme, Settings |

---

## 🚀 PHẦN 6: MỞ RỘNG (OPTIONAL)

### Thêm tính năng Search

**Event:**
```dart
class SearchBooksEvent extends BookEvent {
  final String query;
  const SearchBooksEvent(this.query);
}
```

**State:**
```dart
class BookSearchLoaded extends BookState {
  final List<Book> books;
  const BookSearchLoaded(this.books);
}
```

**Bloc Handler:**
```dart
on<SearchBooksEvent>(_onSearchBooks);

Future<void> _onSearchBooks(
  SearchBooksEvent event,
  Emit<BookState> emit,
) async {
  emit(const BookLoading());
  try {
    final allBooks = await ApiService.getBooks();
    final filtered = allBooks.where((book) =>
      book.title.toLowerCase().contains(event.query.toLowerCase()) ||
      book.author.toLowerCase().contains(event.query.toLowerCase())
    ).toList();
    emit(BookSearchLoaded(filtered));
  } catch (e) {
    emit(BookError(e.toString()));
  }
}
```

### Thêm Pagination

Thêm parameters vào API service và Bloc để hỗ trợ phân trang.

### Thêm Authentication thật với JWT

Thay thế `AuthProvider` giả lập bằng API call thật, lưu token vào SharedPreferences.

---

## 🚀 PHẦN CUỐI: CHẠY APP VÀ KIỂM TRA

> **⏱️ Thời gian:** 15-30 phút
> 
> **🎯 Mục tiêu:** Chạy app Flutter và kết nối với .NET API

---

### Bước 1: Chuẩn bị chạy App

**1.1. Đảm bảo .NET API đang chạy:**

Mở Terminal 1 (cho API):
```bash
cd BookManagementAPI
dotnet run
```

**Kết quả mong đợi:**
```
Now listening on: http://localhost:5000
```

**⚠️ QUAN TRỌNG:** ĐỪNG ĐÓNG Terminal này! API phải chạy liên tục.

**1.2. Kiểm tra API hoạt động:**

Mở browser, truy cập:
```
http://localhost:5000/api/books
```

**Phải thấy:** JSON array chứa các cuốn sách (hoặc `[]` nếu chưa có)

**1.3. Kiểm tra Flutter project:**

Mở Terminal 2 (cho Flutter):
```bash
cd book_management_app
flutter doctor  # Kiểm tra Flutter setup
```

**Phải thấy:** Không có lỗi nghiêm trọng (có thể có warning, không sao)

---

### Bước 2: Chạy Flutter App

**2.1. Liệt kê các thiết bị có sẵn:**
```bash
flutter devices
```

**Kết quả có thể:**
- Android Emulator (nếu đã mở)
- iOS Simulator (nếu đã mở)
- Chrome (cho web)
- Windows (cho desktop)

**2.2. Chạy app:**

**Cách 1: Chạy trên thiết bị đầu tiên**
```bash
flutter run
```

**Cách 2: Chọn thiết bị cụ thể**
```bash
flutter run -d chrome        # Chạy trên Chrome
flutter run -d windows       # Chạy trên Windows
flutter run -d <device-id>   # Chạy trên device cụ thể
```

**Kết quả mong đợi:**
```
Launching lib/main.dart on Chrome in debug mode...
Building Flutter application...
✓ Built build/web
```

**2.3. Đợi app khởi động:**

- App sẽ tự động mở trên thiết bị đã chọn
- Lần đầu chạy có thể mất 1-2 phút (build)
- Các lần sau sẽ nhanh hơn (hot reload)

---

### Bước 3: Test App

**3.1. Màn hình Login:**

**Khi app mở, bạn sẽ thấy:**
- Màn hình đăng nhập
- 2 ô nhập: Username và Password

**Thông tin đăng nhập:**
- Username: `admin`
- Password: `123456`

**Nhập thông tin và click "Đăng Nhập"**

**Kết quả mong đợi:**
- Chuyển sang màn hình Home
- Thấy danh sách sách (nếu API đã seed data)

**3.2. Màn hình Home:**

**Bạn sẽ thấy:**
- AppBar với tiêu đề "Quản Lý Sách"
- Icon mặt trời/mặt trăng (để đổi theme)
- Menu 3 chấm (để đăng xuất)
- Danh sách sách (hoặc thông báo "Chưa có sách nào")

**3.3. Test các tính năng:**

**✅ Test Theme Switcher:**
- Click icon mặt trời/mặt trăng
- Giao diện đổi từ sáng sang tối (hoặc ngược lại)

**✅ Test Xem danh sách:**
- Kéo xuống để refresh (pull to refresh)
- Danh sách sách được load từ API

**✅ Test Thêm sách:**
- Click nút "+" hoặc "Thêm sách"
- Điền form:
  - Tiêu đề: "Test Book"
  - Tác giả: "Test Author"
  - ISBN: "978-1234567890"
  - Số trang: 100
  - Ngày xuất bản: Chọn ngày
- Click "Thêm Sách"
- Thấy thông báo "Thêm sách thành công!"
- Danh sách tự động cập nhật

**✅ Test Sửa sách:**
- Click menu 3 chấm trên một cuốn sách
- Chọn "Sửa"
- Sửa thông tin
- Click "Cập Nhật"
- Thấy thông báo "Cập nhật sách thành công!"

**✅ Test Xóa sách:**
- Click menu 3 chấm trên một cuốn sách
- Chọn "Xóa"
- Xác nhận xóa
- Thấy thông báo "Xóa sách thành công!"

**✅ Test Đăng xuất:**
- Click menu 3 chấm ở AppBar
- Chọn "Đăng xuất"
- Quay lại màn hình Login

---

### Bước 4: Xử lý Lỗi Thường Gặp

**4.1. Lỗi: "No internet connection" hoặc "Connection refused"**

**Nguyên nhân:** API chưa chạy hoặc URL sai

**Giải pháp:**
1. Kiểm tra API đang chạy: `http://localhost:5000/api/books`
2. Kiểm tra URL trong `api_service.dart`:
   - Web: `http://localhost:5000/api`
   - Android Emulator: `http://10.0.2.2:5000/api`
   - iOS Simulator: `http://localhost:5000/api`

**4.2. Lỗi: "Failed to load books: 500"**

**Nguyên nhân:** Database chưa được tạo hoặc migration chưa chạy

**Giải pháp:**
```bash
cd BookManagementAPI
dotnet ef database update
```

**4.3. Lỗi: "FormatException" khi parse JSON**

**Nguyên nhân:** Format JSON từ API không đúng với Model

**Giải pháp:**
1. Kiểm tra API trả về gì: `http://localhost:5000/api/books`
2. So sánh với Model trong `book.dart`
3. Đảm bảo tên field khớp nhau (case-sensitive)

**4.4. Lỗi: "The getter 'books' isn't defined"**

**Nguyên nhân:** Chưa import đúng file hoặc chưa tạo file

**Giải pháp:**
1. Kiểm tra đã tạo tất cả file chưa
2. Kiểm tra import statements
3. Chạy `flutter pub get` lại

**4.5. App không hot reload**

**Giải pháp:**
- Nhấn `r` trong Terminal để hot reload
- Nhấn `R` để hot restart
- Hoặc click nút reload trong DevTools

---

### Bước 5: Kiểm tra Cuối Cùng

**Checklist hoàn thành:**

**Backend (.NET API):**
- [ ] API đang chạy tại `http://localhost:5000`
- [ ] GET `/api/books` trả về danh sách sách
- [ ] POST `/api/books` tạo sách mới thành công
- [ ] PUT `/api/books/{id}` cập nhật sách thành công
- [ ] DELETE `/api/books/{id}` xóa sách thành công

**Frontend (Flutter App):**
- [ ] App chạy được trên thiết bị
- [ ] Màn hình Login hiển thị đúng
- [ ] Đăng nhập thành công với admin/123456
- [ ] Màn hình Home hiển thị danh sách sách
- [ ] Theme switcher hoạt động
- [ ] Thêm sách thành công
- [ ] Sửa sách thành công
- [ ] Xóa sách thành công
- [ ] Đăng xuất hoạt động

**Kết nối:**
- [ ] Flutter app kết nối được với API
- [ ] Dữ liệu được load từ API
- [ ] CRUD operations hoạt động đúng

---

## 📝 TỔNG KẾT

Bạn đã hoàn thành một dự án thực tế kết hợp:

✅ **Bloc** - Quản lý state phức tạp (CRUD operations)  
✅ **Provider** - Quản lý state toàn cục (Auth, Theme)  
✅ **.NET Web API** - Backend server với Entity Framework  
✅ **Clean Architecture** - Tách biệt Models, Services, Blocs, UI  
✅ **Error Handling** - Xử lý lỗi mạng, timeout, validation  
✅ **Real-world Patterns** - Pull to refresh, Optimistic updates, Loading states  

**Kiến thức đạt được:**
- Hiểu khi nào dùng Bloc vs Provider
- Kết hợp Bloc và Provider trong cùng một app
- Tích hợp Flutter với .NET Web API
- Xử lý async operations với Bloc
- Quản lý state phức tạp trong ứng dụng thực tế

---

## 🎯 BÀI TẬP MỞ RỘNG

1. **Thêm tính năng Upload Ảnh:** Cho phép upload ảnh bìa sách
2. **Thêm Categories:** Phân loại sách theo thể loại
3. **Thêm Favorites:** Đánh dấu sách yêu thích (dùng Provider)
4. **Thêm Offline Mode:** Cache dữ liệu để xem khi không có mạng
5. **Thêm Unit Tests:** Viết test cho Bloc và Provider

---

## 📚 TÀI LIỆU THAM KHẢO

- [Flutter Bloc Documentation](https://bloclibrary.dev/)
- [Provider Package](https://pub.dev/packages/provider)
- [.NET Web API Documentation](https://learn.microsoft.com/en-us/aspnet/core/web-api/)
- [Entity Framework Core](https://learn.microsoft.com/en-us/ef/core/)

---

**Chúc bạn thành công! 🎉**
