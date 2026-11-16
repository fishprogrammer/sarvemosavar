# Performance — https://sarvemosavar.ir/

**نمره:** 42/100 · اندازه‌گیری کنسول مرورگر

| متریک | مقدار |
|--------|--------|
| TTFB | ~773ms |
| DCL | ~1448ms |
| Load | ~2736ms |
| LCP | **~6224ms** ❌ |
| CLS | ~0 ✅ |

## ریشه LCP
- عنصر LCP: تصویر اسلاید «بلک فرایدی»
- لوگو برند ابعاد **۴۹۶۱×۳۵۰۸** از `api.crm.sarvemosavar.ir`

## اقدام
1. اسلایدر/لوگو → WebP با عرض واقعی نمایش + `fetchpriority=high` فقط برای LCP
2. SSR/prefetch دادهٔ featured به‌جای waterfall XHR
3. بررسی cache تصاویر CRM
