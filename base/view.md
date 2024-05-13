## Base View Controller

***

## Overview

Base View Controller Simplifies view management in Luminov&#039;s Framework by avoiding initialization of unnecessary classes except when it needed.

***

Base View Controller is a fundamental component of Luminov's MVC, designed to organize view management within your applications. As a core class, it offers a structured approach for handling views, providing a solid foundation for building interactive and optimized web pages.

### Features

1. **View Rendering:** HTML, JSON, or other formats, the controller ensures easy rendering.

2. **Templating Support:** Integrate template engines into your application.

3. **Layout Management:** Support for layout management, define reusable layout templates, organize view components, and apply consistent styling across multiple views

4. **Data Binding:** Bind data to views, dynamic content generation and manipulation.

***

* Class namespace: `\Luminova\Base\BaseViewController`
* This class is an **Abstract class**

***

### Usages

Makes sure to extend `BaseViewController` whenever you want create a new controller class to manage web pages.

Here is an example of controller should look like.

```php
<?php 
namespace App\Controllers;
use \luminova\Base\BaseViewController;

class BackendController extends BaseViewController 
{
	public function __construct(){
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

Access the HTTP request object.

```php
protected \Luminova\Http\Request $request
```

Access user Input validation object.

```php
protected \Luminova\Security\InputValidator $validate
```

Access your application instance.

```php
protected \App\Controllers\Application $app
```

Access library Importer instance

```php
protected \Luminova\Library\Importer $library
```

> *Note:*  In `BaseViewController`, the `$library`, `$validate` or `$request` object is not initialized by default.
> To use any of it you must first initialized it in `__contruct` or `onCreate` method by calling the proper method to create it e.g `$this->request()` 

***

## Methods

### request

Initializes the HTTP request class instance.

```php
protected request(): \Luminova\Http\Request
```

**Return Value:**

`Request` - HTTP request class instance

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

`Importer` - Application instance

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

Controller onCreate method an alternative to __construct

```php
protected onCreate(): void
```

***

### onDestroy

Controller onDestroy method an alternative to __distruct

```php
protected onDestroy(): void
```