---
title: "Client-Side Storage: LocalStorage, SessionStorage, Cookies & IndexedDB"
description: "Learn when to use LocalStorage, SessionStorage, Cookies, and IndexedDB for storing data in the browser."
tags: ["web-storage", "local-storage", "session-storage", "indexeddb", "cookies", "browser-storage"]
section: "web-storage"
codepen: "https://codepen.io/SACHIN-CHAUHAN-the-builder/pen/dPMRNGB"
---

# Client-Side Storage

In web development, choosing the right client-side storage is important for building fast and efficient applications.

> Think of it like choosing where to keep your stuff: a pocket (cookies), a locker (session storage), a drawer (local storage), or a warehouse (IndexedDB).

---

## Understanding Origin

Before diving into storage, understand **Origin**:

| Part | Example |
|------|---------|
| **Domain** | etransfer.in |
| **Protocol** | https:// |
| **Port** | :80 |

**Origin = Domain + Protocol + Port**

> LocalStorage is restricted by **Same Origin Policy** - data stored on `https://example.com` cannot be accessed by `http://example.com` or `https://api.example.com`

---

## Quick Comparison

| Feature | LocalStorage | SessionStorage | Cookies | IndexedDB |
|---------|--------------|----------------|---------|-----------|
| **Size** | 5MB per domain | 5MB per domain | 4KB per domain | >100MB |
| **Expires** | Never | Tab/Window close | Configurable | Never |
| **API** | Synchronous | Synchronous | Synchronous | Asynchronous |
| **Cross-tab** | Yes | No | Yes | Yes |
| **Sent to server** | No | No | Yes (every request) | No |

---

## 1. LocalStorage

**5MB per domain | Synchronous | Persistent**

LocalStorage stores data in key-value format that persists forever until manually deleted.

### Key Characteristics

- **Anyone can read** - Any JavaScript code can read your data
- **Can be compromised** - Not secure for sensitive data
- **Same Origin Policy** - Restricted to same domain, protocol, and port
- **Persists forever** - Data stays even after browser restart

### When to Use

✅ User preferences (theme, language)
✅ Cart data when user is NOT logged in
✅ Non-sensitive cached data

### When NOT to Use

❌ Sensitive information (passwords, tokens)
❌ Large datasets
❌ Data that needs server access

### Code Example

```js
// Store data
localStorage.setItem("theme", "dark");
localStorage.setItem("language", "en");

// Store objects (must stringify)
const user = { name: "John", preferences: { theme: "dark" } };
localStorage.setItem("user", JSON.stringify(user));

// Get data
const theme = localStorage.getItem("theme"); // "dark"
const userData = JSON.parse(localStorage.getItem("user"));

// Remove specific item
localStorage.removeItem("theme");

// Clear all localStorage
localStorage.clear();

// Check if key exists
if (localStorage.getItem("theme")) {
  console.log("Theme is set");
}

// Get all keys
for (let i = 0; i < localStorage.length; i++) {
  const key = localStorage.key(i);
  console.log(key, localStorage.getItem(key));
}
```

---

## 2. SessionStorage

**5MB per domain | Synchronous | Temporary**

SessionStorage works like LocalStorage but data is cleared when the browser tab or window closes.

### Key Characteristics

- **Tab-specific** - Each tab has its own storage
- **Cleared on close** - Data deleted when tab/window closes
- **Duplicating tab** - Copies sessionStorage to new tab
- **Good for temporary data** - Forms, wizard steps

### When to Use

✅ Multi-step form data
✅ Temporary sensitive data
✅ Large datasets during async operations
✅ Data that shouldn't persist

### Code Example

```js
// Store temporary form data
sessionStorage.setItem("formStep", "2");
sessionStorage.setItem("formData", JSON.stringify({
  name: "John",
  email: "john@example.com"
}));

// Get data
const step = sessionStorage.getItem("formStep");
const formData = JSON.parse(sessionStorage.getItem("formData"));

// Remove item
sessionStorage.removeItem("formStep");

// Clear all
sessionStorage.clear();
```

### Tab Duplication Behavior

```js
// If you duplicate a tab:
// - SessionStorage is COPIED to the new tab
// - After that, they are independent
// - Changes in one tab don't affect the other
```

---

## 3. Cookies

**4KB per domain | Sent with every HTTP request**

Cookies are small pieces of data sent to the server with every HTTP request.

### Key Characteristics

- **4KB limit per domain** - Very small storage
- **Affects HTTP performance** - Sent with EVERY request, large cookies slow down your site
- **Expiration control** - Can set when cookie expires
- **Two types**:
  - **Session Cookie** - No expiry date, deleted when browser closes
  - **Persistent Cookie** - Has expiry date, stays until that date

### How Cookie Expiration Works

```
No expiration date provided → Session Cookie → Expires on browser close
Expiration date provided → Persistent Cookie → Expires on that date
```

> **Important:** If you don't set an expiration date, the cookie becomes a session cookie and will be deleted when the user closes their browser.

### Cookie Attributes Explained

| Attribute | What It Does | Example |
|-----------|--------------|---------|
| `HttpOnly` | **Cannot be read by JavaScript** - Protects from XSS attacks | `HttpOnly` |
| `Secure` | **Only sent over HTTPS** - Protects from man-in-middle attacks | `Secure` |
| `SameSite` | Controls cross-site requests | `SameSite=Strict` |
| `Path` | Cookie only sent for this path | `Path=/user/` |
| `Domain` | Cookie sent to all subdomains | `Domain=.example.com` |
| `Expires` | When cookie expires | `Expires=Fri, 31 Dec 2025` |
| `Max-Age` | Seconds until expiry | `Max-Age=86400` |

### SameSite Values

| Value | Behavior |
|-------|----------|
| `Strict` | Only sent from same site (most secure) |
| `Lax` | Sent on top-level navigation (default) |
| `None` | Sent on all requests (requires `Secure`) |

### Cookie Attributes in Detail

**HttpOnly** - Security First
```js
// ❌ Without HttpOnly - JavaScript can read it (vulnerable to XSS)
document.cookie = "token=abc123";
console.log(document.cookie); // "token=abc123" - Hackers can steal this!

// ✅ With HttpOnly - Set by server, JavaScript CANNOT read it
// Set-Cookie: token=abc123; HttpOnly
// document.cookie won't show this cookie
```

**Secure** - HTTPS Only
```js
// Cookie only sent when connection is HTTPS
document.cookie = "token=abc123; Secure";

// On HTTP site: Cookie NOT sent
// On HTTPS site: Cookie IS sent
```

**Path** - Restrict to URL Path
```js
// Cookie only sent for requests to /user/ and below
document.cookie = "userId=123; Path=/user/";

// Request to /user/profile → Cookie SENT
// Request to /user/settings → Cookie SENT  
// Request to /admin → Cookie NOT sent
// Request to / → Cookie NOT sent
```

**Domain** - Subdomain Sharing
```js
// Cookie sent to all subdomains
document.cookie = "session=xyz; Domain=.example.com";

// Sent to: example.com, api.example.com, app.example.com
```

### Cookie Attributes

| Attribute | Purpose |
|-----------|---------|
| `HttpOnly` | Cannot be read by JavaScript (security) |
| `Secure` | Only sent over HTTPS |
| `SameSite=Strict` | Only sent from same site |
| `SameSite=Lax` | Sent on top-level navigation |
| `Domain` | Sent to all subdomains (e.g., *.example.com) |
| `Path` | Restrict to specific path |

### When to Use

✅ Authentication tokens (with HttpOnly)
✅ Data server needs to read
✅ Cross-subdomain data sharing

### When NOT to Use

❌ Large data (affects HTTP performance)
❌ Sensitive info without HttpOnly
❌ Client-only data

### Code Example

```js
// Set a simple cookie
document.cookie = "username=John";

// Set cookie with expiration (7 days)
const expires = new Date(Date.now() + 7 * 24 * 60 * 60 * 1000);
document.cookie = `username=John; expires=${expires.toUTCString()}`;

// Set secure cookie
document.cookie = "token=abc123; secure; samesite=strict; path=/";

// Read all cookies
console.log(document.cookie); // "username=John; token=abc123"

// Parse cookies into object
function getCookies() {
  return document.cookie.split(';').reduce((cookies, cookie) => {
    const [name, value] = cookie.trim().split('=');
    cookies[name] = value;
    return cookies;
  }, {});
}

// Get specific cookie
function getCookie(name) {
  const match = document.cookie.match(new RegExp('(^| )' + name + '=([^;]+)'));
  return match ? match[2] : null;
}

// Delete cookie (set past expiry)
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/";
```

### Session vs Persistent Cookies

```js
// Session Cookie - no expiry, deleted when browser closes
document.cookie = "sessionId=abc123";

// Persistent Cookie - has expiry date
document.cookie = "userId=123; expires=Fri, 31 Dec 2025 23:59:59 GMT";
```

---

## 4. IndexedDB

**>100MB | Asynchronous | Persistent**

IndexedDB is a full NoSQL database in the browser for large, structured data.

### Key Characteristics

- **Large storage** - Can store 100MB+ of data
- **Asynchronous** - Doesn't block the UI
- **Persistent** - Data survives browser restart
- **Structured data** - Supports indexes for fast queries

### When to Use

✅ Large datasets
✅ Data caching for offline apps
✅ Lots of history/logs
✅ Files and blobs

### When NOT to Use

❌ Sensitive information
❌ Simple key-value data (use LocalStorage)
❌ When you need synchronous access

### Code Example

```js
// Open database
const request = indexedDB.open("MyApp", 1);

// Create structure (runs on first open or version change)
request.onupgradeneeded = (event) => {
  const db = event.target.result;
  
  // Create object store (like a table)
  const store = db.createObjectStore("users", { keyPath: "id" });
  
  // Create index for faster queries
  store.createIndex("email", "email", { unique: true });
  store.createIndex("name", "name", { unique: false });
};

// Handle success
request.onsuccess = (event) => {
  const db = event.target.result;
  console.log("Database opened successfully");
};

// Handle error
request.onerror = (event) => {
  console.error("Database error:", event.target.error);
};
```

### CRUD Operations

```js
// ADD data
function addUser(db, user) {
  const tx = db.transaction("users", "readwrite");
  const store = tx.objectStore("users");
  store.add(user);
  
  tx.oncomplete = () => console.log("User added");
  tx.onerror = () => console.error("Error adding user");
}

// READ data
function getUser(db, id) {
  const tx = db.transaction("users", "readonly");
  const store = tx.objectStore("users");
  const request = store.get(id);
  
  request.onsuccess = () => console.log(request.result);
}

// UPDATE data
function updateUser(db, user) {
  const tx = db.transaction("users", "readwrite");
  const store = tx.objectStore("users");
  store.put(user); // put updates if exists, adds if not
}

// DELETE data
function deleteUser(db, id) {
  const tx = db.transaction("users", "readwrite");
  const store = tx.objectStore("users");
  store.delete(id);
}

// GET ALL
function getAllUsers(db) {
  const tx = db.transaction("users", "readonly");
  const store = tx.objectStore("users");
  const request = store.getAll();
  
  request.onsuccess = () => console.log(request.result);
}
```

---

## Storage Priority Guide

When deciding which storage to use:

```
Simple preferences → LocalStorage
Temporary data → SessionStorage  
Auth/Server data → Cookies
Large/Complex data → IndexedDB
```

---

## Security Best Practices

### Don't Store Sensitive Data

```js
// ❌ BAD - Never store passwords or tokens in LocalStorage
localStorage.setItem("password", "secret123");
localStorage.setItem("authToken", "jwt-token-here");

// ✅ GOOD - Use HttpOnly cookies for auth tokens
// (Set by server, not accessible via JavaScript)
```

### Validate Data

```js
// Always validate data from storage
const userData = localStorage.getItem("user");
try {
  const user = JSON.parse(userData);
  if (user && user.name) {
    // Safe to use
  }
} catch (e) {
  // Invalid data, clear it
  localStorage.removeItem("user");
}
```

### Clear Sensitive Data on Logout

```js
function logout() {
  // Clear all storage
  localStorage.clear();
  sessionStorage.clear();
  
  // Clear cookies
  document.cookie.split(";").forEach((c) => {
    document.cookie = c.trim().split("=")[0] + 
      "=;expires=Thu, 01 Jan 1970 00:00:00 UTC;path=/";
  });
}
```

---

## Summary

| Storage | Size | Best For |
|---------|------|----------|
| **LocalStorage** | 5MB | User preferences, non-sensitive persistent data |
| **SessionStorage** | 5MB | Temporary data, form wizards |
| **Cookies** | 4KB | Auth tokens, server-needed data |
| **IndexedDB** | 100MB+ | Large data, offline apps, caching |

> **Golden Rule:** Never store sensitive information in client-side storage without proper encryption. Use HttpOnly cookies for authentication tokens.
