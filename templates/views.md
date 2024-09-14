# Template and View Rendering

***

## Overview

The View class is an essential component of Luminova template management for compiling, presenting, and caching capabilities. 

***

## Introduction

The `View` class serves as the central controller for presenting templates in Luminova. It is designed to compile, present, and cache application views efficiently. The class is highly adaptable, capable of handling various content types, and offers customizable rendering modes to fit different coding styles and business logic needs. 

In Luminova, the `View` class is automatically inherited by the `CoreApplication` class, ensuring seamless access to its methods throughout your application.

### Core Functionalities

1. **Compiling Views**: The `View` class processes template files, transforming them into optimized content for rendering.
2. **Presenting Views**: It controls the display of templates, ensuring that the correct content is shown to the user.
3. **Caching Views**: To enhance performance, the `View` class can cache compiled templates, reducing the need for repeated processing.
4. **Content Adaptability**: It can handle various content types, making it versatile for different presentation needs.
5. **Customizable Rendering**: The rendering mode can be customized to match your specific coding style and business logic requirements [Template Configuration](/configs/template.md).

***

* Class namespace: `\Luminova\Template\View`

***

### Class Access

- **Within the `App\Application` Class**: Access methods using `$this->foo()`.
- **Within View Controller Classes**: Access methods through the application object using `$this->app->foo()`.
- **Within Routing Context Files**: Access methods via the global application variable `$app->foo()`.
- **Within View Files**: Access methods using `$this->foo()` only if template isolation is disabled and the `default` template engine is used.
- **Other Global Scope**: Use the global helper function `app()` to access methods with `app()->foo()`.

> **Note:** 
> Avoid re-initializing the `Application` class multiple times, as this may lead to unintended errors. Instead, always use the `app()` helper function to return a shared instance of your application object.

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

`self` - Return instance of of View class or CoreApplication depending where its called.

> Enabling the `codeblock` button option adds a copy button to your HTML code blocks, allowing easy code copying for users. However, implementing the copy functionality requires `JavaScript` integration.

***

### setFolder

The `setFolder` method allows you to define a base directory for view file resolution, making it easier to organize your application's view files. By using this method, you can control where the framework searches for view files associated with a specific controller, method, or the entire application.

```php
public setFolder(string $path): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path`   | string | The directory path relative to `resources/views/` where the view files will be searched. |

**Return Value:**

`self` - Returns the current instance of the `View` class or `CoreApplication`, depending on where it is called.

**Usage Scenarios:**

- **Controller Global Scope:** If `setFolder` is invoked within a controller's `onCreate` or `__construct` method, all views related to that controller will be searched within the specified folder.
- **Application Global Scope:** When `setFolder` is called in the application's `onCreate` or `__construct` method, the folder will apply to all views across the entire application.
- **Method-Specific Scope:** Calling `setFolder` just before rendering a view within a controller method restricts the folder to that specific method's view resolution.

**Example Usage:**

In this example, the `setFolder` method is used to direct the framework to look for view files in a custom directory (`resources/views/example/`) for all views within a specific controller.

```php
// /app/Controllers/ExampleController.php
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

    public function foo(): int
    {
        // Overrides the default folder for the current method.
        $this->app->setFolder('example2');
        return $this->view('foo');
    }

    //...
}
```

**Explanation:**

- **Controller Global Usage:** Here, `setFolder('example')` in `onCreate` ensures that all view lookups in `ExampleController` will be within `resources/views/example/`.
- **Method-Specific Usage:** If you need a different view directory for a specific method, you can call `setFolder` before rendering that method's view.

> **Important Notes:** 
> 
> **Directory Structure:** Ensure that the specified sub-folder exists within `resources/views/` for `setFolder` to function correctly.
> **Organized Views:** This method helps maintain a clear and organized directory structure in your application, particularly as the number of views grows.

***

### noCaching

By default when `page.caching = true` is enabled in `env` file, all views will be cached and reused. The `noCaching` method allows you to specify views that should be excluded from page caching making it useful for selectively excluding views from page caching, ensuring that dynamic content is always up-to-date. 
This can be configured either in your `routes/context.php` file, `Application` class or your view `Controller` class, but the recommended way is to call it within the application `onCreate` or `__construct` method.

```php
public noCaching(string|array<int,string> $viewName): self
```

**Parameters:**

| Parameter  | Type                        | Description                      |
|------------|-----------------------------|----------------------------------|
| `$viewName` | **string&#124;array<int,string>** | The view name or array list of view names to exclude. |

**Return Value:**

`self` - Return instance of of View class or CoreApplication depending where its called.

**Example**

To disable caching for specific a route method, e,g in this `API` example `insert` and `edit` will be excluded while allowing cache for other route methods.

```php
<?php
$router->post('/fetch', 'ApiController::fetch');
$router->post('/insert', 'ApiController::insert');
$router->post('/edit', 'ApiController::edit');
```

**Controller Implementation**

In your `Application` class, within the `onCreate` or `__construct` method.

```php
// /app/Application.php
<?php
namespace App;

use \Luminova\Core\CoreApplication;
class Application extends CoreApplication
{
    protected function onCreate(): void
    {
        $this->noCaching(['insert_view', 'edit_view']);
    }
}
```

Now in your controller class, when excluded views are rendered it won't be cached.

```php
// /app/Controllers/ApiController.php
<?php
namespace App\Controllers;

use \Luminova\Base\BaseController;
class ApiController extends BaseController
{
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

***

### cacheOnly

Unlike `noCaching` method, the `cacheOnly` method allows you to specify which views should be exclusively cached. This is useful when you want to ensure that only certain views are stored in the cache, while all other views are excluded from caching.

```php
public cacheOnly(string|array<int,string> $viewName): self
```

**Parameters:**

| Parameter  | Type                        | Description                      |
|------------|-----------------------------|----------------------------------|
| `$viewName` | **string&#124;array<int,string>** | A single view name or an array of view names to cache. |

**Return Value:**

`self` - Returns the instance of the View class or `CoreApplication`, depending on where it's called.

**Applicable Usage:**

Let assume you have `100` views but want to cache only three specific views, `home`, `about`, and `contact`, using the `noCaching` method would require listing all `97` other views individually. Instead, you can simplify this by using the `cacheOnly` method. This method allows you to specify only the views you want to cache, while automatically ignoring the rest. By listing just the three views you want to cache, you ensure that only these are cached, and all others are excluded.

---

**Example:**

Enable caching for only 'home', 'about', and 'contact' views

```php
// /app/Application.php
<?php 
namespace App;

use Luminova\Base\CoreApplication;

class Application extends CoreApplication
{
    protected function onCreate(): void 
    {
        $this->cacheOnly(['home', 'about', 'contact']);
    }
}
```

***

### cacheable

Unlike the `noCaching` method that disables caching for selected views, the `cacheable` method allows you to disable view caching for the entire application or controller views. 

If called in a controller's `onCreate` or `__construct` method, **view caching will be disabled** for all views within that controller.  
If called in the application's `onCreate` or `__construct` method, **view caching will be disabled** for the entire application.

```php
public cacheable(bool $allow): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$allow` | **bool** |  Weather to allow caching of views. |

**Return Value:**

`self` - Return instance of of View class or CoreApplication depending where its called.

***

**Example**

The `cacheable` method can be called in the controller's or application's `onCreate` or `__construct` method.
But in this example we will use `Controller` method to disable caching for all `API` routes.

```php
// /app/Controllers/ApiController.php
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

Injections should be performed in your `App\Application` controller class, either after calling `parent::__construct()` in the constructor or within the `onCreate` method of your application class depending on your choice.

```php
public export(string|object $class, ?string $alias = null, bool $initialize =true): true
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$class` | **class-string<\T>&#124;class-object<\T>** | The class name or instance of a class to register. |
| `$alias` | **string&#124;null** | Optional class alias to use in accessing class object (default: null). |
| `$initialize` | **bool** | Whether to initialize class-string or leave it as static class (default: true). |

**Return Value:**

`true` -  Returns true on success, otherwise, an exception will be thrown if an error occurs.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the class does not exist or failed.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If there is an error during registration.

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

`self` - Return instance of of View class or CoreApplication depending where its called.

**Example**

In this example we check if cache exist and not expired before processing heavy database operation.

<p class="text-danger">*Note:* Passing different expiration other than the expiration used during cache will not take effect in checking the expiration.</p>

```php
// /app/Controllers/ExampleController.php
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
public expired(string|null $viewType = 'html'): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$viewType` | **string&#124;null** | The view content extension type (default: `html`). |

**Return Value:**

`bool` - Returns `true` if the cache doesn't exist or has expired.

**Throws**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throw if the cached version doesn't match with the current view type.

> **Note:** 
> 
> Before calling this method, you must first call `cache` method instantiate it for use.
> 
> For the expiration check, we use the expiration set in `env` while saving cache, also don't worry about the cache key as it handled internally to save your precious time.

***

### reuse

The `reuse` method renders the cached version of view content if it exists.

```php
public reuse(): int
```

**Return Value:**

`int` - Returns a status code: `SUCCESS` if the cache exists and is rendered successfully, otherwise `ERROR`.

**Throws**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throws an exception if called without first calling the `cache` method or if the cache file is not.

***

### delete

The `delete` method removes the cache entry for the current request view. 

You can optionally specify an application version to delete its corresponding cache entry, this can be useful in a situation where new contents has been cached using the the old version or vise versa.

```php
public function delete(?string $version = null): bool
```

**Parameters:**

| Parameter | Type         | Description                                                          |
|-----------|--------------|----------------------------------------------------------------------|
| `$version`| **string\|null** | Optional application version whose cache entry should be deleted (default: null). If set to `null`, it deleted cache for the current version. |

**Return Value:**

`bool` - Returns `true` if the cache entry was successfully deleted; `false` otherwise.

**Example Usage:**

Delete the cache entry for the current view.
```php
$deleted = $this->delete();
```

Delete the cache entry for a specific application version.
```
$deleted = $this->delete('1.0.0');
```

***

### clear

The `clear` method removes all cache entries associated with view files. You can optionally specify an application version to clear its corresponding cache entries.

```php
public function clear(?string $version = null): int
```

**Parameters:**

| Parameter | Type         | Description                                                          |
|-----------|--------------|----------------------------------------------------------------------|
| `$version`| **string\|null** | Optional application version whose cache entries should be cleared (default: null). If set to `null`, which clears cache entries for the current version. |

**Return Value:**

`int` - Returns the number of cache entries that were successfully deleted.

**Example Usage:**

Clear all cache entries for the current application version.
```php
$total = $this->clear();
```

Clear cache entries for a specific application version.
```php
$total = $this->clear('1.0.0');
```

***

### header

To optionally set customer headers which will be included in response header.

```php
public header(string $key, mixed $value): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The header key. |
| `$value` | **mixed** | The header value for key. |

**Return Value:**

`self` - Return instance of of View class or CoreApplication depending where its called.

***

### headers

To optionally set customer headers at once which will be included in response header.

```php
public headers(array $headers): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$headers` | **array<string,mixed>** | The headers key-pair. |

**Return Value:**

`self` - Return instance of of View class or CoreApplication depending where its called.

***

### view

The `view` method allows you to specify the name and type of the view content to be rendered or returned. 
This method must be called before using the `respond` or `render` method.

Additionally, do not include the extension type for argument `$viewName` (e.g, `.php`, `.tpl`, `.twg`), only the file name.

```php
public view(string $viewName, string $viewType = 'html'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$viewName` | **string** | The view file name without extension type (e.g, `index`). |
| `$viewType` | **string** | The view content type (default: `html`). |

**Return Value:**

`self` - Return instance of of View class or CoreApplication depending where its called.

**Throws**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throw if invalid or unsupported view type specified.

**Supported View Content Types:**

| Type  | Description     |
|------------|----------|
| `html` | The view contents is HTML format. |
| `json` | The view contents is JSON format. |
| `text\|txt` | The view contents is Plain text (use txt if you want to serve static content). |
| `xml` | The view contents is XML format. |
| `js` | The view contents is JavaScript. |
| `css` | The view contents is CSS. |
| `rdf` | The view contents is RDF format. |
| `atom` | The view contents is Atom format. |
| `rss` | The view contents is RSS feed format. |

***

**Example**

In your controller class.

```php
// /app/Controllers/FooController.php
<?php
namespace App\Controllers;

use \Luminova\Base\BaseController;

class FooController extends BaseController
{
    public function show(): int
    {
        return $this->app->view('show_view', 'html')->render(['foo' => 'bar'], 200);
    }
}
```

> The `view` method is crucial for defining the view template and content type before rendering or responding with the view content. 
> By setting the view name and type, you ensure that the correct content is prepared and handled appropriately.

***

### render

The `render` method allows you to present your application's view content to users, with additional options provided as an array of key-value pairs. 
These options can be accessed within the template view file using `$this->_myVar` or `$_myVar` when view isolation is enabled. 
If variable prefixing is disabled in `/app/Config/Template.php`, then you can access your options directly as variables `$this->myVar` or `$myVar`.

```php
public render(array&lt;string,mixed&gt; $options = [], int $status = 200): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<string,mixed>** |  Additional parameters to pass in the template file. |
| `$status` | **int** | The HTTP status code (default: `200` OK). |

**Return Value:**

`int` - Return status code `STATUS_SUCCESS` or `STATUS_ERROR` on failure.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If read and write permission to writable was denied.

***

### respond

Unlike the `render` method, the `respond` method returns the compiled view content as a string instead of presenting it. If caching is enabled, it will store the content for later use.

```php
public respond(array<string,mixed> $options = [], int $status = 200): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<string,mixed>** |  Additional parameters to pass in the template file. |
| `$status` | **int** | The HTTP status code (default: `200` OK). |

**Return Value:**

`string` - Return the compiled view contents.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If read and write permission to writable was denied.

***

**Example**

In your controller class.

```php
// /app/Controllers/FooController.php
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
public redirect(string $view, int $response_code = 0): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string** | The view name or view path to redirect to. |
| `$response_code` | **int** | The redirect response status code (default: 0). |

***

### link

Creates a relative `URLs` to views or files, ensure the `url` starts from the public root directory. 
These method automatically handle the base path and avoid common pitfalls with relative `URLs`.

For example, if the current view URL is `https://example.com/some/view/foo/bar/baz` and you have a link like `<a href='./bra'>Bra</a>`, this can lead to broken links, particularly during development. 

**Reason**: Relative URLs like `./bra` are resolved relative to the current path. In development or different directory levels, this may not point to the correct location, leading to broken links. 

**Recommended Practices**:

- Use predefined variables within your view files such as `$this->_href` or `$this->_asset`.
- Utilize global functions for generating links, like `href('foo')` for view URLs and `asset('foo')` for asset URLs. 

```php
public static link(string $filename = ''): string 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | Optional view, path or file to prepend to root URL. |

**Return Value:**

`string` - Return full URL to view or file.

**Examples**

To ensure links, images or asset links are not broken, you can use below method:

```php
<?php
$this->app->link('bra'); 
//Returns: /bra
```

***

### viewInfo

Retrieves information about a view file.

```php
public viewInfo(): array
```

**Return Value:**

`array<string,mixed>` - Return an associative array containing information about the view file.

**Return Keys:**

-  `location`: - The full path to the view file.
-  `engine`: - The template engine.
-  `size`: - The size of the view file in bytes.
-  `timestamp`: - The last modified timestamp of the view file.
-  `modified`: - The last modified date and time of the view file (formatted as `Y-m-d H:i:s`).
-  `dirname`: - The directory name of the view file.
-  `extension`: - The extension of the view file.
-  `filename`: - The filename (without extension) of the view file.

***

### Default View Options

When rendering views within the Luminova application, certain default view options are automatically set by default. These default options provide foundational settings for view rendering and can be leveraged to control various aspects of the rendered content.

**Immutable Options**

The following view options are considered immutable and cannot be overridden when rendering views:

1. `viewType` **(string)** - Indicates the view type being rendered.
2. `active` **(string)** - Indicate the view name which is currently been rendered. 
3. `asset` **(string)** - Alternative to create a link to asset folder `<img src="<?= $this->_asset;?>images/file.png">`. 
4. `href` **(string)** - Alternative to create a link to another view `<a href="<?= $this->_href;?>foo">Foo</a>`. 
5. `ALLOW_ACCESS` *(true)* - Constant, use this to deny access to files that only template should have access to

**Mutable Options**

The remaining view options can be customized and overridden based on specific rendering requirements.

1. `nocache` **(bool)** - Optionally define view cache control to exclude view from caching.
2. `title` **(string)** - Specifies the title of the view. If not explicitly set, the default title is derived from the active view and application name .
3. `subtitle` **(string)** - Sets the subtitle of the view. If not provided, the subtitle is automatically generated based on the active view.