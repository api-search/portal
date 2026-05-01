---
layout: page
title: About
description: About the APIs.io developer portal and its static JSON feeds.
---

# About

This is the developer portal for the [APIs.io](https://apis.io) network — the API search engine for the open web.

## What This Portal Provides

APIs.io has evolved from a search engine backed by live APIs into a fully static network. The search engine, provider catalog, capabilities registry, and all other indexes are now published as static sites hosted on GitHub Pages and served via Cloudflare.

This developer portal documents those static sites as if they were APIs — because for the purposes of integration, they are. Each subdomain publishes a `search-index.json` that can be fetched, parsed, and searched directly by any application. No authentication. No rate limits. No API key required.

The nine feeds documented here cover:

- **[Providers]({{ site.url }}/feeds/providers/)** — the organizations that publish APIs
- **[APIs]({{ site.url }}/feeds/apis/)** — individual API records across all providers
- **[Capabilities]({{ site.url }}/feeds/capabilities/)** — Naftiko business workflow specs
- **[Tags]({{ site.url }}/feeds/tags/)** — classification tags with resource counts
- **[Schemas]({{ site.url }}/feeds/schemas/)** — JSON Schema definitions from indexed APIs
- **[AsyncAPI]({{ site.url }}/feeds/asyncapi/)** — event-driven API specifications
- **[JSON-LD]({{ site.url }}/feeds/json-ld/)** — linked data contexts
- **[Rules]({{ site.url }}/feeds/rules/)** — Spectral rulesets for API governance
- **[Vocabularies]({{ site.url }}/feeds/vocabularies/)** — controlled taxonomy definitions

## APIs.json

This portal publishes an [apis.json]({{ site.url }}/apis.json) that catalogs all nine feeds as machine-readable API definitions per the [APIs.json specification](https://apisjson.org). APIs.json is the discovery format that powers the APIs.io index — using it here is intentional.

## Open Source

The portal is open source at [github.com/api-search/developer-portal](https://github.com/api-search/developer-portal). The APIs.io network build scripts live at [github.com/api-search/network](https://github.com/api-search/network).

## Contact

Questions and issues: [github.com/api-search/developer-portal/issues](https://github.com/api-search/developer-portal/issues) or [info@apis.io](mailto:info@apis.io).
