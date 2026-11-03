# My Shop – Lightweight PHP E-Commerce Platform
> [Turkish](./README.tr.md)
> [English](./README.md)

A custom-built, framework-free e-commerce application written in **plain PHP (MVC)**. It ships with a storefront, guest and registered checkout, a role-based admin panel, stock-safe order handling, a configurable settings system (maintenance mode, sales toggle, payment methods) and a pluggable payment layer (PayTR skeleton included).

> The UI is in Turkish and the legal pages target Turkish regulations (KVKK, Mesafeli Satış Sözleşmesi), but the codebase is easy to localize.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Installation](#installation)
- [Environment Variables](#environment-variables)
- [Database](#database)
- [Routes Overview](#routes-overview)
- [Roles & Permissions](#roles--permissions)
- [Security Notes](#security-notes)
- [Payment Providers](#payment-providers)
- [Known Limitations / Roadmap](#known-limitations--roadmap)
- [License](#license)

---

## Features

### Storefront
- Home page with featured products
- Product listing with **search, category filter, price range, sorting and pagination**
- Product detail page with images and variants
- Session-based **shopping cart** (stock and active-status validation, automatic cleanup of deleted/inactive products)
- Cookie consent banner (accept all / essential only)
- Legal pages: Distance Sales Agreement, Privacy & KVKK, Returns Policy
- Global announcement banner controlled from the admin panel

### Customers
- Registration with email verification token
- Login with optional **"remember me"** (hashed token stored server-side)
- Profile management, secure password change (current password required, strength rules)
- Saved addresses (shipping / billing, default address)
- **Guest checkout** (email + phone required) – guest orders are tracked via a cookie
- Automatic **transfer of a guest order to a new account** when the email matches

### Checkout & Orders
- Separate shipping and billing addresses
- Payment methods: bank transfer (`havale`), PayTR, cash on delivery (`kapida`) – each can be toggled in settings
- Order creation inside a **database transaction** with atomic stock decrement (`WHERE stock >= qty`) to prevent overselling
- Order confirmation email (bank transfer instructions included)
- Order cancellation with **automatic restocking**

### Admin Panel
- Dashboard (revenue, order count, pending orders, customer count)
- Product CRUD with multiple image upload (MIME-validated, random filenames), variants, SKU, tax rate, weight, dimensions, featured/active flags
- Category CRUD with unlimited nesting (tree structure)
- Order management (status updates: pending / paid / shipped / delivered, cancel & restock)
- Customer list and detail view with order history
- Site settings:
  - Maintenance mode (message + auto-expiry date)
  - Announcement banner
  - Sales on/off with optional end date
  - Enabled payment methods
- **Developer tools** (level 10000 only): user role management and per-menu permission levels

### Infrastructure
- Custom **Router** with URL parameters and middleware support
- Lightweight **View engine** with layouts
- File-based **Logger** (`storage/logs/app-YYYY-MM-DD.log`)
- **Mailer** (logs to file in development, uses `mail()` in production)
- `.env` configuration via `vlucas/phpdotenv`

---

## Tech Stack

| Layer      | Technology                                   |
|------------|----------------------------------------------|
| Language   | PHP 8.0+ (uses `match`, `str_starts_with`)   |
| Database   | MySQL / MariaDB (PDO, real prepared statements) |
| Autoload   | Composer (PSR-4, namespace `App\`)           |
| Config     | `vlucas/phpdotenv`                           |
| Frontend   | Server-rendered PHP templates (`.html` files), vanilla CSS/JS |
| Local dev  | XAMPP / Apache (works with any PHP server)   |

---

## Project Structure

```
.
├── app/
│   ├── Controllers/
│   │   ├── Account/          # Customer order history
│   │   ├── Admin/            # Auth, Dashboard, Category, Product, Order, User, Settings, Developer
│   │   └── *.php             # Shop, Auth, Cart, Checkout, Profile, Page, Home, Error
│   ├── Core/                 # Auth, Cart, Database, Env, FileUploader, Logger, Mailer,
│   │                         # Router, Session, Settings, View, helpers.php
│   ├── Middleware/           # Admin, Auth, Maintenance, Sales
│   ├── Models/               # Address, Category, Order, Product, User
│   └── Services/Payment/     # PaymentInterface, PaymentManager, PayTRService
├── config/
│   └── database.php          # PDO connection (reads from .env)
├── public/                   # Web root
│   ├── index.php             # Front controller
│   ├── .htaccess
│   ├── js/cookie-consent.js
│   └── uploads/              # Uploaded product images (created automatically)
├── resources/views/
│   ├── account/  admin/  auth/  cart/  checkout/
│   ├── layouts/  pages/  partials/  profile/  shop/
├── routes/
│   ├── web.php               # Web routes
│   └── api.php               # Reserved for API / webhook routes
├── storage/logs/             # Application logs
├── .env.example              # Environment template
└── composer.json
```

---

## Requirements

- PHP **8.0 or higher** with extensions: `pdo_mysql`, `fileinfo`, `mbstring`
- MySQL 5.7+ / MariaDB 10.3+
- Composer
- Apache with `mod_rewrite` (or Nginx with equivalent rewrite rules)

---

## Installation

```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>

# 2. Install dependencies
composer install

# 3. Create your environment file
cp .env.example .env
# then edit .env with your own values

# 4. Create the database and import your schema (see "Database")

# 5. Make sure these directories are writable by the web server
chmod -R 775 storage public/uploads
```

Point your web server's document root to the **`public/`** directory.

For quick local testing with PHP's built-in server:

```bash
php -S localhost:8000 -t public
```

> **XAMPP users:** either place the project so that `public/` is the document root (e.g. via a virtual host) or adjust `.htaccess` accordingly.

---

## Environment Variables

Copy `.env.example` to `.env` and fill in the values. **Never commit your real `.env` file.**

| Variable               | Description                                                        |
|------------------------|--------------------------------------------------------------------|
| `APP_ENV`              | `development` or `production`. In `development`, emails are written to the log instead of being sent. |
| `APP_DEBUG`            | `true` to display errors on screen (never use in production).      |
| `APP_URL`              | Public base URL, used in email links (e.g. `https://example.com`). |
| `DB_HOST`              | Database host (default `localhost`).                               |
| `DB_NAME`              | Database name. **Required.**                                       |
| `DB_USER`              | Database user. **Required.**                                       |
| `DB_PASS`              | Database password.                                                 |
| `PAYTR_MERCHANT_KEY`   | PayTR merchant key (only needed once PayTR is implemented).        |
| `PAYTR_MERCHANT_SALT`  | PayTR merchant salt (only needed once PayTR is implemented).       |

Make sure `.env` is listed in your `.gitignore`:

```gitignore
.env
/vendor/
/storage/logs/*
/public/uploads/*
```

---

## Database

The application expects the following tables (MySQL, `utf8mb4`). Export your schema and include it in the repository as `database/schema.sql` so others can set the project up quickly.

| Table              | Purpose                                                                 |
|--------------------|-------------------------------------------------------------------------|
| `users`            | Customers and staff (`role` is a numeric level), verification & remember tokens |
| `addresses`        | Saved shipping/billing addresses per user                               |
| `categories`       | Hierarchical categories (`parent_id`, `slug`, `sort_order`)             |
| `products`         | Catalog (price, tax rate, stock, SKU, flags)                            |
| `product_images`   | Product images with cover flag and sort order                           |
| `product_variants` | Variants (name/value, price adjustment, stock, SKU)                     |
| `orders`           | Orders with shipping and billing details, status, payment method        |
| `order_items`      | Order line items                                                        |
| `settings`         | Key/value store (`setting_key`, `setting_value`)                        |

### Settings keys used

```
maintenance_mode, maintenance_message, maintenance_end_date,
announcement_status, announcement_text,
sales_active, sales_end_date,
payment_method_havale, payment_method_paytr, payment_method_kapida,
perm_manage_orders, perm_manage_products, perm_manage_categories,
perm_manage_users, perm_manage_settings
```

> Note: `sales_active` must be set to `1` for customers to add items to the cart and check out.

---

## Routes Overview

### Storefront
| Method | URI                     | Description                    |
|--------|-------------------------|--------------------------------|
| GET    | `/`                     | Home                           |
| GET    | `/urunler`              | Product listing & search       |
| GET    | `/urun/{slug}`          | Product detail                 |
| GET    | `/sepet`                | Cart                           |
| POST   | `/sepet/ekle` · `/guncelle` · `/sil` | Cart actions     |
| GET/POST | `/odeme`              | Checkout                       |
| GET    | `/siparis-basarili`     | Order success page             |
| GET    | `/siparislerim`         | Order history (guests see last order) |
| GET    | `/mesafeli-satis` · `/gizlilik-politikasi` · `/iade-kosullari` | Legal pages |

### Customer Account
| Method   | URI                         | Description              |
|----------|-----------------------------|--------------------------|
| GET/POST | `/login`, `/register`       | Authentication           |
| GET      | `/verify-email?token=…`     | Email verification       |
| GET/POST | `/profil`                   | Profile (auth required)  |
| GET      | `/logout`                   | Logout                   |

### Admin (`/admin/...`)
Login, dashboard, categories, products, orders, users, settings and developer tools. All admin routes are protected by `AdminMiddleware`, and each controller additionally checks its own permission in the constructor (defense in depth).

---

## Roles & Permissions

Roles are stored as a **numeric level** in `users.role`:

| Level  | Meaning                                                         |
|--------|-----------------------------------------------------------------|
| < 100  | Customer (no panel access)                                      |
| ≥ 100  | Admin panel access                                              |
| 10000  | Developer – full access, can manage roles and menu permissions  |

Each admin menu (orders, products, categories, users, settings) requires a minimum level that the Developer can change at runtime from **Admin → Developer → Menu Permissions** (stored as `perm_*` settings). A permission with no configured value is denied by default.

---

## Security Notes

Implemented:

- CSRF tokens on all state-changing forms
- Prepared statements everywhere (PDO, emulation disabled); whitelisted `ORDER BY`
- Passwords hashed with `password_hash()`
- Session cookie hardening (`HttpOnly`, `SameSite=Lax`, `Secure` on HTTPS), session ID regeneration on login
- Session fingerprint (IP + User-Agent) to mitigate hijacking
- Output escaping via the `e()` helper
- Image uploads validated by real MIME type, size-limited (5 MB), stored under random names
- Security headers (CSP, `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy`)
- Race-condition-safe stock updates inside transactions
- Role changes protected (no self-demotion, no creation of additional Developer accounts)

Before going to production, please also review the items in [Known Limitations](#known-limitations--roadmap).

---

## Payment Providers

Payments use a provider abstraction:

```php
interface PaymentInterface
{
    public function createPayment(array $orderData): string;
    public function verifyCallback(array $data): bool;
}
```

`PaymentManager::getProvider('paytr')` returns the matching service. To add a new provider (e.g. iyzico or Stripe), implement `PaymentInterface` and register it in `PaymentManager`.

> **Status:** Bank transfer and cash on delivery work end to end. `PayTRService` is currently a **skeleton** and must be completed (token generation, redirect, and callback hash verification) before it is used in production.

---

## Known Limitations / Roadmap

- [ ] **PayTR integration** is a stub; `verifyCallback()` currently returns `true` and must be implemented properly
- [ ] The `/payment/callback` route points to a `CheckoutController::callback()` method that does not exist yet
- [ ] Password reset: the email is sent, but the reset page/route that consumes the token is not implemented, and the forgot-password routes are not registered in `web.php`
- [ ] Address routes used by the profile page (`/profil/adres-ekle`, `/profil/adres-sil/{id}`) and `/resend-verification` are not registered in `web.php`
- [ ] Admin delete actions (`/admin/categories/delete/{id}`, `/admin/products/delete/{id}`) use **GET** and have no CSRF protection – switch to POST
- [ ] Checkout does not yet process the `saved_address_id` option for logged-in users, and the payment-method selection UI is a placeholder
- [ ] Billing fields are collected, but `Order::create()` does not yet bind them to the INSERT statement
- [ ] Admin login has no rate limiting / lockout
- [ ] Product variants are stored but not yet selectable in the cart
- [ ] `public/index.php` enables `display_errors` before `.env` is read – verify it is off in production
- [ ] Add a database schema / migration file and seed data
- [ ] Add automated tests

---

## License

All rights reserved. This repository is published for portfolio/viewing purposes only.
See the [LICENSE](LICENSE) file for details.
