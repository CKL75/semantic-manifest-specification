# The Semantic Manifest

**Version 0.1 | Draft Specification**

---

## What Is a Semantic Manifest?

A Semantic Manifest is a structured, machine-readable index of a website's content, designed to be discovered and consumed by AI crawlers, large language models, and retrieval-augmented generation (RAG) systems.

It complements existing web standards — `sitemap.xml` for crawl discovery, `robots.txt` for crawl instructions, `llms.txt` for high-level site description — by providing a complete, semantic content graph: every page, what it is, what it means, how it relates to other pages, and where its AI-optimized representation lives.

---

## The Problem It Solves

Search and AI systems need to understand not just *that* a page exists, but *what it is*, *what type of content it contains*, and *how it connects to other content* on the same site.

Current standards don't provide this:

- `sitemap.xml` — URLs only, no content type or semantic meaning
- `llms.txt` — flat text, no structure, no per-page data
- JSON-LD in `<head>` — per-page only, no site-wide graph
- RSS/Atom — chronological feeds, not content graphs

A Semantic Manifest fills this gap with a single, streamable, site-wide file that AI systems can parse efficiently at any scale.

---

## File Format

The Semantic Manifest is a valid [NDJSON](https://github.com/ndjson/ndjson-spec) file — one JSON object per line, no blank lines. Each line represents one page or content entity on the site.

URL structure is implementation-defined. The Semantic Manifest specification does not mandate any particular URL pattern, path structure, or routing convention for canonical or md values. Implementers are free to organize content URLs however suits their site — flat paths, nested categories, query parameters, or any other scheme — as long as each value is a valid, resolvable absolute URL.

**Filename:** `semantic-manifest.jsonl`  
**Location:** Served from the site root at `/semantic-manifest.jsonl`  
**Encoding:** UTF-8  
**Content-Type:** `application/x-ndjson`

---

## Discovery

Link to the manifest from the `<head>` of the site's root URL:

```html
<link rel="alternate" type="application/x-ndjson" href="https://example.com/semantic-manifest.jsonl" title="Semantic Manifest">
```

This follows the same pattern as RSS/Atom feed discovery, making it immediately parseable by crawlers that already understand `<link rel="alternate">`.

---

## Fields

### Required

| Field | Type | Description |
|---|---|---|
| `name` | string | Human-readable name of the page or entity |
| `canonical` | string (URL) | The authoritative URL for this page |
| `type` | string | Content type, using schema.org vocabulary where applicable |

### Recommended

| Field | Type | Description |
|---|---|---|
| `md` | string (URL) | URL of the markdown twin — a clean, AI-optimized plain-text rendering of this page's content |
| `description` | string | Brief summary of the page's content |

### Optional

| Field | Type | Description |
|---|---|---|
| `entities` | array of URLs | Canonical URLs of related pages this content references or depends on |
| `dateModified` | string (ISO 8601) | Last modified date |
| `language` | string (BCP 47) | Language of the content, e.g. `en`, `fr`, `de` |
| `license` | string (URL) | URL to the content license, if applicable |

---

## The `md` Field and Markdown Twins

The `md` field is the most important optional field and is strongly recommended for all implementations.

A **markdown twin** is a clean, structured, plain-text rendering of a page's content — stripped of navigation, advertising, and presentational markup — served at a predictable URL. AI systems and LLMs consume markdown twins far more efficiently than raw HTML.

The recommended pattern is a query parameter on the canonical URL:

```
https://example.com/page/?format=md
```

This keeps markdown twins discoverable, cacheable, and trivially generated from existing templates without additional routing infrastructure.

---

## The `type` Field

The `type` field should use [schema.org](https://schema.org) vocabulary where a suitable type exists:

- `Article`
- `Product`
- `Person`
- `Organization`
- `Place`
- `Event`
- `FAQPage`
- `HowTo`
- `Review`
- `Comparison` *(proposed extension — no schema.org equivalent)*

For content types with no schema.org equivalent, use a descriptive PascalCase string and document it in your implementation.

---

## The `entities` Field

The `entities` field expresses relationships between pages using canonical URLs as identifiers. This makes the manifest a traversable content graph rather than a flat list.

Use `entities` when a page's meaning depends on or references other pages on the same site — comparisons, collections, category pages, or relational content of any kind.

```json
{
  "name": "Product A vs Product B",
  "canonical": "https://example.com/compare/product-a-vs-product-b/",
  "md": "https://example.com/compare/product-a-vs-product-b/?format=md",
  "type": "Comparison",
  "entities": [
    "https://example.com/products/product-a/",
    "https://example.com/products/product-b/"
  ],
  "description": "Side-by-side comparison of Product A and Product B."
}
```

---

## Example Entries

### Simple content page

```json
{"name":"Getting Started with Node.js","canonical":"https://example.com/guides/nodejs-getting-started/","md":"https://example.com/guides/nodejs-getting-started/?format=md","type":"Article","description":"A beginner's guide to setting up and running Node.js for the first time."}
```

### Product or listing page

```json
{"name":"Acme Pro 3000","canonical":"https://example.com/products/acme-pro-3000/","md":"https://example.com/products/acme-pro-3000/?format=md","type":"Product","description":"Industrial-grade widget with 3000-series titanium construction."}
```

### Comparison or relational page

```json
{"name":"Acme Pro 3000 vs Acme Pro 2000","canonical":"https://example.com/compare/acme-pro-3000-vs-acme-pro-2000/","md":"https://example.com/compare/acme-pro-3000-vs-acme-pro-2000/?format=md","type":"Comparison","entities":["https://example.com/products/acme-pro-3000/","https://example.com/products/acme-pro-2000/"],"description":"Side-by-side comparison of the Acme Pro 3000 and Acme Pro 2000."}
```

### Person or profile page

```json
{"name":"Jane Smith","canonical":"https://example.com/authors/jane-smith/","md":"https://example.com/authors/jane-smith/?format=md","type":"Person","description":"Jane Smith is a senior contributor covering climate science and energy policy."}
```

### Location or place page

```json
{"name":"Chicago Office","canonical":"https://example.com/locations/chicago/","md":"https://example.com/locations/chicago/?format=md","type":"Place","description":"Our Chicago office serving the greater Midwest region."}
```

---

## Implementation Notes

**Scalability:** NDJSON is streamable. Crawlers can begin parsing before the file finishes downloading, making the format suitable for sites with hundreds of thousands of pages.

**Caching:** The manifest should be regenerated on content publish or update events. For large sites, incremental regeneration (appending or updating individual lines) is preferable to full rebuilds.

**No blank lines:** Valid NDJSON contains exactly one JSON object per line with no blank lines between entries.

**Ordering:** No required ordering. Implementations may order by date modified, content type, or site hierarchy as appropriate.

**Subset manifests:** Large sites may optionally serve per-section manifests (e.g. `/products/semantic-manifest.jsonl`) and link to them from section-level pages, in addition to the root manifest.

---

## Relationship to Other Standards

| Standard | Purpose | Semantic Manifest relationship |
|---|---|---|
| `sitemap.xml` | URL discovery for crawlers | Complementary — sitemap for crawl scheduling, manifest for semantic understanding |
| `robots.txt` | Crawl permissions | Unchanged — robots.txt governs whether crawlers access the manifest |
| `llms.txt` | High-level site description for LLMs | Complementary — llms.txt describes the site, manifest describes every page |
| JSON-LD | Per-page structured data | Complementary — JSON-LD in `<head>` for search engines, manifest for AI systems needing site-wide context |
| RSS/Atom | Chronological content feeds | Different purpose — feeds for subscription, manifest for content graph |

---

## Reference Implementation

Hypersonic SEO ([hypersonicseo.com](https://hypersonicseo.com)) and EduStats ([edustats.app](https://edustats.app)) are the first known live implementations of the Semantic Manifest specification. Hypersonic SEO serves as the primary marketing and services platform for the High-Velocity Content Engine (HVCE), of which the Semantic Manifest is a native, built-in structural component, while EduStats demonstrates production scale, covering approximately 58,000 higher education institution and comparison pages built from IPEDS data.

The manifests are discoverable at:  
[https://hypersonicseo.com/semantic-manifest.jsonl](https://hypersonicseo.com/semantic-manifest.jsonl)
[https://edustats.app/semantic-manifest.jsonl](https://edustats.app/semantic-manifest.jsonl)

---

## Versioning

This is version 0.1 of the Semantic Manifest specification. The format is intentionally minimal. Future versions may define additional standard fields, subset manifest linking, and crawler directives.

Feedback and proposed extensions are welcome.

---

*Semantic Manifest specification proposed by Chris Limner. Reference implementation live at hypersonicseo.com and edustats.app.*
