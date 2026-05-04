---
title: "HTTP Caching"
description: "Learn HTTP caching basics - how browsers and servers store responses to make websites faster."
tags: ["http-caching", "cache-control", "web-performance", "browser-cache"]
section: "web-storage"
---

# HTTP Caching

> HTTP cache stores a response and reuses it for future requests. This makes websites load faster.

---

## Why Caching Matters

When you visit a website, your browser asks the server for files (HTML, CSS, JS, images). Without caching, this happens every single time you visit.

**With caching:**
- Browser saves a copy of the response
- Next time, it uses the saved copy instead of asking the server again
- Result: Faster loading, less server work, less bandwidth used

| Without Cache | With Cache |
|---------------|------------|
| 200-500ms load time | 5-50ms load time |
| Server processes every request | Server only handles first request |
| Downloads everything again | Uses saved copy |

---

## Types of Caches

### Private Cache (Browser Cache)

This is your browser's personal storage. Only you can access it.

```http
Cache-Control: private
```

**Use for:** Personal data, logged-in user content, anything specific to one user.

### Shared Cache (CDN, Proxy)

Sits between users and the server. Multiple users share the same cached content.

**Examples:** Cloudflare, CloudFront, Nginx, Varnish

**Use for:** Public content that's same for everyone (images, CSS, JS files).

---

## Fresh vs Stale

Cached responses have two states:

| State | Meaning | What Happens |
|-------|---------|--------------|
| **Fresh** | Still valid | Use it directly |
| **Stale** | Expired | Check with server first |

The `max-age` value decides how long a response stays fresh:

```http
Cache-Control: max-age=604800
```

This means: "This response is fresh for 604800 seconds (1 week)."

---

## Cache-Control Directives

These are instructions that tell the cache what to do:

| Directive | What It Does |
|-----------|--------------|
| `max-age=N` | Stay fresh for N seconds |
| `no-cache` | Always check with server before using |
| `no-store` | Don't save this at all |
| `private` | Only browser can cache this |
| `public` | Anyone can cache this |
| `immutable` | This never changes |

### Common Time Values

| Value | Duration |
|-------|----------|
| `max-age=3600` | 1 hour |
| `max-age=86400` | 1 day |
| `max-age=604800` | 1 week |
| `max-age=2592000` | 1 month |
| `max-age=31536000` | 1 year |

---

## Validation (Checking if Content Changed)

When a cached response becomes stale, the browser asks the server: "Has this changed?"

### Using ETag

Server sends a unique ID (like a fingerprint) with the response:

```http
ETag: "abc123"
```

Browser asks: "Is it still abc123?"

If yes → Server says "304 Not Modified" (use your cached copy)
If no → Server sends the new content

### Using Last-Modified

Server tells when the file was last changed:

```http
Last-Modified: Tue, 22 Feb 2022 22:00:00 GMT
```

Browser asks: "Changed since this date?"

Same logic - if unchanged, use cache. If changed, get new content.

> **Tip:** Use both ETag and Last-Modified for best results.

---

## no-cache vs no-store

These sound similar but work differently:

| Directive | Saves Copy? | Always Checks Server? | Use When |
|-----------|-------------|----------------------|----------|
| `no-cache` | Yes | Yes | Want fresh content but keep back button working |
| `no-store` | No | N/A | Sensitive data (passwords, banking) |

> **Recommendation:** Prefer `no-cache` over `no-store`. It keeps browser features like back/forward working.

---

## The Vary Header

Sometimes the same URL returns different content based on request headers.

Example: A page in English for `Accept-Language: en` and Japanese for `Accept-Language: ja`.

```http
Vary: Accept-Language
```

This tells the cache: "Store separate copies for different languages."

> **Warning:** Don't use `Vary: User-Agent` - too many variations, cache becomes useless.

---

## Cache Busting

Problem: You cached a file for 1 year, but now you need to update it.

Solution: Change the URL when content changes.

```html
<!-- Old -->
<script src="app.js"></script>

<!-- New - add version or hash -->
<script src="app.v2.js"></script>
<script src="app.js?v=2"></script>
```

Different URL = browser treats it as a new file = downloads fresh copy.

---

## Common Patterns

### For Static Files (JS, CSS, Images)

Cache for a long time + use cache busting:

```http
Cache-Control: public, max-age=31536000, immutable
```

### For HTML Pages

Always check for updates:

```http
Cache-Control: no-cache
```

### For Personalized Content

Private + always check:

```http
Cache-Control: no-cache, private
```

### For API Responses

Short cache or always check:

```http
Cache-Control: private, max-age=60
```

---

## Quick Reference

| Content Type | Cache Strategy |
|--------------|----------------|
| Static files (versioned) | `max-age=31536000, immutable` |
| HTML pages | `no-cache` |
| Personalized content | `no-cache, private` |
| API responses | `private, max-age=60` or `no-cache` |

---

## Summary

1. **Caching makes websites faster** by reusing saved responses
2. **Private cache** = browser only, **Shared cache** = CDN/proxy
3. **Fresh** = use directly, **Stale** = check with server first
4. **max-age** controls how long content stays fresh
5. **no-cache** = always validate, **no-store** = don't save at all
6. **Cache busting** = change URL when content changes
7. **Always set Cache-Control headers** - don't rely on defaults

> **Golden Rule:** Be explicit about caching. Always set `Cache-Control` headers on your responses.
