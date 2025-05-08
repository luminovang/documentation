# HTTP Method Spoofing and Overrides

***

## Overview

Database Builder Class: An Object-Relational Mapping Tool for Simplified CRUD Operations and Object-Oriented Database Management.

***

## Introduction

In standard HTTP, HTML forms only support `GET` and `POST` methods. However, many modern web applications and APIs need to handle additional methods like `PUT`, `PATCH`, and `DELETE` for full RESTful support. This is where method spoofing comes in.

**Method Spoofing** allows a client (usually a web form) to submit a `POST` request while indicating that it should actually be treated as another HTTP method.

---

### Why Use Method Spoofing?

- **RESTful design**: You can properly map your routes and controller methods to appropriate HTTP methods (`PUT` for update, `DELETE` for removal, etc.).
- **Browser compatibility**: HTML doesn't support `PUT` or `DELETE` natively in forms; spoofing makes it possible.
- **Cleaner APIs**: It allows you to design clean and semantically correct API endpoints.

---

### Handling Method Overrides

Luminova automatically detects and handles custom method spoofing during the request boot process, allowing you to use additional HTTP methods like `PUT`, `PATCH`, `DELETE`, and `OPTIONS` even if your form submission is limited to `POST`.

**Important details:**
- Method spoofing is disabled in **production** for security and consistency.
- It only applies when the incoming request is a `POST`.
- It looks for a hidden form field or URL parameter named `_method` or `_METHOD`.
- If a valid override method (`PUT`, `DELETE`, `PATCH`, or `OPTIONS`) is found, it sets `$_SERVER['HTTP_X_HTTP_METHOD_OVERRIDE']` accordingly allowing routing system to handling it as specified method.
- CLI (command-line) requests are ignored.

---

### Method Spoofing

Web forms only support `GET` and `POST` methods. To simulate `PUT`, `PATCH`, or `DELETE` requests, Luminova supports **method spoofing** by using a hidden `_METHOD` field or query param `_method` in your form.

**HTML Form Example:**

```html
<form method="POST" action="/account/update">
   <input type="text" name="name" value="John Doe">
   <input type="hidden" name="_METHOD" value="PUT">
   <button type="submit">Update</button>
</form>
```

Luminova automatically detects this hidden field and internally treats the request as a `PUT`, `PATCH`, or `DELETE`, depending on the value provided.

---

### Method Override Header

You can also override the HTTP method using the standard `X-HTTP-Method-Override` header. This is useful when making AJAX requests, using tools like `curl`, Postman, or in custom HTTP clients.

**JavaScript Example:**

```js
fetch('/account/update', {
   method: 'POST',
   headers: {
      'X-HTTP-Method-Override': 'PUT',
      'Content-Type': 'application/json'
   },
   body: JSON.stringify({ name: 'Jane Doe' })
});
```

> **Note:** The `X-HTTP-Method-Override` header does **not** work with traditional HTML forms. Use the hidden `_method` field instead.

---

### Controller Example

To handle spoofed methods like `PUT`, define the expected method explicitly using the `#[Route]` attribute:

```php
#[Route('/account/update', methods: ['PUT'])]
public function update(): int 
{
   $name = $this->request->getPut('name');
   echo $name; // Jane Doe
}
```

> In this example, even though the request is submitted via `POST`, the hidden `_method=PUT` field or override header will cause Luminova to treat the request as a `PUT`. This allows the route to match and the correct controller method to execute.
