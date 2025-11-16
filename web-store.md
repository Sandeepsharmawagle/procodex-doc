## What is Local Storage?
Local Storage is a feature in web browsers that allows developers to save data in the user's browser. It's part of the Web Storage API, together with session storage.

- Local storage works by accepting data in key-value pairs.
- It retains the data even when the user refreshes the page or closes the tab or browser.

## Properties of Local Storage

### 1. **Storage Capacity**
- **Size**: Up to 5-10 MB per domain (typically 5MB in most browsers)
- Much larger than cookies (4KB limit)

### 2. **Data Persistence**
- Data persists indefinitely until explicitly deleted
- Survives browser restarts, tab closures, and system reboots
- Only cleared by user action or programmatic deletion

### 3. **Scope**
- **Domain-specific**: Each domain has its own separate storage
- **Protocol-specific**: `http://` and `https://` have separate storage
- **Accessible across tabs**: All tabs/windows from same domain share the same storage

### 4. **Data Type**
- Stores only **strings** (key-value pairs)
- Both keys and values must be strings
- Requires JSON conversion for objects/arrays

### 5. **Synchronous Operation**
- All operations are synchronous (blocking)
- May impact performance with large data operations

### 6. **Security**
- No encryption by default
- Accessible via JavaScript (vulnerable to XSS attacks)
- Not sent to server with HTTP requests (unlike cookies)

### 7. **Accessibility**
- Available on `window.localStorage` object
- Accessible only from client-side JavaScript
- Not accessible from web workers or service workers

### 8. **Browser Support**
- Supported in all modern browsers
- IE 8+ and all modern browsers (Chrome, Firefox, Safari, Edge)

### 9. **length Property**
- Returns the number of items stored in local storage
```js
  localStorage.length  // Returns number of stored items
```

### How to Use Local Storage
The Local Storage object provides different methods you can use to interact with it. With these methods, you can add, read and delete data from local storage.

- **Store Data**: For storing the data, use `setItem()` method. It takes two arguments, a key and a value.
```js
  localStorage.setItem(key, value)
```
  If the key does not exist in local storage, the `setItem()` method will create a new key and assign the given value to it. But if a key with the same name exists in local storage, it will update the value of the key with the provided value.

- **Read Data**: To retrieve and use data from local storage, you use the `getItem()` method. This method takes in a key as an argument.
```js
  localStorage.getItem(key)
```
  If the given key exists in local storage, the method returns the value of that key. If it doesn't, the method returns `null`.

- **Store Complex Data**: Local storage only stores strings. For objects or arrays, use `JSON.stringify()` and `JSON.parse()`.
```js
  // Storing
  const user = { name: "Maria", email: "maria@mail.com" }
  localStorage.setItem('user', JSON.stringify(user))
  
  // Retrieving
  const storedUser = localStorage.getItem('user')
  if (storedUser) {
    const userData = JSON.parse(storedUser)
  }
```

- **Delete Data**: Remove a single item with `removeItem()` or clear all data with `clear()`.
```js
  localStorage.removeItem(key)  // Delete single item
  localStorage.clear()          // Delete all items
```

- **Get Key Name**: Use `key()` method to get the name of a key at a specific index.
```js
  localStorage.key(0)  // Returns key name at index 0
```

### Benefits
- **Persistent data**: Data remains after closing browser
- **Offline access**: Works without internet connection
- **More capacity**: Up to 5MB per domain (vs 4KB for cookies)

### Limitations
- **Strings only**: Must convert objects/arrays using JSON methods
- **Security concerns**: Vulnerable to XSS attacks - avoid storing sensitive data
- **No web worker access**: Not available in background threads