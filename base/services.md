# Base Services

***

## Overview

Define your application shared and serializable classes instance in Base Services to use it anywhere in your application.

***

## Introduction

Base Services in the Luminova Framework provide a foundational layer for implementing various business logic and functionalities within your application. These services offer versatility, enabling the retrieval of a shared instance of a class, reinitialization of a class with new arguments, or caching and serializing class instances for seamless utilization throughout your application or by third-party modules.

***

* Class namespace: `\Luminova\Base\BaseServices`
* This class implements:
[\Luminova\Interface\ServicesInterface](#interface/services-interface)
* This class is an **Abstract class**

***

### Registration

To register a class that should be discoverable by the service container, you can configure it in your controller configuration folder located at [/app/Controllers/Config/Services.php](/configs/service.md).

```php 
namespace App\Controllers\Config;

use \Luminova\Base\BaseServices;
use \Some\Class\Foo\YourServiceClassName;

class Services extends BaseServices
{
  public function bootstrap(): void
  {
    static::newService(
      YourServiceClassName::class, // Class name to register.
      null, // Class name alias (defaults to the class basename if null).
      true, // Should return shared instance.
      true, // Should serialize and store the class object.
      [] // Initialization arguments, must be array list (int keys)
    );
    //...
  }
}
```

Using global function `service` to call your class method in service.

```php 
<?php 
service('example')->doFoo();
```

Using class name.

```php 
<?php 
service(SomeClass::class)->doFoo();
```

Using service instance.

```php 
<?php 
use \Luminova\Application\Services;
Services::example()->doFoo();
```

Using global function `factory` to initialize service and call your class method in service.

```php 
<?php 
factory('services')->example()->doFoo();
```

Using factory instance.

```php 
<?php 
use \Luminova\Application\Factory;
Factory::services()->example()->doFoo();
```

To re-instantiate class with new parameters, simply pass the required values to `method()`. To prevent updating the previously stored instance, append `false` and `false` after your initialization parameters.

**Don't share instance nor don't serialize**
```php 
<?php 
service('example', 'Peter', 'PHP', false, false)->doFoo();
```

Using service instance.

```php 
<?php 
use \Luminova\Application\Services;
Services::example('Peter', 'PHP', false, false)->doFoo();
```

**Share instance but don't serialize**
```php 
<?php 
service('example', 'Peter', 'PHP', true, false)->doFoo();
```

Using factory instance.

```php 
<?php 
use \Luminova\Application\Factory;
Factory::service(true, false)->example('Peter', 'PHP')->doFoo();
```

***

## Methods

### newService

Add a service class to the service autoloading.

```php
 protected static final function newService(string $class, ?string $alias = null,  bool $shared = true,  bool $serialize = false,  array $arguments = []): true
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$class` | **class-string** | The class name to add to service. |
| `$alias` | **string&#124;null** | Service class name alias. Defaults to class name. |
| `$shared` | **bool** | Whether the instance should be shared. Defaults to true.. |
| `$serialize` | **bool** | Whether the instance should be serialized and stored in cache. Defaults to false. |
| `$arguments` | **array<int,mixed>** | Optional arguments to initialize the class with. |

**Return Value:**

`true` - Returns true if the service was added successfully, otherwise throws an exception.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - If the service already exists or class argument is not an array list.