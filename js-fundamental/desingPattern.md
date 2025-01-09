
### Design Pattern 

Design pattern is Reusable solution to commonly occur problem. It is not complete design but template or guidelines to help solve problem efficiently while adhering to best practices. 

| **Design Pattern**   | **Purpose**                                                                                     | **Example Use Case in Next.js**                                                                                  | **Pros**                                              | **Cons**                                         |
|-----------------------|-----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|--------------------------------------------------|
| **Module Pattern**    | Encapsulates logic into a reusable module.                                                    | Utility functions (e.g., API clients or helper methods).                                                         | - Reduces global scope pollution.<br>- Easy to reuse. | - May lead to tightly coupled code if overused. |
| **Singleton Pattern** | Ensures only one instance of a class exists.                                                  | Shared database instance (e.g., Prisma) or Axios instance.                                                       | - Centralized management.<br>- Prevents duplication. | - Hard to test in isolation.                   |
| **Observer Pattern**  | Implements a publish-subscribe model for one-to-many communication.                           | Real-time updates in the UI using websockets or event emitters.                                                  | - Decouples components.<br>- Enables reactivity.     | - Can become complex with many dependencies.    |
| **Factory Pattern**   | Creates objects or components dynamically without specifying their exact type or class.        | Dynamically rendering forms, components, or API clients based on configuration.                                  | - Simplifies object/component creation.<br>- Scalable for dynamic cases. | - May add overhead if over-abstracted.         |
| **Decorator Pattern** | Dynamically adds functionality to an object or component without modifying its original code. | Higher-Order Components (HOCs) for authentication, logging, or error handling in Next.js.                        | - Enhances components without modifying them.<br>- Reusable across components. | - Can lead to nested wrappers (wrapper hell).   |



---



---

### **1. Module Pattern**
**Purpose**: Encapsulate reusable logic in a module.  
**Example**:
```javascript
// utils/apiClient.js
const API = (function () {
  const BASE_URL = "https://api.example.com";

  return {
    get: async (endpoint) => {
      const response = await fetch(`${BASE_URL}${endpoint}`);
      return response.json();
    },
  };
})();

export default API;

// Usage in a page:
import API from "../utils/apiClient";

export async function getServerSideProps() {
  const data = await API.get("/users");
  return { props: { data } };
}
```

---

### **2. Singleton Pattern**
**Purpose**: Ensure only one instance of an object exists.  
**Example**:
```javascript
// utils/db.js
import { PrismaClient } from "@prisma/client";

let prisma;

if (!global.prisma) {
  global.prisma = new PrismaClient();
}

prisma = global.prisma;

export default prisma;

// Usage in an API route:
import prisma from "../../utils/db";

export default async function handler(req, res) {
  const users = await prisma.user.findMany();
  res.json(users);
}
```

---

### **3. Observer Pattern**
**Purpose**: Manage event-based communication.  
**Example**:
```javascript
// utils/eventEmitter.js
class EventEmitter {
  constructor() {
    this.events = {};
  }

  subscribe(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(callback);
  }

  emit(event, data) {
    if (this.events[event]) {
      this.events[event].forEach((callback) => callback(data));
    }
  }
}

export const eventEmitter = new EventEmitter();

// Usage in a component:
import { eventEmitter } from "../utils/eventEmitter";

export default function Notification() {
  useEffect(() => {
    const notifyHandler = (message) => {
      console.log("Notification received:", message);
    };

    eventEmitter.subscribe("notify", notifyHandler);

    return () => {
      // Cleanup
    };
  }, []);

  return <div>Listening for notifications...</div>;
}

// Simulating an event:
eventEmitter.emit("notify", "You have a new message!");
```

---

### **4. Factory Pattern**
**Purpose**: Dynamically create components or objects.  
**Example**:
```javascript
// components/FormFactory.js
function createComponent(type, props) {
  switch (type) {
    case "button":
      return <button {...props}>{props.label}</button>;
    case "input":
      return <input {...props} />;
    default:
      return null;
  }
}

// Usage in a component:
const fields = [
  { type: "button", label: "Submit" },
  { type: "input", placeholder: "Enter text" },
];

export default function DynamicForm() {
  return <div>{fields.map((field, index) => createComponent(field.type, { ...field, key: index }))}</div>;
}
```

---

### **5. Decorator Pattern**
**Purpose**: Add extra functionality to a component.  
**Example**:
```javascript
// HOCs/withAuth.js
import { useRouter } from "next/router";

export function withAuth(Component) {
  return function ProtectedComponent(props) {
    const router = useRouter();

    useEffect(() => {
      const user = typeof window !== "undefined" && localStorage.getItem("user");
      if (!user) router.push("/login");
    }, []);

    return <Component {...props} />;
  };
}

// Usage in a page:
function Dashboard() {
  return <div>Welcome to the Dashboard</div>;
}

export default withAuth(Dashboard);
```



## 1. Module Pattern

### Definition/Description
The Module Pattern encapsulates private variables and functions within a single object, providing a public API while preventing global namespace pollution.

### Real-Life Use Case
Used in JavaScript libraries to manage scope and encapsulate functionality, making the code more maintainable and modular.

### Example:
```javascript
const CounterModule = (function() {
    let count = 0; // Private variable

    return {
        increment: function() {
            count++;
            console.log(count);
        },
        decrement: function() {
            count--;
            console.log(count);
        },
        getCount: function() {
            return count;
        }
    };
})();

// Usage
CounterModule.increment(); // 1
CounterModule.increment(); // 2
CounterModule.decrement(); // 1
console.log(CounterModule.getCount()); // 1
```

---

## 2. Singleton Pattern

### Definition/Description
The Singleton Pattern ensures that a class has only one instance and provides a global point of access to it.

### Real-Life Use Case
Commonly found in service classes that manage application-wide data, such as user sessions or logging services.

### Example:
```javascript
const Database = (function() {
    let instance;

    function createInstance() {
        const object = new Object("I am the database instance");
        return object;
    }

    return {
        getInstance: function() {
            if (!instance) {
                instance = createInstance();
            }
            return instance;
        }
    };
})();

// Usage
const db1 = Database.getInstance();
const db2 = Database.getInstance();

console.log(db1 === db2); // true, both are the same instance
```

---

## 3. Observer Pattern

### Definition/Description
The Observer Pattern allows an object (subject) to notify other objects (observers) about changes in its state.

### Real-Life Use Case
Essential for real-time applications like chat apps or stock price trackers, where multiple users need updates based on changes.

### Example:
```javascript
class Subject {
    constructor() {
        this.observers = [];
    }

    subscribe(observer) {
        this.observers.push(observer);
    }

    unsubscribe(observer) {
        this.observers = this.observers.filter(obs => obs !== observer);
    }

    notify(data) {
        this.observers.forEach(observer => observer.update(data));
    }
}

class Observer {
    update(data) {
        console.log(`Observer notified with data: ${data}`);
    }
}

// Usage
const subject = new Subject();
const observer1 = new Observer();
const observer2 = new Observer();

subject.subscribe(observer1);
subject.subscribe(observer2);

subject.notify("Hello Observers!"); // Both observers will receive the notification
```

---

## 4. Factory Pattern

### Definition/Description
The Factory Pattern provides an interface for creating objects without specifying the exact class of object that will be created.

### Real-Life Use Case
Used in UI frameworks where different types of components (buttons, dialogs) need to be instantiated based on user input.

### Example:
```javascript
class Car {
    constructor(model) {
        this.model = model;
    }
}

class Truck {
    constructor(model) {
        this.model = model;
    }
}

class VehicleFactory {
    static createVehicle(type, model) {
        switch (type) {
            case 'car':
                return new Car(model);
            case 'truck':
                return new Truck(model);
            default:
                throw new Error('Vehicle type not supported');
        }
    }
}

// Usage
const myCar = VehicleFactory.createVehicle('car', 'Toyota');
const myTruck = VehicleFactory.createVehicle('truck', 'Ford');

console.log(myCar instanceof Car); // true
console.log(myTruck instanceof Truck); // true
```

---

## 5. Decorator Pattern

### Definition/Description
The Decorator Pattern adds new functionality to existing objects dynamically without altering their structure.

### Real-Life Use Case
Widely used in frameworks like jQuery for enhancing DOM elements with additional behaviors dynamically.

### Example:
```javascript
class Coffee {
    cost() {
        return 5; // Base cost of coffee
    }
}

class MilkDecorator {
    constructor(coffee) {
        this.coffee = coffee;
    }

    cost() {
        return this.coffee.cost() + 1; // Adding cost of milk
    }
}

class SugarDecorator {
    constructor(coffee) {
        this.coffee = coffee;
    }

    cost() {
        return this.coffee.cost() + 0.5; // Adding cost of sugar
    }
}

// Usage
let myCoffee = new Coffee();
myCoffee = new MilkDecorator(myCoffee);
myCoffee = new SugarDecorator(myCoffee);

console.log(myCoffee.cost()); // 6.5 (5 + 1 + 0.5)
```

---
