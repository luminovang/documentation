# Abstract Base Controller for Routable HTTP Controllers

***

## Overview

The BaseController is the starting point for building your own HTTP controllers in Luminova. It helps you handle incoming requests, run your app’s logic, and send responses back to the user.

***

## Introduction

The `BaseController` is the core foundation for building controllers in Luminova, supporting both **MVC** (Model-View-Controller) and **HMVC** (Hierarchical MVC) architectures. It acts as a bridge between the routing system and the template engine, managing the interaction between the application's front-end and back-end layers.

#### Responsibilities of a Controller

- **Handle Requests**: Processes incoming user or system requests.  
- **Execute Application Logic**: Coordinates models, services, and other layers to perform tasks.  
- **Return Responses**: Delivers output, typically via a `view` method or the `response` class.

---

### Key Features

1. **Lazy Loading for Performance**  
   - Core properties such as `$app`, `$request`, and `$validate` are implemented using `Luminova\Utils\LazyObject`.  
   - These are initialized only when accessed, improving performance and reducing memory overhead.

2. **Tight Integration with Routing**  
   - Automatically connects with Luminova’s routing system to map URIs to controller actions.  
   - Offers easy access to common utilities for request handling, validation, and response generation.

3. **Modular and Extensible Architecture**  
   - Easily extend `BaseController` to build custom controllers with pre-integrated features.  
   - Promotes clean, maintainable structure using either traditional MVC or HMVC patterns, ideal for scalable applications.

4. **PHP Route Attributes for Cleaner Definitions**  
   - Define routes using PHP attributes for clarity and reduced boilerplate:
     - `#[Route('/path', methods: ['POST'])]` for routable methods.  
     - `#[Prefix('/prefix', onError: [Error::class, 'handle'])]` for class-level route grouping and error handling.

5. **Flexible Response Handling**  
   - Return custom response types through `Luminova\Template\Response` or the global `response()` helper:
     - `response()->json([...])` — JSON output  
     - `response()->html('<html>')` — HTML content  
     - `response()->xml('<xml>')` — XML responses  
   - Simplifies output formatting to meet varied client expectations.

> By extending `BaseController`, your custom controllers inherit default properties and utility methods that simplify development, enforce consistency, and minimize boilerplate—allowing you to focus on your application's logic.

---

### Routable Method Return Status

In Luminova, controller methods mapped to routes must return an integer that signals the outcome of the request. These return values guide how the routing system responds after method execution:

- **`STATUS_SUCCESS` (`0`)**  
  The request was successfully handled and a valid response was rendered.  
  - Commonly used after rendering views or completing processing steps.  
  - In middleware authentication, this allows the request to proceed to the next controller or action.

- **`STATUS_ERROR` (`1`)**  
  An error occurred during request handling.  
  - Triggers the routing system to return a **404 View Not Found** error.  
  - Typically used when the controller cannot process the request, middleware authentication failure or locate a valid view.

- **`STATUS_SILENCE` (`2`)**  
  The request was processed without any explicit output or error.  
  - No additional routing action is taken.  
  - Useful for background tasks, headless API responses, or scenarios where output is optional.  
  - If nothing is rendered, the response may result in a blank page.

*** 

### Example Usages

To use the `BaseController` class, extend it whenever you create a new controller. This ensures your controller inherits essential features and follows the framework's conventions.

---

#### MVC Controller Implementation

The following example demonstrates a basic implementation of an MVC controller:

```php
// /app/Controllers/Http/MyController.php

namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Route;
use App\Models\User;

class MyController extends BaseController 
{
	// private ?User $user = null;

   /**
    * Called during initialization.
    */
   protected function onCreate(): void
   {
      // Add any setup logic here
      // $this->user = new User();
   }

   /**
    * Example action: Adds a user to the database.
    * 
    * @return int Status of the operation.
    */
	#[Route('/user/add', methods: ['POST'])]
   public function addUser(User $user): int 
   {
      $name = escape($this->request->getPost('name'));
      $added = $user->insert([
         [
            'name' => $name,
            // Add other fields as needed
         ]
      ]);

      return $added ? STATUS_SUCCESS : STATUS_SILENCE;
   }
}
```

---

#### **HMVC Root Module Controller Implementation**

Here's how to implement a root-level HMVC controller:

```php
// /app/Modules/Controllers/Http/MyController.php

namespace App\Modules\Controllers\Http;

use Luminova\Base\BaseController;

class MyController extends BaseController 
{
   /**
    * Called during initialization.
    */
   protected function onCreate(): void
   {
      // Add any setup logic here
   }
}
```

---

#### **HMVC Specific Module Controller Implementation**

Below is an example of a controller specific to an HMVC module:

```php
// /app/Modules/FooModule/Controllers/Http/MyController.php

namespace App\Modules\FooModule\Controllers\Http;

use Luminova\Base\BaseController;

class MyController extends BaseController 
{
   /**
    * Called during initialization.
    */
   protected function onCreate(): void
   {
      // Same as the directory name (app/Modules/FooModule) use `FooModule`
      $this->app->setModule('FooModule');

      // Add any setup logic here
   }
}
```

---

### Notes:
1. **File Organization**:
   - Place your controllers in the appropriate directory based on the architecture (MVC or HMVC).
   - Use descriptive module names for better organization.

2. **Customization**:
   - Override `onCreate` for setup tasks specific to your controller.
   - Add action methods (e.g., `addUser`) as needed to handle application logic.

3. **Best Practices**:
   - Keep your controllers thin by delegating business logic to services or models.
   - Use dependency injection where applicable.

***

### View Content Response

Below is an example of using a template view file to handle an index page.

#### Template File Example:

```html
<!-- File: resources/View/index.php -->
<!DOCTYPE html>
<html lang="en">
<head><title>Index Example</title></head>
<body>
	Current Theme: <?= $this->theme; ?>
</body>
</html>
```

#### Controller Method Example:

```php
#[Route('/', methods: ['GET'])]
public function mainIndex(): int 
{
	return $this->view('index', [
		'theme' => 'dark'
	], 'html', 200);
}
```

Equivalent example using the application instance:

```php
#[Route('/', methods: ['GET'])]
public function mainIndex(): int 
{
	return $this->app->view('index', 'html')->render([
		'theme' => 'dark'
	], 200);
}
```

---

### Returning Rendered View Content

If you need to return the rendered view content instead of sending it directly to the client, you can do the following:

```php
#[Route('/', methods: ['GET'])]
public function mainIndex(): int 
{
	$content = $this->respond('index', [
		'theme' => 'dark'
	], 'html', 200);

	// Process or log the content here if needed
	return STATUS_SUCCESS;
}
```

Equivalent example using the application instance:

```php
#[Route('/', methods: ['GET'])]
public function mainIndex(): int 
{
	$content = $this->app->view('index', 'html')->respond([
		'theme' => 'dark'
	], 200);

	// Process or log the content here if needed
	return STATUS_SUCCESS;
}
```

> This can be useful when you want to send the entire content as an email template.

---

### Custom Response Handling

You can customize responses by interacting with the `Luminova\Template\Response` class:

```php
#[Route('/', methods: ['GET'])]
public function mainIndex(Luminova\Template\Response $response): int 
{
	$response->setStatus(200);
	$response->header('Content-Type', 'application/json');

	return $response->json([
		'foo' => 'Bar'
	]);
}
```

Alternatively, you can use the global helper function for simplicity:

```php
#[Route('/', methods: ['GET'])]
public function mainIndex(): int 
{
	return response(200)->json([
		'foo' => 'Bar'
	]);
}
```

---

### Notes:
1. **Flexibility**:  
   - The `view` method is useful for rendering templates and delivering them to clients.
   - The `respond` method is helpful when you need to manipulate or store the rendered content before returning.

2. **Custom Responses**:  
   - You can easily craft JSON, HTML, XML, or other types of responses using the `Response` class or helper.

3. **Integration**:  
   - These methods align with Luminova's routing and response systems, ensuring a consistent and streamlined approach to handling client requests.

***

## Class Definition

* Class namespace: `\Luminova\Base\BaseController`
* This class implements: [\Luminova\Interface\RoutableInterface](/interface/classes.md#routableinterface)
* This class is an **Abstract class**

***

## Properties

Access to HTTP request object.

```php
protected ?Luminova\Http\Request<Luminova\Interface\LazyInterfac> $request = null;
```

Access to input validation object.

```php
protected ?Luminova\Security\Validation<Luminova\Interface\LazyInterfac> $validate = null;
```

Access to application object.

```php
protected ?App\Application<Luminova\Interface\LazyInterfac> $app = null;
```

***

## Methods

### view

The `view` method serves as a convenient alias or shorthand for rendering views within the controller class.

```php
protected final view(string $view, array $options = [], string $type = self::HTML, int $status = 200): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string** | The view file name without extension type. |
| `$options` | **array<string,mixed>** | Optional options to be passed to view template. |
| `$type` | **string** |  The view content extension type (default: `html`). |
| `$status` | **int** | HTTP status code to response (default: 200 OK).. |

**Return Value:**

`int` - Return one of the following status codes:  
   - `STATUS_SUCCESS` if the view is handled successfully,  
   - `STATUS_SILENCE` if failed, silently terminate without error page allowing you to manually handle the state.

**Examples:**

Render view within controller method:

```php
$this->view('view-name', [...], self::HTML, 200);
```

Equivalent to the above example:

```php
$this->app->view('view-name', self::HTML)->render([...], 200);
```

***

### respond

The `respond` method is also a convenient alias or shorthand for returning view contents within the controller class. 

**View Types:**

Any of these types are supported view type argument for `respond` and `view` method.
   
- `html` - View will render HTML content.
- `json` - View will render JSON content.
- `text` - View will render Plain text content.
- `xml`  - View will render XML content.
- `js`   - View will render JavaScript content.
- `css`  - View will render  CSS content.
- `rdf`  - View will render RDF content.
- `atom` - View will render Atom content.
- `rss`  - View will render  RSS feed content.

```php
protected final respond(string $view, array $options = [], string $type = 'html', int $status = 200): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string** | The view file name without extension type. |
| `$options` | **array<string,mixed>** | Optional options to be passed to view template. |
| `$type` | **string** |  The view content extension type (default: `html`). |
| `$status` | **int** | HTTP status code to response (default: 200 OK).. |

**Return Value:**

`string` - Return view contents which is ready to be rendered.

**Examples:**

Process view and return contents:

```php
$content = $this->respond('view-name', [...], 'html', 200);
```

Equivalent to the above example:

```php
$content = $this->app->view('view-name', 'html')->respond([...], 200);
```
 
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
protected onMiddlewareFailure(string $uri, array $classInfo): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uri` | **string** | The request URI, useful for logging or triggering an error view. |
| `$classInfo` | **array<string,mixed>** | Information about the class where the middleware check failed. |

**Example:**

Render View on Middleware Failure

```php
namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Route;

class AccountController extends BaseController
{
   #[Route('/account/(:root)', methods: ['ANY'], middleware: Route::BEFORE_MIDDLEWARE)]
   public function middleware(): int
   {
      return $this->app->session->online() ? STATUS_SUCCESS : STATUS_ERROR;
   }

   protected function onMiddlewareFailure(string $uri, array $classInfo): void 
   {
      $this->view('login');
   }
}
```