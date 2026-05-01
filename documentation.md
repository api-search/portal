---
layout: page
title: Documentation
description: Documentation for the APIs.io JSON feeds — static data feeds for providers, APIs, capabilities, tags, schemas, and more.
---

# Documentation

The APIs.io developer portal provides static JSON feeds for every resource type indexed by the APIs.io network. This page is the starting point for integrating any of the nine feeds into your application.

## Available Feeds

{% for feed in site.data.feeds %}
### [{{ feed.name }}]({{ site.url }}/feeds/{{ feed.slug }}/)

{{ feed.description }}

- **Feed URL:** [`{{ feed.feed_url }}`]({{ feed.feed_url }})
- **Browse:** [{{ feed.url }}]({{ feed.url }})
- **Docs:** [developer.apis.io/feeds/{{ feed.slug }}/]({{ site.url }}/feeds/{{ feed.slug }}/)

{% endfor %}

---

## Feed Format

All feeds use a compact JSON array format optimized for [MiniSearch](https://lucaong.github.io/minisearch/) client-side search indexing. Field names are abbreviated to keep payload size minimal.

### Common Fields

| Field | Type | Description |
|---|---|---|
| `i` | integer | Sequential index (used as MiniSearch document ID) |
| `type` | string | Resource type: `provider`, `api`, `capability`, `tag`, etc. |
| `n` | string | Name |
| `d` | string | Description (truncated to 300 characters) |
| `t` | array | Tags |
| `u` | string | Canonical URL on the resource's subdomain |

See the individual feed documentation pages for feed-specific fields.

## Integration Examples

### Vanilla JavaScript

```javascript
async function loadFeed(feedUrl) {
  const response = await fetch(feedUrl);
  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  return response.json();
}

const providers = await loadFeed('https://providers.apis.io/search-index.json');
console.log(`Loaded ${providers.length} providers`);
```

### MiniSearch (client-side full-text search)

```javascript
import MiniSearch from 'minisearch';

const docs = await fetch('https://apis.apis.io/search-index.json').then(r => r.json());

const index = new MiniSearch({
  idField: 'i',
  fields: ['n', 'd'],
  storeFields: ['n', 'd', 'u', 't']
});

index.addAll(docs);

const results = index.search('payments REST');
results.forEach(r => console.log(r.n, r.u));
```

### Python

```python
import requests

feeds = {
    'providers': 'https://providers.apis.io/search-index.json',
    'apis': 'https://apis.apis.io/search-index.json',
    'capabilities': 'https://capabilities.apis.io/search-index.json',
}

for name, url in feeds.items():
    data = requests.get(url).json()
    print(f'{name}: {len(data)} items')
```

## APIs.json

This portal publishes an [apis.json]({{ site.url }}/apis.json) listing all nine feeds as machine-readable API definitions following the [APIs.json specification](https://apisjson.org).
