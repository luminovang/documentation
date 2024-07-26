# Base Controller

***

## Overview

Base Controller provides a robust foundation for backend operations in Luminova's framework, offering efficient request handling, validation initialization, and backend view rendering capabilities.

***

## Introduction

The `BaseController` is a core element of Luminova's MVC architecture, functioning as an intermediary between the router and template engines. It suitable for handling backend operations by receiving instructions from the router and conveying them to the template for processing. While similar to the `BaseViewController`, but differs in its approach to request handling and validation. It initializes the class on the go, making easy access to validation user inputs and interact with request objects without requiring class initialization beforehand.

While primarily designed for `backend` operations, the `BaseController` remains compatible with `frontend` applications, offering a seamless integration between `frontend` and `backend` components. 

***

* Class namespace: `\Luminova\Base\BaseController`
* This class is an **Abstract class**

***

### Example usages

In other to use `BaseController` class, you will need to extend it whenever you want create a new controller class.

Below is an example of how a basic controller class should look like.

```php
<?php 
namespace App\Controllers;
use \luminova\Base\BaseController;

class BackendController extends BaseController 
{
	public function __construct()
    {
        parent::__construct();
	}
	
	public function addUser(): int 
	{
		$name = escape($this->request->getPost('name'));
		$added = $builder->table('users')->insert([
			[
				'name' => $name,
				//...
			]
		]);
		return $added ? STATUS_SUCCESS : STATUS_ERROR;
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
protected final validate(): \Luminova\Security\Validation
```

**Return Value:**

`Validation` - Return input validation class instance.

***

### app

Initializes the application class instance.

```php
protected final app(): \App\Application
```

**Return Value:**

`Application` - Return application class instance.

***

### view

The `view` method serves as a convenient alias or shorthand for rendering views within the controller class. It is equivalent to `$this->app->view('view_file')->render()`.

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

The `respond` method is also a convenient alias or shorthand for returning view contents within the controller class. It is equivalent to `$this->app->view('view_file')->respond()`.

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