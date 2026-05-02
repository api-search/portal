---
published: true
layout: post
title: 'A New Developer Portal, Nine Data Feeds, and an Agent-Ready Network'
image: /assets/images/blog/new-developer-portal-feeds-and-agents.png
tags:
- Developer Portal
- Feeds
- Agents
- MCP
- Discovery
- APIs.io
date: 2026-05-02
---

The APIs.io developer portal has been rebuilt from the ground up. The old version documented two HTTP APIs — search and authentication — in a minimal UI that hadn't changed since 2023. The new portal is organized around a different model: nine static JSON feeds that expose the full APIs.io network as consumable data, a design that matches the main apis.io site, and an agent-readiness layer baked into every subdomain in the network. Here is what changed and what it means for developers building on top of APIs.io.

## Nine Feeds, One Network

The core of the new portal is nine feeds, each publishing a different slice of the APIs.io catalog as a flat JSON array at a well-known URL. Every feed uses the same compact schema (`i`, `n`, `d`, `t`, `u`) so a single fetch-and-index pattern works across all nine. No authentication required. CORS headers are set on every response.

**[Providers](https://developer.apis.io/feeds/providers/)** — `https://providers.apis.io/search-index.json`  
Every organization in the catalog: name, description, tags, API count, and capability count. Currently over 2,500 providers. The `ac` (API count) and `cc` (capability count) fields let you filter for providers with published capabilities without fetching their full profile.

**[APIs](https://developer.apis.io/feeds/apis/)** — `https://apis.apis.io/search-index.json`  
Individual API records across all providers. Over 10,000 entries. Each includes a provider slug so you can group APIs by provider client-side without a second request.

**[Capabilities](https://developer.apis.io/feeds/capabilities/)** — `https://capabilities.apis.io/search-index.json`  
Naftiko capability specs — business workflow definitions that deploy as REST APIs or MCP servers from a single YAML file. Over 750 capabilities across hundreds of providers, spanning CRM, payments, storage, messaging, security, and 20+ other canonical categories. This is the feed to pull if you are building an agent that needs to discover what workflows are available across the catalog.

**[Tags](https://developer.apis.io/feeds/tags/)** — `https://tags.apis.io/search-index.json`  
Every tag used across providers and APIs, with counts for both. Pull this feed to build a live tag cloud, a filter sidebar, or to find which tags have the deepest coverage in the catalog.

**[Schemas](https://developer.apis.io/feeds/schemas/)** — `https://schemas.apis.io/search-index.json`  
JSON Schema definitions extracted from indexed APIs — over 43,000 schemas. The feed surfaces reusable data model definitions across the ecosystem, useful for tooling that needs to validate or generate code against common API shapes.

**[AsyncAPI](https://developer.apis.io/feeds/asyncapi/)** — `https://asyncapi.apis.io/search-index.json`  
AsyncAPI specifications indexed across providers: event-driven APIs, message channels, and async operations. Over 136 specs, growing as event-driven providers are profiled.

**[JSON-LD](https://developer.apis.io/feeds/json-ld/)** — `https://json-ld.apis.io/search-index.json`  
JSON-LD context documents and linked data definitions published by API providers. Over 2,800 documents. Useful for discovery tooling that needs to reason about data types and vocabulary alignment across providers.

**[Rules](https://developer.apis.io/feeds/rules/)** — `https://rules.apis.io/search-index.json`  
Spectral ruleset files for OpenAPI and AsyncAPI linting. Includes rulesets from over 600 providers plus the API Commons base rules — 1,568 rules in total spanning OpenAPI, APIs.json, and provider-specific conventions.

**[Vocabularies](https://developer.apis.io/feeds/vocabularies/)** — `https://vocabularies.apis.io/search-index.json`  
Controlled vocabularies and taxonomy definitions used by API providers for classification and semantic consistency across the catalog.

Each feed page in the portal documents the response schema, an example curl request, and JavaScript, MiniSearch, and Python integration snippets. The feeds are also listed in the portal's [APIs.json](https://developer.apis.io/apis.json) with `JSONFeed` and `APICatalog` property types, making the whole set machine-discoverable from a single document.

## MiniSearch Integration

Every feed is sized and structured to work directly with [MiniSearch](https://lucaong.github.io/minisearch/), the lightweight in-browser full-text search library. The abbreviated field names keep payload sizes manageable at scale — the providers feed is around 1 MB for 2,500+ entries, the APIs feed around 4 MB for 10,000+ entries. The pattern is the same across all nine:

```javascript
import MiniSearch from 'minisearch';

const response = await fetch('https://providers.apis.io/search-index.json');
const docs = await response.json();

const index = new MiniSearch({
  idField: 'i',
  fields: ['n', 'd'],
  storeFields: ['n', 'd', 'u', 't']
});

index.addAll(docs);
const results = index.search('payments');
```

This is the same pattern that powers search on apis.io itself — the feeds are the search index.

## Agent-Ready by Design

The feeds are one layer. The other layer is the agent-readiness work applied across every subdomain in the network.

**RFC 9727 API catalogs** are published at `/.well-known/api-catalog` on both `apis.apis.io` and `providers.apis.io`. The catalog at `apis.apis.io/.well-known/api-catalog` lists over 5,500 APIs in RFC 9264 JSON Linkset format, with `service-desc` links to OpenAPI, AsyncAPI, and Postman specs where available. An agent that wants to know what is in the catalog can fetch one URL and parse JSON — no HTML traversal, no per-page crawl.

**Agent Skills** are published at `apis.io/skills/`. Three skills cover the core discovery workflows:

- `discover-apis-io` — primes an agent with the network structure, the eleven subdomains, and the discovery endpoints
- `search-apis` — a concrete recipe for filtering the catalog by keyword, capability, or tag using the feeds
- `fetch-api-spec` — how to follow `service-desc` links to pull and parse upstream OpenAPI / AsyncAPI specs

Every page across the network advertises the skills directory via `<link rel="agent-skills">` in the HTML head and a matching `Link` HTTP header, so agents find the skills from any entry point without needing the URL in advance.

**Markdown content negotiation** is live on API and provider detail pages. Hit any page on `apis.apis.io` or `providers.apis.io` with `Accept: text/markdown` and you get clean structured markdown — title, description, links to specs and docs — instead of HTML. Same information, no parsing tax.

**`robots.txt` with explicit AI permissions** is set on every subdomain using Cloudflare's Content Signals directive alongside the IETF AIPREF draft:

```
Content-Signal: search=yes, ai-input=yes, ai-train=yes
Content-Usage: search=y, ai-input=y, ai-train=y
```

Nothing about APIs.io was previously closed; the difference is that the permission is now machine-readable and placed where crawlers look.

**Web Bot Auth observability headers** are applied to every HTML response. Requests carrying RFC 9421 HTTP Message Signatures tagged `tag="web-bot-auth"` come back with `x-bot-auth` headers surfacing whether the bot signature was absent, claimed-but-unverified, or verified by Cloudflare's edge.

## Capabilities as MCP Servers

The capabilities feed deserves its own note for agent developers. Every capability in the catalog is a Naftiko YAML spec that exposes both a REST API and an MCP server from the same definition. The MCP surface includes `hints:` on each tool — `readOnly`, `destructive`, `idempotent`, `openWorld` — so an agent runtime knows which operations are safe to call autonomously versus which require user confirmation.

Of the 750+ capabilities currently indexed, 652 expose both REST and MCP surfaces. Pull the capabilities feed to find what's available; follow the `u` field link to browse the full YAML including the `exposes:` block; run it locally with [Naftiko fleet](https://github.com/naftiko/fleet). The canonical categories at `capabilities.apis.io/categories/` group capabilities by domain (payments, CRM, storage, monitoring, and 22 others) so you can answer the cross-provider question — "which providers offer object storage?" — without iterating the full feed.

## Start Here

The fastest onramp is the [feeds index](https://developer.apis.io/feeds/). Pick the feed that matches what you are building against, curl it, load it into MiniSearch, and you have a searchable index of that slice of the network running locally in under a minute. For agent integration, start at `apis.io/skills/` — the `discover-apis-io` skill will walk your agent through the full network structure from there.
