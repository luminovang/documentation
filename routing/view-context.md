# Routing Context

***

## Overview

The routing context class allows you to define a route URL prefix for capturing specific types of URL requests.

***

## Introduction

The routing context class enables you to specify a route URL prefix for capture. By adding a new context to your `/public/index.php` and creating the context routes file in `/routes/`, the framework automatically directs all URL prefixes starting with the context name to the corresponding handler in `/routes/my-context.php`. This optimizes performance by loading only the necessary routes.

**Example Usages**

In your `index.php` located at `public/index.php`, and add a new argument to `$app->router->bootstraps()` method, representing a new context URI prefix.

```php
<?php
use App\Controller\Config\ViewErrors;
//...

$app->router->bootstraps($app,
      new Bootstrap('admin', [ViewErrors::class, 'onAdminError']),
	//...
);

$app->router->run();
```

> *Note:* You will need to create the method `onAdminError` in your `ViewErrors` class.

***

After adding the new argument to the `$app->router->bootstraps()` method in your `index.php` located at `public/index.php`, create a handler file in the `/routes/` directory with the same name as the context URI prefix. For instance, if you added a context called `admin` in your index.php, create a file named `admin.php` in the routes directory.

```php
<?php 
/** @global \Luminova\Routing\Router $router */
/** @global \App\Controllers\Application $app */

$router->get('/', 'AdminController::login');
$router->get('/dashboard', 'AdminController::dashboard');
```

> Whenever you visit `https://example.com/admin`, `https://example.com/admin/*`, or `https://localhost/example.com/public/admin`, your admin page will be loaded.

**Setting a Subdirectory for Admin Views**

In many cases, you might want to organize your application views so that each context has a folder to contain all its view files. To achieve this, simply create the subfolder in your `/resources/views/` directory. For example, create `/resources/views/admin/`.

Next, instruct the framework to look for your view files in the `/resources/views/admin` directory of your resources by adding the `setFolder()` method in your context file `/routes/admin.php` before registering routes or before rendering views.

```php 
<?php 
//....
$app->setFolder('admin');

//...
```

**Setting for a particular page**

```php 
<?php 
$router->get('/', function() use($app, $router){
	$app->setFolder('admin');
	$router->view('index')->render();
});

//...
```

> You can also do this within your controller class.

***

* Class namespace: `\Luminova\Routing\Bootstrap`
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
new Bootstrap(string $name, \Closure|array|null $onError = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | Route context name |
| `$onError` | **\Closure&#124;array<int,string>&#124;null** | Optional error callback function to execute |