# Web Storage

---

## Local Storage

Local Storage is a simple way to store data in the browser as key-value pairs.
The data stays even after refreshing or closing the browser.

### Key Points

- Stores data as string only
- Max size ~5MB per domain
- Shared across tabs (same website, same origin)
- Does not expire automatically
- Synchronous
- Stays even after browser restart

### Use Cases

- User Preferences
- Use for themes e.g (Dark Mode or Light Mode / Theme)

### Limitations

- Not secure (visible in DevTools)
- Anyone can read it
- Only string (we need JSON for objects)

### Example

```js
localStorage.setItem("theme", "dark");
localStorage.getItem("theme");
```

---

## Session Storage

Session Storage is similar to Local Storage but works only for a single tab.
Once the tab is closed, the data is removed.

### Key Points

- 5MB per domain
- Synchronous
- Data exists per tab & not sharing between tabs
- Cleared when tab close, window close
- Not shared across tabs
- Duplicating the tab copies the tab sessionStorage into a new tab
- Same as localStorage but only for one tab session
- The data store in only string, for object we need JSON

### Use Cases

- Temporary Form Data
- Navigation Flow (multi-step forms, checkout steps)
- One-Time Data (OTP state, flags)
- Avoid Re-computation (temporary UI or filtered data)

### Limitations

- Data lost on tab close
- Not shared across tabs
- Not secure (vulnerable to XSS)
- Only stores strings (needs JSON conversion)
- No server access

### Example

```js
sessionStorage.setItem("score", "50");
```

---

## Cookies

Cookies are small pieces of data stored in the browser and sent to the server with every request.

### Key Points

- Size limit ~4KB (very small size)
- Can have expiry date
- Sent with HTTP requests
- Can be secured, cannot be read by JavaScript (HttpOnly, Secure)
- HTTP req/res time can be affected due to the cookie size
- The data can only be sent in string, not in object or array

### Use Cases

- Authentication (login sessions)
- Tracking users
- Remembering user login

### Limitations

- Very small size
- Slower (sent with every request)
- Security risks if not handled properly

### Example

```js
document.cookie = "user=raman";
```

---

## IndexedDB

IndexedDB is a full database inside the browser. It is used for storing large and complex data.

### Key Points

- Stores large data (100MB+)
- Supports objects, arrays, files (images, blobs)
- Works asynchronously (non blocking)
- Very fast for searching data
- Don't store sensitive information

### Use Cases

- Offline Apps
- Caching API Data
- Complex data handling

### Example

```js
const request = indexedDB.open("MyDB", 1);
```
