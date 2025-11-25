# HTTP Base Controller

***

## Overview

The HTTP Base Controller is the starting point for building web and API controllers in Luminova. It helps you handle incoming requests, run your app’s logic, and send responses back to the client.

***

## Introduction

The **Base Controller** is the foundation for building **HTTP controllers** in Luminova.
It supports both **MVC** (Model-View-Controller) and **HMVC** (Hierarchical MVC) structures, serving as the link between your application logic and the routing system. Its primary role is to handle incoming requests and generate the correct response back to the client.

---

### What does the Base Controller do?

* **Handles Requests** - Receives and processes incoming HTTP requests.
* **Executes Application Logic** - Works with models, services, and utilities to perform operations.
* **Generates Responses** - Returns templates using the `view()` method or raw output using the `response()` helper.

---

### Key Features

When you extend **Base Controller**, you get built-in features that reduce repetitive code and keep your application consistent. You spend less time on setup and more time writing your app logic.

1. **Faster with Lazy Loading**

   * Properties like `$app`, `$request`, and `$input` load only when you use them, saving memory and improving performance.

2. **Works Seamlessly with Routing**

   * Automatically links to Luminova's routing system.
   * Makes it easy to handle requests, validate data, and send responses.

3. **Easy to Extend and Organize**

   * Build your own controllers on top of base controller without extra setup.
   * Supports both simple MVC and more modular HMVC structures, great for small apps or large projects.

4. **Cleaner Route Definitions with PHP Attributes**

  Define routes directly in your controller with simple annotations:

  * `Luminova\Attributes\Route` - For URI routing view handling.
  * `Luminova\Attributes\Error` - For controller scope error handling.
  * `Luminova\Attributes\Prefix` - For controller scope allowed URI prefixing.

5. **Flexible Responses**

  Easily return different formats using 

  * `Luminova\Template\Response` - Great for APIs, web pages, or mixed apps
  * `Luminova\Template\View` Great for Templates, Web pages or mixed apps

---

### Routable Methods Return Type

A routable method in a controller can return:

* An **integer status exit code** (e.g., `STATUS_SUCCESS`, `STATUS_ERROR`)
* A `Luminova\Template\Response` instance
* Any class implementing `Luminova\Interface\ViewResponseInterface`

* **`STATUS_SUCCESS` (`0`)**
  The request was handled correctly, and the response was rendered successfully.

  * Common after loading a view or completing an action.
  * In middleware, this lets the request continue to the next controller or action.

* **`STATUS_ERROR` (`1`)**
  The request could not be handled properly.

  * The routing system responds with a **404 View Not Found** error.
  * Use this when processing fails, authentication blocks access, or no valid view is found.

* **`STATUS_SILENCE` (`2`)**
  The request was processed, but nothing was explicitly returned or displayed.

  * The router takes no further action.
  * Useful for background tasks, APIs with no visible output, or cases where an empty response is expected.
  * If nothing is rendered, the user may see a blank page.

---

## Examples

In Luminova, creating a controller is simple: **extend `Luminova\Base\Controller`**.
This gives your controller built-in framework features and ensures your methods follow the correct conventions.

> **Best Practices**:
>   - Keep your controllers thin by delegating business logic to services or models.
>   - Use dependency injection where applicable.
>   - Override `onCreate` for setup tasks specific to your controller.
>   - Place your controllers in the appropriate directory based on the architecture (MVC or HMVC).
>   - Use descriptive module names for better organization.

---

### MVC Controller

A basic MVC controller example:

```php
// /app/Controllers/Http/MainController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;
use Luminova\Attributes\Prefix;
use App\Errors\Controllers\ErrorController;

#[Prefix(pattern: '/(:root)', onError: [ErrorController::class, 'onWebError'])]
class MainController extends Controller 
{
   /**
    * Called during initialization.
    */
   protected function onCreate(): void
   {
      // Optional: initialize data here
   }

   /**
    * Index page
    */
   #[Route('/', methods: ['GET'])]
   public function index(): int 
   {
      return $this->view('index');
      // Loads /resources/Views/index.php
   }

   /**
    * About page
    */
   #[Route('/about', methods: ['GET'])]
   public function about(): int 
   {
      return $this->view('about');
      // Loads /resources/Views/about.php
   }
}
```

---

### HMVC Controllers

HMVC controllers are similar to MVC, but:

* Namespaces differ
* Modules require registration in the application class
* Non-root module controllers must define their module name and controllers namespace
* Each module contains it own template view directory

**Root Module Example:**

```php
// /app/Modules/Controllers/Http/MainController.php

namespace App\Modules\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;
use Luminova\Attributes\Prefix;
use App\Errors\Controllers\ErrorController;

#[Prefix(pattern: '/(:root)', onError: [ErrorController::class, 'onWebError'])]
class MainController extends Controller 
{
   protected function onCreate(): void
   {
      // Optional initialization
   }

   /**
    * Index page
    */
   #[Route('/', methods: ['GET'])]
   public function index(): int 
   {
      return $this->view('index');
      // Loads /app/Modules/Views/index.php
   }

   /**
    * About page
    */
   #[Route('/about', methods: ['GET'])]
   public function about(): int 
   {
      return $this->view('about');
      // Loads /app/Modules/Views/about.php
   }
}
```

**Custom Module Example:**

```php
// /app/Modules/Post/Controllers/Http/PostController.php

namespace App\Modules\Post\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;
use Luminova\Attributes\Prefix;
use App\Errors\Controllers\ErrorController;

#[Prefix(pattern: '/post/(:root)', onError: [ErrorController::class, 'onWebError'])]
class PostController extends Controller 
{
   protected function onCreate(): void
   {
      // Register the module (matches directory name)
      $this->app->view->setModule('Post');

      // Optional: other initialization
   }

   /**
    * Index list all posts
    */
   #[Route('/post', methods: ['GET'])]
   public function posts(): int 
   {
      return $this->view('posts');
      // Loads /app/Modules/Post/Views/posts.php
   }

   /**
    * About post
    */
   #[Route('/post/(:integer)', methods: ['GET'])]
   public function about(int $postId): int 
   {
      return $this->view('post');
      // Loads /app/Modules/Post/Views/post.php
   }
}
```

#### Register Custom Module Namespace

Register namespace for **HMVC Post** module controllers:

```php
// /app/Application.php

namespace App;

class Application extends Luminova\Foundation\Core\Application
{
   protected function onCreate(): void 
   {
      $this->router->addNamespace('\\App\\Modules\\Post\\Controllers\\');
   }
}
```

---

## Template Contents

Example of a simple template file for an index page:

```html
<!-- File: resources/View/index.php -->
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Example</title>
</head>
<body>
    Current Theme: <?= $this->_theme; ?>
</body>
</html>
```

---

## Template Handling

### Rendering a View

Render a template by passing its name **without the `.php` extension**:

```php
#[Route('/', methods: ['GET'])]
public function mainIndex(): int 
{
    return $this->view('index', [
        'theme' => 'dark'
    ], 'html', 200);
}
```

Using the application instance:

```php
#[Route('/', methods: ['GET'])]
public function mainIndex(): int 
{
    return $this->app->view->view('index', 'html')->render([
        'theme' => 'dark'
    ], 200);
}
```

---

### Returning View Content

If you want the rendered content **without sending it directly**, use `respond()`:

```php
#[Route('/', methods: ['GET'])]
public function mainIndex(): int 
{
    $content = $this->respond('index', [
        'theme' => 'dark'
    ], 'html', 200);

    // You can process or log $content here
    return STATUS_SUCCESS;
}
```

Equivalent using the application instance:

```php
#[Route('/', methods: ['GET'])]
public function mainIndex(): int 
{
    $content = $this->app->view->view('index', 'html')->respond([
        'theme' => 'dark'
    ], 200);

    return STATUS_SUCCESS;
}
```

> Useful for sending templates as emails or saving output for later.

---

## Response Handling

### Returning a Response Object

You can return a `Response` object directly:

```php
use Luminova\Template\Response;

#[Route('/foo', methods: ['GET'])]
public function foo(): Luminova\Interface\ViewResponseInterface 
{
   return new Response(200, content: ['data' => 'Hello World!']);

   // Alternative using helper:
   // return Luminova\Funcs\response()->content(['data' => 'Hello World!']);
}
```

### Middleware Example

Return a response from middleware logic:

```php
use Luminova\Template\Response;

#[Route('/(:root)', methods: ['ANY'], middleware: Route::HTTP_BEFORE_MIDDLEWARE)]
public function middleware(): Luminova\Interface\ViewResponseInterface|int
{
   // Assuming session is initialized in "App/Application" class
   if ($this->app->session->isOnline()) {
      return STATUS_SUCCESS;
   }

   return (new Response(401, content: ['error' => 'Access Denied!']))
      ->failed();
   
   // Or return status error
   // return STATUS_ERROR;
}
```

### API Response Example

Customize responses using `Luminova\Template\Response`:

```php
#[Route('/', methods: ['GET'])]
public function mainIndex(Luminova\Template\Response $response): int 
{
   $response->setStatus(200);
   $response->header('Content-Type', 'application/json');

   return $response->json([
      'foo' => 'Bar'
   ]);

   // Alternative using helper:
   // return Luminova\Funcs\response(200)->json(['foo' => 'Bar']);
}
```

> For more on template rendering, see [Template Views](/templates/views.md) documentation.

***

## Class Definition

* Class namespace: `Luminova\Base\Controller`
* This class implements: [Luminova\Interface\RoutableInterface](/interface/classes.md#routableinterface)
* This class is an **Abstract class**

***

## Properties

### request

The instance of HTTP request object (lazy).

```php
protected ?Luminova\Http\Request<Luminova\Interface\LazyObjectInterface> $request = null;
```

For documentation see [HTTP Request Class](/http/request.md)

---

### input

The instance of user-input validation class (lazy).

```php
protected ?Luminova\Security\Validation<Luminova\Interface\LazyObjectInterface> $input = null;
```

For documentation see [Input Validation Class](/security/validation.md)

---

### app

The instance of application class (lazy).

```php
protected ?App\Application<Luminova\Interface\LazyObjectInterface> $app = null;
```

For documentation see [Core Application Class](/foundation/application.md)

---

### view

The instance of template view class (lazy).

```php
public ?Luminova\Template\View<Luminova\Interface\LazyObjectInterface> $view = null;
```

For documentation see [Template View Class](/templates/views.md)

***

## Methods

### view

Render a template within the controller.

This method will render specified template and send the output to browser or system

```php
protected final view(string $template, array $options = [], string $type = View::HTML, int $status = 200): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$template` | **string** | The template file name without the extension (e.g., `index`). |
| `$options` | **array<string,mixed>** | Optional scope data to pass to the template. |
| `$type` | **string** | The content type (default: `View::HTML`). |
| `$status` | **int** | HTTP status code to response (default: `200` OK).. |

**Return Value:**

`int` - Return one of the following status codes:  
   - `STATUS_SUCCESS` if the view is handled successfully,  
   - `STATUS_SILENCE` if failed, silently terminate without error page allowing you to manually handle the state.

**Examples:**

Render view within controller method:

```php
$this->view('template-name', [...], View::HTML, 200);
```

Equivalent to the above example:

```php
$this->app->view->view('template-name', View::HTML)->render([...], 200);
```

> The `view` method serves as a convenient alias or shorthand for rendering views within the controller class.

***

### respond

Render template and return content as a string.

Unlike the `view` method, the `respond` method will render specified template and return the output instead of directly sending to browser or system. 

**View Types:**

Any of these types are supported view type argument for `respond` and `view` method.
   
- `html` - View will render HTML content.
- `json` - View will render JSON content.
- `text` - View will render Plain text content.

See documentation for more information about [Template View Class](/templates/views.md).

```php
protected final respond(string $template, array $options = [], string $type = View::HTML, int $status = 200): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$template` | **string** | The template file name without the extension (e.g., `index`). |
| `$options` | **array<string,mixed>** | Optional scope data to pass to the template. |
| `$type` | **string** | The content type (default: `View::HTML`). |
| `$status` | **int** | HTTP status code to response (default: `200` OK).. |

**Return Value:**

`string` - Return the rendered template content as string.

**Examples:**

Process view and return contents:

```php
$content = $this->respond('view-name', [...], View::HTML, 200);
```

Equivalent to the above example:

```php
$content = $this->app->view->view('view-name', View::HTML)
   ->respond([...], 200);
```

> The `respond` method is also a convenient alias or shorthand for returning view contents within the controller class.
 
***

### onCreate

The `onCreate` method in the controller serves as an alternative to `__construct`. It is invoked after the controller class has completed its initialization via the constructor method. When using this method for initialization, there is no need to explicitly call `parent::__construct()`.

```php
protected onCreate(): void
```

***

### onDestroy

The `onDestroy` method in the controller acts as an alternative to `__destruct`. It is called after the `__destruct` method has completed its execution.

```php
protected onDestroy(): void
```

***

### onMiddlewareFailure

Handles the failure of the `middleware` check in the controller.  
Invoked when the `middleware` method returns `STATUS_ERROR`, this allows you render a custom view or display an error message.

```php
protected onMiddlewareFailure(string $uri, array $metadata): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uri` | **string** | The request URI, useful for logging or triggering an error view. |
| `$metadata` | **array<string,mixed>** | Information about the class where the middleware check failed. |

**Example:**

Render View on Middleware Failure

```php
namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;

class AccountController extends Controller
{
   #[Route('/account/(:root)', methods: ['ANY'], middleware: Route::HTTP_BEFORE_MIDDLEWARE)]
   public function middleware(): int
   {
      return $this->app->session->online() 
         ? STATUS_SUCCESS 
         : STATUS_ERROR;
   }

   protected function onMiddlewareFailure(string $uri, array $metadata): void 
   {
      $this->view('login');
   }
}
```