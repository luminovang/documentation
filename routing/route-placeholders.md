# Dynamic Strict Segment Placeholders in Routing

***

## Overview

Segment placeholders allow dynamic patterns to be matched against incoming URLs, which can be defined using regular expression patterns or predefined placeholders.

***

## Introduction

In the context of routing, segment placeholders allow dynamic pattern matching against incoming URLs or CLI Commands, making it possible to capture parameters and pass them into your application controller class as an arguments for further processing. 

In Luminova, you can define segment captures using various methods: regular expression patterns (e.g., `/blog/([a-zA-Z0-9]+)/([0-9]+)`), named placeholders (e.g., `/blog/{title}/{id}`), or predefined strict placeholders (e.g., `/blog/(:string)/(int)`). While regular expressions offer flexibility, named and strict placeholders provide a more convenient and developer-friendly approach, making them especially suitable for beginners.

---

Let me know if you'd like any further modifications!
## Usage Examples

### PHP Attributes with Placeholders

Using placeholders in attributes simplifies route definitions. Here's an example:

```php
// /app/Controllers/Http/WebController.php
<?php 
namespace App\Controllers\Http;

use \Luminova\Base\BaseViewController;
use \Luminova\Attributes\Route;

class WebController extends BaseViewController 
{
    #[Route('/user/(:int)/(:alphanumeric)', methods: ['GET'])]
    public function user(int $id, string $username): int
    {
        // Implementation for user
    }

    #[Route('/file/(:number)', methods: ['GET'])]
    public function file(string $version): int
    {
        // Implementation for file
    }
}
```

### Equivalent Using Regular Expressions

Here’s how the same routes can be defined using regular expressions:

```php
// /app/Controllers/Http/WebController.php
<?php
namespace App\Controllers\Http;

use \Luminova\Base\BaseViewController;
use \Luminova\Attributes\Route;

class WebController extends BaseViewController 
{
    #[Route('/user/(\d+)/([a-zA-Z0-9-]+)', methods: ['GET'])]
    public function user(int $id, string $username): int
    {
        // Implementation for user
    }

    #[Route('/file/([0-9-.]+)', methods: ['GET'])]
    public function file(string $version): int
    {
        // Implementation for file
    }
}
```

***

### Router Methods with Placeholders

Using placeholders in router methods enhances readability and maintainability:

```php
// /routes/web.php
<?php 
$router->get('/user/(:int)/(:alphanumeric)', 'WebController::user');
$router->get('/file/(:number)', 'WebController::file');
```

### Equivalent Using Regular Expressions

Here's the equivalent using regular expressions in the router:

```php
// /routes/web.php
<?php 
$router->get('/user/(\d+)/([a-zA-Z0-9-]+)', 'WebController::user');
$router->get('/file/([0-9-.]+)', 'WebController::file');
```

---

### Placeholders Definition

Below is a detailed explanation of the available router segment placeholders, how they work, and the differences between them.

---

### mixed
   - **Pattern**: `(:mixed)`
   - **Description**: This placeholder matches **any character except `/`**, meaning it will stop at a forward slash (`/`). It can capture letters, numbers, and special characters, but it cannot capture entire paths.
   - **Use Case**: When you need to capture a segment that can be any combination of characters except for `/`.
   - **Example**: 
     - **URL**: `/user/1234`
     - **Route**: `/user/(:mixed)`
     - **Matched Value**: `1234`

---

### any
   - **Pattern**: `(:any)`
   - **Description**: Matches **any sequence of characters**, including `/`. This is useful when you want to capture an entire path or group segments within a single route.
   - **Use Case**: When capturing everything after a certain point, including slashes.
   - **Example**: 
     - **URL**: `/blog/posts/2024/09/title`
     - **Route**: `/blog/(:any)`
     - **Matched Value**: `posts/2024/09/title`

---

### root
   - **Pattern**: `(:root)`
   - **Description**: Matches **any character** from the root segment onward. This works similarly to `:any` but is typically used to denote the starting point for routing, capturing the path from the root directory.
   - **Use Case**: When you want to match routes for middleware, the root segment onward.
   - **Example**:
     - **URL**: `/about/company`
     - **Route**: `/(:root)`
     - **Matched Value**: `about/company`

---

### optional
   - **Pattern**: `(:optional)`
   - **Description**: This placeholder matches **zero or more characters**, but **not including `/`**. It is optional, meaning it will match whether a value is provided or not.
   - **Use Case**: When a segment is optional in a route and can either be empty or contain a value.
   - **Example**:
     - **URL**: `/product`
     - **Route**: `/product/(:optional)`
     - **Matched Value**: (empty, since the value is optional)

---

### alphanumeric
   - **Pattern**: `(:alphanumeric)`
   - **Description**: Matches any **alphanumeric string** (letters and numbers), including **hyphens (`-`)**. This is useful when segment values consist of letters, digits, and hyphens but no special characters or spaces.
   - **Use Case**: For capturing slugs or IDs, kebab-cased string that consist of letters, numbers, and hyphens.
   - **Example**:
     - **URL**: `/category/electronics-123`
     - **Route**: `/category/(:alphanumeric)`
     - **Matched Value**: `electronics-123`

---

### int or integer
   - **Pattern**: `(:int)` or `(:integer)`
   - **Description**: Matches an **integer**, which consists of one or more digits. Both `:int` and `:integer` refer to the same pattern.
   - **Use Case**: When a route segment should only match numeric values (positive whole numbers).
   - **Example**:
     - **URL**: `/user/42`
     - **Route**: `/user/(:int)`
     - **Matched Value**: `42`
   - **Alternative**: `:integer` can also be used, as it is an alias for `:int`.

---

### number
   - **Pattern**: `(:number)`
   - **Description**: Matches a **whole number** or a **decimal number**. This pattern allows both integers and floating-point numbers to be captured.
   - **Use Case**: When capturing both whole numbers, version number or numbers with decimal points.
   - **Example**:
     - **URL**: `/price/49.99`
     - **Route**: `/price/(:number)`
     - **Matched Value**: `49.99`

---

### float
   - **Pattern**: `(:float)`
   - **Description**: Matches **floating-point numbers** with optional signs `(+/-)`. This pattern ensures the number has a decimal point and digits on both sides of it.
   - **Use Case**: When capturing signed or unsigned floating-point numbers.
   - **Example**:
     - **URL**: `/discount/-25.50`
     - **Route**: `/discount/(:float)`
     - **Matched Value**: `-25.50`

---

### double

- **Pattern**: `(:double)`
- **Description**: Matches **floating-point numbers** that can optionally include a sign (`+` or `-`). The number must contain a decimal point, and it requires digits both before and after the decimal. This is useful for capturing more precise numerical values, such as measurements or calculations that are not whole numbers.
- **Use Case**: Use this placeholder when you need to capture numbers that require decimal precision.
- **Example**:
  - **URL**: `/temperature/98.6`
  - **Route**: `/temperature/(:double)`
  - **Matched Value**: `98.6`

**Key Difference Between `:double` and `:float`**:
- **Precision**: Both `:double` and `:float` match floating-point numbers, but in many programming contexts, a `double` refers to a data type with **double-precision** (64-bit) as opposed to `float`, which is typically **single-precision** (32-bit). Although they have behavior in pattern, the choice of placeholder name might reflect the desired precision in your application logic.

---

### string
   - **Pattern**: `(:string)`
   - **Description**: Matches **any string** containing letters, numbers, or special characters. This pattern is versatile and captures almost any string except certain reserved characters.
   - **Use Case**: When you need to capture a wide range of characters in a segment, including special symbols.
   - **Example**:
     - **URL**: `/profile/user_123@example.com`
     - **Route**: `/profile/(:string)`
     - **Matched Value**: `user_123@example.com`

---

### alphabet
   - **Pattern**: `(:alphabet)`
   - **Description**: Matches **alphabetic characters only**, i.e., letters from `A-Z` (both uppercase and lowercase).
   - **Use Case**: When a segment must only contain letters like person name.
   - **Example**:
     - **URL**: `/name/Peter`
     - **Route**: `/name/(:alphabet)`
     - **Matched Value**: `Peter`

---

### path
   - **Pattern**: `(:path)`
   - **Description**: Matches a **path** with multiple segments separated by `/`. This placeholder is particularly useful when you need to capture nested directories or files in CLI command.
   - **Use Case**: When capturing multiple path segments from a command.
   - **Example**:
     - **Command**: `index.php foo file="/images/2024/summer/beach.png"`
     - **Route**: `/files/(:path)`
     - **Matched Value**: `/images/2024/summer/beach.png`

---

### Routing Placeholders

These placeholders are used within your routes to match and capture specific patterns from the URL or Commands, each placeholder provides a way to capture and validate different parts of a URL or Command dynamically. 

| Placeholder    | Pattern                 | Description                                                                                   | Allows `/` | Optional |
|--------------------|-----------------------------|---------------------------------------------------------------------------------------------------|----------------|--------------|
| `(:mixed)`         | `([^/]+)`                   | Matches any character except `/`, capturing segments that contain alphanumeric or special characters. | No             | No           |
| `(:any)`           | `(.*)`                      | Matches any character, including `/`.                                                             | Yes            | No           |
| `(:root)`          | `?.*`                       | Matches any character from the root segment `/`.                                                   | Yes            | Yes          |
| `(:optional)`      | `?([^/]*)?`                  | Matches zero or more characters, except `/`, making the segment optional.                         | No             | Yes          |
| `(:alphanumeric)`  | `([a-zA-Z0-9-]+)`           | Matches alphanumeric characters and hyphens.                                                      | No             | No           |
| `(:int)`           | `(\d+)`                     | Matches integers (one or more digits).                                                            | No             | No           |
| `(:number)`        | `([0-9-.]+)`                | Matches whole numbers, including negative signs and decimals.                                     | No             | No           |
| `(:float)`         | `([+-]?\d+\.\d+)`           | Matches strictly floating-point numbers with optional signs (`+` or `-`).                                   | No             | No           |
| `(:double)`        | `([+-]?\d+(\.\d*)?)'`           | Matches floating-point numbers with signs (`+` or `-`), same as `:float`, but intended for double-precision use cases. | No       | No           |
| `(:string)`        | `([a-zA-Z0-9\W_-]+)`        | Matches strings containing letters, numbers, or special characters.                               | No             | No           |
| `(:alphabet)`      | `([a-zA-Z]+)`               | Matches alphabetic characters only.                                                               | No             | No           |
| `(:path)`          | `((.+)/([^/]+)+)`           | Matches a path with segments separated by `/`, useful for capturing longer paths in CLI routes.    | Yes            | No           |
