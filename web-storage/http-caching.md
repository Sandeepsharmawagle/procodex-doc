---
title: "HTTP Caching"
description: "Learn HTTP caching in simple terms - how browsers and servers store responses to make websites faster."
tags: ["http-caching", "cache-control", "web-performance", "browser-cache"]
section: "web-storage"
---

# HTTP Caching

HTTP caching is simple: **save a copy of a response and reuse it later** instead of asking the server again.

Think of it like keeping a photocopy of a document. Instead of going to the office every time you need it, you just look at your copy.

---

## Why Caching Matters

Without caching, every time you visit a webpage, your browser asks the server for everything - HTML, CSS, JavaScript, images. This is slow and wastes resources.

With caching:
- **Faster loading** - No need to wait for the server
- **Less server load** - Server handles fewer requests
- **Saves bandwidth** - No repeated downloads
- **Better user experience** - Pages load instantly

---

## Types of Caches

### Private Cache (Browser Cache)

This is your browser's personal storage. It saves responses just for you.

When you visit a website, your browser stores files locally. Next time you visit, it uses the saved copies instead of downloading again.

Use `private` when the content is personalized (like your profile page):

```http
Cache-Control: private
```

### Shared Cache

This sits between users and the server. Multiple users share the same cached content.

**Examples:**
- **CDN** (Cloudflare, CloudFront) - Servers spread around the world
- **Reverse Proxy** (Nginx, Varnish) - Sits in front of your server
- **Service Workers** - JavaScript that intercepts requests

Shared caches are great for content that's the same for everyone (like a company logo).

---

## Fresh vs Stale

Cached responses have two states:

| State | Meaning | What Happens |
|-------|---------|--------------|
| **Fresh** | Still valid | Use it directly |
| **Stale** | Expired | Check with server first |

The `max-age` directive tells how long a response stays fresh:

```http
Cache-Control: max-age=604800
```

This means: "This response is good for 604800 seconds (1 week)."

- If less than 1 week has passed → **Fresh** → Use cached copy
- If more than 1 week has passed → **Stale** → Ask server if it changed

---

## Cache-Control Directives

These are instructions that tell caches what to do:

| Directive | What It Does |
|-----------|--------------|
| `max-age=N` | Cache is fresh for N seconds |
| `no-cache` | Always check with server before using |
| `no-store` | Don't save this at all |
| `private` | Only browser can cache (not CDN) |
| `public` | Anyone can cache this |
| `immutable` | This will never change |

### Common max-age Values

| Value | Duration |
|-------|----------|
| `3600` | 1 hour |
| `86400` | 1 day |
| `604800` | 1 week |
| `2592000` | 1 month |
| `31536000` | 1 year |

---

## Validation: Checking if Content Changed

When a cached response becomes stale, the browser doesn't throw it away. Instead, it asks the server: "Did this change?"

If nothing changed, the server says "304 Not Modified" - use your cached copy. This saves bandwidth because no content is sent.

### Two Ways to Validate

**1. Last-Modified / If-Modified-Since**

Server sends when the file was last changed:
```http
Last-Modified: Tue, 22 Feb 2022 22:00:00 GMT
```

Browser asks: "Did it change after this time?"
```http
If-Modified-Since: Tue, 22 Feb 2022 22:00:00 GMT
```

**2. ETag / If-None-Match**

Server sends a unique ID (like a fingerprint):
```http
ETag: "abc123"
```

Browser asks: "Is this still the same?"
```http
If-None-Match: "abc123"
```

**Best practice:** Use both ETag and Last-Modified together.

---

## no-cache vs no-store

These are often confused:

| Directive | Stores Response? | When to Use |
|-----------|------------------|-------------|
| `no-cache` | Yes, but always validates | Content that changes often |
| `no-store` | No, never saves | Sensitive data (banking, passwords) |

**Important:** Prefer `no-cache` over `no-store`. With `no-store`, you lose browser features like back/forward navigation.

For personalized content, use:
```http
Cache-Control: no-cache, private
```

---

## The Vary Header

Sometimes the same URL returns different content based on the request.

Example: A page in English for English users, Japanese for Japanese users.

The `Vary` header tells caches to store separate copies:

```http
Vary: Accept-Language
```

Now the cache keeps different versions for different languages.

**Warning:** Avoid `Vary: User-Agent` - there are too many user agents, so nothing gets cached.

---

## Cache Busting

For files that rarely change (JS, CSS, images), you want long cache times. But how do you update them?

**Solution:** Change the URL when the content changes.

```html
<!-- Old version -->
<script src="app.js"></script>

<!-- With cache busting -->
<script src="app.v2.js"></script>
<script src="app.js?v=abc123"></script>
```

When you update the file, change the version. The browser sees a new URL and downloads fresh content.

For these versioned files, cache for a long time:
```http
Cache-Control: public, max-age=31536000, immutable
```

---

## Common Caching Patterns

### Pattern 1: HTML Pages

HTML can't use cache busting (the URL stays the same), so always validate:

```http
Cache-Control: no-cache
ETag: "page-hash"
Last-Modified: Tue, 22 Feb 2022 20:20:20 GMT
```

For personalized pages (after login):
```http
Cache-Control: no-cache, private
```

### Pattern 2: Static Assets (JS, CSS, Images)

Use versioned URLs and cache forever:

```http
Cache-Control: public, max-age=31536000, immutable
```

### Pattern 3: API Responses

For APIs, it depends on how fresh data needs to be:

```http
# Data that changes often
Cache-Control: no-cache, private

# Data that's okay to be slightly old
Cache-Control: private, max-age=60
```

---

## Reload vs Force Reload

| Action | What It Does |
|--------|--------------|
| **Reload (F5)** | Validates cached content with server |
| **Force Reload (Ctrl+Shift+R)** | Ignores cache, downloads everything fresh |

In JavaScript:
```js
// Normal reload - validates cache
fetch("/", { cache: "no-cache" });

// Force reload - ignores cache
fetch("/", { cache: "reload" });
```

---

## Quick Reference

| Content Type | Cache Strategy |
|--------------|----------------|
| HTML pages | `no-cache` + ETag |
| Static assets (versioned) | `max-age=31536000, immutable` |
| Personalized content | `no-cache, private` |
| Sensitive data | `no-store, private` |
| API responses | `private, max-age=60` or `no-cache` |

---

## Summary

1. **Caching saves responses** to avoid repeated server requests
2. **Private cache** = browser only, **Shared cache** = CDN, proxies
3. **Fresh** = use directly, **Stale** = validate first
4. Use **max-age** to set how long content stays fresh
5. Use **no-cache** for content that must always be validated
6. Use **cache busting** (versioned URLs) for static assets
7. Always set **Cache-Control** explicitly - don't rely on defaults

> **Golden Rule:** Always set explicit Cache-Control headers. Don't let browsers guess.
