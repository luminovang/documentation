# Base Application Architecture

***

## Overview

The base application controller lays the foundation for handling your software development logic, by serving as the base application class other services may rely on.

***

## Introduction

The Core Application serves as the foundational framework base architecture upon which your web application are built. It encapsulates essential functionalities, design patterns, and architectural principles that provide a solid starting point for developing complex applications. Essentially, the Core Application represents the core infrastructure and scaffolding upon which you can construct your application logic.

Your application class must be located in the `/app/` directory, and its name must remain unchanged under any circumstance.

Additionally, application class inherited the methods within the [\Luminova\Template\View](/templates/views.md) trait class.
To learn more on how to render template views refer to the documentation.

***

## Usages

### Basic Application Class Example

This is a basic example of how to extend the application class using the `__construct` method to initialize your routing and other services.

**Using `__construct` to Invoke Parent Initialization:**

```php
// /app/Application.php
namespace App;

use Luminova\Core\CoreApplication;

class Application extends CoreApplication  
{
    public function __construct()
    {
        parent::__construct();

        // For custom HMVC model 
        $this->router->addNamespace('\\App\\Modules\\Blogs\\Controllers\\');
        // Additional setup...
    }
}
```

---

### Overriding the onCreate Hook

Alternatively, you can override the `onCreate` lifecycle hook, which is called automatically after the application is initialized.

**Using `onCreate` for Initialization:**

```php
// /app/Application.php
namespace App;

use Luminova\Core\CoreApplication;

class Application extends CoreApplication  
{
    protected function onCreate(): void
    {
        // Custom HMVC modules
        $this->router->addNamespace('\\App\\Modules\\Blogs\\Controllers\\');
        // Additional setup...
    }
}
```

> **Tip:**
> Prefer using `onCreate()` when extending the application class unless you have specific reasons to override the constructor. This ensures smooth lifecycle and internal initialization flow.

---

### Custom Routing System

To use your own routing system in Luminova, in your application class `Application`, override the `getRouterInstance()` method to return an instance of your custom router.

---

**Step 1: Create Your Custom Router**

```php
// /app/Utils/CustomRouter.php

namespace App\Routing;

use Luminova\Interface\RouterInterface;
use Luminova\Core\CoreApplication;

class CustomRouter implements RouterInterface
{
    public function __construct(CoreApplication $app){

    }

    // Implements route parsing, logging, or middleware logic here
}
```

**Step 2: Extend the Application and Override the Method**

```php
// /app/Application.php

namespace App;

use Luminova\Core\CoreApplication;
use Luminova\Interface\RouterInterface;
use App\Utils\CustomRouter;

class Application extends CoreApplication
{
    /**
     * Override the default router with a custom one.
     *
     * @return RouterInterface
     */
    protected function getRouterInstance(): ?RouterInterface
    {
        return new CustomRouter($this);
    }
}
```

> **Note:**
> Ensure that your `CustomRouter` class implements the **[Luminova RouterInterface](/interface/classes.md#routerinterface)** to maintain full framework compatibility.
>
> When implementing a custom routing system, you are responsible for handling all routing-related features, including:
>
> * Attribute-based route discovery
> * Static file/content serving
> * Console/command routing
> * Any additional services managed by the default routing system
>
> It is **strongly recommended** to use the default Luminova routing system to take full advantage of all built-in features and minimize implementation overhead.

***

## Class Definition

* Class namespace: `\Luminova\Core\CoreApplication`
* This class is an **Abstract class**
* Inherited class: `\Luminova\Template\View`

***

## Properties

### router

The application  router class instance.

```php
public ?Luminova\Interface\RouterInterface $router = null;
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

###  getRouterInstance

Returns an instance of the application routing system.

```php
protected getRouterInstance(): ?RouterInterface<\T>
```

**Return Value:**

`Luminova\Interface\RouterInterface<\T>|null` - Return instance of the routing system, or null to use default.

> You may override this method in your application class to return a custom implementation of the routing system by extending or replacing the default router.

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

### terminate

Trigger application early termination.

This method allows you to terminate the application execution early.

```php
public final terminate(array $info = []): void 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$event` | **string** |Additional termination information to be included in `onterminated` event hook. |

**Example:**

Terminates application:

```php
// /app/Application.php
namespace App;

use Luminova\Core\CoreApplication;

class Application extends CoreApplication
{
	// Before application is object created
	protected function onPreCreate(): void 
	{
		if($instance->ofSomethingIsTrue()){
			$this->terminate([
				'foo' => 'bar'
			]);
		}
	}

	// Before application is terminated
	protected function onTerminate(array $info): bool 
	{
		if(isset($info['uri']) && str_starts_with($info['foo'], '/api/')){
			// Allow termination
			return true;
		}

		// Deny Termination
		return false;
	}

	// After application has been terminated
	protected function onTerminated(array $info): void 
	{
		Logger::debug('Application was terminated', $info);
	}
}
```
> **Note:** The `terminate` method should be call before object creation.

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

### onPreCreate

Lifecycle onPreCreate hook: Triggers once before application object creation.

This allows you to override or create a custom initialization logic before routing system is initialized.

```php
protected function onPreCreate(): void
```

**Example:**

Using Luminova Rate Limiter:

```php
use Luminova\Security\RateLimiter;

protected function onPreCreate(): void 
{
    $rate = new RateLimiter();
    if(!$rate->check()->isAllowed()){
        $rate->respond();

        $this->terminate(); // Optionally terminate application.
    }
}
```

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

- `filename`  **string|null** - Optional controller class file name.
- `namespace` **string|null** - Optional controller class namespace.
- `method`    **string|null** - Optional controller class method name.
- `attrFiles` **int** - Number of controller files scanned for matched attributes.
- `cache`     **bool** - Weather cached version rendered or new content.
- `staticCache` **bool** - Weather is a static cached version (e.g, page.html) or regular cache (e.g, `page`).

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

***

### onTerminate

Called before the application is allowed to terminate.

This method is invoked internally after `terminate()` is called. It determines whether the application termination should proceed or be canceled.

```php
protected function onTerminate(array $info): bool 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$info` | **array** | Additional termination context data passed from `terminate()`. |

**Return Value:**

`bool` - Return `true` to allow termination or `false` to cancel it.

> You can override this method to inspect the `$info` payload and return `true` to allow termination or `false` to prevent it. 
>
> If termination is allowed, the `onTerminated()` method will be triggered.

***

### onTerminated

Triggered after the application has been terminated.

This method is called only if `onTerminate()` returns `true`, indicating that the application is allowed to terminate. Use this hook to perform any final cleanup, logging, or notification logic after termination.

```php
protected function onTerminated(array $info): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$info` | **array** | Contextual information related to the termination request. |

