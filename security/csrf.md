# Cross-Site Request Forgery (CSRF) Protection

***

## Overview

Learn how to secure your application against CSRF attacks by integrating CSRF tokens into forms and API requests. Prevent unauthorized actions, protect user data, and maintain application integrity.

***

## Introduction

The **CSRF (Cross-Site Request Forgery)** class protects your web forms from CSRF attacks — a common security threat where attackers trick a trusted user’s browser into performing unwanted actions on your site. It ensures that every sensitive request truly comes from the legitimate user, not from a malicious external source.

This class works by creating a **unique security token** for each user session. The token must be included in any form or request that changes data (like submitting a form or updating a record). When your application receives the request, the CSRF class checks if the token is valid before processing it. If the token doesn’t match, the request is blocked, preventing unauthorized actions.

---

## Usages

To change how the CSRF token is stored. Either in **session** or **cookie**, update the session configuration setting.

**Storage Options:**

* **`session`** – Stores the token in PHP session data (default, most secure).
* **`cookie`** – Stores the token in a browser cookie (useful for stateless requests or APIs).

**Example:**

```php
App\Config\Session->csrfStorage = 'session';
```

For more details, see the [Session Configuration](/configs/session.md) section.

---

### Protecting Requests

**In HTML Forms**

Use `inputToken()` or `getToken()` to include a CSRF token as a hidden input in any form that performs **POST**, **PUT**, or **DELETE** actions.

```php
<form method="POST" action="/submit">
    <?php
    // Option 1: Automatically generate a hidden CSRF input
    Luminova\Security\CSRF::inputToken();

    // Option 2: Manually include token
    // <input type="hidden" name="csrf_token" value="<?= Luminova\Security\CSRF::getToken(); ?>">
    ?>
    
    <input type="text" name="username" required>
    <button type="submit">Submit</button>
</form>
```

When the form is submitted, the token is sent with it and later verified on the server.

---

**In AJAX Requests**

Include the CSRF token in a meta tag, then attach it to every AJAX request header.

```html
<head>
    <?= Luminova\Security\CSRF::metaToken(); ?>
    <!-- Example output: <meta name="csrf_token" content="eut9n..."> -->
</head>
```

Attach the token when sending data with JavaScript:

```js
const csrfToken = document.querySelector('meta[name="csrf_token"]').getAttribute('content');

fetch('/api/update', {
    method: 'POST',
    headers: { 'X-CSRF-Token': csrfToken },
    body: JSON.stringify({ data: 'example' })
});
```

> **Tip:**
> For single-page applications or AJAX-heavy pages, refresh the token from the backend after each submission and update the meta tag value.

---

### Verifying Token on Submission

Validate the CSRF token before processing any submitted data. Reject the request if the token is missing or invalid.

```php
// app/Controllers/Http/MyController.php
use Luminova\Security\CSRF;

$csrfToken = $this->request->getCsrfToken();

// Or method specific 
// $csrfToken = $this->request->getPost('csrf_token');

if (CSRF::validate($csrfToken, reuseable: false)) {
    // Token is valid; now refresh it to prevent reuse
    CSRF::refresh();
    echo 'Action completed, token refreshed.';
} else {
    echo 'Invalid CSRF token. Request blocked.';
}
```

> **Recommendation:**
> Always refresh the token after successful validation to prevent reuse, especially for sensitive operations.

---

### Checking Token Availability

Check whether a token has already been generated before creating one manually.

```php
use Luminova\Security\CSRF;

if (!CSRF::hasToken()) {
    $token = CSRF::refresh(); // Generate a new one if missing
}
```

---

### Deleting a Token

Remove the stored token when a user logs out or when ending a session to prevent reuse.

```php
use Luminova\Security\CSRF;

// During logout or session cleanup
CSRF::delete();
```

***

## Class Definition

* Class namespace: `Luminova\Security\CSRF`

***

## Methods

### getToken

Retrieves a previously generated CSRF token or generates a new token if none was found, then stores it.

```php
public static getToken(): string
```

**Return Value:**

`string` - Return the generated CSRF token.

***

### refresh

Generates a new CSRF token and stores it.

```php
public static refresh(): string
```

Use this method when you need to regenerate a token after validation.

**Return Value:**

`string` - Return the generated CSRF token.

***

### delete

Delete stored CSRF token.

```php
public static delete(): void
```

***

### inputToken

Generates and displays an HTML hidden input field for the CSRF token.

```php
public static inputToken(): void
```

***

### metaToken

Generates and displays an HTML meta tag for the CSRF token.

```php
public static metaToken(): void
```

***

### validate

Validates a submitted CSRF token.

```php
public static validate(string $token, bool $reuseable = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$token` | **string** | The token submitted by the user. |
| `$reuseable` | **bool** | Whether to retain or delete the token after successful verification (default: true). |

**Return Value:**

`bool` - Return true if the submitted token is valid, false otherwise.

***

### hasToken

Check if a token has already been generated.

```php
public static hasToken(): bool
```

**Return Value:**

`bool` - Returns true if a token has already been created, otherwise false.