---
title: "HTTP Caching: Complete Guide to Browser & Server Caching"
description: "Master HTTP caching - understand Cache-Control directives, validation strategies, cache busting, and common caching patterns for optimal web performance."
author: "ProCodeX Team"
date: "2024-01-30"
tags:
  [
    "http-caching",
    "cache-control",
    "web-performance",
    "browser-cache",
    "cdn",
    "etag",
    "validation",
  ]
section: "web-storage"
---

# HTTP Caching

> The HTTP cache stores a response associated with a request and reuses it for subsequent requests.

---

## How HTTP Caching Works

```
FIRST REQUEST (Cache Miss)
Browser --> Cache --> Server
                        |
Browser <-- Cache <-- Response
              |
              Stores copy

SECOND REQUEST (Cache Hit)
Browser --> Cache --> Returns cached copy
                      (No server request!)
```

---

## Why Caching Matters

| Benefit | Without Cache | With Cache |
|---------|---------------|------------|
| **Response Time** | 200-500ms | 5-50ms |
| **Server Load** | Every request | First only |
| **Bandwidth** | Full transfer | Zero |
| **UX** | Wait | Instant |

---

## Types of Caches

### Private Cache

A cache tied to a specific client (browser cache).

```http
Cache-Control: private
```

| Property | Value |
|----------|-------|
| **Scope** | Single user/browser |
| **Use Case** | Personalized content |

### Shared Cache

Located between client and server, shared among users.

| Type | Examples |
|------|----------|
| **CDN** | Cloudflare, CloudFront |
| **Reverse Proxy** | Nginx, Varnish |
| **Service Worker** | Cache API |

---

## Fresh vs Stale Responses

| State | Meaning | Action |
|-------|---------|--------|
| **Fresh** | age < max-age | Use directly |
| **Stale** | age > max-age | Revalidate first |

### Age Calculation

```http
HTTP/1.1 200 OK
Content-Type: text/html
Date: Tue, 22 Feb 2022 22:22:22 GMT
Cache-Control: max-age=604800
```

- `max-age=604800` = 1 week (in seconds)
- **Fresh** if age < 1 week
- **Stale** if age > 1 week

---

## Cache-Control Directives

| Directive | Purpose |
|-----------|---------|
| `max-age=N` | Fresh for N seconds |
| `no-cache` | Must revalidate before reuse |
| `no-store` | Don't store at all |
| `private` | Browser cache only |
| `public` | Can cache with Auth header |
| `immutable` | Never changes |
| `must-revalidate` | Must revalidate when stale |

### Expires vs max-age

```http
# Old way (HTTP/1.0) - avoid
Expires: Tue, 28 Feb 2022 22:22:22 GMT

# Modern way (HTTP/1.1) - preferred
Cache-Control: max-age=604800
```

> **Rule:** If both exist, `max-age` takes precedence.

---

## Validation (Revalidation)

Stale responses can be revalidated with the origin server.

### Method 1: Last-Modified / If-Modified-Since

```http
# Original Response
HTTP/1.1 200 OK
Last-Modified: Tue, 22 Feb 2022 22:00:00 GMT
Cache-Control: max-age=3600
```

```http
# Revalidation Request
GET /index.html HTTP/1.1
If-Modified-Since: Tue, 22 Feb 2022 22:00:00 GMT
```

```http
# Server Response (not modified)
HTTP/1.1 304 Not Modified
```

### Method 2: ETag / If-None-Match

```http
# Original Response
HTTP/1.1 200 OK
ETag: "33a64df5"
Cache-Control: max-age=3600
```

```http
# Revalidation Request
GET /index.html HTTP/1.1
If-None-Match: "33a64df5"
```

```http
# Server Response (not modified)
HTTP/1.1 304 Not Modified
```

| Method | Pros | Cons |
|--------|------|------|
| **Last-Modified** | Easy for static files | Time sync issues |
| **ETag** | Precise, works everywhere | Needs hash computation |

> **Best Practice:** Send both `ETag` and `Last-Modified`.

---

## The Vary Header

Cache responses separately based on request headers:

```http
Vary: Accept-Language
```

| URL | Accept-Language | Cached Separately |
|-----|-----------------|-------------------|
| `/index.html` | `en-US` | Yes |
| `/index.html` | `ja-JP` | Yes |

> **Warning:** Avoid `Vary: User-Agent` - too many variations kills cache hit rate.

---

## Common Caching Patterns

### Pattern 1: Always Validate

```http
# Non-personalized
Cache-Control: no-cache

# Personalized
Cache-Control: no-cache, private
```

### Pattern 2: Cache Busting (Static Assets)

For JS, CSS, images - cache forever with versioned URLs:

```html
<script src="bundle.v123.js"></script>
<script src="bundle.js?v=123"></script>
```

```http
# Response for versioned assets
Cache-Control: public, max-age=31536000, immutable
```

| max-age | Duration |
|---------|----------|
| `604800` | 1 week |
| `2592000` | 1 month |
| `31536000` | 1 year |

### Pattern 3: HTML Pages

```http
Cache-Control: no-cache
ETag: "abc123"
Last-Modified: Tue, 22 Feb 2022 20:20:20 GMT
```

---

## no-cache vs no-store

| Directive | Stores? | Revalidates? | Use When |
|-----------|---------|--------------|----------|
| `no-cache` | Yes | Always | Fresh content needed |
| `no-store` | No | N/A | Sensitive data |

> **Tip:** Prefer `no-cache, private` over `no-store` to keep back/forward cache working.

---

## Reload vs Force Reload

### Normal Reload (F5)

```http
GET / HTTP/1.1
Cache-Control: max-age=0
If-None-Match: "deadbeef"
```

```js
fetch("/", { cache: "no-cache" });
```

### Force Reload (Ctrl+Shift+R)

```http
GET / HTTP/1.1
Cache-Control: no-cache
```

```js
fetch("/", { cache: "reload" });
```

---

## Quick Reference

| Content Type | Cache Strategy |
|--------------|----------------|
| **Static assets** (versioned) | `max-age=31536000, immutable` |
| **HTML pages** | `no-cache` + ETag |
| **Personalized content** | `no-cache, private` |
| **API responses** | `private, max-age=60` or `no-cache` |

### Example Headers

```http
# Static assets (JS, CSS, images with hash)
Cache-Control: public, max-age=31536000, immutable
ETag: "hash-value"

# HTML pages
Cache-Control: no-cache
ETag: "page-hash"

# Personalized content
Cache-Control: no-cache, private

# API with short cache
Cache-Control: private, max-age=60
```

---

## Summary

| Content Type | Cache Strategy |
|--------------|----------------|
| Static assets (versioned) | `max-age=31536000, immutable` |
| HTML documents | `no-cache` + ETag |
| Personalized content | `no-cache, private` |
| API responses | Depends on freshness needs |

> **Golden Rule:** Always set explicit `Cache-Control` headers. Don't rely on heuristic caching.
