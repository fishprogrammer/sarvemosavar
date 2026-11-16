# چطور middleware لبه را به ریدایرکت‌های استودیو وصل کنیم

## مشکل فعلی

1. استودیو SEO قوانین را در CRM می‌نویسد: `POST/GET api.crm.sarvemosavar.ir/api/seo/redirects/`
2. Next روی درخواست عمومی قبل از هر چیز `X-Path-Class` می‌زند و برای مسیرهای ناشناس **404** برمی‌گرداند
3. جدول ریدایرکت هرگز خوانده نمی‌شود → شمارنده بازدید = ۰

## ترتیب درست در middleware

```
درخواست → [1] lookup ریدایرکت CRM  → اگر match: 301/302 و تمام
         → [2] منطق فعلی path-class / auth / 404
```

ریدایرکت باید **قبل** از بلاک `protected` و صفحه ۴۰۴ اجرا شود.

## پیش‌نیاز CRM (بک‌اند)

یک endpoint **عمومی یا با کلید سرور** برای لیست فعال‌ها (بدون session ادمین):

```
GET /api/seo/redirects/active/
# یا همان GET /api/seo/redirects/?is_active=true با header: X-SEO-Edge-Key
```

خروجی پیشنهادی:

```json
{
  "data": [
    {
      "source_path": "/my-account/",
      "target_url": "/login",
      "status_code": 301,
      "is_active": true
    }
  ]
}
```

بدون این endpoint، middleware نمی‌تواند با توکن ادمین استودیو به CRM بچسبد.

## کد نمونه Next (`middleware.ts` در ریشهٔ فرانت)

```ts
import { NextRequest, NextResponse } from "next/server";

type SeoRedirect = {
  source_path: string;
  target_url: string;
  status_code: 301 | 302;
  is_active: boolean;
};

let cache: { at: number; map: Map<string, SeoRedirect> } | null = null;
const TTL_MS = 60_000; // 1 دقیقه؛ بعد از ذخیره در استودیو حداکثر ۱ دقیقه تأخیر

async function loadRedirectMap(): Promise<Map<string, SeoRedirect>> {
  if (cache && Date.now() - cache.at < TTL_MS) return cache.map;

  const res = await fetch(process.env.SEO_REDIRECTS_URL!, {
    headers: {
      Accept: "application/json",
      "X-SEO-Edge-Key": process.env.SEO_EDGE_KEY || "",
    },
    // Edge: کش کوتاه؛ روی خطای شبکه از کش قبلی استفاده کن
    cache: "no-store",
  });

  if (!res.ok) return cache?.map ?? new Map();

  const json = await res.json();
  const rows: SeoRedirect[] = json.data ?? json.results ?? json;
  const map = new Map<string, SeoRedirect>();

  for (const r of rows) {
    if (!r?.is_active || !r.source_path) continue;
    map.set(normalizeKey(r.source_path), r);
    // واریانت اسلش برای مسیرهای بدون کوئری
    if (!r.source_path.includes("?")) {
      const alt = r.source_path.endsWith("/")
        ? r.source_path.slice(0, -1)
        : r.source_path + "/";
      if (!map.has(normalizeKey(alt))) map.set(normalizeKey(alt), r);
    }
  }

  cache = { at: Date.now(), map };
  return map;
}

function normalizeKey(pathWithOptionalQuery: string): string {
  // decode برای فارسی؛ lower فقط روی host نه لازم
  try {
    return decodeURIComponent(pathWithOptionalQuery);
  } catch {
    return pathWithOptionalQuery;
  }
}

function requestKey(req: NextRequest): string {
  const path = req.nextUrl.pathname;
  const q = req.nextUrl.search; // شامل ?
  return normalizeKey(q ? `${path}${q}` : path);
}

export async function middleware(req: NextRequest) {
  // ۱) ریدایرکت‌های استودیو — اول از همه
  try {
    const map = await loadRedirectMap();
    const hit = map.get(requestKey(req));
    if (hit) {
      const dest = hit.target_url.startsWith("http")
        ? hit.target_url
        : new URL(hit.target_url, req.nextUrl.origin).toString();
      return NextResponse.redirect(dest, hit.status_code || 301);
    }
  } catch {
    // fail-open: سایت نشکند
  }

  // ۲) منطق فعلی شما (path-class / auth / …)
  // return existingPathClassLogic(req)
  return NextResponse.next();
}

export const config = {
  matcher: [
    "/((?!_next/static|_next/image|favicon.ico|.*\\.(?:svg|png|jpg|css|js|woff2)$).*)",
  ],
};
```

### env فرانت

```
SEO_REDIRECTS_URL=https://api.crm.sarvemosavar.ir/api/seo/redirects/active/
SEO_EDGE_KEY=...همان کلید سرور...
```

## نکات match برای مپ فعلی سرو مصور

| نوع مبدأ در استودیو | باید match شود با |
|---------------------|-------------------|
| `/my-account/` | pathname با/بدون `/` |
| `/shop/page/2/?per_page=12` | **pathname + query کامل** (exact) |
| `/product/لیبل-گچی/` | URL-decoded فارسی |
| `/?p=265` | `pathname=/` + `search=?p=265` |

اگر فقط `pathname` را چک کنید، فست‌های ووکامرس و `?p=` از دست می‌روند.

## تست بعد از دیپلوی

```bash
curl -sI https://sarvemosavar.ir/my-account/
# انتظار: HTTP/1.1 301  + Location: https://sarvemosavar.ir/login

curl -sI "https://sarvemosavar.ir/product/%D9%84%DB%8C%D8%A8%D9%84-%DA%AF%DA%86%DB%8C/"
# انتظار: 301 → /shop/products/lbl-egg
```

در استودیو، بعد از یک hit واقعی، ستون «بازدید» باید از ۰ بالا برود (اگر بک‌اند increment دارد؛ در غیر این صورت جداگانه `POST .../hit` لازم است).

## گزینهٔ سریع‌تر (موقت)

اگر دیپلوی middleware طول می‌کشد، همان CSV را موقتاً در Nginx/`next.config` redirects بگذارید — ولی منبع حقیقت باید همان جدول استودیو بماند.

## صاحب کار

- **بک‌اند CRM:** endpoint لیست active + (اختیاری) hit counter  
- **فرانت Next:** تغییر ترتیب middleware + کش TTL  
- **SEO:** بعد از سبز شدن `curl -I`، در GSC روی چند URL مهم Validate fix
