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
icons:
  - Database
  - RefreshCw
  - Clock
  - Shield
  - Zap
  - Server
  - Globe
---

# HTTP Caching

> The HTTP cache stores a response associated with a request and reuses it for subsequent requests.

---

## How HTTP Caching Works

```mermaid
sequenceDiagram
    participant Client as 🌐 Browser
    participant Cache as 💾 Cache
    participant Server as 🖥️ Origin Server
    
    Note over Client,Server: First Request (Cache Miss)
    Client->>Cache: GET /page.html
    Cache->>Server: GET /page.html
    Server-->>Cache: 200 OK + Content
    Cache-->>Client: 200 OK + Content
    Note over Cache: Stores Response
    
    Note over Client,Server: Second Request (Cache Hit)
    Client->>Cache: GET /page.html
    Cache-->>Client: 200 OK (from cache)
    Note over Server: No request needed!
```

---

## Why Caching Matters

```mermaid
graph LR
    subgraph "Without Cache"
        A1[Request] --> B1[Server Processing]
        B1 --> C1[DB Query]
        C1 --> D1[Template Render]
        D1 --> E1[Response]
    end
    
    subgraph "With Cache"
        A2[Request] --> B2[Cache Hit]
        B2 --> E2[Response]
    end
    
    style B2 fill:#22c55e,color:#fff
    style E2 fill:#22c55e,color:#fff
```

| Benefit | Description |
|---------|-------------|
| **Faster Response** | Closer cache = faster delivery (no round-trip to origin) |
| **Reduced Server Load** | No parsing, routing, DB queries, or template rendering |
| **Better UX** | Instant page loads for returning visitors |
| **Lower Bandwidth** | Less data transferred over the network |

---

## Types of Caches

```mermaid
graph TB
    subgraph "Cache Types"
        direction TB
        
        subgraph Private["🔒 Private Cache"]
            PC["Browser Cache"]
            PC1["User-specific"]
            PC2["Personalized content"]
        end
        
        subgraph Shared["🌐 Shared Cache"]
            subgraph Proxy["Proxy Cache"]
                PX["Network Proxy"]
            end
            subgraph Managed["Managed Cache"]
                CDN["CDN"]
                RP["Reverse Proxy"]
                SW["Service Worker"]
            end
        end
    end
    
    Client[("👤 Client")] --> Private
    Private --> Shared
    Shared --> Server[("🖥️ Origin")]
    
    style Private fill:#3b82f6,color:#fff
    style Proxy fill:#f97316,color:#fff
    style Managed fill:#22c55e,color:#fff
```

### 1. Private Cache

A cache tied to a **specific client** (typically browser cache).

```http
Cache-Control: private
```

| Property | Value |
|----------|-------|
| **Scope** | Single user/browser |
| **Use Case** | Personalized content |
| **Shared** | No - prevents information leakage |

> **Important:** Cookies don't automatically make a response private. You must explicitly set `Cache-Control: private` for personalized content.

---

### 2. Shared Cache

Located **between client and server**, shared among multiple users.

#### Proxy Caches

- Implemented by network proxies
- Not managed by developers
- Controlled via HTTP headers
- Less relevant with HTTPS (encrypted traffic can't be cached by proxies)

#### Managed Caches

Explicitly deployed by developers for performance:

| Type | Examples |
|------|----------|
| **Reverse Proxies** | Nginx, Varnish |
| **CDNs** | Cloudflare, AWS CloudFront, Fastly |
| **Service Workers** | Browser Cache API |

```http
# Opt-out of proxy/private cache, use only managed cache
Cache-Control: no-store
```

---

## Fresh vs Stale Responses

```mermaid
stateDiagram-v2
    [*] --> Fresh: Response Received
    Fresh --> Fresh: age < max-age
    Fresh --> Stale: age > max-age
    Stale --> Fresh: Revalidation Success
    Stale --> [*]: Revalidation Fails (New Response)
    
    note right of Fresh: Can be reused directly
    note right of Stale: Must revalidate first
```

Cached responses have two states:

| State | Meaning |
|-------|---------|
| **Fresh** | Valid, can be reused |
| **Stale** | Expired, needs revalidation |

### Age Calculation

**Age** = time elapsed since response was generated (similar to TTL).

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

### Shared Cache Age Header

```mermaid
sequenceDiagram
    participant Client as 🌐 Browser
    participant Cache as 💾 Shared Cache
    participant Server as 🖥️ Server
    
    Server->>Cache: max-age=604800
    Note over Cache: Stores for 1 week
    
    Note over Cache: After 1 day (86400s)
    
    Client->>Cache: GET /page
    Cache-->>Client: max-age=604800, Age: 86400
    Note over Client: Fresh for 518400s more
```

---

## Cache-Control Directives

```mermaid
graph TB
    subgraph "Cache-Control Directives"
        direction TB
        
        subgraph Fresh["Freshness"]
            MA["max-age=N"]
            SMA["s-maxage=N"]
            IMM["immutable"]
        end
        
        subgraph Validation["Validation"]
            NC["no-cache"]
            MR["must-revalidate"]
        end
        
        subgraph Storage["Storage"]
            NS["no-store"]
            PRI["private"]
            PUB["public"]
        end
    end
    
    style Fresh fill:#22c55e,color:#fff
    style Validation fill:#f97316,color:#fff
    style Storage fill:#3b82f6,color:#fff
```

### Freshness Directives

| Directive | Purpose |
|-----------|---------|
| `max-age=<seconds>` | Response is fresh for N seconds |
| `s-maxage=<seconds>` | Same as max-age but for shared caches only |
| `no-cache` | Must revalidate before reuse |
| `no-store` | Don't store the response at all |
| `private` | Only store in private (browser) cache |
| `public` | Can be stored even with Authorization header |
| `immutable` | Content never changes, skip revalidation on reload |
| `must-revalidate` | Must revalidate when stale (no serving stale content) |

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

```mermaid
sequenceDiagram
    participant Client as 🌐 Browser
    participant Cache as 💾 Cache
    participant Server as 🖥️ Server
    
    Note over Client,Server: Cache becomes stale
    
    Client->>Cache: GET /page.html
    Note over Cache: Response is stale!
    
    Cache->>Server: GET /page.html<br/>If-None-Match: "abc123"
    
    alt Content unchanged
        Server-->>Cache: 304 Not Modified
        Note over Cache: Refresh TTL
        Cache-->>Client: 200 OK (cached content)
    else Content changed
        Server-->>Cache: 200 OK + New Content
        Note over Cache: Store new response
        Cache-->>Client: 200 OK (new content)
    end
```

Stale responses aren't discarded - they can be **revalidated** with the origin server.

### Method 1: If-Modified-Since

Uses `Last-Modified` timestamp:

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
Cache-Control: max-age=3600
```

**Result:** No body transferred, cache refreshed for another hour.

---

### Method 2: ETag / If-None-Match

Uses content hash or version identifier:

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
| **Last-Modified** | Easy for static files | Time sync issues on distributed servers |
| **ETag** | Precise, works everywhere | Requires hash computation |

> **Best Practice:** Send both `ETag` and `Last-Modified` when possible.

---

## The Vary Header

```mermaid
graph TB
    subgraph "Without Vary"
        R1["GET /page<br/>Accept-Language: en"] --> C1["Cache"]
        R2["GET /page<br/>Accept-Language: ja"] --> C1
        C1 --> SAME["Same cached response ❌"]
    end
    
    subgraph "With Vary: Accept-Language"
        R3["GET /page<br/>Accept-Language: en"] --> C2["Cache Key:<br/>URL + en"]
        R4["GET /page<br/>Accept-Language: ja"] --> C3["Cache Key:<br/>URL + ja"]
        C2 --> EN["English response ✓"]
        C3 --> JA["Japanese response ✓"]
    end
    
    style SAME fill:#ef4444,color:#fff
    style EN fill:#22c55e,color:#fff
    style JA fill:#22c55e,color:#fff
```

Cache responses separately based on request headers:

```http
Vary: Accept-Language
```

| URL | Accept-Language | Cached Separately |
|-----|-----------------|-------------------|
| `/index.html` | `ja-JP` | Yes |
| `/index.html` | `en-US` | Yes |

> **Warning:** Avoid `Vary: User-Agent` - too many variations, kills cache hit rate. Use feature detection instead.

---

## Common Caching Patterns

### Pattern 1: Default Settings

```mermaid
graph LR
    A["All Responses"] --> B{Personalized?}
    B -->|No| C["Cache-Control: no-cache"]
    B -->|Yes| D["Cache-Control: no-cache, private"]
    
    style C fill:#3b82f6,color:#fff
    style D fill:#f97316,color:#fff
```

Always validate, never serve stale:

```http
# Non-personalized content
Cache-Control: no-cache

# Personalized content
Cache-Control: no-cache, private
```

---

### Pattern 2: Cache Busting (Static Assets)

```mermaid
graph TB
    subgraph "Cache Busting Strategy"
        HTML["index.html<br/>no-cache"] --> JS["bundle.v123.js<br/>max-age=1year"]
        HTML --> CSS["style.v123.css<br/>max-age=1year"]
        HTML --> IMG["logo.abc123.png<br/>max-age=1year"]
    end
    
    UPDATE["Content Update"] --> NEWURL["New URL:<br/>bundle.v124.js"]
    NEWURL --> CACHE["Old cache ignored<br/>New file cached"]
    
    style HTML fill:#f97316,color:#fff
    style JS fill:#22c55e,color:#fff
    style CSS fill:#22c55e,color:#fff
    style IMG fill:#22c55e,color:#fff
```

For JS, CSS, images - cache forever with versioned URLs:

```html
<!-- Before -->
<script src="bundle.js"></script>

<!-- After (cache busting) -->
<script src="bundle.v123.js"></script>
<script src="bundle.js?v=123"></script>
<script src="bundle.YsAIAAAA.js"></script>
```

```http
# Response for versioned assets
HTTP/1.1 200 OK
Cache-Control: public, max-age=31536000, immutable
Last-Modified: Tue, 22 Feb 2022 20:20:20 GMT
ETag: "YsAIAAAA-QG4G6kCMAMBAAAAAAAoK"
```

| max-age Value | Duration | QPACK Index |
|---------------|----------|-------------|
| `604800` | 1 week | 37 |
| `2592000` | 1 month | 38 |
| `31536000` | 1 year | 41 |

---

### Pattern 3: Main Resources (HTML)

Can't use cache busting (URLs are fixed):

```http
# Non-personalized HTML
HTTP/1.1 200 OK
Content-Type: text/html
Cache-Control: no-cache
Last-Modified: Tue, 22 Feb 2022 20:20:20 GMT
ETag: "AAPuIbAOdvAGEETbgAAAAAAABAAE"
```

```http
# Personalized HTML (after login)
HTTP/1.1 200 OK
Content-Type: text/html
Cache-Control: no-cache, private
Last-Modified: Tue, 22 Feb 2022 20:20:20 GMT
ETag: "AAPuIbAOdvAGEETbgAAAAAAABAAE"
Set-Cookie: __Host-SID=AHNtAyt3fvJrUL5g5tnGwER; Secure; Path=/; HttpOnly
```

---

## Don't Cache vs No-Store

```mermaid
graph TB
    subgraph "no-cache"
        NC1["Store in cache ✓"] --> NC2["Always revalidate before use"]
        NC2 --> NC3["304 or 200 response"]
    end
    
    subgraph "no-store"
        NS1["Don't store at all ✗"] --> NS2["Always fetch from server"]
        NS2 --> NS3["Full response every time"]
    end
    
    style NC1 fill:#22c55e,color:#fff
    style NS1 fill:#ef4444,color:#fff
```

### no-cache ≠ "Don't Cache"

| Directive | Behavior |
|-----------|----------|
| `no-cache` | Store, but always revalidate before use |
| `no-store` | Don't store at all |

### When to Use What

| Scenario | Use This |
|----------|----------|
| Personalized content | `Cache-Control: private` |
| Always fresh content | `Cache-Control: no-cache` |
| Sensitive data | `Cache-Control: no-cache, private` |

> **Avoid:** `no-store` loses browser back/forward cache benefits. Prefer `no-cache, private`.

---

## Reload vs Force Reload

```mermaid
sequenceDiagram
    participant Browser as 🌐 Browser
    participant Cache as 💾 Cache
    participant Server as 🖥️ Server
    
    Note over Browser,Server: Normal Reload (F5)
    Browser->>Cache: max-age=0<br/>If-None-Match: "abc"
    Cache->>Server: Conditional Request
    Server-->>Browser: 304 Not Modified
    
    Note over Browser,Server: Force Reload (Ctrl+Shift+R)
    Browser->>Server: no-cache (bypass cache)
    Server-->>Browser: 200 OK + Full Content
```

### Normal Reload

```http
GET / HTTP/1.1
Cache-Control: max-age=0
If-None-Match: "deadbeef"
If-Modified-Since: Tue, 22 Feb 2022 20:20:20 GMT
```

```js
// JavaScript equivalent
fetch("/", { cache: "no-cache" });
```

### Force Reload (Ctrl+Shift+R)

```http
GET / HTTP/1.1
Pragma: no-cache
Cache-Control: no-cache
```

```js
// JavaScript equivalent
fetch("/", { cache: "reload" });
```

---

## Request Collapse

```mermaid
sequenceDiagram
    participant C1 as Client 1
    participant C2 as Client 2
    participant C3 as Client 3
    participant Cache as 💾 Shared Cache
    participant Server as 🖥️ Server
    
    Note over C1,Server: Simultaneous Requests
    
    C1->>Cache: GET /page
    C2->>Cache: GET /page
    C3->>Cache: GET /page
    
    Cache->>Server: GET /page (single request)
    Server-->>Cache: 200 OK
    
    Cache-->>C1: 200 OK
    Cache-->>C2: 200 OK
    Cache-->>C3: 200 OK
    
    Note over Cache: One request serves all!
```

When multiple identical requests hit a shared cache simultaneously:

1. Cache forwards **one request** to origin
2. Reuses response for **all clients**

> **Note:** Add `private` directive if response is personalized to prevent sharing.

---

## Caching Decision Flowchart

```mermaid
flowchart TD
    A[New Response] --> B{Contains sensitive data?}
    
    B -->|Yes| C{User-specific?}
    B -->|No| D{Static asset?}
    
    C -->|Yes| E["no-cache, private"]
    C -->|No| F["no-store"]
    
    D -->|Yes| G{Can use cache busting?}
    D -->|No| H["no-cache + ETag"]
    
    G -->|Yes| I["max-age=31536000, immutable"]
    G -->|No| H
    
    style E fill:#f97316,color:#fff
    style F fill:#ef4444,color:#fff
    style H fill:#3b82f6,color:#fff
    style I fill:#22c55e,color:#fff
```

---

## Quick Reference

### Static Assets (JS, CSS, Images)

```http
Cache-Control: public, max-age=31536000, immutable
ETag: "hash-value"
Last-Modified: <date>
```

### HTML Pages

```http
Cache-Control: no-cache
ETag: "hash-value"
Last-Modified: <date>
```

### Personalized Content

```http
Cache-Control: no-cache, private
```

### API Responses

```http
# Cacheable
Cache-Control: private, max-age=60

# Not cacheable
Cache-Control: no-cache, private
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
