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
section: "system-design"
---

# HTTP Caching

> The HTTP cache stores a response associated with a request and reuses it for subsequent requests.

---

## How HTTP Caching Works

```
┌─────────────────────────────────────────────────────────────┐
│                    HTTP CACHING FLOW                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   FIRST REQUEST (Cache Miss)                                │
│   ─────────────────────────                                 │
│   Browser ──► Cache ──► Server                              │
│                           │                                 │
│                           ▼                                 │
│   Browser ◄── Cache ◄── Response + Headers                  │
│                 │                                           │
│                 └── Stores copy                             │
│                                                             │
│   SECOND REQUEST (Cache Hit)                                │
│   ──────────────────────────                                │
│   Browser ──► Cache ──► Returns cached copy                 │
│                         (No server request!)                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Why Caching Matters

| Benefit | Without Cache | With Cache |
|---------|---------------|------------|
| **Response Time** | 200-500ms | 5-50ms |
| **Server Load** | Every request | First request only |
| **Bandwidth** | Full transfer | Zero transfer |
| **User Experience** | Wait for server | Instant |

```
WITHOUT CACHE                    WITH CACHE
─────────────                    ──────────
Request                          Request
   │                                │
   ▼                                ▼
Server Processing               Cache Hit!
   │                                │
   ▼                                ▼
DB Query                        Response
   │                            (instant)
   ▼
Template Render
   │
   ▼
Response
(slow)
```

---

## Types of Caches

```
┌─────────────────────────────────────────────────────────────┐
│                      CACHE TYPES                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   👤 Client                                                 │
│      │                                                      │
│      ▼                                                      │
│   ┌─────────────────┐                                      │
│   │ PRIVATE CACHE   │  Browser cache                       │
│   │ (per user)      │  User-specific data                  │
│   └────────┬────────┘                                      │
│            │                                                │
│            ▼                                                │
│   ┌─────────────────┐                                      │
│   │ SHARED CACHE    │                                      │
│   │ ├─ Proxy Cache  │  Network proxies                     │
│   │ └─ Managed      │  CDN, Reverse Proxy, Service Worker  │
│   └────────┬────────┘                                      │
│            │                                                │
│            ▼                                                │
│   🖥️ Origin Server                                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Private Cache

A cache tied to a **specific client** (typically browser cache).

```http
Cache-Control: private
```

| Property | Value |
|----------|-------|
| **Scope** | Single user/browser |
| **Use Case** | Personalized content |
| **Shared** | No - prevents info leakage |

> **Important:** Cookies don't automatically make a response private. You must explicitly set `Cache-Control: private`.

### Shared Cache

Located **between client and server**, shared among users.

| Type | Examples | Managed By |
|------|----------|------------|
| **Proxy Cache** | Network proxies | Network admin |
| **CDN** | Cloudflare, CloudFront | Developer |
| **Reverse Proxy** | Nginx, Varnish | Developer |
| **Service Worker** | Cache API | Developer |

---

## Fresh vs Stale Responses

```
┌─────────────────────────────────────────────────────────────┐
│                  RESPONSE LIFECYCLE                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Response Received                                         │
│         │                                                   │
│         ▼                                                   │
│   ┌───────────┐                                            │
│   │   FRESH   │  age < max-age                             │
│   │           │  ✓ Can be reused directly                  │
│   └─────┬─────┘                                            │
│         │                                                   │
│         │ time passes...                                    │
│         ▼                                                   │
│   ┌───────────┐                                            │
│   │   STALE   │  age > max-age                             │
│   │           │  ⚠ Must revalidate first                   │
│   └─────┬─────┘                                            │
│         │                                                   │
│    ┌────┴────┐                                             │
│    ▼         ▼                                             │
│  304 OK    200 OK                                          │
│  (refresh)  (new response)                                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

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

<!doctype html>...
```

- `max-age=604800` = 1 week (in seconds)
- **Fresh** if age < 1 week
- **Stale** if age > 1 week

---

## Cache-Control Directives

```
┌─────────────────────────────────────────────────────────────┐
│               CACHE-CONTROL DIRECTIVES                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   FRESHNESS              VALIDATION         STORAGE         │
│   ──────────             ──────────         ───────         │
│   max-age=N              no-cache           no-store        │
│   s-maxage=N             must-revalidate    private         │
│   immutable                                 public          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

| Directive | Purpose |
|-----------|---------|
| `max-age=N` | Fresh for N seconds |
| `s-maxage=N` | max-age for shared caches only |
| `no-cache` | Must revalidate before reuse |
| `no-store` | Don't store at all |
| `private` | Browser cache only |
| `public` | Can cache even with Auth header |
| `immutable` | Never changes, skip revalidation |
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

```
┌─────────────────────────────────────────────────────────────┐
│                   REVALIDATION FLOW                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Browser: "Is my cached copy still valid?"                 │
│                                                             │
│   GET /page.html                                            │
│   If-None-Match: "abc123"                                   │
│         │                                                   │
│         ▼                                                   │
│   ┌─────────────┐                                          │
│   │   Server    │                                          │
│   └──────┬──────┘                                          │
│          │                                                  │
│     ┌────┴────┐                                            │
│     ▼         ▼                                            │
│   Same?     Changed?                                       │
│     │         │                                            │
│     ▼         ▼                                            │
│   304        200 OK                                        │
│   Not        + New Content                                 │
│   Modified                                                 │
│   (no body)                                                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

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

```
┌─────────────────────────────────────────────────────────────┐
│                    VARY HEADER                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   WITHOUT Vary: Accept-Language                             │
│   ─────────────────────────────                             │
│   /page (en) ──► Cache ──► Same response for all ❌        │
│   /page (ja) ──► Cache ──►                                 │
│                                                             │
│   WITH Vary: Accept-Language                                │
│   ──────────────────────────                                │
│   /page (en) ──► Cache Key: URL + en ──► English ✓         │
│   /page (ja) ──► Cache Key: URL + ja ──► Japanese ✓        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

```http
Vary: Accept-Language
```

> **Warning:** Avoid `Vary: User-Agent` - too many variations kills cache hit rate.

---

## Common Caching Patterns

### Pattern 1: Default (Always Validate)

```http
# Non-personalized
Cache-Control: no-cache

# Personalized
Cache-Control: no-cache, private
```

### Pattern 2: Cache Busting (Static Assets)

```
┌─────────────────────────────────────────────────────────────┐
│                   CACHE BUSTING                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   index.html (no-cache)                                     │
│       │                                                     │
│       ├──► bundle.v123.js  (max-age=1year)                 │
│       ├──► style.v123.css  (max-age=1year)                 │
│       └──► logo.abc12.png  (max-age=1year)                 │
│                                                             │
│   On Update:                                                │
│   ──────────                                                │
│   bundle.v123.js ──► bundle.v124.js (new URL = new cache)  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

```html
<!-- Versioned URLs -->
<script src="bundle.v123.js"></script>
<script src="bundle.js?v=123"></script>
<script src="bundle.YsAIAAAA.js"></script>
```

```http
# Response for versioned assets
Cache-Control: public, max-age=31536000, immutable
ETag: "YsAIAAAA"
```

| max-age | Duration |
|---------|----------|
| `604800` | 1 week |
| `2592000` | 1 month |
| `31536000` | 1 year |

### Pattern 3: HTML Pages (Can't Cache Bust)

```http
# Non-personalized HTML
Cache-Control: no-cache
ETag: "abc123"
Last-Modified: Tue, 22 Feb 2022 20:20:20 GMT
```

```http
# Personalized HTML
Cache-Control: no-cache, private
```

---

## no-cache vs no-store

```
┌─────────────────────────────────────────────────────────────┐
│              no-cache vs no-store                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   no-cache                      no-store                    │
│   ────────                      ────────                    │
│   ✓ Store in cache              ✗ Don't store at all       │
│   ✓ Always revalidate           ✗ Always fetch full        │
│   ✓ 304 possible                ✗ 200 every time           │
│   ✓ Back/forward works          ✗ Back/forward broken      │
│                                                             │
│   PREFER: no-cache, private     AVOID: no-store            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

| Directive | Stores? | Revalidates? | Use When |
|-----------|---------|--------------|----------|
| `no-cache` | Yes | Always | Fresh content needed |
| `no-store` | No | N/A | Sensitive data |

> **Tip:** Prefer `no-cache, private` over `no-store` to keep back/forward cache working.

---

## Reload vs Force Reload

```
┌─────────────────────────────────────────────────────────────┐
│            RELOAD vs FORCE RELOAD                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   NORMAL RELOAD (F5)                                        │
│   ──────────────────                                        │
│   Cache-Control: max-age=0                                  │
│   If-None-Match: "abc"                                      │
│         │                                                   │
│         ▼                                                   │
│   Server checks ──► 304 Not Modified (use cache)           │
│                                                             │
│   FORCE RELOAD (Ctrl+Shift+R)                              │
│   ───────────────────────────                               │
│   Cache-Control: no-cache                                   │
│   (no conditional headers)                                  │
│         │                                                   │
│         ▼                                                   │
│   Server sends ──► 200 OK + Full Content                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

```js
// Normal reload
fetch("/", { cache: "no-cache" });

// Force reload
fetch("/", { cache: "reload" });
```

---

## Request Collapse

```
┌─────────────────────────────────────────────────────────────┐
│                  REQUEST COLLAPSE                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Client 1 ──┐                                             │
│              │                                              │
│   Client 2 ──┼──► Shared Cache ──► ONE request ──► Server  │
│              │         │                                    │
│   Client 3 ──┘         │                                    │
│                        ▼                                    │
│              Response shared with ALL clients               │
│                                                             │
│   ⚠ Add "private" if response is personalized              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Caching Decision Guide

```
┌─────────────────────────────────────────────────────────────┐
│               CACHING DECISION GUIDE                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Is it sensitive data?                                     │
│         │                                                   │
│    ┌────┴────┐                                             │
│   Yes       No                                             │
│    │         │                                             │
│    ▼         ▼                                             │
│  no-cache   Is it a static asset?                          │
│  private          │                                        │
│              ┌────┴────┐                                   │
│             Yes       No                                   │
│              │         │                                   │
│              ▼         ▼                                   │
│         Can version   Is it personalized?                  │
│         the URL?            │                              │
│              │         ┌────┴────┐                         │
│         ┌────┴────┐   Yes       No                         │
│        Yes       No    │         │                         │
│         │         │    ▼         ▼                         │
│         ▼         ▼  no-cache   no-cache                   │
│    max-age=     no-cache private                           │
│    31536000     + ETag                                     │
│    immutable                                               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Content Type | Cache Strategy |
|--------------|----------------|
| **Static assets** (versioned) | `max-age=31536000, immutable` |
| **HTML pages** | `no-cache` + ETag |
| **Personalized content** | `no-cache, private` |
| **API responses** | `private, max-age=60` or `no-cache` |
| **Sensitive data** | `no-cache, private` |

### Example Headers

```http
# Static assets (JS, CSS, images with hash in filename)
Cache-Control: public, max-age=31536000, immutable
ETag: "hash-value"

# HTML pages
Cache-Control: no-cache
ETag: "page-hash"
Last-Modified: <date>

# Personalized content
Cache-Control: no-cache, private

# API with short cache
Cache-Control: private, max-age=60
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│                      SUMMARY                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Static + Versioned URL  ──────►  max-age=1year           │
│                                                             │
│   HTML Documents          ──────►  no-cache + ETag         │
│                                                             │
│   Personalized Content    ──────►  no-cache, private       │
│                                                             │
│   Sensitive Data          ──────►  no-cache, private       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

> **Golden Rule:** Always set explicit `Cache-Control` headers. Don't rely on heuristic caching.
