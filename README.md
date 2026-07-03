# The Semantic Manifest Specification

An open data standard and streamable content graph specification (NDJSON) designed to optimize website discovery, content relations, and text ingestion for AI crawlers, LLMs, and RAG engines.

### The Problem
Traditional web standards are fundamentally broken for AI search engines at scale. 
* **Sitemaps** pass raw URLs without structural context.
* **JSON-LD** is trapped in single-page scopes.
* **llms.txt** files are flat text that consume massive amounts of context window tokens when scaling to thousands of pages.

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
* **Hypersonic SEO Framework:** [hypersonicseo.com/semantic-manifest.jsonl](https://hypersonicseo.com/semantic-manifest.jsonl)

## License & Authorship

Semantic Manifest Specification (v0.1) © 2026 by Chris Limner / Hypersonic SEO.
This work is marked with [CC0 1.0 Universal](LICENSE).