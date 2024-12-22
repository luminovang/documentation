# Base Controller for HTTP Routing Controller Classes

***

## Overview

Base Controller provides a foundation for controller class operations in the Luminova framework. Offering efficient request handling, input validation, response, and view rendering capabilities.

***

## Introduction

The `BaseController` serves as a foundational component in Luminova's **MVC** (Model-View-Controller) and **HMVC** (Hierarchical Model-View-Controller) architectures. It acts as the core interface between the application's routing system and its template engine, orchestrating communication between the front-end and back-end layers of your application.

In essence, a controller is responsible for:

- **Handling Requests**: It processes incoming requests from users or systems.
- **Executing Business Logic**: It coordinates interactions between models, services, and other layers to perform specific tasks.
- **Returning Responses**: It prepares and sends data back to the user or system, often through a `view` method or `response` class.

When extended, the `BaseController` equips your custom controllers with default properties and utilities to enhance development. This ensures consistency and reduces boilerplate code, allowing you to focus on your application's logic.

---

### Key Features

1. **Lazy Loading for Efficiency**:
   - Properties like `$app`, `$request`, and `$validate` are implemented as `Luminova\Utils\LazyObject` instances.  
   - These properties are only initialized when accessed, optimizing memory usage and performance.

2. **Seamless Integration**:
   - Automatically integrates with Luminova's routing system to map URIs to controller actions.
   - Provides access to common utilities and helpers needed for request handling, validation, and response generation.

3. **Extensibility**:
   - Developers can extend the `BaseController` to create custom controllers, inheriting built-in features while adding application-specific logic.
   - Offers a clean structure for organizing application logic in a modular way, whether using a traditional MVC approach or the HMVC pattern for larger projects.

4. **PHP Route Attributes**:  
   Simplify route definition using PHP attributes, such as `#[Route('/route/path', methods: ['POST'])]` for individual routes, and `#[Prefix('/url-prefix', onError: [Error::class, 'errorMethod'])]` for class-level prefixes or error handling. This feature enhances readability and reduces boilerplate code.

5. **Custom Responses**:  
   Return various response types effortlessly using the `Luminova\Template\Response` class or the global helper function `response`. Supported response types include:  
   - `response()->json([...])` for JSON responses.  
   - `response()->html('<html>')` for HTML output.  
   - `response()->xml('<xml>')` for XML responses.  
   These options provide flexibility in responses to client requirements.

---

### Default Properties

The `BaseController` comes with these pre-configured properties:

| **Property**     | **Description**                                                                                     |
|-------------------|-----------------------------------------------------------------------------------------------------|
| `$this->app`      | Provides access to the application instance which also extends View template class, enabling interaction with services, settings, and state.|
| `$this->request`  | Handles HTTP request data, including methods for retrieving query parameters, POST data, Cookie  etc.       |
| `$this->validate` | Facilitates validation of input data, ensuring your application operates on clean and reliable data. |

These properties are loaded only when needed, ensuring optimal resource usage.

---

### Why Using BaseController?

- **Simplifies Development**: Reduces the effort required to set up controllers by providing essential tools out of the box.
- **Promotes Best Practices**: Encourages separation of concerns by keeping business logic separate from routing and views.
- **Scales with Your Application**: Supports both traditional MVC and HMVC patterns, making it suitable for projects of all sizes.

By leveraging `BaseController`, developers can focus on building features while maintaining a clean and maintainable architecture.

*** 

### Example Usages

To use the `BaseController` class, extend it whenever you create a new controller. This ensures your controller inherits essential features and follows the framework's conventions.

---

#### **MVC Controller Implementation**

The following example demonstrates a basic implementation of an MVC controller:

```php
// /app/Controllers/Http/MyController.php

<?php 
namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Route;
use App\Models\User;

class MyController extends BaseController 
{
	//private ?User $user = null;

    /**
     * Called during initialization.
     */
    protected function onCreate(): void
    {
        // Add any setup logic here
		//$this->user = new User();
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

        return $added ? STATUS_SUCCESS : STATUS_ERROR;
    }
}
```

---

#### **HMVC Root Module Controller Implementation**

Here’s how to implement a root-level HMVC controller:

```php
// /app/Modules/Controllers/Http/MyController.php

<?php 
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

<?php 
namespace App\Modules\FooModule\Controllers\Http;

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
   - These methods align with Luminova’s routing and response systems, ensuring a consistent and streamlined approach to handling client requests.

***

## Class Definition

* Class namespace: `\Luminova\Base\BaseController`
* This class is an **Abstract class**

***

## Properties

Access to HTTP request object.

```php
protected ?Luminova\Http\Request<Luminova\Interface\LazyInterface> $request = null;
```

Access to input validation object.

```php
protected ?Luminova\Security\Validation<Luminova\Interface\LazyInterface> $validate = null;
```

Access to application object.

```php
protected \App\Application|Luminova\Utils\LazyObject|null $app = null;
```

***

## Methods

### view

The `view` method serves as a convenient alias or shorthand for rendering views within the controller class. It is equivalent to `$this->app->view('view', 'type')->render()`.

```php
protected final view(string $view, array $options = [], string $type = 'html', int $status = 200): int
```

**Return Value:**

`int` - Return `STATUS_SUCCESS` on success, otherwise `STATUS_ERROR` failure.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string** | The view file name without extension type. |
| `$options` | **array<string,mixed>** | Optional options to be passed to view template. |
| `$type` | **string** |  The view content extension type (default: `html`). |
| `$status` | **int** | HTTP status code to response (default: 200 OK).. |

***

### respond

The `respond` method is also a convenient alias or shorthand for returning view contents within the controller class. It is equivalent to `$this->app->view('view', 'type')->respond()`.

```php
protected final respond(string $view, array $options = [], string $type = 'html', int $status = 200): string
```

**Return Value:**

`string` - Return view contents which is ready to be rendered.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string** | The view file name without extension type. |
| `$options` | **array<string,mixed>** | Optional options to be passed to view template. |
| `$type` | **string** |  The view content extension type (default: `html`). |
| `$status` | **int** | HTTP status code to response (default: 200 OK).. |

***

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