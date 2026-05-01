---
layout: page
title: Developer APIs.io
description: Static JSON feeds and developer resources for the APIs.io network — providers, APIs, capabilities, tags, schemas, and more.
---

<div class="text-center mb-5">
  <h1 class="display-5 fw-bold">APIs.io Developer Portal</h1>
  <p class="lead text-muted">Static JSON feeds for every resource type indexed by the APIs.io network. No authentication. No rate limits. Just data.</p>
  <a href="/feeds/" class="btn btn-primary btn-lg me-2">Browse Feeds</a>
  <a href="https://apis.io" class="btn btn-outline-secondary btn-lg">apis.io &rarr;</a>
</div>

---

## Available Feeds

{% for feed in site.data.feeds %}
<div class="feed-card">
  <div class="d-flex justify-content-between align-items-start">
    <div>
      <h5 class="mb-1"><a href="{{ site.url }}/feeds/{{ feed.slug }}/">{{ feed.name }}</a></h5>
      <p class="text-muted mb-2">{{ feed.description | truncate: 140 }}</p>
      <div>{% for tag in feed.tags %}<span class="badge bg-secondary me-1">{{ tag }}</span>{% endfor %}</div>
    </div>
    <div class="ms-3 flex-shrink-0">
      <a href="{{ feed.feed_url }}" class="btn btn-sm btn-outline-primary me-1" target="_blank" rel="noopener">JSON</a>
      <a href="{{ site.url }}/feeds/{{ feed.slug }}/" class="btn btn-sm btn-outline-dark">Docs</a>
    </div>
  </div>
</div>
{% endfor %}

---

## About

The APIs.io network indexes thousands of APIs, providers, capabilities, and related resources across a set of specialized subdomains. Each subdomain publishes a `search-index.json` — a compact JSON array optimized for client-side search with [MiniSearch](https://lucaong.github.io/minisearch/).

This developer portal documents each feed as an API, describes the response schema, and provides code examples for consuming the feeds in JavaScript, Python, and other languages.

All feeds are:
- **Public** — no API key or registration required
- **Static** — served directly from GitHub Pages via Cloudflare
- **CORS-enabled** — consumable directly from the browser
- **RFC 9727 compatible** — each subdomain publishes `/.well-known/api-catalog`

The portal itself is open source at [github.com/api-search/developer-portal](https://github.com/api-search/developer-portal).
