# Express Middleware (2-Minute Beginner Explanation)

Think of an Express application as a **restaurant**.

When a customer (browser/Postman) sends an order (request), the order doesn't go directly to the chef (route handler). It may first pass through several workers:

1. Security guard checks the customer.
2. Receptionist writes down information.
3. Cashier verifies payment.
4. Then the chef prepares the food.

These workers are like **middleware**.

---

### What is Middleware?

A middleware is a **function that runs between receiving a request and sending a response**.

It can:

* Read the request
* Modify the request
* Perform checks (authentication, validation)
* Log information
* End the request-response cycle
* Pass control to the next middleware

```js
const middleware = (req, res, next) => {
  console.log("Middleware is running");

  next(); // pass control to the next middleware
};
```

---

### Example

```js
import express from "express";

const app = express();

app.use((req, res, next) => {
  console.log("Request received");
  next();
});

app.get("/", (req, res) => {
  res.send("Hello World");
});

app.listen(3000);
```

When someone visits `/`:

```
Request received
Hello World
```

Flow:

```
Client Request
      ↓
Middleware
      ↓
Route Handler
      ↓
Response
```

---

### What is `next()`?

`next()` tells Express:

> "I'm done. Please continue to the next middleware or route handler."

```js
app.use((req, res, next) => {
  console.log("Step 1");
  next();
});

app.use((req, res, next) => {
  console.log("Step 2");
  next();
});
```

Output:

```
Step 1
Step 2
```

Without `next()`, the request stops there.

```js
app.use((req, res, next) => {
  console.log("Step 1");
});
```

The browser will keep waiting because Express doesn't know what to do next.

---

### Why Do Developers Use Middleware?

#### 1. Logging Requests

```js
app.use((req, res, next) => {
  console.log(req.method, req.url);
  next();
});
```

Example output:

```
GET /products
POST /users
```

---

#### 2. Parsing JSON

```js
app.use(express.json());
```

Converts:

```json
{
  "name": "Forhad"
}
```

into:

```js
req.body.name;
```

---

#### 3. Authentication

```js
app.use((req, res, next) => {
  const token = req.headers.authorization;

  if (!token) {
    return res.status(401).send("Unauthorized");
  }

  next();
});
```

Checks whether a user is allowed to access a route.

---

### One-Sentence Definition

> **Middleware is a function that executes after Express receives a request but before the final route handler sends a response, allowing developers to inspect, modify, validate, or process the request.**

For a backend developer, common middleware examples are:

* `express.json()` → read JSON data
* Authentication middleware → check login/token
* Validation middleware → validate input
* Logging middleware → record requests
* Error-handling middleware → handle errors centrally

A good mental model is:

```text
Request
   ↓
Middleware 1
   ↓
Middleware 2
   ↓
Middleware 3
   ↓
Route Handler
   ↓
Response
```

Each middleware gets a chance to work on the request before it reaches the final route handler.
