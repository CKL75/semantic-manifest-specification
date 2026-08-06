# The Semantic Manifest Specification

An open data standard and streamable content graph specification (NDJSON) designed to optimize website discovery, content relations, and text ingestion for AI crawlers, LLMs, and RAG engines.

### The Problem
Traditional web standards were not built to support AI search engines at scale. Existing protocols introduce significant friction when handling modern automated ingestion:
* **Sitemaps** Sitemaps pass raw URLs without structural or relational context.
* **JSON-LD** JSON-LD is isolated to single-page scopes, preventing site-wide graph synthesis.
* **llms.txt** llms.txt files rely on flat text, which rapidly consumes critical context window tokens when scaling across thousands of pages.

### The Solution
The **Semantic Manifest** bridges this gap. It uses a streamable NDJSON format so AI crawlers can parse an entire site's content types, relational entities, and explicitly designated "markdown twins" line-by-line efficiently.

## System Performance Demo

Within hours of launching a ~58,000-page site with a Semantic Manifest linked in the `<head>`, ClaudeBot ingested the entire site at ~7 URLs per second.

📺 **[Watch the System Performance & Crawl Log Demo Video Here](https://youtu.be/gyxrnGQOkjg)**

## Core Documentation

* 📖 **[Read the Full Specification (v0.1)](semantic-manifest-specification.md)**
* 📄 **[View the Reference JSONL](semantic-manifest.jsonl)**

## Reference Implementations

This standard is a native, built-in structural component of the High-Velocity Content Engine (HVCE). 

Live production manifests can be viewed at:
* **EduStats (58,000 pages):** [edustats.app/semantic-manifest.jsonl](https://edustats.app/semantic-manifest.jsonl).
* **Hypersonic SEO Framework:** [structuredpages.com/semantic-manifest.jsonl](https://structuredpages.com/semantic-manifest.jsonl)

## License & Authorship

Semantic Manifest Specification (v0.1) © 2026 by Chris Limner / Structured Pages.
This work is marked with [CC0 1.0 Universal](LICENSE).
