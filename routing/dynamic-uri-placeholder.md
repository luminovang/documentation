# Dynamic URI Placeholders for Routing

***

## Overview

View route URI segments using regex, named placeholders, or predefined patterns. Capture dynamic parts and pass them as arguments to controller methods during request routing.

***

## Introduction

Dynamic view segments are parts of a URI path that change with each request. The routing system processes these dynamic segments and passes them as arguments to the appropriate controller method.

Luminova provides **segment placeholders**,  a predefined patterns you can use directly in your routes **instead of writing raw regular expressions**. These placeholders let you easily define controller routes that **dynamically match URL segments or CLI commands**, automatically passing the captured values to your controller methods.

The [Luminova routing system](/routing/system.md) supports three ways to define route segments:

* **Raw regular expressions:**
  * Most flexible, but harder to read and maintain (e.g. `/blog/([a-zA-Z0-9]+)/([0-9]+)`).
* **Named placeholders:**
  * Clean and descriptive, but no type validation (e.g. `/blog/{title}/{id}`).
* **Predefined placeholders:**
  * Clean, easy to use, strict-type validation (e.g. `/blog/(:string)/(:int)`).

> While raw regex gives you complete control, **named and predefined placeholders are simpler and more reliable**, ideal when you want consistent route definitions without remembering complex patterns.

---

## Defining Routes

### With Predefined Placeholders (Recommended)

Using placeholders makes your routes **cleaner and easier to read**, while still enforcing strict validation of each segment.

**Via Attribute-Based Routing:**

```php
// /app/Controllers/Http/WebController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;

class WebController extends Controller 
{
  #[Route('/user/(:int)/(:username)', methods: ['GET'])]
  public function user(int $id, string $username): int
  {
    // Handles GET /user/42/john-doe
  }

  #[Route('/file/(:number)', methods: ['GET'])]
  public function file(string $version): int
  {
    // Handles GET /file/1.5
  }
}
```

**Via Method-Based Routing:**

```php
// /routes/web.php

$router->get('/user/(:int)/(:username)', 'WebController::user');
$router->get('/file/(:number)', 'WebController::file');
```

---

### With Named Placeholders

Named placeholders make routes **cleaner and more readable**, but they don't enforce validation on their own. This means **any string will match**, even if it’s not the type you expected. Use this style when simplicity matters more than strict input validation.

**Via Attribute-Based Routing:**

```php
// /app/Controllers/Http/WebController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;

class WebController extends Controller 
{
  #[Route('/user/{id}/{username}', methods: ['GET'])]
  public function user(int $id, string $username): int
  {
    // Handles GET /user/42/john-doe
    // but will also match /user/abc/??? if called directly
  }

  #[Route('/file/{version}', methods: ['GET'])]
  public function file(string $version): int
  {
    // Handles GET /file/1.5
    // but will also match /file/anything-here
  }
}
```

**Via Method-Based Routing:**

```php
// /routes/web.php

$router->get('/user/{id}/{username}', 'WebController::user');
$router->get('/file/{version}', 'WebController::file');
```

> **Note:** 
> Named placeholders don't validate input types — that’s up to your controller code. 
> For stricter matching (e.g., only numbers), use predefined placeholders like `(:int)` or `(:number)` instead.

---

### Using Raw Regular Expressions

If you prefer (or need) full control, you can still write raw regex. The behavior is identical with **predefined placeholders**, but the syntax is harder to maintain:

**Via Attribute-Based Routing:**

```php
// /app/Controllers/Http/WebController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;

class WebController extends Controller 
{
  #[Route('/user/(\d+)/([a-zA-Z0-9._-]+)', methods: ['GET'])]
  public function user(int $id, string $username): int
  {
    // Handles GET /user/42/john-doe
  }

  #[Route('/file/([+-]?\d+(?:\.\d+)?)', methods: ['GET'])]
  public function file(string $version): int
  {
    // Handles GET /file/1.5
  }
}
```

**Via Method-Based Routing:**

```php
// /routes/web.php

$router->get('/user/(\d+)/([a-zA-Z0-9._-]+)', 'WebController::user');
$router->get('/file/([+-]?\d+(?:\.\d+)?)', 'WebController::file');
```

> **Recommendation:** 
> Use placeholders whenever possible — they're easier to read, less error-prone, and automatically validated by the router.

---

## Custom Placeholders

Luminova allows you to define **custom pattern placeholders**. This is useful when:

* You have **repeated patterns** across routes.
* You want to use **longer or complex regex**.
* You need to **override default Luminova placeholders**.

Custom placeholders can be defined in your `Application` class (`onCreate` or `__construct`) or globally in `/app/Global.php` **before routing starts**.

> If you define them globally, ensure `feature.app.dev.functions` is enabled in your `.env` for autoloading.

---

### Defining Custom Placeholders

```php
// /app/Application.php

namespace App;

use Luminova\Foundation\Core\Application as CoreApplication;
use Luminova\Routing\Router;

class Application extends CoreApplication
{
  protected function onCreate(): void 
  {
    // Raw pattern → auto parameter
    Router::pattern('slug', '[a-z0-9-]+');         

    // Non-capturing → (?:[a-z0-9-]+)
    Router::pattern('slug', '[a-z0-9-]+', 0);      

    // Capturing → ([a-z0-9-]+)
    Router::pattern('slug', '[a-z0-9-]+', 1);      

    // Already grouped pattern → stays unchanged
    Router::pattern('custom', '([a-z]+)-(\d+)', 1);

    Router::pattern('my-int', '(\d+)');
    Router::pattern('my-username', '([a-zA-Z0-9._-]+)');
    Router::pattern('my-version', '([+-]?\d+(?:\.\d+)?)');
  } 
}
```

---

### Using Custom Placeholders in Routes

**Attribute-based route:**

```php
// /app/Controllers/Http/WebController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;

class WebController extends Controller 
{
  #[Route('/user/(:my-int)/(:my-username)', methods: ['GET'])]
  public function user(int $id, string $username): int
  {
    // Handles GET /user/42/john-doe
  }

  #[Route('/file/(:my-version)', methods: ['GET'])]
  public function file(string $version): int
  {
    // Handles GET /file/1.5
  }
}
```

**Via Method-Based Routing:**

```php
// /routes/web.php

$router->get('/user/(:my-int)/(:my-username)', 'WebController::user');
$router->get('/file/(:my-version)', 'WebController::file');
```

> Once defined, your custom placeholder works in **any route** within the application context, making route definitions cleaner and more consistent.

---

## Default Placeholders

These placeholders allows you to define **dynamic parts of a URL or command**, to capture and validate specific patterns while handling requests.

Below, we break down each placeholder, what it matches, and when to use it.

---

### root

This placeholder matches **everything after the first `/` in URL**, no matter how many segments there are.
It’s similar to `:any`, but suitable when marking the **starting point of a route** and grab the full path that follows.

**Example:**

* **Pattern**: `?(?:/[^/].*)?`
* **Route**: `/(:root)`
* **URI**: `/about/company`
* **Matched Value**: `about/company`

**Use Case:**

Use `:root` if you need to capture the **entire path after the first slash**, such as when writing **middleware routes** or creating a **group route** that should handle any sub-path.

---

### mixed

This placeholder matches **zero or more characters except `/`**, meaning it will stop at a forward slash (`/`). Because it allows zero characters, the matched segment can be empty. It uses a *lazy* match, stopping as early as possible.

**Example:**

* **Pattern**: `([^/]*?)`
* **Route**: `/user/(:mixed)`
* **URI**: `/user/`
* **Matched Value**: *(empty string)*
* **URI**: `/user/1234`
* **Matched Value**: `1234`

**Use Case:**

When you need to capture a segment that may contain any characters (or even be empty) but must stop at `/`.

---

### string

This placeholder matches **one or more characters except `/`**, meaning the segment **cannot be empty**. It also uses a *lazy* match, stopping as early as possible.

**Example:**

* **Pattern**: `([^/]+?)`
* **Route**: `/profile/(:string)`
* **URI**: `/profile/user_123`
* **Matched Value**: `user_123`

**Use Case:**

When you need to capture a segment that must contain at least one character (letters, numbers, or special symbols), but still stop at `/`.

---

### integer

This placeholder matches **whole numbers (integers)** with one or more digits. Both `:int` and `:integer` point to the same pattern.

**Alias:**

`:int` is interchangeable with `:integer`.

**Example:**

* **Pattern**: `(\d+)`
* **Route**: `/user/(:int)`
* **URI**: `/user/42`
* **Matched Value**: `42`

**Use Case:**

When you need a segment to only accept whole, positive numbers.

---

### number

This placeholder matches **whole numbers or decimal numbers**, with optional `+` or `-` signs. It accepts integers and floating-point values.

**Example:**

* **Pattern**: `([+-]?\d+(?:\.\d+)?)`
* **Route**: `/price/(:number)`
* **URI**: `/price/49.99`
* **Matched Value**: `49.99`

**Use Case:**

When you need to capture numeric values that could be either whole numbers or decimals — for example, prices or version numbers.

---

### uuid

Matches a **UUID (Universally Unique Identifier)** in the standard 36-character hexadecimal format, including hyphens. This is useful when a route segment must strictly conform to a UUID pattern (version-agnostic). 

**Example:**

* **Pattern**: `([0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12})`
* **URL**: `/resource/550e8400-e29b-41d4-a716-446655440000`
* **Route**: `/resource/(:uuid)`
* **Matched Value**: `550e8400-e29b-41d4-a716-446655440000`

**Use Case:**

When you need to capture route parameters that **must be valid UUIDs**, such as resource identifiers or API tokens.
Valid [UUID v1-v5](https://www.rfc-editor.org/rfc/rfc4122) values only.

---

### username

This placeholder matches **letters, numbers, dots (`.`), underscores (`_`), and hyphens (`-`) only**. It’s stricter than `:string`, making it ideal for usernames or slugs.

**Example:**

* **Pattern**: `([a-zA-Z0-9._-]+)`
* **Route**: `/profile/(:username)`
* **URI**: `/profile/user_123`
* **Matched Value**: `user_123`

**Use Case:**
When you want to enforce a safe format for usernames, slugs, or identifiers without allowing arbitrary special characters.

---

### version

This placeholder matches **numbers and dots (`.`) only**, making it ideal for semantic versioning like `1.0.0`, `2.1`, or `0.0.1`. It’s stricter than `:string` or `:number` because it enforces a version-like format with one or more dot-separated numeric segments.

**Example:**

* **Pattern**: `(\d+(?:\.\d+)+)`
* **Route**: `/package/(:version)`
* **URI**: `/package/3.6.6`
* **Matched Value**: `3.6.6`

**Use Case:**
When you want to enforce proper version formats for packages, API versions, or software releases, ensuring that each segment is numeric and separated by dots.

---

### any

This placeholder matches **any character, including `/`**, making it useful for capturing entire paths or multiple segments.

**Example:**

* **Pattern**: `(.*)`
* **Route**: `/blog/(:any)`
* **URI**: `/blog/posts/2024/09/title`
* **Matched Value**: `posts/2024/09/title`

**Use Case:**

When you want to capture everything after a certain point in the URL, including slashes.

---

### optional

This placeholder matches **a single segment if it exists — or nothing at all**.
It won’t cross `/` boundaries, and because it’s optional, your route works even if this part of the URL is missing or (e.g, `product//foo`).

**Example:**

* **Pattern**: `?(?:/([^/]*))?`
* **Route**: `/product/(:optional)`
* **URI**: `/product`
* **Matched Value**: *(empty — no segment provided)*

**Use Case:**

Use `:optional` if you want part of your route to be **flexible**, such as an **ID that may or may not be supplied**.

---

### alphanumeric

This placeholder matches any **alphanumeric string**. It’s perfect for identifiers that are letters or numbers.

**Example:**

* **Pattern**: `([a-zA-Z0-9]+)`
* **Route**: `/category/(:alphanumeric)`
* **URI**: `/category/electronics123`
* **Matched Value**: `electronics123`

**Use Case:**

When you need to capture clean, URL-friendly slugs or IDs with no spaces or special characters.

---

### float

This placeholder matches **floating-point numbers** with optional `+` or `-` signs. It always requires a decimal point and digits on both sides.

**Example:**

* **Pattern**: `([+-]?\d+\.\d+)`
* **Route**: `/discount/(:float)`
* **URI**: `/discount/-25.50`
* **Matched Value**: `-25.50`

**Use Case:**

When you need to ensure the segment only contains signed or unsigned floating-point numbers.

---

### double

This placeholder matches **floating-point numbers** with optional `+` or `-` signs. Unlike `:float`, it allows digits before and after the decimal but does not force both — so `98.6` works, but `98.` does not.

**Example:**

* **Pattern**: `([+-]?\d+(\.\d+)?)`
* **Route**: `/temperature/(:double)`
* **URI**: `/temperature/98.6`
* **Matched Value**: `98.6`

**Use Case:**

When you need to capture decimal numbers and indicate that they’re intended for **double-precision** values in your application logic.

> **Key Difference Between `:double` and `:float`:**
>
> * In most programming languages, `float` is single-precision (32-bit) while `double` is double-precision (64-bit).
> * In routing, both match decimal numbers, but you might use `:double` to reflect a need for higher precision in calculations.

---

### alphabet

This placeholder matches **letters only** (A–Z, case-insensitive). It does not allow numbers or symbols.

**Example:**

* **Pattern**: `([a-zA-Z]+)`
* **Route**: `/name/(:alphabet)`
* **URI**: `/name/Peter`
* **Matched Value**: `Peter`

**Use Case:**

When a segment must contain only alphabetic characters — useful for names or labels with no digits.

---

### path

This placeholder matches **entire paths with multiple segments** separated by `/`. It’s useful for capturing nested directories or files, such as in CLI commands.

**Example:**

* **Pattern**: `((.+)/([^/]+)+)`
* **Route**: `/files/(:path)`
* **Command Input**: `index.php foo file="/images/2024/summer/beach.png"`
* **Matched Value**: `/images/2024/summer/beach.png`

**Use Case:**

When you need to grab full file paths or multi-level segments without breaking at `/`.