---
title: "Client-Side Storage: Local Storage, Session Storage & IndexedDB"
description: "A crisp guide to choosing the right browser storage - Local Storage, Session Storage, and IndexedDB with use cases, pros/cons, and code examples."
author: "ProCodeX Team"
date: "2024-01-30"
tags:
  [
    "web-storage",
    "local-storage",
    "session-storage",
    "indexeddb",
    "browser-storage",
    "client-side-storage",
  ]
section: "web-storage"
codepen: "https://codepen.io/SACHIN-CHAUHAN-the-builder/pen/dPMRNGB"
---

# Client-Side Storage: Choosing the Right Option

> Modern web apps demand fast, seamless experiences. Understanding client-side storage is key to delivering that.

In the web development landscape, choosing the correct client-side storage option is crucial for creating responsive and effective applications.

---

## Storage Options at a Glance

```
┌───────────────────────────────────────────────┐
│           CLIENT-SIDE STORAGE                 │
├───────────────┬───────────────┬───────────────┤
│ LOCAL STORAGE │SESSION STORAGE│  INDEXEDDB    │
├───────────────┼───────────────┼───────────────┤
│ 📦 5-10 MB    │ 📦 ~5 MB      │ 📦 GBs+       │
│ 🔑 Key-Value  │ 🔑 Key-Value  │ 🗄️ Structured │
│ ♾️ Persistent │ ⏱️ Tab Only   │ ♾️ Persistent │
│ 🌐 Cross-Tab  │ 🔒 Tab Only   │ 🌐 Cross-Tab  │
│ ⚡ Sync API   │ ⚡ Sync API   │ ⚡ Async API  │
└───────────────┴───────────────┴───────────────┘
```

---

## 1. Local Storage

**Persistent key-value storage that survives browser restarts.**

```
┌────────────────────────────────────┐
│        LOCAL STORAGE FLOW          │
├────────────────────────────────────┤
│                                    │
│  [Browser] → [Set Data] → [Store] │
│                              │     │
│  [Close Browser]             │     │
│       │                      ▼     │
│       └→ [Reopen] ← [Data OK ✓]   │
│                                    │
│  ✓ Data persists indefinitely     │
└────────────────────────────────────┘
```

Local Storage stores data persistently in key/value pairs. Data doesn't expire - even after closing and reopening the browser, data remains accessible.

### Key Facts

| Property | Value |
|----------|-------|
| **Persistence** | Never expires until deleted |
| **Capacity** | 5-10 MB |
| **Data Type** | Strings only |
| **Scope** | Shared across all tabs |
| **API** | Synchronous |

### API Methods

```js
// Set item
localStorage.setItem("key", "value");

// Get item
let data = localStorage.getItem("key");

// Remove item
localStorage.removeItem("key");

// Clear all
localStorage.clear();

// Store objects
const user = { name: "John", age: 25 };
localStorage.setItem("user", JSON.stringify(user));

// Retrieve objects
const stored = JSON.parse(localStorage.getItem("user"));
```

---

## 2. Session Storage

**Temporary storage that lasts only for the current tab.**

```
┌────────────────────────────────────┐
│       SESSION STORAGE FLOW         │
├────────────────────────────────────┤
│                                    │
│  [Tab] → [Set Data] → [Storage]   │
│                           │        │
│  [Close Tab]              │        │
│       │                   ▼        │
│       └────→ [Data Gone ✗]        │
│                                    │
│  ✗ Data cleared when tab closes   │
└────────────────────────────────────┘
```

Session Storage works like Local Storage but data only lasts for the page session. Once the tab closes, all data is lost.

### Key Facts

| Property | Value |
|----------|-------|
| **Persistence** | Cleared on tab close |
| **Capacity** | ~5 MB |
| **Data Type** | Strings only |
| **Scope** | Tab-specific only |
| **API** | Synchronous |

### API Methods

```js
// Set item
sessionStorage.setItem("key", "value");

// Get item
let data = sessionStorage.getItem("key");

// Remove item
sessionStorage.removeItem("key");

// Clear all
sessionStorage.clear();
```

---

## 3. IndexedDB

**Full NoSQL database in the browser.**

```
┌────────────────────────────────────┐
│        INDEXEDDB STRUCTURE         │
├────────────────────────────────────┤
│                                    │
│  🗄️ Database                      │
│  ├── 📁 Object Store: Users       │
│  │   ├── Index: email             │
│  │   └── Index: name              │
│  ├── 📁 Object Store: Products    │
│  │   └── Index: category          │
│  └── 📁 Object Store: Orders      │
│                                    │
│  ✓ Complex queries & indexing     │
└────────────────────────────────────┘
```

IndexedDB is a full NoSQL database for large, structured data including files and blobs.

### Key Facts

| Property | Value |
|----------|-------|
| **Persistence** | Until deleted |
| **Capacity** | Hundreds of MBs to GBs |
| **Data Type** | Objects, files, blobs |
| **Scope** | Shared across tabs |
| **API** | Asynchronous |

### Basic Example

```js
// Open database
let request = indexedDB.open("myDB", 1);

// Create schema
request.onupgradeneeded = (event) => {
  let db = event.target.result;
  db.createObjectStore("users", { keyPath: "id" });
};

// Use database
request.onsuccess = (event) => {
  let db = event.target.result;
  let tx = db.transaction("users", "readwrite");
  let store = tx.objectStore("users");
  store.add({ id: 1, name: "John" });
};
```

---

## Comparison Table

| Feature | Local | Session | IndexedDB |
|---------|-------|---------|-----------|
| **Persistence** | Forever | Tab only | Forever |
| **Size** | 5-10 MB | ~5 MB | GBs+ |
| **Data Type** | String | String | Any |
| **API** | Sync | Sync | Async |
| **Cross-Tab** | ✓ | ✗ | ✓ |

---

## Decision Guide

```
┌─────────────────────────────────────┐
│      WHICH STORAGE TO USE?          │
├─────────────────────────────────────┤
│                                     │
│  Data Size?                         │
│     │                               │
│  ┌──┴──┐                           │
│  ▼     ▼                           │
│ Small  Large → IndexedDB           │
│  │                                  │
│  ▼                                  │
│ Persist after tab close?            │
│     │                               │
│  ┌──┴──┐                           │
│  ▼     ▼                           │
│ Yes    No → Session Storage        │
│  │                                  │
│  ▼                                  │
│ Need complex queries?               │
│     │                               │
│  ┌──┴──┐                           │
│  ▼     ▼                           │
│ Yes    No → Local Storage          │
│  │                                  │
│  └──→ IndexedDB                    │
│                                     │
└─────────────────────────────────────┘
```

---

## When to Use Each

### ✅ Local Storage

- User preferences (themes, language)
- Data that persists between sessions
- Simple key-value storage (< 5 MB)

**Examples:** Theme, language, feature flags

### ✅ Session Storage

- Session-specific data (checkout cart)
- Temporary data that shouldn't persist
- Data that shouldn't share across tabs

**Examples:** Form wizard, temp filters, tokens

### ✅ IndexedDB

- Large amounts of structured data
- Complex queries with indexing
- Offline-first applications
- File/blob storage

**Examples:** Offline data, cached APIs, files

---

## Pros and Cons

### Local Storage

| ✅ Pros | ❌ Cons |
|---------|---------|
| Easy to use | 5-10 MB limit |
| Persists forever | Strings only |
| Simple API | Blocks main thread |

### Session Storage

| ✅ Pros | ❌ Cons |
|---------|---------|
| Auto-cleanup | Same size limits |
| Tab isolation | No cross-tab |
| Simple API | Strings only |

### IndexedDB

| ✅ Pros | ❌ Cons |
|---------|---------|
| Massive storage | Complex API |
| Any data type | Learning curve |
| Async (non-blocking) | More code |

---

## Quick Reference

```
┌─────────────────────────────────────┐
│         QUICK REFERENCE             │
├─────────────────────────────────────┤
│                                     │
│ Simple + Persistent → Local Storage │
│                                     │
│ Simple + Temporary → Session Storage│
│                                     │
│ Complex + Large → IndexedDB         │
│                                     │
└─────────────────────────────────────┘
```

---

## Conclusion

Choose based on your needs:

- **Local Storage** → Simple settings that persist
- **Session Storage** → Temporary session data
- **IndexedDB** → Large, structured, offline data

> Understanding these options helps you build faster, more resilient web applications.
