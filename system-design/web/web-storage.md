---
title: "Web Storage Guide: Local Storage, Session Storage, Cookies, and IndexedDB"
description: "Comprehensive guide to browser storage options: Local Storage, Session Storage, Cookies, and IndexedDB. Learn when to use each, their capabilities, limitations, and best practices."
author: "ProCodeX Team"
date: "2024-01-30"
tags:
  [
    "web-storage",
    "local-storage",
    "session-storage",
    "cookies",
    "indexeddb",
    "browser-storage",
    "web-development",
  ]
section: "system-design"
codepen: "https://codepen.io/SACHIN-CHAUHAN-the-builder/pen/dPMRNGB"
---

---

# Web Storage With Methods & Examples

This file explains **Local Storage**, **Session Storage**, **Cookies**, and **IndexedDB** with differences, methods, and working code examples.

---

# 1. Local Storage

Local Storage stores data **persistently** across browser sessions.

## Key Points

- Data persists even after the browser is closed.
- Storage capacity: **5–10 MB**.
- Stored **per domain** and shared across all tabs of that domain.
- Stores **string data only**.
- Not secure for sensitive information.
- Suitable for **long-term** storage of non-sensitive data.
- Uses a simple **key–value** structure.

## Methods

- `localStorage.setItem(key, value)`
- `localStorage.getItem(key)`
- `localStorage.removeItem(key)`
- `localStorage.clear()`

## Example

```js
localStorage.setItem("name", "Sachin");
const user = localStorage.getItem("name");
console.log(user); // Sachin

localStorage.removeItem("name");
localStorage.clear();
```

---

# 2. Session Storage

Session Storage is similar to Local Storage but lasts only for a single session.

## Key Points

- Data is cleared when the **tab or window closes**.
- Storage capacity: **~5 MB**.
- **Tab-specific**.
- Client-side only.

## Methods

- `sessionStorage.setItem(key, value)`
- `sessionStorage.getItem(key)`
- `sessionStorage.removeItem(key)`
- `sessionStorage.clear()`

## Example

```js
sessionStorage.setItem("token", "12345");
console.log(sessionStorage.getItem("token"));
sessionStorage.removeItem("token");
sessionStorage.clear();
```

---

# 3. Cookies

Cookies are small pieces of data stored in the browser and are automatically sent to the server with HTTP requests (unless flagged otherwise).

## Key Points

- Very small storage limit: **~4 KB**.
- Can have an expiry time.
- Automatically included in **HTTP requests** (unless `HttpOnly`/`SameSite`/`Secure` rules apply).
- Can be secured using `HttpOnly`, `Secure`, `SameSite`.

## Example

### Set Cookie

```js
document.cookie =
  "username=Sachin; expires=Fri, 31 Dec 2025 23:59:59 GMT; path=/";
```

### Get Cookies

```js
console.log(document.cookie);
```

### Delete Cookie

```js
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 GMT; path=/";
```

---

# 4. IndexedDB

IndexedDB is an in-browser NoSQL database for storing large and structured data.

## Key Points

- Can store **MBs to GBs**.
- Supports objects, arrays, images, files, and other complex data.
- Asynchronous and non-blocking.
- Ideal for offline-capable applications.

## Basic Example

```js
const request = indexedDB.open("MyDB", 1);

request.onupgradeneeded = function (event) {
  const db = event.target.result;
  db.createObjectStore("users", { keyPath: "id" });
};

request.onsuccess = function (event) {
  const db = event.target.result;
  const tx = db.transaction("users", "readwrite");
  const store = tx.objectStore("users");
  store.add({ id: 1, name: "Sachin", age: 22 });
};
```

---

## Differences Between Web Storage Types

| Feature                      | Local Storage  | Session Storage  | Cookies         | IndexedDB              |
| ---------------------------- | -------------- | ---------------- | --------------- | ---------------------- |
| Persistence                  | Until cleared  | Until tab closes | Based on expiry | Persistent             |
| Size                         | 5–10 MB        | ~5 MB            | ~4 KB           | MBs–GBs                |
| Shared Across Tabs           | Yes            | No               | Yes             | Yes                    |
| Sent to Server Automatically | No             | No               | Yes             | No                     |
| Data Type                    | Strings        | Strings          | Strings         | Complex data (objects) |
| Use Case                     | Long-term data | Temporary data   | Auth, tracking  | Offline/large storage  |

---
