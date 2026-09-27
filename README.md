# ASÜ Hub — Kampüsün Dijital Hafızası

Aksaray Üniversitesi öğrencileri için geliştirilmiş, ders notu paylaşımı ve ikinci el kitap takası yapılabilen bir topluluk platformu. Backend kodu olmadan, tamamen istemci tarafından (frontend) [Supabase](https://supabase.com) üzerinden Auth, Database ve Storage servisleri kullanılarak inşa edilmiştir.

🔗 **Canlı Demo:** [elifaytekinn-e.github.io/su-student-hub](https://elifaytekinn-e.github.io/su-student-hub/)
📸 **Ekran Görüntüleri:** <img width="1497" height="691" alt="image" src="https://github.com/user-attachments/assets/9cac9812-04e2-4b83-a6cf-11aa0e59ed2a" />


---

## ✨ Özellikler

### Öğrenciler için
- **Ders Notu Paylaşımı** — Fakülte/bölüm bazlı filtreleme, arama, dosya yükleme; paylaşılan notlar admin onayından geçtikten sonra yayınlanır.
- **Yorum & Puanlama** — Her nota 5 yıldıza kadar puan verilebilir ve yorum bırakılabilir.
- **Sahaf (Kitap Takas Pazarı)** — Satılık / takaslık / bağış kategorilerinde fotoğraflı ilan; satıcıyla doğrudan WhatsApp üzerinden iletişim.
- **Profil Yönetimi** — Takma ad, fakülte/bölüm bilgisi, şifre değiştirme, kendi paylaşımlarının listesi.
- **Kimlik Doğrulama** — E-posta/şifre ile kayıt ve giriş (Supabase Auth).

### Yöneticiler için
- **Genel Bakış Paneli** — Kayıtlı öğrenci, toplam not, sahaf ilanı ve onay bekleyen içerik sayıları; Chart.js ile fakülte dağılım grafiği.
- **Not Onay Akışı** — Yüklenen notlar önce moderasyona düşer, admin onaylamadan herkese açık listede görünmez.
- **Duyuru Yönetimi** — Ana sayfada gösterilecek duyuruları yayınlama.
- **İçerik Moderasyonu** — Uygunsuz not/kitap ilanlarını silme.
- **Rol Bazlı Erişim** — Panel yalnızca `profiles.is_admin = true` olan kullanıcılara açık; sunucu tarafında (Supabase sorgusu ile) doğrulanır.

---

## 🛠️ Kullanılan Teknolojiler

| Katman | Teknoloji |
|---|---|
| Arayüz | HTML5, Bootstrap 5, Font Awesome |
| Grafikler | Chart.js |
| Backend / Veritabanı | [Supabase](https://supabase.com) (PostgreSQL) |
| Kimlik Doğrulama | Supabase Auth |
| Dosya Depolama | Supabase Storage |
| Barındırma önerisi | Netlify / Vercel / GitHub Pages (statik dosyalar) |

Projede herhangi bir build aracı (webpack, vite vb.) veya paket yöneticisi kullanılmamıştır — tüm bağımlılıklar CDN üzerinden yüklenir. Bu, kurulumu basitleştirir ancak üretim ortamında paket sürümlerini sabitlemek (pinning) isteyebilirsiniz.

---

## 📂 Proje Yapısı

```
su-student-hub/
├── index.html           # Ana sayfa (arama, duyuru, öne çıkan özellikler)
├── notes.html            # Ders notları listesi, filtreleme, yorum/puanlama
├── books.html             # Sahaf / kitap takas pazarı
├── profile.html            # Kullanıcı profil paneli
├── login.html               # Giriş / kayıt
├── yonetim-paneli.html       # Admin paneli
├── faq.html                   # Sıkça sorulan sorular
└── 404.html                    # Hata sayfası
```

---

## 🚀 Kurulum

### 1. Supabase projesi oluşturun
[supabase.com](https://supabase.com) üzerinden ücretsiz bir proje açın ve aşağıdaki tabloları oluşturun:

| Tablo | Amaç |
|---|---|
| `profiles` | Kullanıcı profili (`nickname`, `faculty`, `department`, `is_admin`) |
| `notes` | Ders notları (`title`, `faculty`, `department`, `description`, `file_url`, `is_approved`, `user_id`) |
| `books` | Sahaf ilanları (`title`, `author`, `category`, `price`, `contact_info`, `image_url`, `user_id`) |
| `comments` | Not yorumları (`note_id`, `comment_text`, `stars`, `user_name`) |
| `announcements` | Duyurular (`text`) |

**Storage bucket'ları:** `course_notes` (not dosyaları), `book_images` (kitap fotoğrafları).

> ⚠️ **Önemli:** Her tabloda Row Level Security (RLS) etkinleştirilmeli ve uygun politikalar tanımlanmalıdır (örn. bir kullanıcının yalnızca kendi kaydını güncelleyebilmesi, `is_admin` alanının yalnızca sunucu tarafından değiştirilebilmesi). Bu proje istemci tarafında rol kontrolü yapsa da gerçek güvenlik RLS politikalarına dayanır.

### 2. Bağlantı bilgilerini güncelleyin
Her HTML dosyasındaki şu satırları kendi Supabase proje bilgilerinizle değiştirin:

```javascript
const SUPABASE_URL = 'https://PROJE-ID.supabase.co';
const SUPABASE_KEY = 'PUBLISHABLE-ANAHTARINIZ';
```

> Not: Burada kullanılan "publishable" (anon) anahtar tasarım gereği herkese açık olabilir; asıl güvenlik RLS politikalarındadır — `service_role` anahtarını asla istemci koduna koymayın.

### 3. Yerelde çalıştırın
Build adımı gerekmez; herhangi bir statik dosya sunucusuyla açabilirsiniz:

```bash
npx serve .
# veya
python3 -m http.server 8000
```

### 4. İlk admin kullanıcısını oluşturun
`login.html` üzerinden kayıt olun, ardından Supabase panelinden ilgili kullanıcının `profiles` tablosundaki `is_admin` alanını `true` yapın.

---

## 🔒 Bilinen Sınırlamalar / Yol Haritası

- [ ] Silinen not/kitap kayıtlarıyla birlikte ilgili Storage dosyaları da temizlenmeli.
- [ ] Supabase client kurulum kodu ortak bir `config.js` dosyasına taşınmalı.
- [ ] "Şifremi Unuttum" akışı gerçek bir sıfırlama linkine bağlanmalı.

---

## 📄 Lisans

Bu proje eğitim/portfolyo amaçlı geliştirilmiştir.

---

**Geliştirici:** Elif Aytekin
**İletişim:** Elifaytekinn@icloud.com
