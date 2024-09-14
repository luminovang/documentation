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

* Class namespace: `\Luminova\Core\CoreApplication`
* This class is an **Abstract class**
* Inherited class: `\Luminova\Template\View`

***

### Example

This is an example a basic application class may look like using the `__construct` method.  

```php 
<?php
namespace App;

use \Luminova\Core\CoreApplication;
class Application extends CoreApplication  
{
	public function __construct()
	{
		parent::__construct();
	}
}
```

Alternatively, this is an of application class may using the `onCreate` method.  

```php 
<?php
namespace App;

use \Luminova\Core\CoreApplication;
class Application extends CoreApplication  
{
	protected function onCreate(): void
	{
		//...
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

###  getInstance

The singleton `getInstance` method allows you to return a shared `static` instance  of your application class.

```php
public static final getInstance(): static
```

***

### getView

The `getView` method allows you to the current view `URI` segments.

```php
public final getView(): string
```

**Return Value:**

`string` - Returns current view URI segment.

***

## Application Events
To handle command events in your application controller class based on actions, below are the list of events to listen to.

***

### __on

The `__on` method in your application class allows you trigger an application event methods.

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

The `onCreate` method in your application class serves as an alternative to the `__construct()` method. It is invoked after all necessary initializations have been performed, ensuring that your application is fully initialized before any operations are carried out on template views.

```php
protected onCreate(): void
```

> This method provides a convenient hook for executing additional setup logic or configurations specific to your application after the standard initialization process.

***

### onFinish

Application on finish even, which triggers once application router has finished handling request.
This trigger weather error occurs or not.

```php
protected onFinish(): void
```

***

### onFinish

Application on finish even, which triggers once application router has finished handling request.
This trigger weather error occurs or not.

```php
protected onFinish(): void
```

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
