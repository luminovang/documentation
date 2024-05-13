## Base Controller

***

## Overview

Base Controller provides a robust foundation for backend operations in Luminova&#039;s framework, offering efficient request handling, validation initialization, and backend view rendering capabilities.

***

Base Controller serves as a fundamental component of Luminova's MVC, designed to extend backend operations within your applications. It shares similarities with the **Base View Controller** but differs in its approach to request handling and validation initialization, making it particularly suitable for backend view rendering like as development.

## Features

1. **Request Handling** The Base Controller initializes request handling functionality, providing seamless access to request data, such as query parameters, request headers, and body content. This ensures efficient processing of incoming requests.

2. **Validation Initialization** Unlike the Base View Controller, which focuses on view rendering and presentation logic, the Base Controller includes built-in initialization for request validation. This enables developers to validate incoming data against predefined rules, ensuring data integrity and security in backend operations.

3. **Compatibility** While primarily designed for backend operations, the Base Controller remains compatible with frontend applications, offering a seamless integration between frontend and backend components. This enables developers to build end-to-end solutions with consistency and ease.

***

* Class namespace: `\Luminova\Base\BaseController`
* Parent class: [\Luminova\Base\BaseViewController](/base/view.md)
* This class is an **Abstract class**

***

### Example usages

Makes sure to extend it whenever you want create a new controller class.
Here is an example of controller should look like.

```php
<?php 
namespace App\Controllers;
use \luminova\Base\BaseController;

class BackendController extends BaseController 
{
    /** @var \ Luminova\Http\Request $this->request */
    /** @var \ Luminova\Application $this->app */
    /** @var \Luminova\Security\InputValidator $this->validate */
		
	public function __construct(){
        parent::__construct();
	}
	
	public function addUser(): int 
	{
		$name = $this->request->getPost('name');
		//...
		return STATUS_SUCCESS;
	}
}
```

***

## Properties

HTTP request object

```php
protected ?\Luminova\Http\Request $request
```

Input validation object

```php
protected ?\Luminova\Security\InputValidator $validate
```

Application instance

```php
protected ?\App\Controllers\Application $app
```

Importer instance

```php
protected ?\Luminova\Library\Importer $library
```

***

## Methods

### request

Initializes the HTTP request class instance

```php
protected request(): \Luminova\Http\Request
```

**Return Value:**

`Request` - HTTP request object

***

### validate

Initializes the input validator class instance.

```php
protected validate(): \Luminova\Security\InputValidator
```

**Return Value:**

`InputValidator` - input validation object

***

### app

Initializes the application class instance.

```php
protected app(): \App\Controllers\Application
```

**Return Value:**

`Application` - Application instance

***

### library

Initializes the application class instance.

```php
protected library(): \Luminova\Library\Importer
```

**Return Value:**

`Importer` Application instance

***

### view

Shorthand to render view in controller class.

```php
protected view(string $view, array $options = []): int
```

**Return Value:**

`int` Return STATUS_SUCCESS on success, otherwise STATUS_ERROR failure.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string** | The view name to render. |
| `$options` | **array** | Optional options to be passed to view template. |

***

### respond

Shorthand to respond view contents in controller class.

```php
protected respond(string $view, array $options = []): string
```

**Return Value:**

`string` Return view contents which is ready to be rendered.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string** | The view name to render. |
| `$options` | **array** | Optional options to be passed to view template. |

***

### onCreate

Controller onCreate method an alternative to **__construct**.
When you use this method for initialization it eliminate the need of calling `parent::__construct()`.

```php
protected onCreate(): void
```

***

### onDestroy

The controller onDestroy method is an alternative to __distruct

```php
protected onDestroy(): void
```