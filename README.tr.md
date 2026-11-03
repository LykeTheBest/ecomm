# Mağazam – Hafif PHP E-Ticaret Platformu

> [English](./README.md)
> [Türkçe](./README.tr.md)

**Saf PHP (MVC)** ile yazılmış, framework bağımsız (özel yapım) bir e-ticaret uygulamasıdır. E-ticaret ön yüzü (storefront), misafir ve kayıtlı kullanıcı ödeme adımları, rol tabanlı bir yönetim paneli (admin), stok güvenli sipariş yönetimi, yapılandırılabilir bir ayarlar sistemi (bakım modu, satış anahtarı, ödeme yöntemleri) ve takılabilir bir ödeme katmanı (PayTR iskeleti dahil) ile birlikte gelir.

> Kullanıcı arayüzü (UI) Türkçedir ve yasal sayfalar Türk mevzuatını (KVKK, Mesafeli Satış Sözleşmesi) hedeflemektedir; ancak kod tabanı yerelleştirmeye (localization) oldukça uygundur.

---

## İçindekiler

* [Özellikler](https://www.google.com/search?q=%23%C3%B6zellikler)
* [Teknoloji Yığını](https://www.google.com/search?q=%23teknoloji-y%C4%B1%C4%9F%C4%B1n%C4%B1)
* [Proje Yapısı](https://www.google.com/search?q=%23proje-yap%C4%B1s%C4%B1)
* [Gereksinimler](https://www.google.com/search?q=%23gereksinimler)
* [Kurulum](https://www.google.com/search?q=%23kurulum)
* [Ortam Değişkenleri (.env)](https://www.google.com/search?q=%23ortam-de%C4%9Fi%C5%9Fkenleri)
* [Veritabanı](https://www.google.com/search?q=%23veritaban%C4%B1)
* [Rotalara Genel Bakış](https://www.google.com/search?q=%23rotalara-genel-bak%C4%B1%C5%9F)
* [Roller ve İzinler](https://www.google.com/search?q=%23roller-ve-izinler)
* [Güvenlik Notları](https://www.google.com/search?q=%23g%C3%BCvenlik-notlar%C4%B1)
* [Ödeme Sağlayıcıları](https://www.google.com/search?q=%23%C3%B6deme-sa%C4%9Flay%C4%B1c%C4%B1lar%C4%B1)
* [Bilinen Sınırlamalar / Yol Haritası](https://www.google.com/search?q=%23bilinen-s%C4%B1n%C4%B1rlamalar--yol-haritas%C4%B1)
* [Lisans](https://www.google.com/search?q=%23lisans)

---

## Özellikler

### Mağaza Ön Yüzü (Storefront)

* Öne çıkan ürünlerin yer aldığı ana sayfa
* **Arama, kategori filtresi, fiyat aralığı, sıralama ve sayfalama** özellikli ürün listeleme
* Görseller ve varyantlar içeren ürün detay sayfası
* Oturum tabanlı **alışveriş sepeti** (stok ve aktiflik durumu doğrulaması, silinen/pasif ürünlerin otomatik temizlenmesi)
* Çerez (Cookie) onay banner'ı (tümünü kabul et / yalnızca esansiyel)
* Yasal sayfalar: Mesafeli Satış Sözleşmesi, Gizlilik Politikası & KVKK, İade Koşulları
* Yönetim panelinden kontrol edilebilen küresel duyuru banner'ı

### Müşteriler

* E-posta doğrulama token'ı ile kayıt olma
* İsteğe bağlı **"Beni Hatırla"** özellikli giriş (sunucu tarafında saklanan hash'lenmiş token)
* Profil yönetimi, güvenli şifre değiştirme (mevcut şifre zorunluluğu, güçlü şifre kuralları)
* Kayıtlı adresler (teslimat / fatura adresi, varsayılan adres seçimi)
* **Misafir alışverişi (Guest checkout)** (e-posta ve telefon zorunluluğu) – misafir siparişleri çerezler aracılığıyla takip edilir
* E-posta eşleştiğinde misafir siparişinin yeni açılan hesaba otomatik olarak **aktarılması**

### Ödeme ve Siparişler

* Ayrı teslimat ve fatura adresleri
* Ödeme yöntemleri: Havale/EFT (`havale`), PayTR, kapıda ödeme (`kapida`) – her biri ayarlardan açılıp kapatılabilir
* Aşırı satışları (overselling) önlemek için atomik stok düşürme (`WHERE stock >= qty`) içeren bir **veritabanı transaksiyonu** içinde sipariş oluşturma
* Sipariş onay e-postası (havale talimatları dahil)
* **Otomatik stok iadesi (restocking)** özellikli sipariş iptali

### Yönetim Paneli (Admin Panel)

* Kontrol Paneli (ciro, sipariş sayısı, bekleyen siparişler, müşteri sayısı)
* Çoklu görsel yükleme (MIME doğrulamalı, rastgele dosya adları), varyantlar, stok kodu (SKU), vergi oranı, ağırlık, boyutlar, öne çıkan/aktif bayrakları ile Ürün CRUD
* Sınırsız iç içe geçme (ağaç yapısı) özellikli Kategori CRUD
* Sipariş yönetimi (durum güncellemeleri: bekliyor / ödendi / kargolandı / teslim edildi, iptal et ve stoka geri ekle)
* Sipariş geçmişi ile birlikte Müşteri listesi ve detay görünümü
* Site ayarları:
* Bakım modu (mesaj + otomatik bitiş tarihi)
* Duyuru banner'ı
* İsteğe bağlı bitiş tarihiyle satışları açma/kapatma
* Aktif ödeme yöntemleri


* **Geliştirici araçları** (yalnızca seviye 10000): kullanıcı rol yönetimi ve menü başı izin seviyeleri

### Altyapı

* URL parametreleri ve ara katman (middleware) desteği içeren özel **Router (Yönlendirici)**
* Şablon yapılarına sahip hafif **View (Görünüm) motoru**
* Dosya tabanlı **Logger (`storage/logs/app-YYYY-MM-DD.log`)**
* **Mailer** (geliştirme ortamında dosyaya yazar, prodüksiyon ortamında `mail()` kullanır)
* `vlucas/phpdotenv` aracılığıyla `.env` yapılandırması

---

## Teknoloji Yığını

| Katman | Teknoloji |
| --- | --- |
| Dil | PHP 8.0+ (`match`, `str_starts_with` kullanır) |
| Veritabanı | MySQL / MariaDB (PDO, gerçek hazırlanan sorgular - prepared statements) |
| Otomatik Yükleme | Composer (PSR-4, `App\` namespace) |
| Yapılandırma | `vlucas/phpdotenv` |
| Arayüz (Frontend) | Sunucu taraflı işlenen PHP şablonları (`.html` dosyaları), saf CSS/JS |
| Yerel Geliştirme | XAMPP / Apache (herhangi bir PHP sunucusuyla çalışır) |

---

## Proje Yapısı

```
.
├── app/
│   ├── Controllers/
│   │   ├── Account/          # Müşteri sipariş geçmişi
│   │   ├── Admin/            # Kimlik doğrulama, Panel, Kategori, Ürün, Sipariş, Kullanıcı, Ayarlar, Geliştirici
│   │   └── *.php             # Shop, Auth, Cart, Checkout, Profile, Page, Home, Error
│   ├── Core/                 # Auth, Cart, Database, Env, FileUploader, Logger, Mailer,
│   │                         # Router, Session, Settings, View, helpers.php
│   ├── Middleware/           # Admin, Auth, Maintenance, Sales
│   ├── Models/               # Address, Category, Order, Product, User
│   └── Services/Payment/     # PaymentInterface, PaymentManager, PayTRService
├── config/
│   └── database.php          # PDO bağlantısı (.env dosyasından okur)
├── public/                   # Web kök dizini (Document Root)
│   ├── index.php             # Front controller
│   ├── .htaccess
│   ├── js/cookie-consent.js
│   └── uploads/              # Yüklenen ürün görselleri (otomatik oluşturulur)
├── resources/views/
│   ├── account/  admin/  auth/  cart/  checkout/
│   ├── layouts/  pages/  partials/  profile/  shop/
├── routes/
│   ├── web.php               # Web rotaları
│   └── api.php               # API / webhook rotaları için ayrılmıştır
├── storage/logs/             # Uygulama logları
├── .env.example              # Ortam değişkenleri şablonu
└── composer.json

```

---

## Gereksinimler

* PHP **8.0 veya üzeri** ve şu uzantılar: `pdo_mysql`, `fileinfo`, `mbstring`
* MySQL 5.7+ / MariaDB 10.3+
* Composer
* `mod_rewrite` etkin Apache (veya eşdeğer yönlendirme kurallarına sahip Nginx)

---

## Kurulum

```bash
# 1. Projeyi klonlayın
git clone https://github.com/<kullanici-adiniz>/<repo-adiniz>.git
cd <repo-adiniz>

# 2. Bağımlılıkları yükleyin
composer install

# 3. Ortam dosyanızı oluşturun
cp .env.example .env
# ardından .env dosyasını kendi değerlerinize göre düzenleyin

# 4. Veritabanını oluşturun ve şemanızı içeri aktarın (bkz. "Veritabanı")

# 5. Bu dizinlerin web sunucusu tarafından yazılabilir olduğundan emin olun
chmod -R 775 storage public/uploads

```

Web sunucunuzun kök dizinini (Document Root) **`public/`** klasörüne yönlendirin.

PHP'nin yerleşik sunucusuyla hızlıca test etmek için:

```bash
php -S localhost:8000 -t public

```

> **XAMPP kullanıcıları:** Projeyi `public/` dizini ana dizin olacak şekilde konumlandırabilir (örn. Virtual Host kullanarak) veya `.htaccess` dosyasını buna göre ayarlayabilirsiniz.

---

## Ortam Değişkenleri (.env)

`.env.example` dosyasını `.env` olarak kopyalayın ve değerleri doldurun. **Gerçek `.env` dosyanızı asla Git'e commit etmeyin.**

| Değişken | Açıklama |
| --- | --- |
| `APP_ENV` | `development` veya `production`. Geliştirme ortamında e-postalar gönderilmek yerine log dosyasına yazılır. |
| `APP_DEBUG` | Hataları ekranda göstermek için `true` (asla canlı/production ortamında açmayın). |
| `APP_URL` | E-posta bağlantılarında kullanılan genel temel URL (örn. `[https://example.com](https://example.com)`). |
| `DB_HOST` | Veritabanı sunucusu (varsayılan `localhost`). |
| `DB_NAME` | Veritabanı adı. **Zorunludur.** |
| `DB_USER` | Veritabanı kullanıcı adı. **Zorunludur.** |
| `DB_PASS` | Veritabanı şifresi. |
| `PAYTR_MERCHANT_KEY` | PayTR mağaza anahtarı (yalnızca PayTR entegre edildiğinde gerekir). |
| `PAYTR_MERCHANT_SALT` | PayTR mağaza salt değeri (yalnızca PayTR entegre edildiğinde gerekir). |

`.env` dosyanızın `.gitignore` dosyasında listelendiğinden emin olun:

```gitignore
.env
/vendor/
/storage/logs/*
/public/uploads/*

```

---

## Veritabanı

Uygulama şu tabloları beklemektedir (MySQL, `utf8mb4`). Projeyi hızlıca kurabilmek için veritabanı şemanızı dışa aktarıp `database/schema.sql` adı altında depoya dahil edebilirsiniz.

| Tablo | Amaç |
| --- | --- |
| `users` | Müşteriler ve personel (`role` sayısal bir seviyedir), doğrulama ve hatırla token'ları |
| `addresses` | Kullanıcı başına kaydedilmiş teslimat/fatura adresleri |
| `categories` | Hiyerarşik kategoriler (`parent_id`, `slug`, `sort_order`) |
| `products` | Katalog (fiyat, vergi oranı, stok, SKU, bayraklar) |
| `product_images` | Kapak bayrağı ve sıralama düzenine sahip ürün görselleri |
| `product_variants` | Varyantlar (ad/değer, fiyat farkı, stok, SKU) |
| `orders` | Teslimat ve fatura detayları, durum ve ödeme yöntemi içeren siparişler |
| `order_items` | Sipariş kalemleri |
| `settings` | Anahtar/değer deposu (`setting_key`, `setting_value`) |

### Kullanılan Ayar Anahtarları (Settings Keys)

```
maintenance_mode, maintenance_message, maintenance_end_date,
announcement_status, announcement_text,
sales_active, sales_end_date,
payment_method_havale, payment_method_paytr, payment_method_kapida,
perm_manage_orders, perm_manage_products, perm_manage_categories,
perm_manage_users, perm_manage_settings

```

> Not: Müşterilerin sepete ürün ekleyebilmesi ve ödeme yapabilmesi için `sales_active` ayarının `1` olarak yapılması gerekir.

---

## Rotalara Genel Bakış

### Mağaza Ön Yüzü

| Metot | URI | Açıklama |
| --- | --- | --- |
| GET | `/` | Ana Sayfa |
| GET | `/urunler` | Ürün listeleme ve arama |
| GET | `/urun/{slug}` | Ürün detayı |
| GET | `/sepet` | Sepet |
| POST | `/sepet/ekle` · `/guncelle` · `/sil` | Sepet işlemleri |
| GET/POST | `/odeme` | Ödeme / Sipariş Adımı (Checkout) |
| GET | `/siparis-basarili` | Sipariş başarı sayfası |
| GET | `/siparislerim` | Sipariş geçmişi (misafirler son siparişi görür) |
| GET | `/mesafeli-satis` · `/gizlilik-politikasi` · `/iade-kosullari` | Yasal sayfalar |

### Müşteri Hesabı

| Metot | URI | Açıklama |
| --- | --- | --- |
| GET/POST | `/login`, `/register` | Kimlik Doğrulama |
| GET | `/verify-email?token=…` | E-posta doğrulama |
| GET/POST | `/profil` | Profil (giriş zorunlu) |
| GET | `/logout` | Çıkış Yap |

### Yönetim Paneli (`/admin/...`)

Giriş, kontrol paneli, kategoriler, ürünler, siparişler, kullanıcılar, ayarlar ve geliştirici araçları. Tüm admin rotaları `AdminMiddleware` ile korunmaktadır ve her kontrolcü ayrıca kendi kurucusunda (constructor) kendi iznini kontrol eder (derinlemesine savunma - defense in depth).

---

## Roller ve İzinler

Roller `users.role` alanında **sayısal bir seviye** olarak saklanır:

| Seviye | Anlamı |
| --- | --- |
| < 100 | Müşteri (yönetim paneli erişimi yok) |
| ≥ 100 | Yönetim paneli erişimi |
| 10000 | Geliştirici – tam erişim, rolleri ve menü izinlerini yönetebilir |

Her admin menüsü (siparişler, ürünler, kategoriler, kullanıcılar, ayarlar), Geliştiricinin **Admin → Geliştirici → Menü İzinleri** bölümünden çalışma zamanında değiştirebileceği minimum bir seviye gerektirir (`perm_*` ayarları olarak saklanır). Değeri tanımlanmamış bir izin varsayılan olarak reddedilir.

---

## Güvenlik Notları

Uygulanan güvenlik önlemleri:

* Durum değiştiren tüm formlarda CSRF token koruması
* Her yerde hazırlanan sorgular (PDO, emülasyon devre dışı); güvenli beyaz listeye alınmış `ORDER BY` ifadeleri
* `password_hash()` ile şifrelenmiş parolalar
* Oturum çerezi sertleştirmesi (`HttpOnly`, `SameSite=Lax`, HTTPS üzerinde `Secure`), giriş yapıldığında oturum ID yenilenmesi
* Oturum ele geçirmeyi (hijacking) azaltmak için oturum parmak izi (IP + User-Agent)
* `e()` yardımcı fonksiyonu ile çıktı kaçırma (Output escaping)
* Gerçek MIME türüyle doğrulanan, boyutu sınırlandırılmış (5 MB), rastgele adlarla saklanan görsel yüklemeleri
* Güvenlik başlıkları (CSP, `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy`)
* İşlemler (transactions) içinde yarış durumuna (race condition) karşı güvenli stok güncellemeleri
* Rol değişikliklerine karşı koruma (kendi rütbesini düşürmeme, ek Geliştirici hesabı oluşturulamaması)

Canlı ortama geçmeden önce lütfen [Bilinen Sınırlamalar](https://www.google.com/search?q=%23bilinen-s%C4%B1n%C4%B1rlamalar--yol-haritas%C4%B1) bölümündeki maddeleri de gözden geçirin.

---

## Ödeme Sağlayıcıları

Ödemeler bir sağlayıcı soyutlaması (provider abstraction) kullanır:

```php
interface PaymentInterface
{
    public function createPayment(array $orderData): string;
    public function verifyCallback(array $data): bool;
}

```

`PaymentManager::getProvider('paytr')` eşleşen servisi döndürür. Yeni bir sağlayıcı eklemek için (örn. iyzico veya Stripe), `PaymentInterface` arayüzü uygulanmalı ve `PaymentManager` içine kaydedilmelidir.

> **Durum:** Havale ve kapıda ödeme uçtan uca çalışmaktadır. `PayTRService` şu anda bir **iskelet (stub)** halindedir ve canlı ortama alınmadan önce tamamlanmalıdır (token üretimi, yönlendirme ve callback hash doğrulaması).

---

## Bilinen Sınırlamalar / Yol Haritası

* [ ] **PayTR entegrasyonu** bir taslaktır; `verifyCallback()` şu anda her zaman `true` döndürmektedir ve düzgün bir şekilde kodlanmalıdır
* [ ] `/payment/callback` rotası henüz var olmayan bir `CheckoutController::callback()` metoduna işaret etmektedir
* [ ] Şifre sıfırlama: e-posta gönderilmektedir ancak token'ı işleyen sıfırlama sayfası/rotası uygulanmamıştır ve şifremi unuttum rotaları `web.php` içinde kayıtlı değildir
* [ ] Profil sayfası tarafından kullanılan adres rotaları (`/profil/adres-ekle`, `/profil/adres-sil/{id}`) ve `/resend-verification` rotaları `web.php` içinde kayıtlı değildir
* [ ] Admin silme eylemleri (`/admin/categories/delete/{id}`, `/admin/products/delete/{id}`) **GET** metotlarını kullanmaktadır ve CSRF korumasına sahip değildir – POST metoduna çevrilmelidir
* [ ] Ödeme adımı (Checkout), giriş yapmış kullanıcılar için `saved_address_id` seçeneğini henüz işlememektedir ve ödeme yöntemi seçim arayüzü bir yer tutucudur (placeholder)
* [ ] Fatura alanları toplanmaktadır ancak `Order::create()` bunları henüz INSERT sorgusuna bağlamamaktadır
* [ ] Admin girişinde oran sınırlaması (rate limiting) / hesap kilitleme bulunmamaktadır
* [ ] Ürün varyantları veritabanında saklanmakta ancak sepette henüz seçilememektedir
* [ ] `public/index.php`, `.env` okunmadan önce `display_errors` özelliğini etkinleştirmektedir – canlı ortamda kapalı olduğundan emin olunmalıdır
* [ ] Bir veritabanı şeması / migrasyon dosyası ve örnek veriler (seed) eklenmelidir
* [ ] Otomatik testler eklenmelidir

---

## Lisans

Tüm hakları saklıdır. Bu depo yalnızca portfolyo / inceleme amacıyla yayınlanmıştır.
Ayrıntılar için [LICENSE](https://www.google.com/search?q=LICENSE) dosyasına bakın.
