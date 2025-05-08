# Method-Based URI Prefixing for Route Context

***

## Overview

The routing context class allows you to define a route URL prefix for capturing specific types of URL requests.

***

## Introduction

The url routing **Prefix** in the Luminova framework is a powerful tool for managing application routes. 
It allows you to organize routes by their URL prefixes, improving performance by loading only the relevant route context file based on the request URL prefix. 
This method not only enhances performance but also helps maintain a clean and organized codebase by promoting separation of concerns.

The prefix configuration offers readability and clarity, each context is explicitly defined with its `URL` prefix name and associated error handler, this makes it easier to understand the routing configuration at a glance. 

***

### Adding Prefix

You can configure routing prefix in two ways: using the `Prefix` class object or an `Array`.
To add a new prefix, you need to define it in your `/public/index.php` file withing the router `context` method arguments. 

**Using the Prefix Object**

To add a context using the `Prefix` object. 
This setup automatically routes all `URLs` with the specified prefix name to the corresponding file in `/routes/`. 
For example, `URLs` starting with `api` will be handled by `/routes/api.php`.

The Prefix instance requires two arguments:

- `name` (string) - Specifies the route URL prefix.
- `onError` (\Closure|array<int,string>|null) - Defines the handler for 404 errors.

```php
// /public/index.php

use \Luminova\Boot;
use \Luminova\Routing\Prefix;
use \App\Controller\Config\ViewErrors;
//...

Boot::http()->router->context(
    new Prefix('admin', [ViewErrors::class, 'onAdminError']),
	//...
)->run();
```

**Using Array Configuration**

Alternatively, you can use an associative array to define prefixes. 
This method is useful for managing performance when dealing with a large number of routes. 
Initializing prefix object on application runtime may impact performance of your application if more prefix are in use.

Each prefix argument should be defined with an associative array containing two keys: `prefix` and `error`. For example:

- `prefix` (string) - The URI prefix name.
- `error` (Closure|array|null) - The URI context 404 error handler.

```php
// /public/index.php

use \Luminova\Boot;
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
> For both `Prefix` object and `Array` configuration, ensure that the prefix name matches the file name in the `/routes/` directory for proper routing.
> 
> Additionally, you will have to create the error handler method `onApiError` in your `ViewErrors` class or create a custom class.

***

After adding the new route `prefix` in your `public/index.php`, create the corresponding handler file in the `/routes/` directory with the same name as the URI prefix name. 

For instance, if you added a prefix for `admin` in your `index.php`, create a file name `admin.php` in the routes directory.

```php
// /routes/admin.php
<?php 
$router->get('/', 'AdminController::login');
$router->get('/dashboard', 'AdminController::dashboard');
```

> Now whenever you visit `https://example.com/admin`, `https://example.com/admin/foo`, or `https://localhost/example.com/public/admin`, your administrator page will be loaded only.

***

### Subdirectory

Setting a subdirectory for your prefix helps organize your application view files, ensuring each prefix context has its own view folder. 

To achieve this, follow these steps.

1. Create a sub-folder in your `/resources/Views/` directory for each context. For example, create an `admin` folder: `/resources/Views/admin/`.

2. Instruct the framework to look for your view files in the corresponding subdirectory. 
This can be done by calling the `setFolder` method of the application template view in your controller class `onCreate` or within the file `/routes/admin.php`, before rendering view.

By doing this, you ensure that your application's view files are neatly organized and easily manageable within their respective prefix name.

**Within your controller class**

```php
// /app/Controllers/AdminController.php

<?php
namespace App\Controllers;

use Luminova\Base\BaseController;
class AdminController extends BaseController
{
	protected onCreate(): void 
	{
		$this->app->setFolder('admin');
	}
}
```

**In context file**

```php 
// /routes/admin.php

<?php 
//....
$app->setFolder('admin');

//...
```

**Setting for a particular view**

This can be done before rendering view, withing your controller class method or using closure.

```php 
// /routes/admin.php
<?php 
$router->get('/', function(Router $router, CoreApplication $app){
	$app->setFolder('admin');
	$router->view('index')->render();
});

//...
```

***

## Class Definition

* Class namespace: `\Luminova\Routing\Prefix`
* This class is marked as **final** and can't be subclassed

***

## Constants

**Predefined Prefix Names**

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

Initialize constructor to register a router prefix.

```php
new Prefix(string $name, \Closure|array|null $onError = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The route URL prefix name (e.g, `blog`). |
| `$onError` | **\Closure&#124;array<int,string>&#124;null** | Optional prefix context error handler. |

> Error handler supports any callable, or list array with two elements, the first element should be class name while the second is method name.