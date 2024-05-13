# Base Services

***

## Overview

Define your application shared and serializable classes instance in Base Services to use it anywhere in your application.

***

## Introduction

Base Services in the Luminova Framework provide a foundational layer for implementing various business logic and functionalities within your application. These services offer versatility, enabling the retrieval of a shared instance of a class, reinitialization of a class with new arguments, or caching and serializing class instances for seamless utilization throughout your application or by third-party modules.

***

* Class namespace: `\Luminova\Base\BaseServices`
* Parent class: [\Luminova\Application\Services](/application/services.md)
* This class implements:
[\Luminova\Interface\ServicesInterface](#interface/services-interface)
* This class is an **Abstract class**

***

### Registration

To register a class that should be discoverable by the service container, you can configure it in your controller configuration folder located at `/app/Controllers/Config/Services.php`.

```php 
namespace App\Controllers\Config;

use \Luminova\Base\BaseServices;
use \Some\Class\Foo\Example;

class Services extends BaseServices
{
    public static function bootstrap(): void
    {
        static::addService(Example::class, "Test Argument", true, true);
				//...
    }
}
```

Using global function `service` to call your class method in service.

```php 
<?php 
service('example')->doFoo();
```

Using service instance.

```php 
<?php 
\Luminova\Application\Services::example()->doFoo();
```

Using global function `factory` to initialize service and call your class method in service.

```php 
<?php 
factory('services')->example()->doFoo();
```

Using factory instance.

```php 
<?php 
\Luminova\Application\Factory::services()->example()->doFoo();
```

To re-instantiate an 'example' class with new parameters, simply pass the desired values to 'example()'. To prevent updating the previously stored instance, append 'false' and 'false' after your initialization parameters.

**Don't share instance nor don't serialize**
```php 
<?php 
service('example', 'Peter', 'PHP', false, false)->doFoo();
```

Using service instance.

```php 
<?php 
\Luminova\Application\Services::example('Peter', 'PHP', false, false)->doFoo();
```

**Share instance but don't serialize**
```php 
<?php 
service('example', 'Peter', 'PHP', true, false)->doFoo();
```

Using factory instance.

```php 
<?php 
\Luminova\Application\Factory::services(true, false)->example('Peter', 'PHP')->doFoo();
```

***

## Methods

### addService

Add instance or class name to service shared instance

```php
protected static addService(string|object $classOrInstance, ...$arguments): true
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$classOrInstance` | **string &#124;object** | Class name or instance of a class |
| `$arguments` | **arguments** | Arguments to initialize class with. |
| `$shared` | **bool** | Whether class the instance should be shared or not (default: true). |
| `$serialize` | **bool** | Whether the instance should be serialized and store in cache or not, (default: false). |

**Return Value:**

`true` - Return true service was added, otherwise throw an excption.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes#RuntimeException) - If service name already exist or unable to initiate class.

> *Note:*
> 
> The last 2 argument should be boolean value to indicate whether shared instance or serialized cached