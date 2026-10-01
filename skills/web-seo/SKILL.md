---
name: web-seo
description: >-
  Expert technical and on-page SEO for crawlability, indexability, canonicalization, metadata,
  semantic HTML, schema, internal linking, sitemaps, robots, Core Web Vitals, and search audits.
  Use for public pages, migrations, content templates, discovery defects, and search strategy.
---

# Web SEO - Expert

## Mental model

SEO is making a useful, eligible document easy for search engines and users to discover,
understand, select, render, and trust. It is a system, not title-tag copywriting. A page must
be crawlable before it can be indexed, indexable before it can rank, and useful before ranking
creates durable value.

Model the pipeline:

```
discovery -> crawl -> render -> index selection -> query matching -> result selection -> user value
```

Debug the earliest broken stage. Improving keyword density cannot fix a blocked crawler. Adding
schema cannot fix duplicate canonical signals. Better title copy cannot make thin content useful.

## Intent and page role

Before implementation, classify query intent: informational, navigational, commercial research,
transactional, or local. Then choose a page role: home, category, product, feature, article,
documentation, profile, or tool. One URL should have one primary search purpose.

| Intent | Page shape | Wrong choice cost |
|---|---|---|
| Informational | Direct answer, evidence, internal next steps | User bounces after vague marketing |
| Commercial research | Comparison, constraints, proof | Product page misses evaluation need |
| Transactional | Price, availability, clear CTA | Friction blocks conversion |
| Navigational | Clear brand or destination signals | Duplicate pages split relevance |

Do not create pages merely to target phrase variants. Consolidate near-duplicate intent into a
strong canonical page unless users truly need different content, inventory, locale, or action.

## Crawlability and indexability

Crawlability means bots can discover and fetch URL. Indexability means page is eligible for
search index. Keep these signals aligned:

- Return 200 for useful canonical content, 301 for permanent moved URL, 404 or 410 for absent URL.
- Use crawlable `<a href>` links for important navigation; buttons do actions.
- Do not require client interaction or scroll to expose primary indexable content.
- Do not block necessary CSS, JS, or image resources from rendering.
- Use `noindex` for pages intentionally excluded; do not list them in XML sitemap.
- Keep robots rules, meta robots, canonicals, and sitemap mutually consistent.
- Avoid redirect chains. One redirect from old URL to final canonical is preferred.

Soft 404 is a 200 page that tells users content is missing. It wastes crawl and creates bad
index signals. Return proper status for actual absence.

## Canonicalization framework

Canonical is a hint for duplicate or substantially similar pages, not a traffic-routing tool.
Use absolute preferred HTTPS URL. Self-canonicalize unique indexable pages. Canonical duplicate
parameters, sort variants, print pages, or alternate paths to one preferred document.

Do not canonicalize a page to a URL with different intent, language, pagination meaning, or
substantially different content. Do not send sitemap URL, `og:url`, internal links, and canonical
to competing variants such as `http`, `www`, trailing slash, or tracking parameter versions.

For locale, use separate stable URLs and reciprocal `hreflang` entries including `x-default` when
appropriate. Every target must be canonical, indexable, and actually language-appropriate.

## Metadata as a system

Title, description, heading, visible introduction, canonical, Open Graph, and structured data
must describe same page truth. Metadata does not compensate for mismatched visible content.

- Title: unique, specific topic plus brand where useful. Often 50-60 characters, but accuracy
  beats a character target because search engines can rewrite titles.
- Description: concise human summary, often 120-155 characters. Treat as invitation, not rank factor.
- One logical H1 per primary document. Use headings to structure content, not to style text.
- `og:title`, `og:description`, `og:url`, and `og:image` control social sharing, not ranking.
- Use 1200 by 630px Open Graph image as common starting dimension, with meaningful crop and alt.

Generate metadata from content model only if page-specific values exist. Template reuse is fine;
copying generic text across thousands of pages is not.

## Semantic HTML and internal links

Semantic HTML gives search and assistive technologies structural evidence: `main`, `nav`, `article`,
headings, lists, tables, links, captions, image alt, and clear document language.

Use descriptive anchors that communicate destination: "Compare team plans" rather than "Learn more."
Link important pages from relevant pages using natural context. A sitemap is discovery support, not
substitute for internal information architecture. Find and repair orphan pages.

Alt text explains image purpose in surrounding context. Do not stuff keywords. Decorative images
use empty alt. Product images need meaningful attributes that user cannot infer from nearby text.

## Structured data

Schema is a factual annotation layer, not a ranking potion. Add JSON-LD only when visible page
content supports every material field and page qualifies for that schema type.

Good candidates: Organization, Product, BreadcrumbList, Article, Event, LocalBusiness, FAQ when
visible FAQ content exists, and software app information when accurate. Validate syntax and
eligible rich results. Do not invent reviews, prices, availability, authors, or FAQs.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "Acme Planner",
  "description": "Route planning software for dispatch teams"
}
</script>
```

## Core Web Vitals

Field targets at 75th percentile:

| Metric | Good | What moves it |
|---|---:|---|
| LCP | <= 2.5s | Fast server response, LCP image preload, correct image size |
| INP | <= 200ms | Reduce long tasks, defer noncritical JS, cheap handlers |
| CLS | <= 0.1 | Reserve media and ad space, avoid late font or content shifts |

Measure field data where available, then lab data to reproduce. Do not optimize a lighthouse
score while ignoring actual slow route, device, or user flow. Lazy-loading hero image, adding
unused preloads, and hiding content to improve score are SEO theater.

## Sitemap and robots

XML sitemap contains canonical, indexable URLs users should find. Include accurate `lastmod`
only when meaningful content changed. Split very large sitemaps. Reference sitemap from robots
or submit it through search tooling. Robots controls crawling, not reliable removal from index:
use `noindex` when page can be crawled, removal tools when urgent.

## Failure modes experts catch

- `noindex` page appears in sitemap and canonical points elsewhere.
- Client-side route has no server-rendered main content.
- Every title starts with brand and hides topic in truncated result.
- Pagination canonicalizes all pages to page one, erasing deep inventory.
- URL migration creates chains, loops, or old internal links.
- Schema claims rating or availability unavailable to user.
- Core page has huge LCP image with missing `sizes` or width and height.
- Keyword-targeted pages cannibalize each other because intent overlaps.

## Critique rubric

Reject SEO work when:

1. Target user intent and page role are undefined.
2. A crawler cannot reach, fetch, render, or understand primary content.
3. Canonical, internal links, sitemap, robots, and Open Graph disagree.
4. Metadata is generic, duplicated, misleading, or disconnected from visible content.
5. Schema contains claims absent from page or unsupported by product data.
6. Performance work does not identify metric, route, and causal bottleneck.
7. Strategy uses hidden text, link schemes, keyword stuffing, or bot-only content.

## Refs

- https://developers.google.com/search/docs
- https://schema.org/docs/schemas.html
- https://web.dev/vitals/
- https://ogp.me/
