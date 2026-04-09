# ES6 & DOM Manipulation - Complete Beginner's Guide

A comprehensive from-scratch guide to modern JavaScript (ES6+) and DOM manipulation.

## Table of Contents

1. [What is ES6](#what-is-es6)
2. [Variables (let, const)](#variables-let-const)
3. [Arrow Functions](#arrow-functions)
4. [Template Literals](#template-literals)
5. [Destructuring](#destructuring)
6. [Spread and Rest Operators](#spread-and-rest-operators)
7. [Modules (import/export)](#modules-importexport)
8. [Classes](#classes)
9. [Promises and Async/Await](#promises-and-asyncawait)
10. [DOM Selection](#dom-selection)
11. [DOM Manipulation](#dom-manipulation)
12. [DOM Events](#dom-events)
13. [DOM Traversal](#dom-traversal)
14. [Complete Examples](#complete-examples)
15. [API Reference](#api-reference)

---

## What is ES6

ES6 (ECMAScript 2015) is a major update to JavaScript that introduced many modern features.

**Before ES6 (Old way):**

```javascript
var name = "John";
var greeting = "Hello " + name + "!";

function sayHello(name) {
  return "Hello " + name;
}
```

**After ES6 (Modern way):**

```javascript
const name = "John";
const greeting = `Hello ${name}!`;

const sayHello = (name) => `Hello ${name}`;
```

---

## Variables (let, const)

### let - Mutable Variable

```javascript
// let can be reassigned
let count = 0;
count = 1; // ✅ Works
count = 2; // ✅ Works

// let has block scope
if (true) {
  let blockScoped = "only in this block";
  console.log(blockScoped); // ✅ Works
}
console.log(blockScoped); // ❌ Error - not defined

// let in loops
for (let i = 0; i < 3; i++) {
  // Each iteration has its own i
  setTimeout(() => console.log(i), 100); // 0, 1, 2
}
```

### const - Immutable Variable

```javascript
// const cannot be reassigned
const PI = 3.14159;
PI = 3.14; // ❌ Error - cannot reassign

// But const objects can be mutated
const person = { name: "John", age: 30 };
person.age = 31; // ✅ Works - property can change
person.city = "New York"; // ✅ Works - can add properties

// const arrays can be modified
const numbers = [1, 2, 3];
numbers.push(4); // ✅ Works
numbers[0] = 10; // ✅ Works
numbers = [5, 6, 7]; // ❌ Error - cannot reassign

// Always use const by default, use let only when you need to reassign
const apiUrl = "https://api.example.com"; // Never changes
let currentPage = 1; // Changes when user navigates
```

### var vs let vs const Comparison

| Feature      | var      | let      | const    |
| ------------ | -------- | -------- | -------- |
| Scope        | Function | Block    | Block    |
| Reassignable | Yes      | Yes      | No       |
| Hoisted      | Yes      | No (TDZ) | No (TDZ) |
| Redeclare    | Yes      | No       | No       |

```javascript
// var - function scoped
function testVar() {
  if (true) {
    var x = 10;
  }
  console.log(x); // 10 (leaks out of block)
}

// let - block scoped
function testLet() {
  if (true) {
    let y = 10;
  }
  console.log(y); // Error (stays in block)
}
```

---

## Arrow Functions

### Basic Syntax

```javascript
// Traditional function
function add(a, b) {
  return a + b;
}

// Arrow function
const add = (a, b) => {
  return a + b;
};

// Implicit return (no {} needed)
const add = (a, b) => a + b;

// Single parameter (no parentheses needed)
const double = (x) => x * 2;

// No parameters (empty parentheses)
const sayHello = () => "Hello!";

// Returning object (wrap in parentheses)
const createPerson = (name, age) => ({ name, age });
```

### Arrow Functions vs Regular Functions

```javascript
// THIS binding is different!
const person = {
  name: "John",

  // Regular function - has its own 'this'
  greetRegular: function () {
    console.log(this.name); // "John" - 'this' is person
  },

  // Arrow function - inherits 'this' from parent scope
  greetArrow: () => {
    console.log(this.name); // undefined - 'this' is window/global
  },

  // Arrow function in callback - preserves 'this'
  greetAsync: function () {
    setTimeout(() => {
      console.log(this.name); // "John" - arrow inherits 'this'
    }, 100);

    setTimeout(function () {
      console.log(this.name); // undefined - regular function has its own 'this'
    }, 100);
  },
};

// When to use arrow functions:
// 1. Callbacks
const doubled = [1, 2, 3].map((x) => x * 2);

// 2. Promise chains
fetch(url).then((response) => response.json());

// 3. Event listeners (when you need parent 'this')
button.addEventListener("click", () => {
  console.log(this); // 'this' is from parent scope
});

// When NOT to use arrow functions:
// 1. Object methods (need own 'this')
// 2. Constructors (can't use with 'new')
// 3. Dynamic context (need arguments object)
```

---

## Template Literals

### Basic Usage

```javascript
// Old way - string concatenation
const name = "John";
const age = 30;
const oldGreeting = "Hello " + name + ", you are " + age + " years old.";

// Template literal - cleaner and more readable
const newGreeting = `Hello ${name}, you are ${age} years old.`;

// Multi-line strings (no need for \n or +)
const multiLine = `
  This is line 1
  This is line 2
  This is line 3
`;

// Expressions inside ${}
const price = 19.99;
const tax = 0.08;
const total = `Total: $${(price * (1 + tax)).toFixed(2)}`;

// Function calls inside ${}
const getUserInfo = () => "John Doe";
const message = `User: ${getUserInfo()}`;

// Ternary operators inside ${}
const isLoggedIn = true;
const status = `Status: ${isLoggedIn ? "Logged In" : "Logged Out"}`;
```

### Tagged Templates

```javascript
// Custom template tag function
function highlight(strings, ...values) {
  let result = "";
  strings.forEach((string, i) => {
    result += string;
    if (values[i]) {
      result += `<mark>${values[i]}</mark>`;
    }
  });
  return result;
}

const name = "John";
const action = "logged in";
const message = highlight`User ${name} has ${action}`;
// Result: "User <mark>John</mark> has <mark>logged in</mark>"

// Real-world example: SQL query builder
function sql(strings, ...values) {
  return strings.reduce((result, string, i) => {
    result += string;
    if (values[i]) {
      result += `'${values[i].replace(/'/g, "''")}'`; // Escape quotes
    }
    return result;
  }, "");
}

const userId = "123";
const query = sql`SELECT * FROM users WHERE id = ${userId}`;
// Result: "SELECT * FROM users WHERE id = '123'"
```

---

## Destructuring

### Object Destructuring

```javascript
// Basic object destructuring
const user = {
  name: "John",
  age: 30,
  email: "john@example.com",
};

// Old way
const name = user.name;
const age = user.age;

// ES6 way
const { name, age } = user;
console.log(name); // "John"
console.log(age); // 30

// Renaming variables
const { name: userName, age: userAge } = user;
console.log(userName); // "John"

// Default values
const { country = "USA", city = "Unknown" } = user;
console.log(country); // "USA" (default used)
console.log(city); // "Unknown" (default used)

// Nested destructuring
const person = {
  name: "John",
  address: {
    street: "123 Main St",
    city: "Boston",
    zip: "02101",
  },
};

const {
  address: { street, city },
} = person;
console.log(street); // "123 Main St"
console.log(city); // "Boston"

// Rest operator in destructuring
const { name, ...rest } = user;
console.log(name); // "John"
console.log(rest); // { age: 30, email: "john@example.com" }
```

### Array Destructuring

```javascript
// Basic array destructuring
const colors = ["red", "green", "blue"];
const [first, second, third] = colors;
console.log(first); // "red"
console.log(second); // "green"
console.log(third); // "blue"

// Skipping elements
const [primary, , tertiary] = colors;
console.log(primary); // "red"
console.log(tertiary); // "blue"

// Default values
const [a, b, c, d = "yellow"] = colors;
console.log(d); // "yellow" (default used)

// Rest operator
const [head, ...tail] = colors;
console.log(head); // "red"
console.log(tail); // ["green", "blue"]

// Swapping variables
let x = 1,
  y = 2;
[x, y] = [y, x];
console.log(x, y); // 2, 1

// Function returns array
function getCoordinates() {
  return [40.7128, -74.006];
}
const [lat, lng] = getCoordinates();
console.log(lat, lng); // 40.7128, -74.0060
```

### Function Parameter Destructuring

```javascript
// Object parameter destructuring
function displayUser({ name, age, email = "N/A" }) {
  console.log(`Name: ${name}, Age: ${age}, Email: ${email}`);
}

displayUser({ name: "John", age: 30 });
// Name: John, Age: 30, Email: N/A

// Array parameter destructuring
function getFirstTwo([first, second]) {
  return { first, second };
}

const result = getFirstTwo(["a", "b", "c"]);
console.log(result); // { first: "a", second: "b" }

// Nested destructuring in parameters
function processPerson({ name, address: { city } }) {
  console.log(`${name} lives in ${city}`);
}

processPerson({
  name: "John",
  address: { city: "Boston", street: "123 Main" },
}); // "John lives in Boston"
```

---

## Spread and Rest Operators

### Spread Operator (...) - Expanding Arrays/Objects

```javascript
// Spread with arrays - combine arrays
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4, 5, 6]

// Copy array (shallow copy)
const original = [1, 2, 3];
const copy = [...original];
copy.push(4);
console.log(original); // [1, 2, 3] (unchanged)
console.log(copy); // [1, 2, 3, 4]

// Spread with objects - merge objects
const obj1 = { name: "John", age: 30 };
const obj2 = { city: "Boston", country: "USA" };
const merged = { ...obj1, ...obj2 };
console.log(merged);
// { name: "John", age: 30, city: "Boston", country: "USA" }

// Override properties (order matters)
const user = { name: "John", age: 30 };
const updated = { ...user, age: 31 };
console.log(updated); // { name: "John", age: 31 }

// Spread in function calls
const numbers = [1, 2, 3, 4, 5];
const max = Math.max(...numbers);
console.log(max); // 5

// Convert NodeList to array
const divs = document.querySelectorAll("div");
const divArray = [...divs];
divArray.forEach((div) => console.log(div));

// Spread strings to arrays
const str = "hello";
const chars = [...str];
console.log(chars); // ["h", "e", "l", "l", "o"]
```

### Rest Operator (...) - Collecting Remaining Elements

```javascript
// Rest in function parameters
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}
console.log(sum(1, 2, 3, 4)); // 10

// Rest with other parameters
function greet(greeting, ...names) {
  return `${greeting}, ${names.join(" and ")}!`;
}
console.log(greet("Hello", "John", "Jane", "Bob"));
// "Hello, John and Jane and Bob!"

// Rest in array destructuring
const [first, second, ...rest] = [1, 2, 3, 4, 5];
console.log(first); // 1
console.log(second); // 2
console.log(rest); // [3, 4, 5]

// Rest in object destructuring
const { name, ...details } = { name: "John", age: 30, city: "Boston" };
console.log(name); // "John"
console.log(details); // { age: 30, city: "Boston" }
```

---

## Modules (import/export)

### Export Types

```javascript
// Named exports (multiple per file)
// math.js
export const PI = 3.14159;
export const E = 2.71828;

export function add(a, b) {
  return a + b;
}

export class Calculator {
  multiply(a, b) {
    return a * b;
  }
}

// Or export at the end
const subtract = (a, b) => a - b;
const divide = (a, b) => a / b;
export { subtract, divide };

// Default export (one per file)
// user.js
const user = {
  name: "John",
  age: 30
};
export default user;

// Or
export default class User {
  constructor(name) {
    this.name = name;
  }
}

// Mixed exports
// api.js
export const API_URL = "https://api.example.com";
export function fetchData() { ... }
export default function api() { ... }
```

### Import Types

```javascript
// Import named exports
import { PI, E, add } from "./math.js";
console.log(PI); // 3.14159
console.log(add(2, 3)); // 5

// Import all named exports as an object
import * as MathUtils from "./math.js";
console.log(MathUtils.PI); // 3.14159
console.log(MathUtils.add(2, 3)); // 5

// Import with aliases
import { add as sum, subtract as minus } from "./math.js";
console.log(sum(5, 3)); // 8
console.log(minus(5, 3)); // 2

// Import default export
import user from "./user.js";
console.log(user.name); // "John"

// Import default and named together
import api, { API_URL, fetchData } from "./api.js";

// Import all (including default)
import * as API from "./api.js";
console.log(API.default()); // default export
console.log(API.API_URL); // named export

// Dynamic import (code splitting)
const module = await import("./math.js");
console.log(module.add(2, 3));
```

### Real-world Module Example

```javascript
// utils/validation.js
export const validateEmail = (email) => {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
};

export const validatePhone = (phone) => {
  return /^\d{10}$/.test(phone);
};

export const validateRequired = (value) => {
  return value && value.trim() !== "";
};

// utils/formatters.js
export const formatCurrency = (amount) => {
  return new Intl.NumberFormat("en-US", {
    style: "currency",
    currency: "USD",
  }).format(amount);
};

export const formatDate = (date) => {
  return new Intl.DateTimeFormat("en-US").format(new Date(date));
};

export default {
  formatCurrency,
  formatDate,
};

// app.js
import { validateEmail, validatePhone } from "./utils/validation.js";
import formatters from "./utils/formatters.js";

console.log(validateEmail("test@example.com")); // true
console.log(formatters.formatCurrency(19.99)); // "$19.99"
```

---

## Classes

### Basic Class Syntax

```javascript
// ES6 class
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    return `Hello, my name is ${this.name}`;
  }

  haveBirthday() {
    this.age++;
    return `Happy birthday! You are now ${this.age}`;
  }
}

const john = new Person("John", 30);
console.log(john.greet()); // "Hello, my name is John"
console.log(john.haveBirthday()); // "Happy birthday! You are now 31"
```

### Class Inheritance

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    return `${this.name} makes a sound`;
  }

  static classify() {
    return "This is an animal";
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Call parent constructor
    this.breed = breed;
  }

  speak() {
    return `${this.name} barks!`;
  }

  fetch() {
    return `${this.name} fetches the ball`;
  }
}

class Cat extends Animal {
  speak() {
    return `${this.name} meows!`;
  }
}

const dog = new Dog("Rex", "Labrador");
console.log(dog.speak()); // "Rex barks!"
console.log(dog.fetch()); // "Rex fetches the ball"
console.log(dog.name); // "Rex"
console.log(Dog.classify()); // "This is an animal"

const cat = new Cat("Whiskers");
console.log(cat.speak()); // "Whiskers meows!"
```

### Getters and Setters

```javascript
class User {
  constructor(firstName, lastName) {
    this._firstName = firstName;
    this._lastName = lastName;
  }

  // Getter
  get fullName() {
    return `${this._firstName} ${this._lastName}`;
  }

  // Setter
  set fullName(name) {
    const [first, last] = name.split(" ");
    this._firstName = first;
    this._lastName = last;
  }

  // Getter with computation
  get initials() {
    return `${this._firstName[0]}${this._lastName[0]}`;
  }

  // Private field (ES2022)
  #password = "secret";

  getPassword() {
    return this.#password;
  }
}

const user = new User("John", "Doe");
console.log(user.fullName); // "John Doe" (getter)
user.fullName = "Jane Smith"; // (setter)
console.log(user.fullName); // "Jane Smith"
console.log(user.initials); // "JS"
```

### Static Methods and Properties

```javascript
class MathUtils {
  static PI = 3.14159;

  static add(a, b) {
    return a + b;
  }

  static multiply(a, b) {
    return a * b;
  }

  static factorial(n) {
    if (n <= 1) return 1;
    return n * this.factorial(n - 1);
  }
}

// Call without instantiating
console.log(MathUtils.PI); // 3.14159
console.log(MathUtils.add(5, 3)); // 8
console.log(MathUtils.factorial(5)); // 120
```

---

## Promises and Async/Await

### Promises

```javascript
// Creating a promise
const fetchData = () => {
  return new Promise((resolve, reject) => {
    // Simulate async operation
    setTimeout(() => {
      const success = true;

      if (success) {
        resolve({ data: "Hello World", status: 200 });
      } else {
        reject(new Error("Failed to fetch data"));
      }
    }, 1000);
  });
};

// Using promises
fetchData()
  .then((result) => {
    console.log(result.data); // "Hello World"
    return result.data;
  })
  .then((data) => {
    console.log("Processing:", data);
  })
  .catch((error) => {
    console.error("Error:", error.message);
  })
  .finally(() => {
    console.log("Operation completed");
  });

// Promise chaining
const getUser = () => Promise.resolve({ id: 1, name: "John" });
const getPosts = (userId) => Promise.resolve([{ title: "Post 1" }]);
const getComments = (postId) => Promise.resolve([{ text: "Comment 1" }]);

getUser()
  .then((user) => getPosts(user.id))
  .then((posts) => getComments(posts[0].id))
  .then((comments) => console.log(comments))
  .catch((error) => console.error(error));
```

### Promise Methods

```javascript
// Promise.all - waits for all promises to resolve
const promise1 = Promise.resolve(1);
const promise2 = Promise.resolve(2);
const promise3 = Promise.resolve(3);

Promise.all([promise1, promise2, promise3]).then((values) =>
  console.log(values),
); // [1, 2, 3]

// Promise.allSettled - waits for all promises to settle (resolve or reject)
const promises = [
  Promise.resolve(1),
  Promise.reject("Error"),
  Promise.resolve(3),
];

Promise.allSettled(promises).then((results) => {
  results.forEach((result) => {
    if (result.status === "fulfilled") {
      console.log("Success:", result.value);
    } else {
      console.log("Failed:", result.reason);
    }
  });
});

// Promise.race - returns first promise that settles
const fast = new Promise((resolve) => setTimeout(() => resolve("fast"), 100));
const slow = new Promise((resolve) => setTimeout(() => resolve("slow"), 500));

Promise.race([fast, slow]).then((result) => console.log(result)); // "fast"

// Promise.any - returns first successful promise
const maybeFail1 = Promise.reject("Error 1");
const maybeFail2 = Promise.reject("Error 2");
const success = Promise.resolve("Success");

Promise.any([maybeFail1, maybeFail2, success]).then((result) =>
  console.log(result),
); // "Success"
```

### Async/Await

```javascript
// Basic async/await
async function fetchUserData() {
  try {
    const response = await fetch("https://api.example.com/user");
    const data = await response.json();
    console.log(data);
    return data;
  } catch (error) {
    console.error("Error:", error);
    throw error;
  }
}

// Multiple awaits
async function getUserFullData() {
  try {
    const user = await fetchUser();
    const posts = await fetchPosts(user.id);
    const comments = await fetchComments(posts[0].id);

    return { user, posts, comments };
  } catch (error) {
    console.error("Failed to load data:", error);
  }
}

// Parallel execution with Promise.all
async function loadAllData() {
  const [user, posts, comments] = await Promise.all([
    fetchUser(),
    fetchPosts(),
    fetchComments(),
  ]);

  return { user, posts, comments };
}

// Async arrow function
const getData = async () => {
  const response = await fetch("/api/data");
  return response.json();
};

// Async IIFE (Immediately Invoked Function Expression)
(async () => {
  const data = await fetchData();
  console.log(data);
})();

// Error handling with try/catch
async function processData() {
  try {
    const data = await fetchData();
    const processed = await processData(data);
    return processed;
  } catch (error) {
    console.error("Processing failed:", error);
    return null;
  }
}
```

### Real-world API Example

```javascript
// API service module
const API_BASE = "https://jsonplaceholder.typicode.com";

class ApiService {
  static async get(endpoint) {
    try {
      const response = await fetch(`${API_BASE}${endpoint}`);

      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }

      return await response.json();
    } catch (error) {
      console.error(`GET ${endpoint} failed:`, error);
      throw error;
    }
  }

  static async post(endpoint, data) {
    try {
      const response = await fetch(`${API_BASE}${endpoint}`, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(data),
      });

      if (!response.ok) {
        throw new Error(`HTTP ${response.status}`);
      }

      return await response.json();
    } catch (error) {
      console.error(`POST ${endpoint} failed:`, error);
      throw error;
    }
  }

  static async getUsers() {
    return this.get("/users");
  }

  static async getPosts(userId) {
    return this.get(`/users/${userId}/posts`);
  }

  static async createPost(data) {
    return this.post("/posts", data);
  }
}

// Usage
async function displayUserPosts() {
  try {
    const users = await ApiService.getUsers();
    const firstUser = users[0];

    const posts = await ApiService.getPosts(firstUser.id);
    console.log(`${firstUser.name} has ${posts.length} posts`);

    const newPost = await ApiService.createPost({
      title: "My Post",
      body: "This is my post content",
      userId: firstUser.id,
    });

    console.log("Created post:", newPost);
  } catch (error) {
    console.error("Failed:", error);
  }
}
```

---

## DOM Selection

### Basic Selection Methods

```javascript
// Select by ID
const header = document.getElementById("header");
console.log(header);

// Select by class name (returns HTMLCollection)
const items = document.getElementsByClassName("item");
console.log(items[0]);
console.log(items.length);

// Select by tag name
const paragraphs = document.getElementsByTagName("p");

// Select by name attribute
const radios = document.getElementsByName("gender");

// Modern methods - querySelector (returns first match)
const firstButton = document.querySelector(".btn-primary");
const mainDiv = document.querySelector("#main");
const firstInput = document.querySelector('input[type="text"]');

// querySelectorAll (returns NodeList)
const allButtons = document.querySelectorAll(".btn");
const allInputs = document.querySelectorAll("input, select, textarea");
const listItems = document.querySelectorAll("ul li");

// Convert NodeList to array for array methods
const buttonArray = [...document.querySelectorAll(".btn")];
buttonArray.forEach((btn) => console.log(btn));

// Or use Array.from
const divArray = Array.from(document.querySelectorAll("div"));
```

### Selection from Specific Element

```javascript
const container = document.getElementById("container");

// Find within container only
const containerButtons = container.querySelectorAll(".btn");
const containerTitle = container.querySelector(".title");

// Children
const children = container.children; // HTMLCollection
const firstChild = container.firstElementChild;
const lastChild = container.lastElementChild;

// Parent
const parent = container.parentElement;
const closestSection = container.closest("section");

// Siblings
const next = container.nextElementSibling;
const prev = container.previousElementSibling;
```

---

## DOM Manipulation

### Creating and Adding Elements

```javascript
// Create elements
const div = document.createElement("div");
const p = document.createElement("p");
const span = document.createElement("span");

// Set content
p.textContent = "This is a paragraph";
p.innerHTML = "<strong>Bold text</strong>";

// Set attributes
div.id = "myDiv";
div.className = "container main";
div.classList.add("active", "visible");
div.classList.remove("hidden");
div.classList.toggle("dark-mode");
div.setAttribute("data-id", "123");
div.setAttribute("aria-label", "Description");

// Style directly
div.style.backgroundColor = "blue";
div.style.color = "white";
div.style.padding = "10px";
div.style.borderRadius = "5px";

// Append to DOM
document.body.appendChild(div);
container.appendChild(p);
container.insertBefore(span, container.firstChild);

// Insert HTML
container.insertAdjacentHTML("beforebegin", "<div>Before container</div>");
container.insertAdjacentHTML("afterbegin", "<div>First child</div>");
container.insertAdjacentHTML("beforeend", "<div>Last child</div>");
container.insertAdjacentHTML("afterend", "<div>After container</div>");
```

### Removing Elements

```javascript
// Remove element
const element = document.getElementById("toRemove");
element.remove(); // Modern way

// Remove child from parent
const parent = document.getElementById("parent");
const child = document.getElementById("child");
parent.removeChild(child);

// Remove all children
while (parent.firstChild) {
  parent.removeChild(parent.firstChild);
}

// Clear inner HTML
parent.innerHTML = "";
```

### Modifying Content

```javascript
const element = document.getElementById("content");

// Text content
element.textContent = "Plain text only";
element.innerText = "Also plain text, respects CSS visibility";

// HTML content
element.innerHTML = "<strong>HTML content</strong>";

// For forms
const input = document.querySelector("input");
input.value = "New value";
input.checked = true;
input.disabled = true;
input.readOnly = false;

// For selects
const select = document.querySelector("select");
select.value = "option2";
select.selectedIndex = 1;

// Data attributes
element.dataset.userId = "123";
element.dataset.role = "admin";
console.log(element.dataset); // { userId: "123", role: "admin" }
```

---

## DOM Events

### Adding Event Listeners

```javascript
// Basic event listener
const button = document.getElementById("myButton");

button.addEventListener("click", () => {
  console.log("Button clicked!");
});

// Named function
function handleClick(event) {
  console.log("Event:", event.type);
  console.log("Target:", event.target);
  console.log("Current Target:", event.currentTarget);
}

button.addEventListener("click", handleClick);

// Remove event listener
button.removeEventListener("click", handleClick);

// One-time event
button.addEventListener(
  "click",
  () => {
    console.log("Runs once");
  },
  { once: true },
);
```

### Event Object Properties

```javascript
element.addEventListener("click", (event) => {
  // Event type
  console.log(event.type); // "click"

  // Target elements
  console.log(event.target); // Element that triggered event
  console.log(event.currentTarget); // Element listener attached to

  // Mouse position
  console.log(event.clientX, event.clientY); // Relative to viewport
  console.log(event.pageX, event.pageY); // Relative to page
  console.log(event.screenX, event.screenY); // Relative to screen

  // Modifier keys
  console.log(event.ctrlKey); // Ctrl key pressed?
  console.log(event.shiftKey); // Shift key pressed?
  console.log(event.altKey); // Alt key pressed?
  console.log(event.metaKey); // Meta/Cmd key pressed?

  // Stop propagation
  event.stopPropagation(); // Prevent bubbling

  // Prevent default behavior
  event.preventDefault(); // Stop default action (e.g., link navigation)
});
```

### Common Event Types

```javascript
// Mouse events
element.addEventListener("click", () => {});
element.addEventListener("dblclick", () => {});
element.addEventListener("mousedown", () => {});
element.addEventListener("mouseup", () => {});
element.addEventListener("mouseenter", () => {});
element.addEventListener("mouseleave", () => {});
element.addEventListener("mousemove", () => {});
element.addEventListener("mouseover", () => {});
element.addEventListener("mouseout", () => {});

// Keyboard events
document.addEventListener("keydown", (e) => {
  console.log(e.key); // 'a', 'Enter', 'ArrowUp'
  console.log(e.code); // 'KeyA', 'Enter', 'ArrowUp'
  console.log(e.keyCode); // Deprecated but sometimes used
});
document.addEventListener("keyup", () => {});
document.addEventListener("keypress", () => {}); // Deprecated

// Form events
form.addEventListener("submit", (e) => {
  e.preventDefault(); // Prevent page reload
  const formData = new FormData(form);
});
input.addEventListener("change", () => {});
input.addEventListener("input", () => {});
input.addEventListener("focus", () => {});
input.addEventListener("blur", () => {});
select.addEventListener("change", () => {});

// Window events
window.addEventListener("load", () => {});
window.addEventListener("resize", () => {});
window.addEventListener("scroll", () => {});
window.addEventListener("beforeunload", (e) => {
  e.preventDefault();
  e.returnValue = "";
});

// Drag and drop events
element.addEventListener("dragstart", () => {});
element.addEventListener("dragend", () => {});
element.addEventListener("dragover", (e) => e.preventDefault());
element.addEventListener("drop", (e) => {
  e.preventDefault();
  const files = e.dataTransfer.files;
});
```

### Event Delegation

```javascript
// Instead of adding listeners to many elements
const buttons = document.querySelectorAll(".btn");
buttons.forEach((btn) => {
  btn.addEventListener("click", handleClick);
});

// Use event delegation - add one listener to parent
const container = document.getElementById("container");
container.addEventListener("click", (event) => {
  // Check if clicked element is a button
  if (event.target.matches(".btn")) {
    console.log("Button clicked:", event.target.textContent);
  }

  // For dynamic elements
  if (event.target.closest(".delete-btn")) {
    const item = event.target.closest(".todo-item");
    item.remove();
  }
});

// Example: Dynamic todo list
const todoList = document.getElementById("todoList");

todoList.addEventListener("click", (e) => {
  // Delete button clicked
  if (e.target.classList.contains("delete-btn")) {
    e.target.closest(".todo-item").remove();
  }

  // Checkbox toggled
  if (e.target.classList.contains("todo-checkbox")) {
    const todoText = e.target.nextElementSibling;
    todoText.style.textDecoration = e.target.checked ? "line-through" : "none";
  }
});
```

---

## DOM Traversal

### Moving Between Elements

```javascript
const current = document.getElementById("current");

// Parent traversal
const parent = current.parentElement;
const grandParent = current.parentElement.parentElement;
const closestForm = current.closest("form");

// Child traversal
const children = current.children; // HTMLCollection
const firstChild = current.firstElementChild;
const lastChild = current.lastElementChild;

// Sibling traversal
const nextSibling = current.nextElementSibling;
const prevSibling = current.previousElementSibling;

// All siblings
const siblings = [...current.parentElement.children].filter(
  (child) => child !== current,
);

// Example: Find next input field
function focusNextInput(currentInput) {
  const form = currentInput.closest("form");
  const inputs = [...form.querySelectorAll("input, select, textarea")];
  const currentIndex = inputs.indexOf(currentInput);
  const nextInput = inputs[currentIndex + 1];
  if (nextInput) nextInput.focus();
}
```

---

## Complete Examples

### Example 1: Dynamic Todo List

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .todo-container {
        max-width: 500px;
        margin: 0 auto;
        padding: 20px;
        font-family: Arial, sans-serif;
      }
      .todo-input {
        display: flex;
        gap: 10px;
        margin-bottom: 20px;
      }
      .todo-input input {
        flex: 1;
        padding: 10px;
        border: 1px solid #ddd;
        border-radius: 4px;
      }
      .todo-input button {
        padding: 10px 20px;
        background: #007bff;
        color: white;
        border: none;
        border-radius: 4px;
        cursor: pointer;
      }
      .todo-item {
        display: flex;
        align-items: center;
        gap: 10px;
        padding: 10px;
        border-bottom: 1px solid #eee;
      }
      .todo-item.completed span {
        text-decoration: line-through;
        color: #999;
      }
      .delete-btn {
        margin-left: auto;
        background: #dc3545;
        color: white;
        border: none;
        padding: 5px 10px;
        border-radius: 4px;
        cursor: pointer;
      }
    </style>
  </head>
  <body>
    <div class="todo-container">
      <h1>Todo List</h1>
      <div class="todo-input">
        <input type="text" id="todoInput" placeholder="Enter a new task..." />
        <button id="addBtn">Add</button>
      </div>
      <div id="todoList"></div>
      <div class="todo-stats">
        <span id="totalCount">0</span> total |
        <span id="completedCount">0</span> completed |
        <span id="activeCount">0</span> active
      </div>
    </div>

    <script>
      // State
      let todos = [
        { id: 1, text: "Learn JavaScript", completed: false },
        { id: 2, text: "Build a project", completed: true },
        { id: 3, text: "Master ES6", completed: false },
      ];

      // DOM elements
      const todoList = document.getElementById("todoList");
      const todoInput = document.getElementById("todoInput");
      const addBtn = document.getElementById("addBtn");
      const totalCount = document.getElementById("totalCount");
      const completedCount = document.getElementById("completedCount");
      const activeCount = document.getElementById("activeCount");

      // Save to localStorage
      function saveToLocalStorage() {
        localStorage.setItem("todos", JSON.stringify(todos));
      }

      // Load from localStorage
      function loadFromLocalStorage() {
        const stored = localStorage.getItem("todos");
        if (stored) {
          todos = JSON.parse(stored);
        }
      }

      // Update statistics
      function updateStats() {
        const total = todos.length;
        const completed = todos.filter((t) => t.completed).length;
        const active = total - completed;

        totalCount.textContent = total;
        completedCount.textContent = completed;
        activeCount.textContent = active;
      }

      // Render todo list
      function renderTodos() {
        if (!todoList) return;

        todoList.innerHTML = "";

        todos.forEach((todo) => {
          const todoItem = document.createElement("div");
          todoItem.className = `todo-item ${todo.completed ? "completed" : ""}`;
          todoItem.dataset.id = todo.id;

          const checkbox = document.createElement("input");
          checkbox.type = "checkbox";
          checkbox.checked = todo.completed;
          checkbox.addEventListener("change", () => toggleTodo(todo.id));

          const textSpan = document.createElement("span");
          textSpan.textContent = todo.text;

          const deleteBtn = document.createElement("button");
          deleteBtn.textContent = "Delete";
          deleteBtn.className = "delete-btn";
          deleteBtn.addEventListener("click", () => deleteTodo(todo.id));

          todoItem.appendChild(checkbox);
          todoItem.appendChild(textSpan);
          todoItem.appendChild(deleteBtn);
          todoList.appendChild(todoItem);
        });

        updateStats();
        saveToLocalStorage();
      }

      // Add new todo
      function addTodo() {
        const text = todoInput.value.trim();
        if (!text) return;

        const newTodo = {
          id: Date.now(),
          text: text,
          completed: false,
        };

        todos.push(newTodo);
        todoInput.value = "";
        renderTodos();
      }

      // Toggle todo completion
      function toggleTodo(id) {
        const todo = todos.find((t) => t.id === id);
        if (todo) {
          todo.completed = !todo.completed;
          renderTodos();
        }
      }

      // Delete todo
      function deleteTodo(id) {
        todos = todos.filter((t) => t.id !== id);
        renderTodos();
      }

      // Event listeners
      addBtn.addEventListener("click", addTodo);
      todoInput.addEventListener("keypress", (e) => {
        if (e.key === "Enter") addTodo();
      });

      // Initialize
      loadFromLocalStorage();
      renderTodos();
    </script>
  </body>
</html>
```

### Example 2: Modal Dialog System

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .modal {
        display: none;
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: rgba(0, 0, 0, 0.5);
        z-index: 1000;
        justify-content: center;
        align-items: center;
      }
      .modal.active {
        display: flex;
      }
      .modal-content {
        background: white;
        padding: 20px;
        border-radius: 8px;
        max-width: 500px;
        width: 90%;
        animation: slideIn 0.3s ease;
      }
      @keyframes slideIn {
        from {
          transform: translateY(-50px);
          opacity: 0;
        }
        to {
          transform: translateY(0);
          opacity: 1;
        }
      }
      .modal-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 15px;
      }
      .close-btn {
        background: none;
        border: none;
        font-size: 24px;
        cursor: pointer;
      }
      .modal-footer {
        margin-top: 20px;
        text-align: right;
      }
      .btn {
        padding: 8px 16px;
        border: none;
        border-radius: 4px;
        cursor: pointer;
      }
      .btn-primary {
        background: #007bff;
        color: white;
      }
      .btn-secondary {
        background: #6c757d;
        color: white;
      }
    </style>
  </head>
  <body>
    <button id="openModalBtn">Open Modal</button>

    <div id="myModal" class="modal">
      <div class="modal-content">
        <div class="modal-header">
          <h2>Modal Title</h2>
          <button class="close-btn">&times;</button>
        </div>
        <div class="modal-body">
          <p>This is the modal content. You can put any HTML here!</p>
          <input type="text" placeholder="Enter something..." id="modalInput" />
        </div>
        <div class="modal-footer">
          <button class="btn btn-secondary" id="cancelBtn">Cancel</button>
          <button class="btn btn-primary" id="confirmBtn">Confirm</button>
        </div>
      </div>
    </div>

    <script>
      class Modal {
        constructor(modalElement) {
          this.modal = modalElement;
          this.closeBtn = this.modal.querySelector(".close-btn");
          this.cancelBtn = this.modal.querySelector("#cancelBtn");
          this.confirmBtn = this.modal.querySelector("#confirmBtn");
          this.onConfirm = null;

          this.init();
        }

        init() {
          // Close on X button
          this.closeBtn?.addEventListener("click", () => this.close());

          // Close on cancel button
          this.cancelBtn?.addEventListener("click", () => this.close());

          // Close on backdrop click
          this.modal.addEventListener("click", (e) => {
            if (e.target === this.modal) this.close();
          });

          // Handle confirm
          this.confirmBtn?.addEventListener("click", () => {
            if (this.onConfirm) {
              const input = this.modal.querySelector("#modalInput");
              this.onConfirm(input?.value || null);
            }
            this.close();
          });

          // Close on Escape key
          document.addEventListener("keydown", (e) => {
            if (e.key === "Escape" && this.isOpen()) {
              this.close();
            }
          });
        }

        open() {
          this.modal.classList.add("active");
          document.body.style.overflow = "hidden";
        }

        close() {
          this.modal.classList.remove("active");
          document.body.style.overflow = "";
        }

        isOpen() {
          return this.modal.classList.contains("active");
        }

        setConfirmCallback(callback) {
          this.onConfirm = callback;
        }
      }

      // Usage
      const modal = new Modal(document.getElementById("myModal"));
      const openBtn = document.getElementById("openModalBtn");

      openBtn.addEventListener("click", () => {
        modal.setConfirmCallback((value) => {
          console.log("User entered:", value);
          alert(`You entered: ${value || "nothing"}`);
        });
        modal.open();
      });
    </script>
  </body>
</html>
```

### Example 3: Drag and Drop File Upload

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .drop-zone {
        width: 100%;
        max-width: 500px;
        height: 200px;
        border: 2px dashed #ccc;
        border-radius: 8px;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        margin: 20px auto;
        transition: all 0.3s ease;
      }
      .drop-zone.drag-over {
        border-color: #007bff;
        background: rgba(0, 123, 255, 0.1);
      }
      .file-list {
        max-width: 500px;
        margin: 20px auto;
      }
      .file-item {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 10px;
        border: 1px solid #ddd;
        margin-bottom: 5px;
        border-radius: 4px;
      }
      .upload-progress {
        width: 100%;
        height: 5px;
        background: #eee;
        border-radius: 3px;
        overflow: hidden;
      }
      .upload-progress-bar {
        height: 100%;
        background: #007bff;
        width: 0%;
        transition: width 0.3s ease;
      }
    </style>
  </head>
  <body>
    <div class="drop-zone" id="dropZone">
      <p>📁 Drag & Drop files here</p>
      <p>or</p>
      <input type="file" id="fileInput" multiple />
    </div>
    <div id="fileList" class="file-list"></div>

    <script>
      class FileUploader {
        constructor(dropZoneId, fileListId) {
          this.dropZone = document.getElementById(dropZoneId);
          this.fileInput = document.getElementById("fileInput");
          this.fileList = document.getElementById(fileListId);
          this.files = [];

          this.init();
        }

        init() {
          // Prevent default drag behaviors
          ["dragenter", "dragover", "dragleave", "drop"].forEach(
            (eventName) => {
              this.dropZone.addEventListener(
                eventName,
                this.preventDefaults,
                false,
              );
              document.body.addEventListener(
                eventName,
                this.preventDefaults,
                false,
              );
            },
          );

          // Highlight drop zone when dragging over
          ["dragenter", "dragover"].forEach((eventName) => {
            this.dropZone.addEventListener(eventName, () => {
              this.dropZone.classList.add("drag-over");
            });
          });

          ["dragleave", "drop"].forEach((eventName) => {
            this.dropZone.addEventListener(eventName, () => {
              this.dropZone.classList.remove("drag-over");
            });
          });

          // Handle dropped files
          this.dropZone.addEventListener("drop", (e) => {
            const files = e.dataTransfer.files;
            this.handleFiles(files);
          });

          // Handle file input change
          this.fileInput.addEventListener("change", (e) => {
            this.handleFiles(e.target.files);
          });
        }

        preventDefaults(e) {
          e.preventDefault();
          e.stopPropagation();
        }

        handleFiles(files) {
          const fileArray = Array.from(files);
          fileArray.forEach((file) => this.addFile(file));
          this.fileInput.value = ""; // Clear input
        }

        addFile(file) {
          const fileItem = {
            id: Date.now() + Math.random(),
            file: file,
            name: file.name,
            size: this.formatFileSize(file.size),
            type: file.type,
            progress: 0,
            status: "pending",
          };

          this.files.push(fileItem);
          this.renderFileItem(fileItem);
          this.uploadFile(fileItem);
        }

        formatFileSize(bytes) {
          if (bytes === 0) return "0 Bytes";
          const k = 1024;
          const sizes = ["Bytes", "KB", "MB", "GB"];
          const i = Math.floor(Math.log(bytes) / Math.log(k));
          return (
            parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + " " + sizes[i]
          );
        }

        renderFileItem(fileItem) {
          const fileDiv = document.createElement("div");
          fileDiv.className = "file-item";
          fileDiv.id = `file-${fileItem.id}`;

          fileDiv.innerHTML = `
          <div style="flex: 1">
            <div><strong>${fileItem.name}</strong> (${fileItem.size})</div>
            <div class="upload-progress">
              <div class="upload-progress-bar" id="progress-${fileItem.id}"></div>
            </div>
            <div><small id="status-${fileItem.id}">Pending...</small></div>
          </div>
          <button class="remove-btn" data-id="${fileItem.id}">Remove</button>
        `;

          this.fileList.appendChild(fileDiv);

          // Add remove button handler
          const removeBtn = fileDiv.querySelector(".remove-btn");
          removeBtn.addEventListener("click", () =>
            this.removeFile(fileItem.id),
          );
        }

        async uploadFile(fileItem) {
          const formData = new FormData();
          formData.append("file", fileItem.file);

          // Update status
          const statusEl = document.getElementById(`status-${fileItem.id}`);
          const progressBar = document.getElementById(
            `progress-${fileItem.id}`,
          );

          fileItem.status = "uploading";
          if (statusEl) statusEl.textContent = "Uploading...";

          // Simulate upload with progress
          try {
            // Simulate progress (replace with actual fetch with XMLHttpRequest for progress)
            for (let progress = 0; progress <= 100; progress += 10) {
              await new Promise((resolve) => setTimeout(resolve, 100));
              fileItem.progress = progress;
              if (progressBar) progressBar.style.width = `${progress}%`;
              if (statusEl && progress === 100)
                statusEl.textContent = "Processing...";
            }

            // Simulate API call
            await new Promise((resolve) => setTimeout(resolve, 500));

            fileItem.status = "completed";
            if (statusEl) {
              statusEl.textContent = "✅ Upload completed!";
              statusEl.style.color = "green";
            }

            console.log("Uploaded:", fileItem.name);
          } catch (error) {
            fileItem.status = "failed";
            if (statusEl) {
              statusEl.textContent = "❌ Upload failed";
              statusEl.style.color = "red";
            }
          }
        }

        removeFile(id) {
          const fileDiv = document.getElementById(`file-${id}`);
          if (fileDiv) fileDiv.remove();
          this.files = this.files.filter((f) => f.id !== id);
        }
      }

      // Initialize
      const uploader = new FileUploader("dropZone", "fileList");
    </script>
  </body>
</html>
```

---

## API Reference

### DOM Selection Methods

| Method                         | Description          | Returns         |
| ------------------------------ | -------------------- | --------------- |
| `getElementById(id)`           | Select by ID         | Element or null |
| `getElementsByClassName(name)` | Select by class      | HTMLCollection  |
| `getElementsByTagName(name)`   | Select by tag        | HTMLCollection  |
| `querySelector(selector)`      | CSS selector (first) | Element or null |
| `querySelectorAll(selector)`   | CSS selector (all)   | NodeList        |

### DOM Manipulation Methods

| Method                   | Description             |
| ------------------------ | ----------------------- |
| `createElement(tag)`     | Create new element      |
| `appendChild(element)`   | Add as last child       |
| `insertBefore(new, ref)` | Insert before reference |
| `removeChild(element)`   | Remove child            |
| `remove()`               | Remove element (modern) |
| `cloneNode(deep)`        | Clone element           |

### Event Methods

| Method                                | Description            |
| ------------------------------------- | ---------------------- |
| `addEventListener(type, fn, options)` | Add event listener     |
| `removeEventListener(type, fn)`       | Remove event listener  |
| `dispatchEvent(event)`                | Trigger event manually |

### ES6 Features Quick Reference

| Feature          | Syntax          | Use Case                    |
| ---------------- | --------------- | --------------------------- |
| `let`            | `let x = 5;`    | Mutable variables           |
| `const`          | `const x = 5;`  | Immutable variables         |
| Arrow function   | `() => {}`      | Callbacks, shorter syntax   |
| Template literal | `` `${var}` ``  | String interpolation        |
| Destructuring    | `{a, b} = obj`  | Extract values              |
| Spread           | `...arr`        | Copy/merge arrays/objects   |
| Rest             | `...args`       | Collect remaining arguments |
| Modules          | `import/export` | Code organization           |
| Classes          | `class X {}`    | OOP patterns                |
| Promises         | `new Promise()` | Async operations            |
| Async/Await      | `async/await`   | Clean async code            |

---

## Useful Resources

- MDN Web Docs: https://developer.mozilla.org/
- JavaScript.info: https://javascript.info/
- ES6 Features: https://github.com/lukehoban/es6features

---

Created for beginners learning ES6 and DOM manipulation

```

```
