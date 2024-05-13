## Base Application

***

## Overview

The base application controller lays the foundation for handling your software development logic, by serving as the base application class other services may rely on.

***

The Base Application serves as the foundational framework upon which your web application are built. It encapsulates essential functionalities, design patterns, and architectural principles that provide a solid starting point for developing complex applications. Essentially, the Base Application represents the core infrastructure and scaffolding upon which you can construct your application logic.

***

* Class namespace: `\Luminova\Base\BaseApplication`
* This class is an **Abstract class**
* Parent class: `\Luminova\Application\Foundation`

***

### Example

Your application class is located in `/app/Controllers/` directory.
This is how your application should look something like this  

```php 
namespace App\Controllers;

use \Luminova\Base\BaseApplication;
class Application extends BaseApplication  
{
	public function __construct()
	{
		parent::__construct();
	}
}
```

Alternatively, you can use the `onCreate` method, ensuring that your application is initialized before the `onCreate` method is called.

```php 
namespace App\Controllers;

use \Luminova\Base\BaseApplication;
class Application extends BaseApplication  
{
	protected function onCreate(): void
	{

	}
}
```

***

## Properties

Framework version name.

```php
public const VERSION
```

Minimum required php version.

```php
public const MIN_PHP_VERSION
```

Command line tool version.

```php
public const NOVAKIT_VERSION
```

Router instance made available in your application controller.

```php
public \Luminova\Routing\Router $router
```

***

## Methods

###  getInstance

Get the base application instance shared singleton class instance.

```php
public static getInstance(): static
```

***

### getView

Get the current view segment.

```php
public getView(): string
```

**Return Value:**

`string` - Returns current view segment.

***

### getBase

Get application base path from router.

```php
public getBase(): string
```

**Return Value:**

`string` - Return base directory.

***

### onCreate

Application on create method, an alternative method to __construct()

```php
protected onCreate(): void
```

***

### version

Get the framework version name or code.

```php
public static version(bool $integer = false): string|int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$integer` | **bool** | Return version code or version name (default: name). |