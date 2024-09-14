# Request Error Handling Attributes

***

## Overview

Luminova's error attribute allows you to define a global error handler for HTTP routes, in class global scope before the class definition starts.

***

## Introduction

The Luminova `Error` attribute replaces the default `Context` implementation for managing and handling context errors. It offers a straightforward method to define error handlers for `HTTP` applications. By using attributes, you can keep your routing logic and error handling close to your controller methods, improving readability and maintainability.

The `Error` attribute allows you to set a global error handler for HTTP routes. It can be defined in the global scope of a class, before the class definition starts.

***

### URI Prefix

First, let's talk about URL prefixes. In Luminova, a URL prefix is the initial part of a URL path after the scheme (e.g., http or https) and hostname.

***

### Definition Example

```php
// app/controllers/HTTPController.php

namespace App\Controllers;

use Luminova\Base\BaseController;
use Luminova\Attributes\Route;
use Luminova\Attributes\Error;
use App\Controllers\Errors\ViewErrors;

#[Error('web', '/.*', [ViewErrors::class, 'onWebError'])]
class HTTPController extends BaseController
{
    // Your controller methods
}
```

***

**Prefix Examples:**

- `http://example.com/` - This URL has no prefix and will match a pattern like `/`.
- `http://example.com/foo` - This URL has the prefix `foo` and will match a pattern like `/foo`.
- `http://example.com/account/main/users/dashboard` - This URL has the prefix `account` and will match a pattern like `/account/.*`.

***

### Understanding Error URI Prefix

Don't be confused by the context URI prefix `web`. Using `web` as the prefix name doesn't mean only URLs that start with `web` are handled by this error handler method `onWebError`. It is a generic way to tell Luminova to handle every URL with this this error handler if no other custom error handler prefix is defined or found to handle the URL.

**Examples:**

```php
#[Error('web', '/.*', [ViewErrors::class, 'onWebError'])]
```

- `https://example.com/account/page`: Handled by the `web` error method `onWebError`.
- `https://example.com/web/page`: Handled by the `web` error method `onWebError`.
- `https://example.com/panel/page`: Handled by the `web` error method `onWebError` if no `panel` context is defined.

```php
#[Error('panel', '/.*', [ViewErrors::class, 'onPanelError'])]
```

- `https://example.com/panel/page`: Handled by the `panel` error method `onPanelError` because it is explicitly defined.

***

### Error Attribute Constructor

The constructor for the route error global handling attribute allows you to set up a route context with a specific URI pattern and error handler.

```php
public __construct(string $context = 'web', string $pattern = '/.*', \Closure|array|null $onError = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$context` | **string** | The route error context name for URI prefixing. |
| `$pattern` | **string** | The URI route pattern to match for the current error handling (e.g., `/`, `/.*`, `/blog/([0-9-.]+)`). |
| `$onError` | **\Closure\|array\|null** | The error handler, which can be a Closure or an array specifying a class and method. |

***

### Usage Examples

You can use the `Error` attribute to specify error handling for different parts of your application.

**Global Handler**

This example will call the `onWebError` method whenever a `404` error occurs if no other error context is defined to handle the prefix error.

```php
// app/controllers/WebController.php

namespace App\Controllers;

use Luminova\Base\BaseViewController;
use Luminova\Attributes\Route;
use Luminova\Attributes\Error;
use App\Controllers\Errors\ViewErrors;

#[Error('web', '/.*', [ViewErrors::class, 'onWebError'])]
class WebController extends BaseViewController
{
    #[Route('/', methods: ['GET'])]
    public function index(): void
    {
        // Your code here
    }
}
```

**Multiple Handlers**

You can define different error handlers for different prefixes by defining multiple contexts.

```php
// app/controllers/WebController.php

namespace App\Controllers;

use Luminova\Base\BaseViewController;
use Luminova\Attributes\Route;
use Luminova\Attributes\Error;
use App\Controllers\Errors\WebErrors;

#[Error('admin', '/(?!dashboard/).*', [WebErrors::class, 'main'])]
#[Error('admin', '/dashboard/.*', [WebErrors::class, 'dashboard'])]
class WebController extends BaseViewController
{
    #[Route('/admin', methods: ['GET'])]
    public function home(): void
    {
        // Your code here
    }

    #[Route('/admin/dashboard', methods: ['GET'])]
    public function dashboard(): void
    {
        // Your code here
    }
}
```

> **In the above setup:**
> 
> The first `Error` attribute `'/ (?!dashboard/).*'` matches all routes except those starting with `/dashboard/`.
>
> The second `Error` attribute `'/dashboard/.*'` matches routes starting with `/dashboard/`.