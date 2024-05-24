# Base View Controller

***

## Overview

Base View Controller Simplifies view management in Luminov's Framework by avoiding initialization of unnecessary classes except when it needed.

***

## Introduction

The `BaseViewController` is the fundamental component of Luminova's MVC component, serving as an important intermediary for managing view operations. This core class offers a structured approach to accepts instructions from the router and transmitting the relevant information to the template class for rendering views.

It establishes a solid foundation for building interactive and optimized web applications, well-suited for client-side rendering. While its primary focus is not limited to frontend operations, it also suitable for backend applications, ensuring consistency between frontend and backend functionalities.

***

* Class namespace: `\Luminova\Base\BaseViewController`
* This class is an **Abstract class**

***

### Usages

To use `BaseViewController` class, you will need to extend it whenever you want create a new view controller class.

Below is an example of how a basic view controller class may look like, in this example we use `__construct`, optionally you can use `onCreate` method instead.

```php
<?php 
namespace App\Controllers;
use \luminova\Base\BaseViewController;

class BackendController extends BaseViewController 
{
	public function __construct()
    {
        parent::__construct();
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
protected ?\Luminova\Security\InputValidator $validate = null;
```

Access to application object.

```php
protected ?\App\Controllers\Application $app = null;
```

> *Note:*  In `BaseViewController`, the `$validate` or `$request` object is not initialized by default.
> To use access it properties you must first initialized it in `__construct` or `onCreate` method by calling the proper method to create it e.g `$this->request()`.

***

## Methods

### request

Initializes the HTTP request class instance

```php
protected final request(): \Luminova\Http\Request
```

**Return Value:**

`Request` - Return request class instance.

***

### validate

Initializes the input validation class instance.

```php
protected final validate(): \Luminova\Security\InputValidator
```

**Return Value:**

`InputValidator` - Return input validation class instance.

***

### app

Initializes the application class instance.

```php
protected final app(): \App\Controllers\Application
```

**Return Value:**

`Application` - Return application class instance.

***

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
| `$view` | **string** | The view name to render. |
| `$options` | **array** | Optional options to be passed to view template. |
| `$type` | **string** | The type of view content you are compiling (default: `html`). |

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
| `$view` | **string** | The view name to respond with. |
| `$options` | **array** | Optional options to be passed to view template. |
| `$type` | **string** | The type of view content you are compiling (default: `html`). |

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