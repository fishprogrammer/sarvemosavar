# Redirects applied for all GSC/studio 404s — 2026-11-14

Catalog locked to **11 live products** (sitemap):

| slug | title |
|------|-------|
| tag-without-template | کارت تشکر (مربع و مستطیل) |
| templated-tag | کارت تشکر(با قالب اختصاصی) |
| shabrang-label | لیبل شبرنگ |
| product-property-label | برچسب اموال |
| lbl-dtf | لیبل DTF |
| lbl-pvcwhite | لیبل PVC سفید |
| lbl-egg | لیبل گچی (شکننده) |
| pvc-lable | لیبل PVC نیمه شفاف (برفکی) |
| lbl-uvdtf | لیبل UV-DTF |
| lbl-void | لیبل Void |
| leble-paper | لیبل کاغذی |

## Old WP product → new

| old | new |
|-----|-----|
| لیبل-گچی | `/shop/products/lbl-egg` |
| لیبل-شبرنگ | `/shop/products/shabrang-label` |
| لیبل-uv-dtf | `/shop/products/lbl-uvdtf` |
| لیبل-pvc-مات | `/shop/products/lbl-pvcwhite` |
| لیبل-pvc-شفاف / ونیل-شفاف / ونیل-مات | `/shop/products/pvc-lable` |
| لیبل-ژله-ای، لیبل-هلوگرام، مش، ونیل-براقی | `/shop` (no matching SKU) |

## Other buckets

- Woo `/product-category/*`, `/shop/page/*` (+ facets) → `/shop`
- `/my-account`, `/account-security*`, `/user_area` → `/login`
- `/home*`, vanity `/studio_sarv*`, author, feeds → `/` (or `/blog` for `?p=265`)
- `/چاپ-سرو-مصور` → `/about-us`
- Blog junk `/blog/{slug}/studio_sarv*` → `/blog/{slug}`
- Wrong `/products/{slug}` → `/shop/products/{slug}`

## Studio CRM status

- Built **160** candidate rules (incl. slash variants); CRM stores **~96 unique** rows (slash/query dedupe).
- Files: `SEO/05-tracking/redirect-map-sarvemosavar.csv`, `redirects-to-apply-2026-11-14.json`
- **Still blocked for Google/users until Next middleware honors CRM redirects** (see `seo-studio-live-redirects.md`).
