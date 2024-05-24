# Routing Context

***

## Overview

The routing context class allows you to define a route URL prefix for capturing specific types of URL requests.

***

## Introduction

The routing context is a powerful feature in the Luminova framework for managing your application's routing. It enables you to specify route URL prefixes and handle them in separate contexts, promoting separation of concerns.

By adding a new context in your `/public/index.php` and creating the corresponding routes file in `/routes/`, the framework automatically directs all URL prefixes starting with the context name to the relevant handler in `/routes/my-context.php`. This approach optimizes performance by loading only the necessary routes.

You can set up contexts in two ways: using an `Array` for configuration or employing the `Context` class object. This flexibility allows you to choose the method that best fits your application's complexity and your coding style preferences.

***

### Examples

In your `index.php` located at `public/index.php`, and add a new argument to `$app->router->context()` method, representing a new context URI prefix.

Using The `Context` class instance.

```php
/public/index.php
<?php
use \Luminova\Boot;
use \Luminova\Routing\Context;
use App\Controller\Config\ViewErrors;
//...

Boot::http()->router->context(
    new Context('admin', [ViewErrors::class, 'onAdminError']),
	//...
)->run();
```

Using The `Array` to setup.
The array setup expect only two elements as show below.

`prefix` (string) - The URI prefix name.
`error` (Closure|array|null) - The URI context 404 error handler.

```php
/public/index.php
<?php
use \Luminova\Boot;
use \Luminova\Routing\Context;
use App\Controller\Config\ViewErrors;
//...

Boot::http()->router->context(
	[
        'prefix' => 'admin', 
        'error' => [ViewErrors::class, 'onAdminError']
    ],
	//...
)->run();
```

> *Note:* 
> You will have to create the method `onAdminError` in your `ViewErrors` class.

***

After adding the new context to router `context` method in your `public/index.php`, create the corresponding handler file in the `/routes/` directory with the same name as the context URI prefix. 
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

The `Context` class, offers better readability and clarity, each context is explicitly defined with its type and associated error handler. 
This makes it easier to understand the routing configuration at a glance. 

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