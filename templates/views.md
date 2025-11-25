# Views and Template Rendering

***

## Overview

The View class renders templates with optional caching and layout inheritance, ideal for MVC/HMVC apps where you need structured and fast HTML output.

***

## Introduction

The Luminova's template **View** class is used to load and render templates content, capable of handling dynamic contents in **MVC** or **HMVC** application. It compiles your templates, presents them as HTML or other formats, and can cache the final output to speed up page loads.

Unlike the [Content Response Class](/templates/response.md), which is for sending raw or API data, **View** focuses on rendering **PHP, Smarty or Twig** template contents (e.g, `html`, `xml`, `js`, etc.) and gives you options for layout inheritance, rendering modes, and template configuration to match your coding style.

---

### Features

1. **Fast Rendering** – Compiles and minifies templates automatically.
2. **Flexible Output** – Supports templates that return HTML, arrays, objects, or scalars.
3. **Built-in Caching** – Saves rendered templates for better performance.
4. **Highly Configurable** – Adjust rendering modes via [Template Rendering Configuration](/configs/template#lmv-docs-templateisolation).

---

## Template Rendering Modes

Luminova allows you render templates in two ways: **Global Mode** (the default) or **Isolation Mode**.
You can switch modes in the [Template View Configuration](/configs/template.md).

---

## Global Mode

Global mode gives your template direct access to `$this` and `self`.
That means the template can reach:

* view-specific properties
* public methods on the view class
* application properties through `$this->app`

```php
// /resources/Views/index.php

// View options
$this->_title;

// Application properties
$this->app->propertyName;
```

**Use this when:**

You want simple access to everything with no extra setup.
Good for quick builds or templates that talk to the application often.

---

## Isolation Mode

In isolation mode, the template **cannot** use `$this` or `self`.
Instead, Luminova gives you a clean `$self` object with only what the view exposes.

```php
// /resources/Views/index.php

// View options
$self->_title;

// Application properties
$self->app->propertyName;
```

**Use this when:**

You want a stricter boundary between the template and the rest of the app.
Useful for cleaner structure, better security, and easier testing.

---

## Template Scope Options

Luminova makes a set of variables automatically available inside every template.
These variables give you details about the current view and help you control how the template behaves.

These options are split into two groups: **Immutable** (system-controlled) and **Mutable** (you can override them).

---

### Immutable Options

These are set by the system and **cannot** be changed when rendering a view.

**Note:**
`$scope` in the below examples refers to:

* **`$this` in Global Mode** – Refers to the current view class instance. You can access view methods and application properties directly.
* **`$self` in Isolation Mode** – Refers to an instance of `Luminova\Template\Engines\Scope`, which safely exposes only allowed view properties and the application object.

> **Note:**
> Accessing `$self` in **non-isolation mode** will throw a `RuntimeException`.
> It is also recommended **not to override `$self`** inside the template file, as it may break the intended scope and guard functionality.

---

### View Type

Variable Name: `viewType` *(string)*

Represents the type of template content being rendered (e.g, `html`, `xml` etc..).

**Examples:**

```php
// resources/Views/example.php

// When variable prefixing is enabled
echo $scope->_viewType; 

// When variable prefixing is disable
echo $scope->viewType;
```

---

### Current Template

Variable Name: `active` *(string)*

The name of the current template file without the extension (e.g, `index`, `about` etc..).

**Examples:**

```php
// resources/Views/example.php

// When variable prefixing is enabled
echo $scope->_active; 

// When variable prefixing is disable
echo $scope->active;
```

---

### Assets

Variable Name: `asset` *(string)*

The base path to your public assets directory.

Use it as the starting point before adding the final file path.

**Examples:**

```php
// resources/Views/example.php

// When variable prefixing is enabled
<img src="<?= $scope->_asset; ?>images/file.png">

// When variable prefixing is disable
<img src="<?= $scope->asset; ?>images/file.png">

// Result: /assets/images/file.png
```

> Also a global helper function available `Luminova\Funcs\asset(...)`

---

### Links

Variable Name: `href` *(string)*

The base href used for building links.

**Examples:**

```php
// resources/Views/example.php

// When variable prefixing is enabled
<a href="<?= $scope->_href; ?>about">About Us</a>

// When variable prefixing is disable
<a href="<?= $scope->href; ?>about">About Us</a>

// Result: /about
```

> Also a global helper function available `Luminova\Funcs\href(...)`

---

### Access Control

Constant Name: `ALLOW_ACCESS` *(bool: true)*

A constant you can use to block direct access to view files.

**Example:**

```php
<?php
defined('ALLOW_ACCESS') || die('Access to file is not allowed');

// Template contents here
```

> This ensure that only view class can render the template.

---

## Mutable Options

These values **can** be overridden when rendering a template.

### Template Cache

Variable Name: `noCache` *(bool)*

If `true`, the template will be skipped by the caching system if caching is enabled (see `env('page.caching')`).

**Examples:**

```php
// /app/Controllers/Http/MyController.php

public function posts(): int 
{
    return $this->view('posts', [
        'noCache' => true
    ]);
}
```

---

### Page Title

Variable Name: `title` *(string)*

The current page title.
If not set, Luminova uses: `"<active template>" – "<application name>"`

**Examples:**

```php
// /app/Controllers/Http/MyController.php

public function posts(): int 
{
    return $this->view('posts', [
        'title' => 'My Blog Post'
    ]);
}
```

---

### Page Subtitle

Variable Name: `subtitle` *(string)*

The current page subtitle.
If not set, it falls back to the title.

**Examples:**

```php
// /app/Controllers/Http/MyController.php

public function posts(): int 
{
    return $this->view('posts', [
        'subtitle' => 'My Blog Post Contents'
    ]);
}
```

---

## View Scope Options Access

Template options are extra values passed into a view during rendering.
How you access them depends on your **[Template Variable Prefixing](/configs/template#lmv-docs-variableprefixing)** setting in the configuration.

### Prefixing Enabled

When prefixing is set to `TRUE`, Luminova adds an underscore (`_`) before every option key:

```php
$this->_fooOption;
$self->_fooOption;
```

This applies in both global and isolation modes.

---

### Prefixing Disabled

When prefixing is set to `FALSE`, options appear as normal property names:

```php
$this->fooOption;
$self->fooOption;
```

> Be careful in isolation mode:
> Do not use `self` as an option name, since `self` is reserved for the isolated scope object.

---

### Raw Options

With prefixing set to `NULL`, Luminova skips property injection and exposes the raw `$options` array directly:

```php
$options['fooOption'];
```

This works the same way in both rendering modes.

> You can also fetch values manually using the view helper methods:
`getOption()` and `getOptions()`.

---

## Accessing Application Properties

Inside any template, you always have access to your main application instance through `$app`.

Both **protected** and **public** properties or methods in your `App\Application` class become available.

```php
// /app/Application.php
namespace App;

use Luminova\Foundation\Core\Application as CoreApplication;

class Application extends CoreApplication
{
    protected ?Session $session = null;

    protected function onCreate(): void 
    {
        $this->session = new Session();
        $this->session->setStorage('users');
        $this->session->start();
    }
}
```

Then inside your view:

```php
// /resources/Views/example.php

$this->app->session->isOnline();

// or in isolation mode
$self->app->session->isOnline();
```

For exporting properties in a controlled way, see the `export()` method in this [template view](/templates/views#lmv-docs-export) documentation.

---

### Other Variables

* Variable Name: `$_VIEW_FILEPATH` *(string)* The absolute file path to the current template.
* Variable Name: `$_VIEW_TYPE` *(string)* The template view content type.

**Examples:**

```php
// resources/Views/example.php

echo $_VIEW_FILEPATH; 
// /path/application/resources/Views/example.php

echo $_VIEW_TYPE;
// html
```

---

## Template Contents

Luminova templates support two main ways to provide content: **direct output** or **returning content**. How the content is processed depends on its type.

### Outputting Content

This is the traditional way, the template directly outputs HTML or text. Use this when you want the template to immediately render to the browser or output buffer.

```php
// resources/Views/example.php

<div>
    <p><?= $this->_user->name; ?></p>
    <a href="mailto:<?= $this->_user->email; ?>"><?= $this->_user->email; ?></a>
</div>
```

---

### Returning Content

Templates can also **return values** instead of echoing them. Luminova automatically **executes callables** and **formats the returned data** before sending it to the client.

#### Supported return types and behavior:

| Type                                          | Output Handling                                                |
| --------------------------------------------- | -------------------------------------------------------------- |
| **Scalar** (`string`, `int`, `float`, `bool`) | Converted to string and sent directly.                         |
| **Array**                                     | JSON-encoded and sent with `Content-Type: application/json`.   |
| **Object**                                    | JSON-encoded with `Content-Type: application/json` (unless it implements `__toString()` or `toString()`).            |
| **Stringable Object**                         | Converted to string via casting to `(string)` and sent.        |
| **SimpleXMLElement**                          | Converted to XML via `asXML()` with `Content-Type: application/xml`. |
| **DOMDocument**                               | Converted to XML via `saveXML()` with `Content-Type: application/xml`. |
| **Callable**                                  | Executed, and its return value is processed recursively using the same rules.   |

**Examples:**

Returning an array.

```php
// resources/Views/example.php

return [
    'name'  => $this->_user->name,
    'email' => $this->_user->email
];
```

Returning an object implementing `__toString()`

```php
return new class($this->_user) {
    public function __construct(private User $user) {}
    public function __toString(): string { 
        return sprintf(
            'Contact %s, via %s',
            $this->user->name,
            $this->user->email
        ); 
    }
};
```

Returning a DOMDocument.

```php
$dom = new DOMDocument();

$dom->loadXML('
    <user>
        <name>' . $this->_user->name . '</name>
        <email>' . $this->_user->email . '</email>
    </user>'
);

return $dom;
```

Returning a SimpleXMLElement → sent as XML

```php
$xml = new SimpleXMLElement('<user/>');

$xml->addChild('name', $this->_user->name);
$xml->addChild('email', $this->_user->email);

return $xml;
```

Returning a callable.

```php
return function(): array 
{
    return ['timestamp' => time()];
};
```

> **Note:** 
>
> Returning `arrays` or `objects` automatically produces **JSON output** to the client. 
> Returning `scalars`, `Stringable` objects, or `XML` types are rendered according to their type. 
> Callables are executed before output.

****

## Class Definition

* Class namespace: `Luminova\Template\View`
* This class is marked as **final** and can't be subclassed
* This class implements:
[Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)

***

## Constants

### Template Content Types 

These constants define template content type identifiers used by the framework for operations such as caching, minification, and other processing. For more rendering support see [Response Class Documentation](/templates/response.md)

| Constant | Type   | Value  | Description                              |
| :------- | :----- | :----- | :--------------------------------------- |
| `HTML`   | string | 'html' | HTML templates or content.               |
| `JSON`   | string | 'json' | JSON-formatted content.                  |
| `TEXT`   | string | 'txt'  | Plain text content.                      |
| `XML`    | string | 'xml'  | XML-formatted content.                   |
| `JS`     | string | 'js'   | JavaScript files or code blocks.         |
| `CSS`    | string | 'css'  | CSS stylesheets or inline styles.        |
| `RDF`    | string | 'rdf'  | RDF-formatted content for semantic data. |
| `ATOM`   | string | 'atom' | ATOM feed format.                        |
| `RSS`    | string | 'rss'  | RSS feed format.                         |
| `BIN`    | string | 'bin'  | Binary string content format.            |

> **Note:** 
>
> They are **not** the same as HTTP header `Content-Type` values, but are used internally for purposes such as caching, minification, and content processing. 
> If no explicit HTTP header content type is set, these constants can also be used to determine the appropriate response header.

***

## Properties

### app

Holds the application instance.

```php
public ?Luminova\Foundation\Core\Application $app = null
```

> Does not create a circular reference with the view instance.

**Examples:**

```php
$this->app; 
// or  
$self->app;
```

> **When to use:**
> Use this property when you need access to application-level services, configuration, or methods directly from within a template without instantiating or passing the application object manually.
> Use `export` for private properties.

***

## Methods

### constructor

Initialize the View object.

This constructor sets up template configuration for view management, and loads environment-based options.

```php
public __construct(?Luminova\Foundation\Core\Application $app): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$app` | **CoreApplication&#124;null** | Optional application object.<br/>If omitted, templates will not have access to the application instance via (`$this->app` or `$self->app`). |

> **Note:** You may not need to initialize this class manually as it is already available within the `Luminova\Foundation\Core\Application` class, which can be accessed inside controller classes.
> For global helper access, use `Luminova\Funcs\view(...)`.

***

### view

Sets the view template and its content type for rendering.

It resolves the template path and prepares it for rendering or later access.

Call this method to specify which view file to use, before any of these methods (`render()`, `respond()`, `promise()`, `exists()` or `info()`) are called. 

**Search Paths:**
- `/resources/Views/` — MVC view directory.
- `/app/Modules/Views/` — HMVC root view directory.
- `/app/Modules/<Module>/Views/` — HMVC module-specific views.

**Supported Content Types:**
- `html`, `json`, `text|txt`, `xml`, `js`, `css`, `rdf`, `atom`, `rss`

> The `$template` must exclude file extensions (`.php`, `.tpl`, `.twg`, etc).
> For unsupported types, use `response(...)->render(...)` for manual handling.

```php
final public view(string $template, string $type = self::HTML): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$template` | **string** | View filename without extension (e.g., `dashboard/index`). |
| `$type` | **string** | The template content type (default: `View::HTML`). |

**Return Value:**

`self` - Return instance of template view class.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If `$type` is not a supported view type.

**Example:**

Usage in Controller:

```php
// /app/Controllers/Http/FooController.php

// Render view and return status
$status = $this->app->view->view('profile', View::HTML)
    ->render(['id' => 1]);

// Render view and return content
$content = $this->app->view->view('settings', View::JSON)
    ->response(['tab' => 'privacy']);

// Render view and return promise object
$promise = $this->app->view->view('invoice', View::HTML)
    ->promise(['orderId' => 101]);

// Return template filesystem information
$info = $this->app->view->view('index', View::HTML)
    ->info();
```

Direct Usage:

```php
$view = new View(app: $application);
```

***

### render

Render and immediately send the view output.

This method renders view content with an optional parameters to make globally available
within the template view file.

```php
final public render(array<string,mixed> $options = [], int $status = 200): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<string,mixed>** | Additional parameters to pass in the template (available inside view). |
| `$status` | **int** | The HTTP status code (default: 200 OK). |

**Return Value:**

`int` - Return one of the following status codes:
- `STATUS_SUCCESS` if the view is handled successfully,
- `STATUS_SILENCE` if failed, silently terminate without error page allowing you to manually handle the state.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the view rendering fails.

**Example:**

Display template view with options:

```php
// /app/Controllers/Http/FooController.php

public function fooView(): int 
{
    return $this->app->view->view('name')
        ->render([...], 200);
}
```

***

### respond

Render the view and return the output as a string.

This method renders selected template view and return the rendered contents string.

```php
final public respond(array<string,mixed> $options = [], int $status = 200): ?string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<string,mixed>** | Additional parameters to pass in the template (available inside view). |
| `$status` | **int** | HTTP status code (default: 200 OK). |

**Return Value:**

`string|null` - Return the compiled view contents or null if no content.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the view rendering fails.

**Example:**

Display your template view or send as an email:

```php
// /app/Controllers/Http/MailController.php

use Luminova\Utility\Email\Mailer;

public function mailView(): int 
{
    $content = $this->app->view->view('name')
        ->respond(['foo' => 'bar'], 200);

    Mailer::to('peter@example.com')->send($content);
}
```

***

### promise

Return a promise that resolves with rendered view contents.

Renders the template view file and returns a promise that resolves with the rendered contents, or rejects if the template is missing or rendering fails.

```php
final public promise(array<string,mixed> $options = [], int $status = 200): PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<string,mixed>** | Additional parameters to pass in the template (available inside view). |
| `$status` | **int** | HTTP status code (default: 200 OK). |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Return a promise that resolves with rendered view contents or rejects with an error.

**Example:**

Display your template view or send as an email:

```php
// /app/Controllers/Http/FooController.php

public function fooView(): int 
{
    $content = $this->app->view->view('name')
        ->promise(['foo' => 'bar'])
        ->then(function(string $content) {
            Mailer::to('peter@luminova.ng')->send($content);
        })->catch(function(Exception $e) {
            logger('debug', $e->getMessage());
        });
}
```

***

### info

Returns metadata about the specified view template.

Provides useful diagnostic and contextual information about a template file without rendering.

```php
final public info(?string $key = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | Optional key to retrieve a specific value. |

**Return Value:**

`array<string,mixed>|mixed` - Return the value for that key (null if not found), otherwise return metadata array.

**Example:**

```php
$info = $this->app->view->view('dashboard')
    ->info();

// Get the modified
$modified = $this->app->view->view('dashboard')
    ->info('modified');
```

***

### exists

Check if a template view file exists.

```php
final public exists(): bool
```

**Return Value:**

`bool` - Return true if the template view file exists, false otherwise.

**Example:**

```php
if($this->app->view->view('admin')->exists()){
    // Yes
}
```

***

### export

Export a class instance or fully qualified name for later access in templates.

This allows registering services, classes, or any custom object
so that it can be accessed in the template via its alias.

> Alternatively you can access public/protected properties and methods in application class directly using `$this->app->property` or `$self->app->property`.

```php
final public export(object|string $target, ?string $alias = null, bool $shared = false): true
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$target` | **object&#124;string** | The class name, object instance to expose. |
| `$alias` | **string&#124;null** | Optional alias for reference (Defaults to class class/object name). |
| `$shared` | **bool** | If true and `$target` is class name, the same instance will be reused. |

**Return Value:**

`bool` - Returns `true` if imported, otherwise throw error.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If arguments are invalid or alias already exists.

**Example:**

```php
// /app/Application.php

class Application extends CoreApplication
{
    protected function onCreate(): void 
    {
        $session = new Session();
        $session->setStorage('users');
        $session->start();

        // Export the session object for use in views
        $this->view->export($session, 'session');
    }
}
```

> **Note:**
> If `$target` is not an object, it’s treated as a class name that will be instantiated later.

---

#### Accessing Exported Data in Views

Exported properties can be accessed using their `$alias`.
If no alias is provided, the class basename is used automatically.

```php
// /resources/Views/example.php
$this->session->isOnline();

// or when using isolated mode
$self->session->isOnline();
```

You can also retrieve exports through the helper method:

```php
// /resources/Views/example.php
$this->getExport('session');
// or
$self->getExport('session');
```

***

### header

Set a single response header.

This allows you to add a response header for template rendering,

```php
final public header(string $key, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The template response header key. |
| `$value` | **mixed** | The header value for key. |

**Return Value:**

`self` - Return instance of template view class.

**Example:**

```php
$this->app->view->header('Content-Type', 'application/json');
```

***

### headers

Set multiple response headers at once.

This allows you to add a custom response headers for template rendering,

```php
final public headers(array<string,mixed> $headers): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$headers` | **array<string,mixed>** | Associative array of headers key-pair. |

**Return Value:**

`self` - Return instance of template view class.

**Example:**

```php
$this->app->view->headers([
    'Cache-Control' => 'no-cache',
    'Content-Type' => 'text/html; charset=utf-8',
]);
```

***

### setModule

Sets the HMVC module name for the current controller class.

This identifies the module that the controller belongs to, typically matching the folder name under `app/Modules/<Module>`. 

For example, a controller located at
`app/Modules/Blog/Controllers/PostController.php` should use `'Blog'` as the module name.

> Use an empty string if the controller is not part of root module (i.e., global scope).

```php
final public setModule(string $module = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$module` | **string** | The module name (e.g., 'Blog'). Must not contain slashes or backslashes. |

**Return Value:**

`self` - Return instance of template view class.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the module name contains invalid characters (e.g., slashes).

**Example:**

```php
// /app/Modules/Blog/Controllers/Http/ExampleController.php

namespace App\Modules\Blog\Controllers\Http;

use Luminova\Base\Controller;

class ExampleController extends Controller
{
    protected function onCreate(): void
    {
        $this->app->view->setModule('Blog');
    }

    //...
}
```

> **Note:** 
>
> This method is intended for **HMVC** design pattern only, and should be called once in the controller’s `__construct` or `onCreate` method—before rendering any views.

***

### setFolder

Sets the view subfolder used to locate view files within the application template view directory.

Valid base locations include:
- `resources/Views/` - MVC View directory.
- `app/Modules/Views/` - HMVC root view directory.
- `app/Modules/<Module>/Views/` - HMVC custom module view directory.

```php
final public setFolder(string $path): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | Subfolder name to look for views. |

**Return Value:**

`self` - Return instance of template view class.

> **Notes:**
> - When used in a controller's `onCreate` or `__construct`, all views for that controller will be searched in this folder.
> - When used in the application's `onCreate` or `__construct`, it sets the default folder for all views.
> - When used in a controller method before rendering, only that method's view resolution is affected.

**Example:**

In this example, the `setFolder` method is used to direct the framework to look for template files in a custom directory (`resources/Views/example/`) for all views within a specific controller.

```php
// /app/Controllers/Http/ExampleController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;

class ExampleController extends Controller
{
    protected function onCreate(): void
    {
        $this->app->view->setFolder('example');
    }

    public function show(): int
    {
        return $this->view('show');
    }

    public function foo(): int
    {
        // Overrides the default folder for the current method.
        $this->app->view->setFolder('example2');

        return $this->view('foo');
    }
}
```

***

### setUriPathDepth

Set URI relative parent directories depth.

This method allows you to manually set how many parent directories (`../`) to prepend to asset and link paths.

It overrides Luminova's default auto-detection based on the current URI depth.
Use it when working with nested views or custom routes that require explicit relative path control.

**Example depth values:**

- `1` → `../`
- `2` → `../../`

```php
final public setUriPathDepth(int $depth): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$depth` | **int** | Number of `../` segments to prepend. |

**Return Value:**

`self` - Return instance of template view class.

> Useful when custom routing or nested views affect the correct relative path for assets.

**Example:**

Force asset paths to go up **one** directory:

```php
// /app/Controllers/Http/ExampleController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;

class ExampleController extends Controller
{
    #[Route('app/account', methods: ['GET'])]
    protected function example(): void
    {
        // Override auto depth detection to use only one "../"
        $this->app->view->setUriPathDepth(1);

        return $this->view('account');
    }
}
```

In the corresponding view file:

```php
// /resources/Views/account.php

echo asset('images/logo.png');
// OR
echo "{$this->_asset}images/logo.png";
// Generates: ../assets/images/logo.png

echo href('account/foo');
// OR
echo "{$this->_href}account/foo";
// Generates: ../account/foo
```

> Normally, accessing `https://example.com/app/account` would trigger auto-detection of `../../` in development environment like `XAMPP` due to the URI depth (`public/app/account`).
> But since we explicitly set the depth to `1`, only a single `../` is prepended to asset and link paths.

***

### codeblock

HTML codeblock minification behavior.

This method allows you to configure whether HTML `<code>` blocks should be excluded from minification
and optionally display a copy button.

```php
final public codeblock(bool $minify, bool $button = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$minify` | **bool** | Whether to skip minifying `<code>` blocks. |
| `$button` | **bool** | Whether to show a &quot;copy&quot; button inside code blocks (default: false). |

**Return Value:**

`self` - Return instance of template view class.

***

### noCaching

Exclude specific templates from being cached.

By default when `page.caching = true` is enabled in `env`, all view templates are cached and reused.
This method allows you to exclude one or more templates name from caching it rendered content. Useful for selectively exclusion, ensuring that dynamic content is always up-to-date. 

```php
final public noCaching(string|string[] $template): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$template` | **string&#124;string[]** | A single template name or an array of template names to ignore from caching. |

**Return Value:**

`self` - Return instance of template view class.

**Example:**

To disable caching for specific a view templates, example `insert` and `edit` will be excluded while allowing cache for other templates.

```php
// /app/Application.php

namespace App;

use Luminova\Foundation\Core\Application as CoreApplication;

class Application extends CoreApplication
{
    protected function onCreate(): void
    {
        $this->view->noCaching(['insert', 'edit']);
    }
}
```

> This can be configured either in your `routes/<context>.php` file, `Application` class or your view `Controller` class. > The recommended approach is within the application `onCreate` or `__construct` method.

***

### cacheOnly

Specify templates that should be cached exclusively.

This method allows you to explicitly add one or more templates that should only be cached.

Unlike `noCaching` method, the `cacheOnly` method allows you to specify which view templates should be exclusively cached while others are ignored. This is useful when you want to ensure that only certain views are cache and excluded others entirely.

```php
final public cacheOnly(string|string[] $template): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$template` | **string&#124;string[]** | A single template name or an array of template names to allow for caching. |

**Return Value:**

`self` - Return instance of template view class.

**Example:**

Enable caching for only `home`, `about`, and `contact` views

```php
// /app/Application.php

namespace App;

use Luminova\Foundation\Core\Application as CoreApplication;

class Application extends CoreApplication
{
    protected function onCreate(): void 
    {
        $this->view->cacheOnly(['home', 'about', 'contact']);
    }
}
```

> **Applicable Usage:**
>
> If you have `100` views but only want to cache three of them (e.g,`home`, `about`, and `contact`). Using `noCaching` would mean listing all the other `97` views.
> Instead, use `cacheOnly` to list just the views you want cached. This way, only those three are cached, and everything else is automatically excluded.

***

### cacheable

Enable or disable view caching globally at the controller or application level.

```php
final public cacheable(bool $cacheable = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$cacheable` | **bool** | Whether to allow view caching. |

**Return Value:**

`self` - Return instance of template view class.

> If used in a controller’s `onCreate()` or `__construct()`, all views under that controller will follow this rule.
> If used in the application class, applies to all views globally.
> Useful in api context to manually handle caching.

**Example:**

The `cacheable` method can be called in the controller's or application's `onCreate` or `__construct` method.

```php
// /app/Application.php

namespace App\Controllers\Http;

use Luminova\Foundation\Core\Application as CoreApplication;

class Application extends CoreApplication
{
    protected function onCreate(): void
    {
        $this->view->cacheable(false);
    }
}
```

> The `cacheable` method provides a straightforward way to control view caching by setting the caching status globally or for specific controllers, you can ensure that dynamic content remains current without being cached.

***

### cache

Enables view response caching for reuse on future requests.

When called, this method marks the response to be cached. You can optionally
specify a custom expiration time; otherwise, the default from the `.env` config will be used.

```php
final public cache(\DateTimeInterface|int|null $expiry = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$expiry` | **\DateTimeInterface&#124;int&#124;null** | Optional cache expiration. Accepts:<br />- `DateTimeInterface` for specific expiration date/time,<br />- `int` for duration in seconds,<br />- `null` to use the default expiration from configuration. |

**Return Value:**

`self` - Return instance of template view class.

**Example:**

Basic usage with conditional caching.

```php
// /app/Controllers/Http/FooController.php

public function fooView(Luminova\Base\Model $model): int 
{
    $cache = $this->app->view->cache(60); // Cache for 60 seconds

    if ($cache->expired()) {
        $heavy = $model->doHeavyProcess();

        return $cache->view('foo')
            ->render(['data' => $heavy]);
    }

    return $cache->reuse(); // Reuse the previously cached response
}
```

***

### delete

Deletes the cache entry for the current request view.

```php
final public delete(?string $version = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$version` | **string&#124;null** | Optional. Specify the application version to delete (default: `null`). |

**Return Value:**

`bool` - Return true if the cache entry was deleted; false otherwise.

***

### clear

Clears all view cache entries.

```php
final public clear(?string $version = null): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$version` | **string&#124;null** | Optional. Specify the application version to clear (default: null). |

**Return Value:**

`int` - Return the number of deleted cache entries.

**Example:**

Clear all cache entries for the current application version.

```php
$total = $this->clear();
```

Clear cache entries for a specific application version.
```php
$total = $this->clear('1.0.0');
```

***

### expired

Check if page cache has expired.

Note: the expiration check we use the time used while saving cache.

```php
final public expired(?string $type = self::HTML): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string&#124;null** | The view content extension type (default: `self::HTML`). |

**Return Value:**

`bool` - Returns true if cache doesn't exist or expired.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - Throw if the cached version doesn't match with the current view type.

> **Note:** 
> 
> Before calling this method, you must first call `cache` method instantiate it for use.
> 
> For the expiration check, we use the expiration set in `env` while saving cache, also don't worry about the cache key as it handled internally to save your precious time.

***

### reuse

Reuse previously cached view content if available.

```php
final public reuse(): int
```

**Return Value:**

`int` - Returns one of the following status codes:
- `STATUS_SUCCESS` if cache was found and successfully reused,
- `STATUS_SILENCE` if no valid cache was found — silent exit, allowing manual fallback logic.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If called without first calling `cache()` method.

**Example:**

```php
public function homepage(Luminova\Base\Model $model): int
{
    // Enable caching for 2 minutes
    $this->app->view->cache(120); 
    
    if ($this->app->view->reuse() === STATUS_SUCCESS) {
        // Cache hit, response already sent
        return STATUS_SUCCESS; 
    }
    
    $data = $model->getHomepageData();
    return $this->app->view->view('home')
        ->render(['data' => $data]);
}
```

***

### onExpired

Conditionally renew cached view if expired, otherwise reuse it.

```php
final public onExpired(string $type, Closure $renew, mixed ...$arguments): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | Type of content to check cache for, (e.g. `View::HTML`, `View::JSON`). |
| `$renew` | **\Closure** | Callback to execute if cache has expired. Should return `STATUS_SUCCESS` or `STATUS_SILENCE`. |
| `$arguments` | **mixed** | Optional arguments to pass to the `$renew` callback. DI not supported. |

**Return Value:**

`int` - Return the status code:
- Status code from the callback if cache is expired or bypassed,
- Otherwise, status code from `reuse()` if valid cache is used.

**Example:**

```php
public function books(): int 
{
	return $this->app->view->cache(300)->onExpired(View::HTML, function(string $group): int {
		return $this->view('books', [
         'group' => $group,
			'books' => [
               ['id' => 100, 'name' => 'Basic Programming'],
               ['id' => 1001, 'name' => 'Advanced Programming']
         ]
		]);
	}, 'programming');
}
```

***

### redirect

Redirect to a different URI or route.

```php
final public redirect(string $uri, int $status = 302): never
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uri` | **string** | The target URI or route. |
| `$status` | **int** | The HTTP redirect status code (default: 302). |

***

### link

Generate a relative URI from the public root directory.

This method creates a relative path for routes or public assets (e.g., CSS, JS, images)
starting from the controller’s public directory. In production, it returns a root-relative path.
In development, it calculates the relative path based on URI segments.

```php
final public static link(string $route = '', ?int $depth = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$route` | **string** | Optional route or file path to append after the base path. |
| `$depth` | **int&#124;null** | Optional depth to parent directory (used in development mode only).<br />If null, the method auto-detects the depth. |

**Return Value:**

`string` - Return a relative or root-based URL to the file or route.

**Example:**

```html
<link href="<?= $this->link('assets/css/main.css') ?>" rel="stylesheet">
<a href="<?= $this->link('about') ?>">About Us</a>
```

***

### hasOption

Checks if a given name exists as a view option.

Useful for determining whether a value was set in the current view context via configuration options.

```php
final public hasOption(string $name): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The option name to check. |

**Return Value:**

`bool` - Returns `true` if the option exists, otherwise `false`.

***

### isExported

Checks if a given property exists as an exported alias.

Useful for determining whether a value was made available to the
current view via the `export()` method.

```php
final public isExported(string $name): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The export name or alias to check. |

**Return Value:**

`bool` - Returns `true` if the export exists, otherwise `false`.

***

### getExport

Retrieves a property exported from the application context.

```php
final public getExport(string $name): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The export property class name or alias used when exporting. |

**Return Value:**

`mixed|null` - Return the export value, or null if not found.

***

### getOption

Retrieves a value from the view's context options.

```php
final public getOption(string $key): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The option name. |

**Return Value:**

`mixed|null` - Returns the option value if set; otherwise, checks for the key prefixed with `_` and returns that value if found. Returns null if neither exists.

> **Note:** 
>
> Supports fallback to prefixed keys for flexibility and backward compatibility.

***

### getExports

Returns all exported application properties available to the view.

```php
final public getExports(): array<string,mixed>
```

**Return Value:**

`array<string,mixed>` - Return an associative array of exported properties.

***

### getOptions

Returns all view context options extracted for the view.

```php
final public getOptions(): array<string,mixed>
```

**Return Value:**

`array<string,mixed>` - Return an associative array of view context options.

***

### getTemplate

Get the template filename.

By default, returns the resolved template name (which may differ from the original
if a fallback like `404` was used). 

Pass `false` to get the original template name exactly as specified in the controller.

```php
final public getTemplate(bool $resolved = true): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$resolved` | **bool** | Whether to return the resolved filename (true) or the original (false). |

**Return Value:**

`string` - Return the current view template file basename.

***

### getStatusCode

Get the current HTTP status code.

Useful when you need to access the same status code that was used to render the template directly from inside the template itself.

```php
final public getStatusCode(): string
```

**Return Value:**

`int` - Return the HTTP status code (e.g., `200`, `404`, `500`).

***

### toTitle

Converts the current view template name to a formatted page title.

Replaces underscores, dashes, hyphens, and commas with spaces, capitalizes words,
and optionally appends the application name as a suffix.

```php
final public toTitle(bool $suffix = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$suffix` | **bool** | Whether to append the app name as a suffix (default: false). |

**Return Value:**

`string` - Return the formatted page title.