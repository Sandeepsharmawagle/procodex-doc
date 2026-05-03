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

### Interactive Demo

```playground
title=Local Storage Demo
height=350
[html]
<div class="container">
  <h2>Local Storage Demo</h2>
  <input type="text" id="input" placeholder="Enter a value..." />
  <button onclick="saveData()">Save to LocalStorage</button>
  <button onclick="loadData()">Load from LocalStorage</button>
  <button onclick="clearData()">Clear</button>
  <p id="output">Stored value will appear here</p>
</div>
[css]
.container {
  text-align: center;
}
input {
  padding: 10px;
  margin: 10px;
  border: 1px solid #444;
  border-radius: 5px;
  background: #2a2a3e;
  color: #fff;
  width: 200px;
}
button {
  padding: 10px 15px;
  margin: 5px;
  border: none;
  border-radius: 5px;
  background: #6366f1;
  color: white;
  cursor: pointer;
}
button:hover {
  background: #4f46e5;
}
#output {
  margin-top: 20px;
  padding: 15px;
  background: #2a2a3e;
  border-radius: 5px;
}
[js]
function saveData() {
  const value = document.getElementById('input').value;
  localStorage.setItem('myData', value);
  document.getElementById('output').textContent = 'Saved: ' + value;
}

function loadData() {
  const value = localStorage.getItem('myData');
  document.getElementById('output').textContent = value ? 'Loaded: ' + value : 'No data found';
}

function clearData() {
  localStorage.removeItem('myData');
  document.getElementById('output').textContent = 'Data cleared!';
  document.getElementById('input').value = '';
}

// Load existing data on start
loadData();
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

### Interactive Demo

```playground
title=Session Storage Demo
height=350
[html]
<div class="container">
  <h2>Session Storage Demo</h2>
  <p>This data will be lost when you close the tab!</p>
  <input type="text" id="sessionInput" placeholder="Enter temporary data..." />
  <button onclick="saveSession()">Save to SessionStorage</button>
  <button onclick="loadSession()">Load</button>
  <p id="sessionOutput">Session data will appear here</p>
  <p class="note">Try closing and reopening this tab - data will be gone!</p>
</div>
[css]
.container { text-align: center; }
input {
  padding: 10px;
  margin: 10px;
  border: 1px solid #444;
  border-radius: 5px;
  background: #2a2a3e;
  color: #fff;
  width: 200px;
}
button {
  padding: 10px 15px;
  margin: 5px;
  border: none;
  border-radius: 5px;
  background: #10b981;
  color: white;
  cursor: pointer;
}
button:hover { background: #059669; }
#sessionOutput {
  margin-top: 20px;
  padding: 15px;
  background: #2a2a3e;
  border-radius: 5px;
}
.note {
  font-size: 12px;
  color: #888;
  margin-top: 10px;
}
[js]
function saveSession() {
  const value = document.getElementById('sessionInput').value;
  sessionStorage.setItem('tempData', value);
  document.getElementById('sessionOutput').textContent = 'Saved: ' + value;
}

function loadSession() {
  const value = sessionStorage.getItem('tempData');
  document.getElementById('sessionOutput').textContent = value ? 'Loaded: ' + value : 'No session data';
}

loadSession();
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

### Interactive Demo

```playground
title=Cookies Demo
height=380
[html]
<div class="container">
  <h2>Cookies Demo</h2>
  <div class="input-group">
    <input type="text" id="cookieName" placeholder="Cookie name" />
    <input type="text" id="cookieValue" placeholder="Cookie value" />
  </div>
  <button onclick="setCookie()">Set Cookie</button>
  <button onclick="getCookies()">Get All Cookies</button>
  <button onclick="deleteCookie()">Delete Cookie</button>
  <pre id="cookieOutput">Cookies will appear here</pre>
</div>
[css]
.container { text-align: center; }
.input-group { margin: 10px 0; }
input {
  padding: 10px;
  margin: 5px;
  border: 1px solid #444;
  border-radius: 5px;
  background: #2a2a3e;
  color: #fff;
  width: 150px;
}
button {
  padding: 10px 15px;
  margin: 5px;
  border: none;
  border-radius: 5px;
  background: #f59e0b;
  color: white;
  cursor: pointer;
}
button:hover { background: #d97706; }
#cookieOutput {
  margin-top: 20px;
  padding: 15px;
  background: #2a2a3e;
  border-radius: 5px;
  text-align: left;
  white-space: pre-wrap;
  font-size: 13px;
}
[js]
function setCookie() {
  const name = document.getElementById('cookieName').value;
  const value = document.getElementById('cookieValue').value;
  if (name && value) {
    document.cookie = name + '=' + value + '; max-age=3600';
    getCookies();
  }
}

function getCookies() {
  const cookies = document.cookie;
  document.getElementById('cookieOutput').textContent = 
    cookies ? 'Cookies:\n' + cookies.split('; ').join('\n') : 'No cookies set';
}

function deleteCookie() {
  const name = document.getElementById('cookieName').value;
  if (name) {
    document.cookie = name + '=; max-age=0';
    getCookies();
  }
}

getCookies();
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

### Interactive Demo

```playground
title=IndexedDB Demo
height=400
[html]
<div class="container">
  <h2>IndexedDB Demo</h2>
  <div class="input-group">
    <input type="text" id="itemName" placeholder="Item name" />
    <input type="number" id="itemPrice" placeholder="Price" />
  </div>
  <button onclick="addItem()">Add Item</button>
  <button onclick="getAllItems()">Get All Items</button>
  <button onclick="clearDB()">Clear Database</button>
  <div id="dbOutput">Items will appear here</div>
</div>
[css]
.container { text-align: center; }
.input-group { margin: 10px 0; }
input {
  padding: 10px;
  margin: 5px;
  border: 1px solid #444;
  border-radius: 5px;
  background: #2a2a3e;
  color: #fff;
  width: 150px;
}
button {
  padding: 10px 15px;
  margin: 5px;
  border: none;
  border-radius: 5px;
  background: #8b5cf6;
  color: white;
  cursor: pointer;
}
button:hover { background: #7c3aed; }
#dbOutput {
  margin-top: 20px;
  padding: 15px;
  background: #2a2a3e;
  border-radius: 5px;
  min-height: 100px;
}
.item {
  padding: 8px;
  margin: 5px 0;
  background: #3a3a4e;
  border-radius: 4px;
}
[js]
let db;
const request = indexedDB.open('ShopDB', 1);

request.onerror = () => console.log('DB Error');
request.onsuccess = (e) => {
  db = e.target.result;
  getAllItems();
};
request.onupgradeneeded = (e) => {
  db = e.target.result;
  db.createObjectStore('items', { keyPath: 'id', autoIncrement: true });
};

function addItem() {
  const name = document.getElementById('itemName').value;
  const price = document.getElementById('itemPrice').value;
  if (name && price) {
    const tx = db.transaction('items', 'readwrite');
    tx.objectStore('items').add({ name, price: Number(price) });
    tx.oncomplete = () => {
      document.getElementById('itemName').value = '';
      document.getElementById('itemPrice').value = '';
      getAllItems();
    };
  }
}

function getAllItems() {
  const tx = db.transaction('items', 'readonly');
  const req = tx.objectStore('items').getAll();
  req.onsuccess = () => {
    const items = req.result;
    const output = document.getElementById('dbOutput');
    if (items.length === 0) {
      output.innerHTML = 'No items in database';
    } else {
      output.innerHTML = items.map(i => 
        '<div class="item">' + i.name + ' - $' + i.price + '</div>'
      ).join('');
    }
  };
}

function clearDB() {
  const tx = db.transaction('items', 'readwrite');
  tx.objectStore('items').clear();
  tx.oncomplete = getAllItems;
}
```

---

## Comparison Table

| Feature | Local Storage | Session Storage | Cookies | IndexedDB |
|---------|--------------|-----------------|---------|-----------|
| Size Limit | ~5MB | ~5MB | ~4KB | 100MB+ |
| Expiry | Never | Tab close | Configurable | Never |
| Shared Across Tabs | Yes | No | Yes | Yes |
| Sent with HTTP | No | No | Yes | No |
| Data Type | String | String | String | Any |
| Async | No | No | No | Yes |
