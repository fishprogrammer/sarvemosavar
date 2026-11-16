# SEO Studio live capability — 2026-11-14

Target: `https://sarvemosavar.ir/seo/studio/404` (+ redirects / settings)

## Verdict

Studio **can write** redirects/meta to CRM, but public Next.js **does not yet honor** those redirects at the edge. Creating a 301 in Studio ≠ live HTTP 301 for visitors/Googlebot.

## What works (live)

| Area | Status | Notes |
|------|--------|-------|
| Auth + Studio UI | OK | Staff session can open `/seo/studio/*` |
| 404 monitor | OK | Live hits (e.g. `/studio_sarv_ir/` ×12, Woo `/product/…`, `/shop/page/3/`, Google referers) |
| Create redirect (UI) | OK | Form: `rd-source`, `rd-target`, `rd-code` (301/302), active |
| CRM API create | OK | `POST https://api.crm.sarvemosavar.ir/api/seo/redirects/` → 201 |
| Settings | OK | Site/org name, title template, robots, IndexNow, sitemap toggles |

### Test rows created in Studio (active)

1. `/my-account/` → `/login` · 301  
2. `/frequently-asked-questions/` → `/` · 301  

Payload example:

```json
{
  "source_path": "/frequently-asked-questions/",
  "target_url": "/",
  "status_code": 301,
  "is_active": true
}
```

Response code: `seo_redirect_created` (HTTP 201).

## What does NOT work yet (blocker)

Public HEAD/GET for the same paths still return **HTTP 404**:

- `/my-account/` → 404 · `X-Path-Class: protected`
- `/frequently-asked-questions/` → 404 · `X-Path-Class: protected`
- `/product/لیبل-گچی/` → 404 · `X-Path-Class: protected`
- `/shop/page/3/` → 404 · `X-Path-Class: standalone`

Redirect hit counter in Studio stays **0** (rules never matched on the edge).

**Root cause (inferred):** Next middleware/path-class returns 404 before consulting CRM redirect table (or redirect lookup not wired / not cached to edge).

## UI limits

- Status codes in form: **301 / 302 only** (no 410 for feeds/author archives).
- Query-string sources (Woo facets) must be stored as full `source_path` including `?…` — edge matcher must support exact query match or strip rules.

## Immediate engineering ask

1. Wire Next middleware (or edge config) to active CRM redirects **before** path-class 404.
2. Cache/list endpoint for edge (low latency); invalidate on create/update/delete.
3. After wire: re-verify test rows return 301 Location, then bulk-load `SEO/05-tracking/redirect-map-sarvemosavar.csv` (~50 mapped, 5 NEEDS_MAP).
4. Optionally add 410 support for `/…/feed/` and author archives.

## Do not bulk-import yet

Until edge honors CRM redirects, filling the table only stores DB rows — GSC 404s will not clear.
