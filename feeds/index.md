---
layout: page
title: APIs.io JSON Feeds
description: Static JSON feeds for every resource type indexed by APIs.io — providers, APIs, capabilities, tags, schemas, AsyncAPI specs, JSON-LD contexts, rules, and vocabularies.
---

# APIs.io JSON Feeds

The APIs.io network publishes static JSON feeds for every resource type it indexes. Each feed is a MiniSearch-compatible JSON array served directly from the resource's subdomain. No authentication required — all feeds are public and freely consumable.

<div class="row row-cols-1 row-cols-md-2 g-4 mt-2">
{% for feed in site.data.feeds %}
<div class="col">
  <div class="feed-card h-100">
    <h5><a href="{{ site.url }}/feeds/{{ feed.slug }}/">{{ feed.name }}</a></h5>
    <p class="text-muted mb-2">{{ feed.description | truncate: 120 }}</p>
    <div class="mb-2">
      {% for tag in feed.tags %}<span class="badge bg-secondary me-1">{{ tag }}</span>{% endfor %}
    </div>
    <div class="d-flex gap-2">
      <a href="{{ feed.feed_url }}" class="btn btn-sm btn-outline-primary" target="_blank" rel="noopener">JSON Feed</a>
      <a href="{{ feed.url }}" class="btn btn-sm btn-outline-secondary" target="_blank" rel="noopener">Browse</a>
      <a href="{{ site.url }}/feeds/{{ feed.slug }}/" class="btn btn-sm btn-outline-dark">Docs</a>
    </div>
  </div>
</div>
{% endfor %}
</div>

---

## Feed Format

All feeds use a compact JSON array format optimized for [MiniSearch](https://lucaong.github.io/minisearch/) client-side indexing. Field names are abbreviated to keep feed size small:

| Field | Type | Present in | Description |
|---|---|---|---|
| `i` | integer | all | Sequential index position |
| `type` | string | all | Resource type (provider, api, capability, tag, etc.) |
| `n` | string | all | Name |
| `d` | string | most | Description (truncated to 300 chars) |
| `t` | array | most | Tags |
| `u` | string | all | Detail page URL |
| `ac` | integer | providers, tags | API count |
| `cc` | integer | providers | Capability count |
| `pc` | integer | tags | Provider count |

## Using the Feeds

### Fetch and search

```javascript
import MiniSearch from 'minisearch';

const res = await fetch('https://providers.apis.io/search-index.json');
const docs = await res.json();

const index = new MiniSearch({ fields: ['n', 'd', 't'], storeFields: ['n', 'u', 'ac'] });
index.addAll(docs);

const results = index.search('payments');
```

### Fetch and display

```javascript
const res = await fetch('https://capabilities.apis.io/search-index.json');
const capabilities = await res.json();

capabilities.forEach(cap => {
  console.log(cap.n, cap.u);
});
```

## RFC 9727 API Catalog

Each subdomain also publishes a machine-readable API catalog at `/.well-known/api-catalog` per [RFC 9727](https://www.rfc-editor.org/rfc/rfc9727):

- `https://providers.apis.io/.well-known/api-catalog`
- `https://apis.apis.io/.well-known/api-catalog`
- `https://capabilities.apis.io/.well-known/api-catalog`
