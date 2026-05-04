---
title: "HTTP Caching"
description: "Learn HTTP caching in simple terms - how browsers and servers store responses to make websites faster."
tags: ["http-caching", "cache-control", "web-performance", "browser-cache"]
section: "system-design"
---

# HTTP Caching

HTTP caching is simple: **save a copy of a response and reuse it later** instead of asking the server again.

> Think of it like a **photocopy machine**. Instead of going to the main office every time you need a document, you keep a photocopy at your desk. Faster and easier!

---

## Why Caching Matters

Imagine ordering food from a restaurant. Without caching, you'd call the restaurant for every single bite. With caching, you order once and keep leftovers in your fridge.

**Without caching:**
- Browser asks server for everything, every time
- Slow page loads
- Server gets overwhelmed
- Wastes internet bandwidth

**With caching:**
- Browser reuses saved copies
- Pages load instantly
- Server handles fewer requests
- Saves data and battery

> **Real example:** When you visit YouTube, your browser caches the logo, icons, and CSS. Next time you visit, these load instantly from your computer - not from YouTube's servers.

---

## Types of Caches

### Private Cache (Your Browser)

This is like **your personal notebook**. Only you can see what's written in it.

Your browser saves files on your computer. When you revisit a site, it uses these saved files instead of downloading again.

```http
Cache-Control: private
```

**Use private when:**
- Content is personalized (your profile, your settings)
- You don't want others to see this data
- It's specific to one user

> **Example:** Your Facebook feed is private. It should only be cached in YOUR browser, not on a shared server where others might see it.

### Shared Cache (CDN, Proxy)

This is like a **public library**. Everyone in the neighborhood shares the same books.

Shared caches sit between users and the server. They store content that's the same for everyone.

**Types of shared caches:**
- **CDN** (Cloudflare, CloudFront) - Servers spread worldwide, closer to users
- **Reverse Proxy** (Nginx) - Sits in front of your main server
- **Service Workers** - JavaScript that intercepts browser requests

> **Example:** Netflix uses CDNs. The same movie is cached on servers near you, so millions of people don't all download from one place.

---

## Fresh vs Stale: Is My Cache Still Good?

Think of cached responses like **milk in your fridge**:
- **Fresh milk** = Safe to drink, use it directly
- **Stale milk** = Check the expiry date, maybe smell it first

| State | Meaning | What Browser Does |
|-------|---------|-------------------|
| **Fresh** | Not expired yet | Uses cached copy directly |
| **Stale** | Past expiry time | Asks server "did this change?" |

### How Long is Fresh?

The `max-age` directive sets the expiry time in seconds:

```http
Cache-Control: max-age=604800
```

This means: "Good for 604800 seconds (1 week)."

| max-age Value | Human Readable |
|---------------|----------------|
| `60` | 1 minute |
| `3600` | 1 hour |
| `86400` | 1 day |
| `604800` | 1 week |
| `2592000` | 1 month |
| `31536000` | 1 year |

> **Think of it this way:** `max-age=86400` is like putting a sticker on your leftovers saying "eat within 24 hours."

---

## Cache-Control: The Boss of Caching

`Cache-Control` is the header that tells browsers and caches what to do. It's like a **set of instructions** on a package.

| Directive | Plain English |
|-----------|---------------|
| `max-age=3600` | "Keep this for 1 hour" |
| `no-cache` | "You can keep it, but always ask me before using" |
| `no-store` | "Don't save this at all, ever" |
| `private` | "Only the browser can save this, not CDNs" |
| `public` | "Anyone can cache this" |
| `immutable` | "This will literally never change" |

### Real-World Analogies

- **`max-age=3600`** = "This coupon expires in 1 hour"
- **`no-cache`** = "Call me before using this coupon to check if it's still valid"
- **`no-store`** = "Burn this message after reading" (spy movie style)
- **`private`** = "For your eyes only"
- **`public`** = "Feel free to share with everyone"
- **`immutable`** = "Carved in stone, will never change"

---

## Validation: "Hey Server, Did This Change?"

When cached content becomes stale, the browser doesn't delete it. Instead, it asks the server: **"Is my copy still good?"**

This is like calling a friend: "Hey, is that address you gave me last month still correct?"

If nothing changed, the server responds with **304 Not Modified** - meaning "yes, your copy is fine, use it." This saves bandwidth because no actual content is sent.

### Two Ways to Check

**Method 1: Last-Modified (When was it changed?)**

Server tells browser when the file was last updated:
```http
Last-Modified: Tue, 22 Feb 2022 22:00:00 GMT
```

Browser asks: "Did it change after Feb 22?"
```http
If-Modified-Since: Tue, 22 Feb 2022 22:00:00 GMT
```

> Like asking: "Has the menu changed since my last visit?"

**Method 2: ETag (Unique fingerprint)**

Server gives the file a unique ID:
```http
ETag: "abc123"
```

Browser asks: "Is the ID still abc123?"
```http
If-None-Match: "abc123"
```

> Like asking: "Is this still version 2.0 of the document?"

**Best practice:** Use both together for maximum compatibility.

---

## no-cache vs no-store: The Confusing Twins

These sound similar but are very different:

| | no-cache | no-store |
|---|----------|----------|
| **Saves copy?** | Yes | No |
| **Before using** | Always checks with server | N/A (nothing saved) |
| **Back button works?** | Yes | No |
| **Use for** | Content that changes often | Sensitive data (passwords, banking) |

> **no-cache** = "Keep the photocopy, but call me before using it"
> **no-store** = "Shred the document immediately"

**Important tip:** Prefer `no-cache` over `no-store` when possible. With `no-store`, browser features like back/forward navigation break.

For personalized but not sensitive content:
```http
Cache-Control: no-cache, private
```

---

## The Vary Header: Same URL, Different Content

Sometimes the same URL returns different content based on who's asking.

> **Example:** A website shows English content to English users and Spanish content to Spanish users. Same URL, different response.

The `Vary` header tells caches: "Store separate copies based on this header."

```http
Vary: Accept-Language
```

Now the cache keeps:
- One copy for English users
- One copy for Spanish users

**Warning:** Never use `Vary: User-Agent`. There are thousands of different user agents, so nothing would ever get cached!

---

## Cache Busting: Forcing Fresh Downloads

**Problem:** You cached a JavaScript file for 1 year. Now you need to update it. How do you tell browsers to get the new version?

**Solution:** Change the URL!

```html
<!-- Before update -->
<script src="app.js"></script>

<!-- After update - new URL forces fresh download -->
<script src="app.v2.js"></script>
<script src="app.js?v=2"></script>
<script src="app.abc123.js"></script>
```

Since the URL changed, browsers treat it as a completely new file and download it fresh.

> **Analogy:** It's like changing your phone number when you want people to stop calling the old one.

For versioned files, cache aggressively:
```http
Cache-Control: public, max-age=31536000, immutable
```

This says: "Cache for 1 year, and don't even bother checking - it will never change."

---

## Common Caching Patterns

### Pattern 1: HTML Pages

HTML URLs can't change (you can't version `index.html`), so always validate:

```http
Cache-Control: no-cache
ETag: "page-v1"
```

For logged-in users:
```http
Cache-Control: no-cache, private
```

### Pattern 2: Static Assets (JS, CSS, Images)

Use versioned filenames and cache forever:

```http
Cache-Control: public, max-age=31536000, immutable
```

### Pattern 3: API Responses

Depends on how fresh data needs to be:

```http
# Real-time data (stock prices)
Cache-Control: no-store

# User-specific data
Cache-Control: no-cache, private

# Data that's okay to be 1 minute old
Cache-Control: private, max-age=60
```

---

## Reload vs Force Reload

| Action | Keyboard | What Happens |
|--------|----------|--------------|
| **Reload** | F5 | Checks if cached content is still valid |
| **Force Reload** | Ctrl+Shift+R | Ignores cache, downloads everything fresh |

> **Reload** = "Is my cached copy still good?"
> **Force Reload** = "I don't care, give me everything fresh!"

---

## Quick Reference Cheat Sheet

| Content Type | What to Use |
|--------------|-------------|
| HTML pages | `no-cache` + ETag |
| JS/CSS (versioned) | `max-age=31536000, immutable` |
| Images (versioned) | `max-age=31536000, immutable` |
| User profile data | `no-cache, private` |
| Banking/passwords | `no-store, private` |
| Public API data | `max-age=60` |
| Private API data | `private, max-age=60` |

---

## Summary: The Key Takeaways

1. **Caching = saving copies** to avoid repeated downloads
2. **Private cache** = your browser only
3. **Shared cache** = CDN, proxies (shared by many users)
4. **Fresh** = use directly, **Stale** = check with server first
5. **max-age** = how long before it expires
6. **no-cache** = save it, but always validate before using
7. **no-store** = don't save at all (use sparingly!)
8. **Cache busting** = change URL to force fresh download
9. **ETag/Last-Modified** = efficient way to check if content changed

> **Golden Rule:** Always set explicit `Cache-Control` headers. Never let browsers guess what to do!
