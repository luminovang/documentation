# Core Application Architecture

***

## Overview

The Luminova Core Application represents the infrastructure and scaffolding upon which you can construct your application logic extend to build on.

***

## Introduction

The Luminova **Core Application** is the base class that every Luminova application is built on. It provides the essential features, patterns, and structure you need to develop both simple and advanced applications.

Think of it as the foundation and framework that hold your application together.

**Your application class must:**

* Be located in the `/app/` directory.
* Always be named as **`Application`**, this name cannot be changed.

***

## Examples

### Basic Application Class

This is a basic example of how to extend the application class, using the `__construct` method to initialize your routable module controllers namespace for HMVC application and other services as needed.

**Using `__construct` to Invoke Parent Initialization:**

```php
// /app/Application.php
namespace App;

use Luminova\Foundation\Core\Application as CoreApplication;

class Application extends CoreApplication  
{
  public function __construct()
  {
    // Initialize parent class.
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

use Luminova\Foundation\Core\Application as CoreApplication;

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
use Luminova\Foundation\Core\Application as CoreApplication;

class CustomRouter implements RouterInterface
{
  public function __construct(CoreApplication $app){}

  // Implements route parsing, logging, or middleware logic here
}
```

**Step 2: Extend the Application and Override the Method**

```php
// /app/Application.php

namespace App;

use Luminova\Foundation\Core\Application as CoreApplication;
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
> Ensure that your `CustomRouter` class implements the **[Luminova\Interface\RouterInterface](/interface/classes.md#routerinterface)** to maintain full framework compatibility.
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

* Class namespace: `Luminova\Foundation\Core\Application`
* This class is an **Abstract class**

> **Note:** 
> Cloning the application object resets its `$view` property to null.
> Avoid relying on internal `__get` or `__call` magic for `$view`. This behavior may be removed in future releases. Access views explicitly (e.g., `$this->view->fooView` or `$this->app->view->fooView`) instead of using `$this->fooView` or `$this->app->fooView` in controller classes."**

***

## Properties

### router

Instance of application routing system.

```php
public ?Luminova\Interface\RouterInterface $router = null;
```

For more information about [Luminova Routing System](/routing/system.md) documentation.

---

### view

The template view object (lazy).

```php
public ?Luminova\Template\View<LazyObjectInterface> $view = null;
```

For detailed information see [Template View Class](/templates/views.md) documentation.

***

## Methods

The methods and properties of the base application are a combination of those inherited from the `View`, your `Application`, and the `CoreApplication` class. These classes ensure that properties and methods are accessible wherever the application object is invoked, based on their visibility. You can access them accordingly.

### setInstance

Set the singleton instance to a new application instance.

```php
public static setInstance(Luminova\Foundation\Core\Application $app): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$app` | **CoreApplication** | The application instance to set. |

**Return Value:**

`static` - Return the new shared application instance.

***

### getInstance

The singleton `getInstance` method allows you to return a shared `static` instance  of your application class.

```php
public static getInstance(): static
```

**Return Value:**

`static` - Return a shared application instance.

***

### getRouterInstance

Returns an instance of the application routing system.

This method should return instance of a class that implements `RouterInterface` to be use as default application routing system.

```php
protected getRouterInstance(): ?\T<RouterInterface>
```

**Return Value:**

`T<Luminova\Interface\RouterInterface>|null` - Return instance of the routing system, or null to use default.

> You may override this method in your application class to return a custom implementation of the routing system by extending or replacing the default router.

***

### getView

Get the current view instance.

Returns either a `View` object or a lazy-loaded wrapper implementing `LazyObjectInterface`. If no view has been initialized, this method creates a new `View` instance without assigning it to `$this->view`.

```php
public final getView(): View|View<LazyObjectInterface>
```

**Return Value:**

`Luminova\Template\View|Luminova\Template\View<Luminova\Interface\LazyObjectInterface>` - Return the current view instance or a newly created one.

> **Note:** 
> Calling this method does **not** update the `$view` property if it is null. 
> This means `$app->getView()` and `$app->view` may not reference the same object.

***

### getUri

Get the current request URI from the router.

The **getUri** method allows you to the current view `URI` segments.

```php
public final getUri(): string
```

**Return Value:**

`string` - Return the URI of the current request.

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

use Luminova\Foundation\Core\Application as CoreApplication;

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

### bind

Bind a class or interface for dependency injection (DI) in controller methods.

This lets you map an interface or class name to a concrete implementation or a closure. When a controller method type-hints a dependency, this binding ensures the right object is injected.

```php
public final bind(string $abstract, Closure|string $resolver): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$abstract` | **string** | The interface or class name to bind. |
| `$resolver` | **Closure\|string** | The concrete class or closure returning the instance. |

**Return Value:**

`self` - Return instance of application class.

**Example:**

Simple and advanced bindings:

```php
// /app/Application.php

namespace App;

use Luminova\Foundation\Core\Application as CoreApplication;

class Application extends CoreApplication
{
    protected function onPreCreate(): void 
    {
        // Bind interface to implementation
        $this->bind(MyInterface::class, MyConcreteClass::class);

        // Bind with custom logic (e.g., logger setup)
        $this->bind(\Psr\Log\LoggerInterface::class, function () {
            return new \MyApp\Log\FileLogger('/writable/logs/app.log');
        });
    }
}
```

> **Note:** Prefer class names for simple bindings. Use closures when instantiation requires logic.

**In Controller Usage:**

```php
// /app/Controllers/Http/MainController.php 

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Psr\Log\LoggerInterface;

class MainController extends Controller
{
  // Inject logger via interface class name
  public function myMethod(LoggerInterface $logger): int 
  {
    $logger->log('debug', 'Log message...');
    return STATUS_SUCCESS;
  }
}
```

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
protected onPreCreate(): void
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
protected onCreate(): void
```

#### Usage

This method is designed to execute custom setup logic or configurations specific to your application after standard initialization. Unlike the constructor, `onCreate` will **not** be called again if a new application instance is created using `new Application()`.

> **Note:** Use `onCreate` for tasks that depend on a fully initialized application, such as registering additional routes, loading configuration files, or setting up services.

***

### onDestroy

The `onDestroy` lifecycle method is triggered **once** during the application's lifecycle, specifically when the application object is destroyed. It is designed to be overridden in subclasses to handle custom cleanup tasks, such as releasing resources or closing connections.

```php
protected onDestroy(): void
```

#### Usage
You can optionally call `gc_collect_cycles()` in your implementation to force the collection of any existing garbage cycles, ensuring that memory is efficiently reclaimed.

> **Note:** Use `onDestroy` to clean up tasks that should only occur when the application is about to shut down, such as saving state or flushing logs.

***

### onStart

Lifecycle onStart hook: Triggered when the application starts handling a request.

```php
protected onStart(array $info): void {}
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$info` | **array<string,mixed>** | Request state information. |

***

### onFinish

Lifecycle onFinish hook: Triggered after a request is handled, regardless of success or failure.

```php
protected onFinish(array $info): void {}
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
- `cache`     **bool** - Whether cached version rendered or new content.
- `staticCache` **bool** - Whether is a static cached version (e.g, page.html) or regular cache (e.g, `page`).

***

### onContextInstalled

Application on context installed, which triggers once application route context has successfully registered request context.

```php
protected onContextInstalled(string $context): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$context` | **string** | The context name that was registered. |

***

### onViewPresent

Application on view presented event, which is triggered after view controller method was called.

```php
protected onViewPresent(string $uri): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uri` | **string** | The view URI that was presented. |

***

### onCommandPresent

Application on command presented event, which is triggered after command controller was called.

```php
protected onCommandPresent(array $options): void
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
protected onTerminate(array $info): bool 
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
protected onTerminated(array $info): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$info` | **array** | Contextual information related to the termination request. |

***

### onShutdown

Called after script shutdown triggered by a fatal error or forced termination.

This hook gives the application a final chance to inspect the shutdown state.
If it returns `false`, the framework will skip further error handling—allowing the application to take full control (e.g., for custom logging or rendering).

```php
public static onShutdown(array $error): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$error` | **array<string,mixed>** | The last recorded error before shutdown (if any). |

**Return Value:**

`bool` - Return `false` to take over shutdown handling, `true` to let the framework proceed.

> **Note:** 
>
> This hook only get called if shutting down because of an error.
> Returning `true` lets the framework continue with its default error page or logging flow.
