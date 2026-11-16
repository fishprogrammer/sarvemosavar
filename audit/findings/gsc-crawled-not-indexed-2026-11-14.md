# GSC: Crawled — currently not indexed (2026-11-14)

Drilldown: [Search Console](https://search.google.com/search-console/index/drilldown?resource_id=https%3A%2F%2Fsarvemosavar.ir%2F&item_key=CAMYFyAC)

| Field | Value |
|-------|-------|
| Affected | **112** |
| First detected | 2026-10-17 |
| Last GSC update | 2026-11-09 |
| Validate fix | available (do **not** treat junk URLs as “fixed to index”) |

## Mix (examples table + scrape)

| Bucket | ~Count | Action |
|--------|--------|--------|
| Woo `/product-category/*` + facets | **56** | 301 → `/shop` (strip `per_row`/`per_page`/`shop_view`) |
| Woo `/shop/?…` + `/shop/page/*?…` | **44** | 301 → `/shop` |
| Real blog posts | **7** | Keep; strengthen links + **Request indexing** |
| Legacy `/feed/` / author / hello-world | **5** | 301 → `/` or `/shop` |

### Blog URLs that deserve indexing (crawled Nov 10)

All live `200`, `index,follow`, solid copy (~2–3.5k words):

- `/blog/white-pvc-label` (+ 6 siblings)

**URL Inspection (white-pvc-label, no slash):**  
`URL is not on Google` because **Alternate page with proper canonical tag**.  
User-declared canonical was `…/blog/white-pvc-label/` (with slash). Google treats the non-slash URL as alternate.

Fix shipped (`f2ba528`): force canonicals without trailing slash + proxy 301 `/blog/slug/` → `/blog/slug`.  
Indexing request already available as “Request again” on that inspect page.

## Why Google leaves most of these out

1. **Soft 404 / thin duplicates** — hundreds of facet permutations of the same shop/category.
2. **Low value** — feeds and WP leftovers.
3. **Discovery without ranking value** — blogs are fine technically; Google crawled but chose not to serve yet (new stack / thin internal links / competition). Not a hard block.

## Engineering done / next

- Proxy: strip Woo facets → `/shop`; collapse blog trailing slash; kill `/feed` paths.
- SEO: internal links from home/shop to the 7 blogs; Request indexing per URL in GSC.
- Do **not** expect Validate Fix on this reason to “index” facet junk — after 301s they should leave this bucket as redirects.
