# Render View Class

***

## Overview

Managing and customizing your template view rendering and behavior with Luminova&#039;s TemplateTrait class.

***

## Introduction

In this documentation, we will walk you through the `Template` class, which is responsible in rendering, presenting and customizing view templates within your Luminova's application. The template object is automatically inherited by your base application controller classes `BaseApplication`, providing convenient access to its methods anywhere in your application codebase.

***

### Class Access

- In your application controller class, call it methods using `$this->app->foo()`.
- In routing context files, access the methods using `$app->foo()`.
- In view files, access the methods using `$this->foo()`, if template isolation is not enabled and you are using `default` template engine.
- In other context where `BaseApplication` object is not initialized by default, you can use a global helper function `app()->foo()`.

> Avoid reinitializing your `Application` instance multiple times as this may cause unintended error, instead always use helper function `app()`, to return a shared instance of your application object.

***

* Class namespace: `\Luminova\Template\TemplateTrait`
* Configuration: [Template Configuration](/configs/template.md) 

***

### codeblock

Set if HTML codeblock tags should be ignore during page minification.

```php
public codeblock(bool $minify, bool $button = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$minify` | **bool** | Indicate if codeblocks should be minified (default: false). |
| `$button` | **bool** | Indicate if codeblock tags should include a copy button (default: false). |

**Return Value:**

`TemplateTrait` - Return class instance.

> Enabling codeblock button will add a copy button to your HTML code blocks, allowing users to easily copy the code. 
> 
> You will need to implement the copy logic using JavaScript.

***

### setFolder

With this method you can set a sub directory folder name for template to look for your view file.
Your need to create the sub folder in `resources/views/`.

This method allows you to specify a subdirectory within the resource view `resources/views/` directory where the template engine will look for your view files.

```php
public setFolder(string $path): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | folder name to search for view. |

**Return Value:**

`TemplateTrait` - Return class instance.

**Example**

To set a custom directory where your view files should be looked for.
Optionally you can call method `setFolder` anywhere in your controller methods before rendering template. 

```php
<?php 
namespace App\Controllers;

use \Luminova\Base\BaseController;
class ApiController extends BaseController
{
    protected function onCreate(): void
    {
        $this->app->setFolder('v2');
    }

    //...
}
```

***

### noCaching

You can use this method to specify views that should be excluded from page caching. This can be configured either in your `routes/context.php` file or within your application controller class.

```php
public noCaching(string|array&lt;int,string&gt; $viewName): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$viewName` | **string&#124;array<int,string>** | view name or array of view names. |

**Return Value:**

`TemplateTrait` - Return class instance.

**Example**

To disabled caching for selected APIs routes `insert` and `edit` while allow cache for other routes.

```php
<?php 
$router->post('/fetch', 'ApiController::fetch');
$router->post('/insert', 'ApiController::insert');
$router->post('/edit', 'ApiController::edit');
```

In your `API` controller class `onCreate` or `__construct` method.

```php
<?php 
namespace App\Controllers;

use \Luminova\Base\BaseController;
class ApiController extends BaseController
{
    protected function onCreate(): void
    {
        $this->app->noCaching(['insert_view', 'edit_view']);
    }

    public function insert(): int 
    {
        return $this->view('insert_view', [
            //...
        ])
    }

    //...
}
```

> The `noCaching` method can also be called in controller `onCreate` method or `__construct` method.

***

### cacheable

To disabled page view caching for entire routing context use the `cacheable` method globally in the routing context file or withing your middleware method to disabled caching of the routes within the context.

> By default caching of routes context are enabled once you have specified `page.caching = true` in your .env file. 

```php
public cacheable(bool $allow): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$allow` | **bool** | Set the caching context status. |

**Return Value:**

`TemplateTrait` - Return class instance.

> The `cacheable` method can also be called in controller `onCreate` method or `__construct` method.

**Example**

To disabled caching for all APIs routes.

```php
<?php 
namespace App\Controllers;

use \Luminova\Base\BaseController;
class ApiController extends BaseController
{
    protected function onCreate(): void
    {
        $this->app->cacheable(false);
    }

    //...
}
```

***

### export

The export method allows you to inject dependencies into your application's views, making them accessible within the view template. Injections should be performed in your application controller class, either after calling `parent::__construct()` in the constructor or within the `onCreate()` method of your application controller class.

```php
public export(string|object $class, string|null $alias = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$class` | **class-string&#124;class-object** | The class name or instance of a class to register. |
| `$alias` | **string&#124;null** | Optional class name alias. |

**Return Value:**

`bool` - true on success, false on failure.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#RuntimeException) - If the class does not exist or failed.
- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#InvalidArgumentException) - If there is an error during registration.

**Examples**

With no alias indicated.

```php
<?php 
$this->export(new FooClass());
//Or 
$this->export(FooClass::class);
```
Will be accessed like.
```php
$this->FooClass->myMethod()
```
Alias indicated.

```php
<?php 
$this->export(new FooClass(), 'foo');
//Or 
$this->export(FooClass::class, 'foo');
```
It can be accessed within your template view files as shown below.
```php
$this->foo->myMethod()
```

Or in your controller classes as.
```php
$this->app->foo->myMethod()
```

***

### cache

The cache method enables you to cache and store responses to reuse them on subsequent requests for the same content, ensuring that cached versions are served when the same resource is requested again.

```php
public cache(\DateTimeInterface|int|null $expiry = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$expiry` | **\DateTimeInterface&#124;int&#124;null** |  |

**Return Value:**

`TemplateTrait` - Return class instance.

***

### expired

The expired method allows you to check whether a cached item has expired or not. Before using this method, you must first instantiate cache the `cache()` method.

```php
public expired(): bool
```

**Return Value:**

`bool` Returns true if cache doesn't exist or expired.

**Example Usage**

To check if a cached item has expired, use the expired method as follows:

```php
<?php 
namespace App\Controllers;

use \Luminova\Base\BaseViewController;
class PageController extends BaseViewController
{
    public function page(): int
    {
        $cache = $this->app->cache(3500);
        if($cache->expired()){
            return $cache->view('page')->render(['data' => doHeavyProcess()]);
        }

        return $cache->reuse();
    }
}
```

***

### reuse

Reuse cached content if the cache exists.

```php
public reuse(): int
```

**Return Value:**

`int` - Returns true if cache exist and rendered else return false.

***

### respond

Get the rendered contents of a view, if cache is enabled it will store the content for later use.

```php
public respond(array $options = [], int $status = 200): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<string,mixed>** | Additional options to pass in the template file. |
| `$status` | **int** | HTTP status code (default: 200 OK) |

**Return Value:**

`string` - The rendered view contents.

> This method can be useful when you want to send view as email template.

```php
<?php 
class FooController extends BaseController {
	//...
	public function foo(): int 
	{
		$content = $this->respond('foo', ['foo' => 'bar']);
		Mailer::to('peter@example.com')->send($content);
		return STATUS_SUCCESS
	}
}
```

***

### redirect

The redirect method is used to redirect to another view URI.

```php
public redirect(string $view, int $response_code): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string** | view name or name with segments |
| `$response_code` | **int** | response status code |

***

### view

The view method is used to specify the view name and type of view to render within your application.

```php
public view(string $viewName, string $viewType = 'html'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$viewName` | **string** | view name|
| `$viewType` | **string** | Type of content, differs from Content-Type and file formats. `(html, json, text or xml)` |

**Return Value:**

`TemplateTrait` - Return class instance.

***

### render

This method renders your application view content with additional options provided as an array of key-value pairs. These options can be accessed within the template view file using `$this->_myvar`. If you set `public static bool $useVariablePrefix` to false in `/app/Controllers/Config/Template.php`, then you can access your options directly as variable `$myVar`.

```php
public render(array&lt;string,mixed&gt; $options = [], int $status = 200): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<string,mixed>** | additional parameters to pass in the template file $this-&gt;_myOption |
| `$status` | **int** | HTTP status code (default: 200 OK) |

**Return Value:**

`int` - Return status code.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#InvalidArgumentException) - If invalid view type was passed.
- [\Luminova\Exceptions\ViewNotFoundException](/exceptions/classes.md#ViewNotFoundException) - If the view is not found
- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#RuntimeException) - If read and write permission to writable was denied.

***

### viewInfo

Retrieves information about a view file.

```php
public viewInfo(): array
```

**Return Value:**

`array` - An associative array containing information about the view file:
-  'location': The full path to the view file.
-  'engine': The template engine.
-  'size': The size of the view file in bytes.
-  'timestamp': The last modified timestamp of the view file.
-  'modified': The last modified date and time of the view file (formatted as 'Y-m-d H:i:s').
-  'dirname': The directory name of the view file.
-  'extension': The extension of the view file.
-  'filename': The filename (without extension) of the view file.

***

### Default View Options

When rendering views within the Luminova application, certain default view options are automatically set by default. These default options provide foundational settings for view rendering and can be leveraged to control various aspects of the rendered content.

**Immutable Options**

The following view options are considered immutable and cannot be overridden when rendering views:

1. `viewType` *(string)* - Indicates the view type being rendered.
2. `active` *(string)* - Indicate the view name which is currently been rendered. 
3. `asset` *(string)* - Alternative to create a link to asset folder `<img src="<?= $this->_asset;?>images/file.png">`. 
4. `href` *(string)* - Alternative to create a link to another view `<a href="<?= $this->_href;?>foo">`. 
5. `ALLOW_ACCESS` *(true)* - Constant, use this to deny access to files that only template should have access to

**Mutable Options**

The remaining view options can be customized and overridden based on specific rendering requirements.

1. `nocache` *(bool)* - Optionally define view cache control to exclude view from caching.
2. `title` *(string)* - Specifies the title of the view. If not explicitly set, the default title is derived from the active view and application name .
3. `subtitle` *(string)* - Sets the subtitle of the view. If not provided, the subtitle is automatically generated based on the active view.