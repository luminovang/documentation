# Base Class for HTTP Routing View Controllers

***

## Overview

Base View Controller Simplifies view management in Luminov's Framework by avoiding initialization of unnecessary classes except when it needed.

***

## Introduction

The `BaseViewController` is the fundamental component of Luminova's MVC component, serving as an important intermediary for managing view operations. This core class offers a structured approach to accepts instructions from the router and transmitting the relevant information to the template class for rendering views.

It establishes a solid foundation for building interactive and optimized web applications, well-suited for client-side rendering. While its primary focus is not limited to frontend operations, it also suitable for backend applications, ensuring consistency between frontend and backend functionalities.

> **Deprecated Notice**  `BaseViewController` is **deprecated** as of version **3.4.0**. It has been replaced by `Luminova\Base\BaseController`. The `BaseViewController` no longer serves its intended purpose and is scheduled for removal in a future release. Developers are encouraged to update their code to use the `BaseController` for continued support and functionality.

***

* Class namespace: `\Luminova\Base\BaseViewController`
* This class is an **Abstract class**

***

### Usages

To use `BaseViewController` class, you will need to extend it whenever you want create a new view controller class.

Below is an example of how a basic view controller class may look like, in this example we use `__construct`, optionally you can use `onCreate` method instead.

```php
// app/Controllers/Http/FrontendController.php
<?php 
namespace App\Controllers\Http;

use \Luminova\Base\BaseViewController;

class FrontendController extends BaseViewController 
{
	protected function onCreate(): void
    {
        // Initialization implementation
	}
	
	public function user(): int 
	{
		return $this->view('user', [
		    'foo' => 'bar'
		]);
	}
}
```

***

## Properties

Access to HTTP request object.

```php
protected ?\Luminova\Http\Request $request = null;
```

Access to input validation object.

```php
protected ?\Luminova\Security\Validation $validate = null;
```

Access to application object.

```php
protected ?\App\Application $app = null;
```

> *Note:*  In `BaseViewController`, the `$validate` or `$request` object is not initialized by default.
> To use access it properties you must first initialized it in `__construct` or `onCreate` method by calling the proper method to create it e.g `$this->request()`.

***

## Methods

### view

The `view` method serves as a convenient alias or shorthand for rendering views within the `ViewController` class. It is equivalent to `$this->app->view('view_file')->render()`.

```php
protected final view(string $view, array $options = [], string $type = 'html'): int
```

**Return Value:**

`int` - Return `STATUS_SUCCESS` on success, otherwise `STATUS_ERROR` failure.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string** | The view file name without extension type. |
| `$options` | **array<string,mixed>** | Optional options to be passed to view template. |
| `$type` | **string** | The view content extension type (default: `html`). |

***

### respond

The `respond` method is also a convenient alias or shorthand for returning view contents within the `ViewController` class. It is equivalent to `$this->app->view('view_file')->respond()`.

```php
protected final respond(string $view, array $options = [], string $type = 'html'): string
```

**Return Value:**

`string` - Return view contents which is ready to be rendered.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string** | The view file name without extension type. |
| `$options` | **array<string,mixed>** | Optional options to be passed to view template. |
| `$type` | **string** | The view content extension type (default: `html`). |

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

The `onCreate` method serves as an alternative to `__construct`. It is invoked after the controller class has completed its initialization via the constructor method. When using this method for initialization, there is no need to explicitly call `parent::__construct()`.

```php
protected onCreate(): void
```

***

### onDestroy

The `onDestroy` method acts as an alternative to `__destruct`. It is called after the `__destruct` method has completed its execution.

```php
protected onDestroy(): void
```