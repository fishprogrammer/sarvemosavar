# GSC Indexing + Migration 404 — sarvemosavar.ir

**بررسی:** 2026-11-14 19:29 · آخرین آپدیت GSC: 2026-11-09

- Indexed: **30**
- Not indexed: **282** (۵ دلیل)

## دلایل Not indexed

| دلیل | تعداد | منبع |
|------|-------|------|
| Crawled - currently not indexed | 112 | Google systems |
| Alternate page with proper canonical tag | 65 | Website |
| Not found (404) | 55 | Website |
| Excluded by noindex tag | 26 | Website |
| Page with redirect | 24 | Website |

## Not found (404) — ۵۵ صفحه

اولین تشخیص: **2026-10-17** (هم‌زمان با مهاجرت)

### نمونه‌ها (WP/WooCommerce قدیمی)

- `https://sarvemosavar.ir/product/لیبل-ژله-ای/`
- `https://sarvemosavar.ir/product/لیبل-pvc-مات/`
- `https://sarvemosavar.ir/product/لیبل-گچی/`
- `https://sarvemosavar.ir/product-category/outdoor/?per_row=3&per_page=18&shop_view=grid`
- `https://sarvemosavar.ir/shop/page/3/?per_row=2&per_page=9&shop_view=grid`
- `https://sarvemosavar.ir/چاپ-سرو-مصور/`
- `https://sarvemosavar.ir/frequently-asked-questions/`
- `https://sarvemosavar.ir/my-account/`

### الگوی اقدام

1. مپینگ کامل ۵۵ URL
2. ۳۰۱ به مسیرهای جدید React/Django
3. پاکسازی query ووکامرس
4. Validate fix در GSC

## مسیرهای جدید سالم (نمونه)

- `https://sarvemosavar.ir/shop`
- `https://sarvemosavar.ir/shop/products/leble-paper`
- `https://sarvemosavar.ir/blog`
- `https://sarvemosavar.ir/about-us`
- `https://sarvemosavar.ir/contact`

## پروب زنده مسیرهای WP (تأییدشده)

| الگو | وضعیت | پیشنهاد |
|------|--------|---------|
| /product/ /product-category/ | 404 | 301 → /shop یا /shop/products/{slug} |
| /my-account/ | 404 | 301 → /login |
| /category/ /tag/ /feed/ | 404 | 301 → /blog یا 410 |
| /wp-admin/ /xmlrpc.php /sample-page/ | 404 | 410 |
| /shop/ /cart/ /checkout/ /blog/ | 200 | نگه دارید |
| /?p=1 | 200 (SPA) | مپ ID پست‌های قدیمی لازم است |

Sitemap فعلی: **27** URL (۱۱ محصول + ۱۳ بلاگ + خانه/فروشگاه/وبلاگ).

جدول شروع مپینگ: SEO/05-tracking/redirect-map-sarvemosavar.csv

