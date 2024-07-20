# Routing Context

***

## Overview

The routing context class allows you to define a route URL prefix for capturing specific types of URL requests.

***

## Introduction

The **routing context** in the Luminova framework is a powerful tool for managing application routes. It allows you to organize routes by their URL prefixes, improving performance by loading only the relevant context file based on the request URL. This method not only enhances performance but also helps maintain a clean and organized codebase by promoting separation of concerns.

The context configuration, offers better readability and clarity, each context is explicitly defined with its type and associated error handler, this makes it easier to understand the routing configuration at a glance. 

***

### Adding Context

You can configure routing contexts in two ways: using the `Context` class object or an `Array`.
To add a new context, you need to configure it in your `/public/index.php` file. 

**Using the Context Object**

To add a context using the `Context` object. This setup automatically routes all `URLs` with the specified prefix to the corresponding file in `/routes/`. For example, `URLs` starting with `api` will be handled by `/routes/api.php`.

The Context instance requires two arguments:

- `name` (string) - Specifies the route URL prefix.
- `onError` (\Closure|array<int,string>|null) - Defines the handler for 404 errors.

```php
/public/index.php
<?php
use \Luminova\Boot;
use \Luminova\Routing\Context;
use \App\Controller\Config\ViewErrors;
//...

Boot::http()->router->context(
    new Context('admin', [ViewErrors::class, 'onAdminError']),
	//...
)->run();
```

**Using Array Configuration**

Alternatively, you can use an associative array to define contexts. This method is useful for managing performance when dealing with a large number of routes. Initializing context object on application runtime may impact performance of your application.

Each prefix argument should be defined with an associative array containing two keys: `prefix` and `error`. For example:

- `prefix` (string) - The URI prefix name.
- `error` (Closure|array|null) - The URI context 404 error handler.

```php
/public/index.php
<?php
use \Luminova\Boot;
use \Luminova\Routing\Context;
use \App\Controller\Config\ViewErrors;
//...

Boot::http()->router->context(
	  [
        'prefix' => 'web', 
        'error' => [ViewErrors::class, 'onWebError']
    ],
		[
        'prefix' => 'api', 
        'error' => [ViewErrors::class, 'onApiError']
    ],
		[
        'prefix' => 'cli'
    ],
	//...
)->run();
```

This configuration ensures that URLs starting with `api` are routed correctly and errors are handled by the specified method.

> **Note:** 
> For both `Context` object and `Array` configuration, ensure that the context name matches the file name in the `/routes/` directory for proper routing.
> 
> You will have to create the method `onApiError` in your `ViewErrors` class.

***

After adding the new context to router `context` method in your `public/index.php`, create the corresponding handler file in the `/routes/` directory with the same name as the context URI prefix name. 

For instance, if you added a context prefix for `admin` in your `index.php`, create a file name `admin.php` in the routes directory.

```php
/routes/admin.php
<?php 
$router->get('/', 'AdminController::login');
$router->get('/dashboard', 'AdminController::dashboard');
```

> Now whenever you visit `https://example.com/admin`, `https://example.com/admin/foo`, or `https://localhost/example.com/public/admin`, your administrator page will be loaded only.

***

### Subdirectory

Setting a subdirectory for your context helps organize your application view files, ensuring each context has its own view folder. 

To achieve this, follow these steps.

1. Create a sub-folder in your `/resources/views/` directory for each context. For example, create an `admin` folder: `/resources/views/admin/`.

2. Instruct the framework to look for your view files in the corresponding subdirectory. This can be done by calling the `setFolder` method of the application template in your context file (`/routes/admin.php`), before rendering view, or within your view controller class.

By doing this, you ensure that your application's view files are neatly organized and easily manageable within their respective contexts.

**Within your controller class**

```php
<?php
class MyController extends BaseController
{
	protected onCreate(): void 
	{
		$this->app->setFolder('admin');
	}
}
```

**In context file**

```php 
<?php 
//....
$app->setFolder('admin');

//...
```

**Setting for a particular view**

```php 
<?php 
$router->get('/', function(Router $router, BaseApplication $app){
	$app->setFolder('admin');
	$router->view('index')->render();
});

//...
```

***

## Context Class

* Class namespace: `\Luminova\Routing\Context`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

***

## Constants

**Predefined Context Names**

Here are predefined context names that you might want to use:

- `web`: Default context for handling web page requests.
- `api`: Default context for handling API requests.
- `cli`: Default context for handling command-line interface (CLI) requests.

The `web`, `api`, and `cli` contexts are installed by default, so you don't need to add them manually. If you prefer not to use the `api` and `cli` contexts, you can remove them from your `index.php` file and delete the corresponding files in `/routes/`.

| Constant | Visibility | Type | Value |
|:---------|:-----------|:-----|:------|
|`WEB`|public|string|'web'|
|`API`|public|string|'api'|
|`CLI`|public|string|'cli'|
|`CONSOLE`|public|string|'console'|
|`WEBHOOK`|public|string|'webhook'|

> The `web` context name cannot be changed. The framework uses it to identify any URL request that doesn't have a registered prefix context in your application, such as `assets` and other URL requests.

***

## Methods

### constructor

Initialize context class constructor with required `URL` prefix name.

```php
new Context(string $name, \Closure|array|null $onError = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | Route context name |
| `$onError` | **\Closure&#124;array<int,string>&#124;null** | Optional error callback function to execute. |

> Error handler supports any callable, or list array with two elements, the first element should be class name while the second is method name.