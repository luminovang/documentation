# Core Application Architecture Class

***

## Overview

The base application controller lays the foundation for handling your software development logic, by serving as the base application class other services may rely on.

***

## Introduction

The Core Application serves as the foundational framework base architecture upon which your web application are built. It encapsulates essential functionalities, design patterns, and architectural principles that provide a solid starting point for developing complex applications. Essentially, the Core Application represents the core infrastructure and scaffolding upon which you can construct your application logic.

Your application class must be located in the `/app/` directory, and its name must remain unchanged under any circumstance.

Additionally application class inherited the methods within the [\Luminova\Template\View](/templates/views.md) trait class.
To lean more on how to render template views refer to the documentation.

***

## Class Definition

* Class namespace: `\Luminova\Core\CoreApplication`
* This class is an **Abstract class**
* Inherited class: `\Luminova\Template\View`

***

### Example

This is an example a basic application class may look like using the `__construct` method.  

```php 
// /app/Application.php
<?php
namespace App;

use \Luminova\Core\CoreApplication;

class Application extends CoreApplication  
{
	public function __construct()
	{
		parent::__construct();
        $this->router->addNamespace('\\App\\Modules\\Blogs\\Controllers\\');
        // ...
	}
}
```

Alternatively, this is an of application class may using the `onCreate` method.  

```php 
// /app/Application.php
<?php
namespace App;

use \Luminova\Core\CoreApplication;

class Application extends CoreApplication  
{
	protected function onCreate(): void
	{
        $this->router->addNamespace('\\App\\Modules\\Blogs\\Controllers\\');
		// ...
	}
}
```

***

## Properties

### router

The application  router class instance.

```php
public ?Router $router = null;
```
***

## Methods

The methods and properties of the base application are a combination of those inherited from the `View`, your `Application`, and the `CoreApplication` class. These classes ensure that properties and methods are accessible wherever the application object is invoked, based on their visibility. You can access them accordingly.

### setInstance

Set the singleton instance to a new application instance.

```php
public static setInstance(\Luminova\Core\CoreApplication $app): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$app` | **CoreApplication** | The application instance to set. |

**Return Value:**

`static` - Return the new shared application instance.

***

###  getInstance

The singleton `getInstance` method allows you to return a shared `static` instance  of your application class.

```php
public static getInstance(): static
```

**Return Value:**

`static` - Return a shared application instance.

***

### getView

The `getView` method allows you to the current view `URI` segments.

```php
public final getView(): string
```

**Return Value:**

`string` - Returns current view URI segment.

***

## Lifecycle Hooks & Events

To handle command events in your application controller class based on actions, below are the list of events to listen to.

***

### __on

The `__on` method in your application class allows you trigger an application event or hook methods.

```php
public __on(string $event, mixed ...$arguments): void 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$event` | **string** | The event method name to trigger. |
| `...$arguments` | **mixed** | [mixed ...$] Optional event method arguments. |

***

### onCreate

The `onCreate` lifecycle method in your application class provides an alternative to the constructor (`__construct()`). It is triggered **once** during the application's lifecycle, immediately after all necessary initializations are completed. This ensures the application is fully prepared before any template views or additional operations are executed.

```php
protected function onCreate(): void
```

#### Usage

This method is designed to execute custom setup logic or configurations specific to your application after standard initialization. Unlike the constructor, `onCreate` will **not** be called again if a new application instance is created using `new Application()`.

> **Note:** Use `onCreate` for tasks that depend on a fully initialized application, such as registering additional routes, loading configuration files, or setting up services.

***

### onDestroy

The `onDestroy` lifecycle method is triggered **once** during the application's lifecycle, specifically when the application object is destroyed. It is designed to be overridden in subclasses to handle custom cleanup tasks, such as releasing resources or closing connections.

```php
protected function onDestroy(): void
```

#### Usage
You can optionally call `gc_collect_cycles()` in your implementation to force the collection of any existing garbage cycles, ensuring that memory is efficiently reclaimed.

> **Note:** Use `onDestroy` to clean up tasks that should only occur when the application is about to shut down, such as saving state or flushing logs.

***

### onStart

Lifecycle onStart hook: Triggered when the application starts handling a request.

```php
protected function onStart(array $info): void {}
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$info` | **array<string,mixed>** | Request state information. |

***

### onFinish

Lifecycle onFinish hook: Triggered after a request is handled, regardless of success or failure.

```php
protected function onFinish(array $info): void {}
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$info` | **array<string,mixed>** | Request controller information. |

**Class Info keys:**

- `filename`  **(string|null)** Optional controller class file name.
- `namespace` **(string|null)** Optional controller class namespace.
- `method`    **(string|null)** Optional controller class method name.
- `attrFiles` **(int)** Number of controller files scanned for matched attributes.
- `cache`     **(bool)** Weather cached version rendered or new content.
- `staticCache` **(bool)** Weather is a static cached version (e.g, page.html) or regular cache (e.g, `page`).

***

### onContextInstalled

Application on context installed, which triggers once application route context has successfully registered request context.

```php
protected function onContextInstalled(string $context): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$context` | **string** | The context name that was registered. |

***

### onViewPresent

Application on view presented event, which is triggered after view controller method was called.

```php
protected function onViewPresent(string $uri): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uri` | **string** | The view URI that was presented. |

***

### onCommandPresent

Application on command presented event, which is triggered after command controller was called.

```php
protected function onCommandPresent(array $options): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array** | The command options that was presented. |
