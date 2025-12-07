[readme.md](https://github.com/user-attachments/files/24013216/readme.md)

<p align="center">
<img src="Image.png"
width="300" height="300">
</p>

<h3 align="center">Kütüphane Uygulaması</h3>



# 📖 İçindekiler
- [Genel Bakış](#genel-bakış)
- [Özellikler](#özellikler)
- [Teknolojiler](#teknolojiler)
- [Veritabanı Şeması](#veritabanı-şeması)
- [API Dokümantasyonu](#api-dokümantasyonu)
- [Ekip Üyeleri](#ekip-üyeleri)

# Genel Bakış
**Kütüphane Uygulaması**, TypeScript ile yazılmış bir front-end ve C# (.NET) ile geliştirilmiş bir back-end içeren full-stack bir kütüphane yönetim uygulamasıdır. Kitaplar üzerinde CRUD işlemleri, ödünç alma/geri verme, kullanıcı yönetimi ve listeleme gibi temel kütüphane fonksiyonlarını sağlar.



# Özellikler
## Login/Register Paneli
- 📝 İsim-soyisim, e-mail, telefon numarası, doğum tarihi ile kayıt
- 🔒 Güvenli şifre belirleme
- 🚪 E-mail ve şifre ile kolay giriş
## Kullanıcı Paneli
- 🔍 Kitap arama
- 👁️ Kitap detaylarını görme 
- 📚 Kitap listeleme, filtreleme 
- 🔄 Kitap ödünç alma ve iade etme
- 📜 Aktif kitaplar,geçmiş kitaplar,ödenmemiş cezalar, ceza geçmişi görüntüleme

## Admin Paneli
- 🛠️  Kitapları görüntüleme, ekleme, düzenleme, silme
- 👮  Üyeleri görüntüleme, ceza atama
- 📜  Aktif ödünç, geçmiş işlem gecikmiş iade görüntüleme
- 📍 Salon, raf ekleme
- 🏷️ Kitap kategorisi ekleme
- 🏢 Yayınevi ekleme,düzenleme, silme
- ✍️ Yazar ekleme, düzenleme, silme
  

# Teknolojiler

## **Back-end**
- **Framework:** ASP.NET Core (.NET 8)
- **ORM:** Entity Framework Core (SQL Server)
- **Kimlik Doğrulama:** ASP.NET Identity + JWT Bearer Authentication
- **Mapping:** AutoMapper
- **Loglama:** Serilog (Console + File sink, günlük log dosyaları)
- **Dokümantasyon:** Swagger (JWT güvenlik tanımları ile)
- **JSON İşleme:** Newtonsoft.Json (ReferenceLoopHandling.Ignore ile)
- **Katmanlı Mimari:**
- Controllers → API endpoint’leri
- Services → iş mantığı
- Repositories → veri erişim katmanı
- DTOs → veri transfer objeleri



## **Front-end**
- **Framework:** Next.js 16
- **Dil:** TypeScript 5
- **UI Kütüphanesi:** React 19
- **Stil:** TailwindCSS 4 (PostCSS ile entegre)
- **API İletişimi:** Axios
- **Bildirim Sistemi:** React Hot Toast
- **Linting:** ESLint (Next.js core-web-vitals + TypeScript kuralları)




# Veritabanı Şeması
```
┌────────────────────┐
│      AppUser       │
├────────────────────┤
│ Id (PK)            │
│ FirstName          │
│ LastName           │
│ DateOfBirth        │
│ Email              │
│ RefreshToken       │
│ RefreshTokenExpiry │
└───────┬────────────┘
        │ 1:N
        ▼
┌───────────────┐         ┌──────────────┐
│    Loans      │         │   BookCopy   │
├───────────────┤         ├──────────────┤
│ Id (PK)       │         │ Id (PK)      │
│ UserId (FK)   │────────►│ BookId (FK)  │
│ BookCopyId(FK)│         │ ShelfId (FK) │
│ LoanDate      │         │ BarcodeNumber│ 
│ ExpectedReturn│         │ IsAvailable  │
│ ActualReturn  │         └──────┬───────┘
└──────┬────────┘                │ N:1
       │ 1:N                     ▼
       ▼                  ┌──────────────┐
┌───────────────┐         │    Shelf     │
│    Fines      │         ├──────────────┤
├───────────────┤         │ Id (PK)      │
│ Id (PK)       │         │ ShelfCode    │
│ LoanId (FK)   │         │ RoomId (FK)  │
│ FineTypeId(FK)│         └──────┬───────┘
│ Amount        │                │ N:1
│ Status        │                ▼
│ IsActive      │         ┌──────────────┐
│ IssuedDate    │         │    Room      │
└───────────────┘         ├──────────────┤
                          │ Id (PK)      │
┌──────────────┐          │ RoomCode     │
│  FineType    │          │ Description  │
├──────────────┤          └──────────────┘
│ Id (PK)      │
│ Name         │
│ DailyRate    │
└──────────────┘

┌────────────────┐          ┌──────────────┐
│     Book       │          │   Author     │
├────────────────┤          ├──────────────┤
│ Id (PK)        │          │ Id (PK)      │
│ Title          │          │ FirstName    │
│ ISBN (unique)  │          │ LastName     │
│ PageCount      │          └──────┬───────┘
│ PublicationYr  │                 │ N:M
│ Language       │                 ▼
│ CategoryId(FK) │          ┌──────────────┐
│ PublisherId(FK)│          │  BookAuthor  │
└──────┬─────────┘          ├──────────────┤
       │ 1:N                │ BookId (FK)  │
       ▼                    │ AuthorId (FK)│
┌──────────────┐            └──────────────┘
│  Category    │
├──────────────┤
│ Id (PK)      │
│ Name         │
└──────────────┘

┌──────────────┐
│  Publisher   │
├──────────────┤
│ Id (PK)      │
│ Name         │
└──────────────┘
```

# API Dokümantasyonu

## 🔑 Kimlik Doğrulama
- `POST /api/auth/register` → Yeni kullanıcı kaydı  
- `POST /api/auth/login` → Giriş işlemi, JWT token döner  
- `POST /api/auth/refresh-token` → Token yenileme

## 📚 Kitap Yönetimi
- `POST /api/book/add-book` → Yeni kitap ekle  
- `GET /api/book/get-book/{id}` → Kitap detaylarını getir  
- `GET /api/book/get-all-books` → Tüm kitapları listele  
- `PUT /api/book/update-book/{id}` → Kitap güncelle  
- `DELETE /api/book/delete-book/{id}` → Kitap sil  
- `POST /api/book/add-book-copy` → Yeni kitap kopyası ekle  
- `PUT /api/book/update-book-copy/{id}` → Kitap kopyası güncelle  
- `DELETE /api/book/delete-book-copy/{id}` → Kitap kopyası sil  
- `POST /api/book/add-book-author` → Kitaba yazar ekle  
- `GET /api/book/is-book-author-exists/{bookId}/{authorId}` → Yazar kitapta var mı kontrolü  
- `GET /api/book/get-book-details/{id}` → Kitap + kopya + yazar detayları  
- `GET /api/book/get-by-name` → Kitap adına göre arama  
- `GET /api/book/other-by-author` → Aynı yazarın diğer kitapları  
- `GET /api/book/all-book-copies` → Tüm kitap kopyaları

## 👤 Kullanıcı Yönetimi
- `GET /api/user/me` → Giriş yapan kullanıcının bilgileri  
- `GET /api/user/stats` → Kullanıcıya özel istatistikler  
- `GET /api/user` → Tüm kullanıcıları listele (Admin)  
- `GET /api/user/{id}` → Belirli kullanıcıyı getir (Admin)  
- `GET /api/user/email/{email}` → E-posta ile kullanıcı arama (Admin)

## 📦 Ödünç Alma / İade
- `GET /api/loan/my-active-loans` → Kullanıcının aktif ödünçleri  
- `GET /api/loan/my-returned-loans` → Kullanıcının iade ettiği kitaplar  
- `GET /api/loan/can-borrow` → Kullanıcı ödünç alabilir mi?  
- `POST /api/loan` → Kitap ödünç alma  
- `PUT /api/loan/update-loan` → Ödünç bilgisi güncelle  
- `POST /api/loan/return-book` → Kitap iade et  
- `GET /api/loan/get-all-loans` → Tüm ödünç kayıtları (Admin)  
- `GET /api/loan/overdue` → Geciken ödünçler (Admin)  
- `GET /api/loan/returned` → İade edilenler (Admin)

## 💰 Ceza Yönetimi
- `POST /api/fine/issue` → Yeni ceza oluştur (Admin)  
- `GET /api/fine/by-email` → Kullanıcıya ait cezalar (Admin)  
- `GET /api/fine/my-active-fines` → Kullanıcının aktif cezaları  
- `GET /api/fine/my-history-fines` → Kullanıcının geçmiş cezaları  
- `POST /api/fine/revoke/{fineId}` → Ceza iptali (Admin)  
- `POST /api/fine/pay` → Ceza ödeme (Kullanıcı)

## 📁 Diğer Yönetim Alanları
- **Yazarlar**: `GET /api/author`, `POST`, `DELETE`, `PUT`, `GET by-name`, `GET pageable`  
- **Kategoriler**: `GET /api/category/list`, `POST`, `DELETE`, `PUT`, `GET by-name`, `GET pageable`  
- **Yayıncılar**: `GET`, `POST`, `DELETE`, `PUT`, `GET by-name`, `GET pageable`  
- **Odalar & Raflar**: `GET`, `POST`, `PUT`, `GET room/{roomId}`, `GET search`  
- **Dashboard**: `GET` → Admin panel verileri  
- **Ceza Türleri**: `GET`, `POST`, `PUT update`, `GET by id`


# Ekip Üyeleri
 👤 213301129-Kaan Pulat

 👤 223301006-Ataberk Bakır

👤 223301007-Serdar Aşlakcı

👤 223301031-Taha Yasin Uruç

👤 223301157 Ömerhan Sezgin






