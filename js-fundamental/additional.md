### **EventEmitter (Custom Implementation)**

- **Purpose**: Manages custom events by allowing you to emit and listen to events.
- **Methods**:
  - `on(eventName, listener)`: Adds an event listener for a specific event.
  - `emit(eventName, ...args)`: Triggers the event, calling all the associated listeners.
  - `removeListener(eventName, listener)`: Removes a specific listener from an event.
  - `removeAllListeners(eventName)`: Removes all listeners for a specific event.
- **Use Case**: Useful in scenarios where you want components to communicate without directly coupling them.

#### **Example**:

```javascript
class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(eventName, listener) {
    if (!this.events[eventName]) {
      this.events[eventName] = [];
    }
    this.events[eventName].push(listener);
  }

  emit(eventName, ...args) {
    if (this.events[eventName]) {
      this.events[eventName].forEach(listener => listener(...args));
    }
  }

  removeListener(eventName, listener) {
    if (this.events[eventName]) {
      this.events[eventName] = this.events[eventName].filter(l => l !== listener);
    }
  }

  removeAllListeners(eventName) {
    if (this.events[eventName]) {
      this.events[eventName] = [];
    }
  }
}

// Example Usage
const emitter = new EventEmitter();
emitter.on('greet', name => console.log(`Hello, ${name}!`));
emitter.emit('greet', 'Alice'); // Output: Hello, Alice!
```

---

### **Promise.all, Promise.race, and Promise.any**

#### **Promise.all**

- **Purpose**: Resolves when all promises resolve or rejects if any promise rejects.
- **Behavior**:
  - Resolves with an array of results from all promises.
  - Rejects immediately if one of the promises is rejected.

#### **Example**:

```javascript
Promise.all([Promise.resolve(1), Promise.resolve(2)])
  .then(values => console.log(values))  // Output: [1, 2]
  .catch(error => console.log(error));
```

---

#### **Promise.race**

- **Purpose**: Resolves or rejects as soon as the first promise settles (either resolves or rejects).
- **Behavior**:
  - Resolves with the first resolved promise or rejects with the first rejected promise.

#### **Example**:

```javascript
Promise.race([Promise.resolve(1), Promise.reject('Error')])
  .then(value => console.log(value))  // Output: 1 (resolved first)
  .catch(error => console.log(error)); // Output: Error (if rejected first)
```

---

#### **Promise.any**

- **Purpose**: Resolves when the first promise resolves, ignoring rejections.
- **Behavior**:
  - Resolves with the first successfully resolved promise.
  - Rejects only if **all** promises are rejected.

#### **Example**:

```javascript
Promise.any([Promise.reject('Error 1'), Promise.resolve(2), Promise.resolve(3)])
  .then(value => console.log(value))  // Output: 2 (first resolved)
  .catch(error => console.log(error)); // Output: AggregateError if all reject
```


Sure! Let's break down the topics with brief explanations and code examples.

### **1. JavaScript Security**

#### **Key Concepts**:
- **Cross-Site Scripting (XSS)**:
  - **Definition**: An attack that injects malicious scripts into web pages.
  - **Prevention**: Sanitize user inputs, use proper escaping techniques (e.g., `textContent` instead of `innerHTML`), Content Security Policy (CSP).

  **Example** (XSS attack):
  ```html
  <div id="message"></div>
  <script>
    var userInput = "<script>alert('XSS')</script>";
    document.getElementById('message').innerHTML = userInput;  // Vulnerable to XSS
  </script>
  ```

  **Prevention**:
  ```javascript
  document.getElementById('message').textContent = userInput; // Safe from XSS
  ```

- **Cross-Origin Resource Sharing (CORS)**:
  - **Definition**: A mechanism that allows restricted resources on a web page to be requested from another domain.
  - **Prevention**: Configure servers to allow specific origins or use proxy servers to bypass CORS restrictions.

- **CSRF (Cross-Site Request Forgery)**:
  - **Definition**: A malicious user sending unauthorized requests from a victim's browser.
  - **Prevention**: Use anti-CSRF tokens, check `Referer` header.

---

### **2. Reactive Programming**

#### **Key Concepts**:
- **Observable Streams**: In reactive programming, data flows as a stream, and you can observe these streams for changes.
- **RxJS (Reactive Extensions for JavaScript)**: A library to work with observables and asynchronous data streams.
  
#### **Basic Example (using RxJS)**:
```javascript
// Import RxJS
import { Observable } from 'rxjs';

// Creating an Observable
const observable = new Observable(subscriber => {
  subscriber.next('Hello');
  subscriber.next('Reactive Programming');
  subscriber.complete();
});

// Subscribing to the observable
observable.subscribe({
  next(x) { console.log(x); },
  complete() { console.log('Stream Complete'); }
});

// Output:
// Hello
// Reactive Programming
// Stream Complete
```

- **Key RxJS Operators**:
  - `map()`: Transforms values in the stream.
  - `filter()`: Filters values based on a condition.
  - `merge()`: Combines multiple observables.

---

### **3. WebSockets**

#### **Key Concepts**:
- **Definition**: A WebSocket provides full-duplex communication channels over a single TCP connection.
- **Use case**: Real-time data exchange (e.g., live chat, notifications).

#### **Example**:

```javascript
// Creating a WebSocket connection
const socket = new WebSocket('wss://example.com/socket');

// When the connection is open
socket.addEventListener('open', () => {
  console.log('Connected to WebSocket');
  socket.send('Hello, Server!');
});

// Receiving messages from the WebSocket server
socket.addEventListener('message', (event) => {
  console.log('Message from server:', event.data);
});

// Handle WebSocket errors
socket.addEventListener('error', (error) => {
  console.log('WebSocket Error:', error);
});

// Close the WebSocket connection
socket.addEventListener('close', () => {
  console.log('WebSocket connection closed');
});
```

- **Advantages of WebSockets**: 
  - Real-time communication without polling.
  - Reduced latency.
  - Efficient for push notifications.

---

### **4. Memory Leaks and Optimization**

#### **Key Concepts**:
- **Memory Leaks**:
  - Occur when memory that is no longer needed is not released, causing performance degradation.
  - Common causes include global variables, unclosed event listeners, and forgotten timers.

- **Identifying Memory Leaks**:
  - Use browser developer tools (Memory tab in Chrome DevTools) to identify leaks by tracking detached DOM nodes, event listeners, and closures.

#### **Example** (Memory Leak):
```javascript
let element = document.getElementById('myElement');
window.addEventListener('resize', function() {
  console.log('Resizing window');
});
// This will cause a memory leak if not removed, because the event listener is not cleared.
```

- **Fix**:
```javascript
// Remove the event listener to avoid memory leak
window.removeEventListener('resize', resizeHandler);
```

#### **Optimization Techniques**:
- **Debouncing and Throttling**: Optimize functions that trigger frequently (e.g., scroll, resize).
  - **Debouncing**: Executes a function after a delay, useful for limiting rapid calls.
  - **Throttling**: Ensures a function is executed at most once in a given time frame.

**Example** (Debouncing):
```javascript
let debounceTimer;
window.addEventListener('resize', () => {
  clearTimeout(debounceTimer);
  debounceTimer = setTimeout(() => {
    console.log('Resize completed');
  }, 300);  // Only triggers after 300ms of inactivity
});
```

**Example** (Throttling):
```javascript
let lastExecutionTime = 0;
window.addEventListener('resize', () => {
  const now = new Date().getTime();
  if (now - lastExecutionTime >= 200) {
    lastExecutionTime = now;
    console.log('Resize triggered');
  }
});
```

#### **Tools for Memory Optimization**:
- **Garbage Collection**: JavaScript's garbage collector reclaims memory when objects are no longer in use.
- **WeakMap/WeakSet**: Efficiently store objects with weak references to prevent memory leaks when objects are no longer needed.

**Example** (WeakMap):
```javascript
const weakMap = new WeakMap();
let obj = { name: 'Object' };
weakMap.set(obj, 'This is a weak reference');
obj = null;  // The object is garbage collected when no longer referenced
```

---

### **Summary**:

- **JavaScript Security**: Protect your application from XSS, CSRF, and CORS issues.
- **Reactive Programming**: Use RxJS to handle asynchronous events and streams reactively.
- **WebSockets**: Establish real-time, bidirectional communication between the client and server.
- **Memory Leaks and Optimization**: Prevent and detect memory leaks, and use techniques like debouncing, throttling, and garbage collection for performance optimization.