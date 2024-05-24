# View Handling

***

## Overview

Managing and customizing your template view rendering and behavior with Luminova's TemplateTrait class.

***

## Introduction

In this documentation, we'll delve into the functionalities of the `TemplateView` class, which acts as the core controller for Luminova templates. This class is responsible in compiling, presenting, and caching application views. Its adaptable nature enables it to handle various content types, while its rendering mode can be customized to suit your coding style and business logic. The template object is automatically inherited by `BaseApplication` class, providing a convenient access to its methods throughout your application codebase.

***

### Class Access

- Within the `App\Controllers\Application` class, access to it methods should be through the `$this->foo()`.
- Within the view controllers classes, access to it methods should be through the application object `$this->app->foo()`.
- Within the routing context files, access it methods through application global variable  `$app->foo()`.
- Within the view files, access the methods using `$this->foo()`, only if template isolation is not enabled and you are using the `default` template engine.
- In your global scope, you can use the global helper function `app` to access it methods `app()->foo()`.

> *Note:* Avoid re-initializing your `Application` class multiple times as this may cause unintended error, instead always use helper function `app()`, to return a shared instance of your application object.

***

* Class namespace: `\Luminova\Template\TemplateView`
* Configuration: [Template Configuration](/configs/template.md) 

***

### codeblock

The `codeblock` method enables you to disable minification for HTML code blocks enclosed within `<pre><code></code></pre>` tags during view content minification. This feature is particularly useful when you need to display code on your webpage. If minification is applied, the removal of new lines can make the code difficult for users to read.

```php
public codeblock(bool $minify, bool $button = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$minify` | **bool** | Indicate if code-blocks should be minified (default: false). |
| `$button` | **bool** | Indicate if code-block tags should include a copy button (default: false). |

**Return Value:**

`self` - Returns the class object, including the application object.

> Enabling the `codeblock` button option adds a copy button to your HTML code blocks, allowing easy code copying for users. However, implementing the copy functionality requires `JavaScript` integration.

***

### setFolder

The `setFolder` method can be used to achieve a Hierarchical Model-View-Controller (HMVC) structure. It allows you to organize your application's view files into subdirectories. 
This method informs the framework's template engine where to look for template files for an entire controller class, a specific controller method, or an entire route context.

```php
public setFolder(string $path): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | Directory path to search for view. |

**Return Value:**

`self` - Returns the class object, including the application object.

> *Note:* For `setFolder` to function properly, you must create the sub-folder within the `resources/views/` directory.

**Example**

To set a custom directory where your view files should be looked for in view folder e.g `resources/views/example/`

```php
<?php 
namespace App\Controllers;

use \Luminova\Base\BaseController;

class ExampleController extends BaseController
{
    protected function onCreate(): void
    {
        $this->app->setFolder('example');
    }

    public function show(): int
    {
        return $this->view('show');
    }

    //...
}
```

> In this example, the `setFolder` method is used to set the view folder to `example`, so when you call the `show` method, it will look for the view file in `resources/views/example/show.php`.
>
> This approach helps maintain a clear and organized structure for your view files, especially in large applications with many views.

***

### noCaching

By default when `page.caching = true` is enabled in `env` file, all views will be cached and reused. The `noCaching` method allows you to specify views that should be excluded from page caching making it useful for selectively excluding views from page caching, ensuring that dynamic content is always up-to-date. 
This can be configured either in your `routes/context.php` file, `Application` class or your view `Controller` class.

```php
public noCaching(string|array<int,string> $viewName): self
```

**Parameters:**

| Parameter  | Type                        | Description                      |
|------------|-----------------------------|----------------------------------|
| `$viewName` | **string&#124;array<int,string>** | The view name or array list of view names to exclude. |

**Return Value:**

`self` - Returns the class object, including the application object.

**Example**

To disable caching for specific a route method, e,g in this `API` example `insert` and `edit` will be excluded while allowing cache for other route methods.

```php
<?php
$router->post('/fetch', 'ApiController::fetch');
$router->post('/insert', 'ApiController::insert');
$router->post('/edit', 'ApiController::edit');
```

**Controller Implementation**

In your `ApiController` class, within the `onCreate` or `__construct` method.

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
        return $this->view('insert_view');
    }

    public function edit(): int 
    {
        return $this->view('edit_view');
    }

    public function fetch(): int 
    {
        return $this->view('fetch_view');
    }

    // Other methods...
}
```

> The `noCaching` method can also be called in the application's `onCreate` method or `__construct` method.
> But to have more convenient access in customizing based on controller, it is recommended to call it within your view controller class instead.

***

### cacheable

Unlike the `noCaching` method that disables caching for specific views, the `cacheable` method allows you to disable view caching for an entire routing context or controller class. 
Optionally, you can call this method globally in the routing context file within the `middleware` method to disable caching for the entire route context.

```php
public cacheable(bool $allow): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$allow` | **bool** | Set the caching status true or false. |

**Return Value:**

`self` - Returns the class object, including the application object.

***

**Example**

The `cacheable` method can be called in the controller's `onCreate` method or `__construct` method.
But in this example we will use `onCreate` method to disable caching for all `API` routes.

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

    // Other methods...
}
```

> The `cacheable` method provides a straightforward way to control view caching by setting the caching status globally or for specific controllers, you can ensure that dynamic content remains current without being cached.

***

### export

The `export` method allows you to inject dependencies making them accessible within your `template` files, controllers, and anywhere you have access to `Application` object. 

Injections should be performed in your `App\Controllers\Application` controller class, either after calling `parent::__construct()` in the constructor or within the `onCreate()` method of your application class depending on your choice.

```php
public export(string|object $class, string|null $alias = null, bool $initialize =true): true
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$class` | **class-string&#124;class-object** | The class name or instance of a class to register. |
| `$alias` | **string&#124;null** | Optional class name alias. |
| `$initialize` | **bool** | Whether to initialize class-string or leave it as static class (default: true). |

**Return Value:**

`true` -  Returns true on success, otherwise, an exception will be thrown if an error occurs.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - If the class does not exist or failed.
- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - If there is an error during registration.

**Examples**

1. **Exporting Class without Alias**

    ```php
    <?php 
    $this->export(new FooClass());
    ```

2. **Exporting Class by Namespace**

    If you do not want the class to be initialized, pass `false` as the third argument.

    ```php
    <?php
    $this->export(FooClass::class);
    ```

3. **Exporting Class with Alias**

    ```php
    <?php 
    $this->export(FooClass::class, 'foo');
    ```

### Accessing Properties

**Accessing Class within the Template Files**

To access class within your template files, you must use either `$this` or `$self` when view isolation is enabled.
1. **Direct Access**

    ```php
    <?php
    $this->FooClass->myMethod();
    ```

2. **Accessing Class by Alias**

    ```php
    <?php
    $this->foo->myMethod();
    ```

3. **Accessing Static Class Method**

    ```php
    <?php
    $this->foo::myMethod();
    ```

**Accessing Class Properties within Your Controller Class**

To access class properties within your controller class, use the application object.

```php
<?php
$this->app->foo->myMethod();
```
> Globally you can access properties anywhere that `application` object is available or using global helper function `app()`.

***

### cache

The `cache` method allows you to manually manage view caching, updating the view with new content or reusing the cached version on subsequent requests. 
The cache key generation for caches is handled automatically, so you don't need to specify cache keys manually. 

Optionally, you can specify cache expiry if you don't want to use the global expiration set in the `.env` file.

```php
public cache(\DateTimeInterface|int|null $expiry = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$expiry` | **\DateTimeInterface&#124;int&#124;null** | The cache expiration, set to `null` to use default expiration from .env file. |

**Return Value:**

`self` - Returns the class object, including the application object.

**Example**

In this example we check if cache exist and not expired before processing heavy database operation.

<p style="color:red;">*Note:* Passing different expiration other than the expiration used during cache will not take effect in checking the expiration.</p>

```php
<?php
namespace App\Controllers;

use \Luminova\Base\BaseController;

class ExampleController extends BaseController
{
    protected function show(): int
    {
        $cache = $this->app->cache(60); 

        if($cache->expired()){
            $heavy = $db->doHeavyProcess();
            return $this->view('show_view', ['data' => $heavy]);
        }

        return $cache->reuse();
    }
}
```

> The `cache` method offers fine-grained control over view caching, by specifying cache expiration and checking for cached content, you can optimize performance by avoiding redundant processing and ensuring that users receive the most up-to-date content when necessary.

***

### expired

The `expired` method checks whether a cached item has expired, allowing you to decide whether to renew the view or reuse the cached version.

```php
public expired(): bool
```

**Return Value:**

`bool` - Returns `true` if the cache doesn't exist or has expired.

> **Note:** Before calling this method, you must first instantiate the cache with the `cache()` method.

***

### reuse

The `reuse` method renders the cached version of view content if it exists.

```php
public reuse(): int
```

**Return Value:**

`int` - Returns a status code: `SUCCESS` if the cache exists and is rendered successfully, otherwise `ERROR`.

**Throws**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - Throws an exception if called without first calling the `cache` method or if the cache file is not 

***

### view

The `view` method allows you to specify the name and type of the view content to be rendered or returned. 
This method must be called before using the `respond` or `render` methods.

```php
public view(string $viewName, string $viewType = 'html'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$viewName` | **string** | The view name to render or respond with. |
| `$viewType` | **string** | Type of content, differs from Content-Type and file formats. |

**Return Value:**

`self` - Returns the class object, including the application object.

**Supported View Types:**

| Type  | Description     |
|------------|----------|
| `html` | HTML content. |
| `json` | JSON content. |
| `text` | Plain text content. |
| `xml` | XML content. |
| `js` | JavaScript content. |
| `css` | CSS content. |
| `rdf` | RDF content. |
| `atom` | Atom content. |
| `rss` | RSS feed content. |

***

**Example**

In your controller class.

```php
<?php
namespace App\Controllers;

use \Luminova\Base\BaseController;

class FooController extends BaseController
{
    public function show(): int
    {
        return $this->app->view('show_view')->render(['foo' => 'bar']);
    }
}
```

> The `view` method is crucial for defining the view template and content type before rendering or responding with the view content. 
> By setting the view name and type, you ensure that the correct content is prepared and handled appropriately.

***

### render

The `render` method allows you to present your application's view content to users, with additional options provided as an array of key-value pairs. 
These options can be accessed within the template view file using `$this->_myVar` or `$_myVar` when view isolation is enabled. 
If variable prefixing is disabled in `/app/Controllers/Config/Template.php`, then you can access your options directly as variables `$this->myVar` or `$myVar`.

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

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - If invalid view type was passed.
- [\Luminova\Exceptions\ViewNotFoundException](/exceptions/classes.md#viewnotfoundexception) - If the view is not found
- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - If read and write permission to writable was denied.

***

### respond

Unlike the `render` method, the `respond` method returns the compiled view content as a string instead of presenting it. If caching is enabled, it will store the content for later use.

```php
public respond(array $options = [], int $status = 200): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<string,mixed>** | Additional options to pass in the template file. |
| `$status` | **int** | HTTP status code (default: 200 OK) |

**Return Value:**

`string` - Return compiled view contents.

***

**Example**

In your controller class.

```php
<?php
namespace App\Controllers;

use \Luminova\Base\BaseController;
use \App\Services\Mailer;

class FooController extends BaseController
{
    public function foo(): int
    {
        $content = $this->respond(['foo' => 'bar']);
        Mailer::to('peter@luminova.ng')->send($content);
        return STATUS_SUCCESS;
    }
}
```

> The `respond` method allows for greater flexibility in handling view content by returning it as a string. 
> This is particularly useful for scenarios such as downloading, sending emails or processing the view content further before presenting it. 

***

### redirect

To transit from one view URI to another.

```php
public redirect(string $view, int $response_code): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string** | view name or name with segments |
| `$response_code` | **int** | response status code |

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