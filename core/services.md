# Core Custom Service Layer Autoloading Class

***

## Overview

Define your application shared and serializable classes instance in Base Services to use it anywhere in your application.

***

## Introduction

Core Services in the Luminova Framework provide a foundational layer for implementing various business logic and functionalities within your application. These services offer versatility, enabling the retrieval of a shared instance of a class, reinitialization of a class with new arguments, or caching and serializing class instances for seamless utilization throughout your application or by third-party modules.

***

* Class namespace: `\Luminova\Base\CoreServices`
* This class implements:
[\Luminova\Interface\ServicesInterface](/interface/classes.md#servicesinterface)
* This class is an **Abstract class**

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

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the service already exists or class argument is not an array list.

***

## Examples

### Service Registration

To register a class that should be discoverable in service, you must configure your class in service configuration folder located at [/app/Config/Services.php](/configs/service.md).

Additionally, enable the service feature by setting `feature.app.services = enable` in your environment configuration file.

```php 
namespace App\Config;

use \Luminova\Base\CoreServices;
use \Some\Class\Foo\YourServiceClassName;

class Services extends CoreServices
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
***

Once you are done with class registrations, you can now access class anywhere using global helper function `service` or `Service` instance to call your class method in service.

**Using global helper function**

 Calling class method with the service alias if specified during class registration.

```php 
<?php 
service('example')->doFoo();
```

Calling class method directly from the class namespace string.

```php 
<?php 
service(SomeClass::class)->doFoo();
```

**Using service instance**

Optionally class your service from service class static instance.

```php 
<?php 
use \Luminova\Application\Services;
Services::example()->doFoo();
```
> Calling or initializing class with namespace is not supported when you use service static method.

***

### Re-Initialization

To re-instantiate class with new constructor arguments is simple, to do that you will need to pass the required values to `service` method after the first parameter which is always the calling service. 

Additionally, to prevent updating the previously stored instance, set the `serialize` argument to `false`, also set `shared` to false if you don't want to return a shared instance.

**Examples**

Don't share instance nor don't serialize.
```php 
<?php 
service('example', false, false, 'param1', 'param2')->doFoo();
```

Share instance, but don't don't serialize.

```php 
<?php 
service('example', true, false, 'param1', 'param2')->doFoo();
```
***

Using service instance is a bit difference in term of how it accept your arguments.
To directly call service using the service class object, you mus specify `serialize` or `shared` as the last 2 arguments after your initialization arguments. Example the order should follow `Service::foo(arg1, arg2, serialize, shared)`.

```php 
<?php 
use \Luminova\Application\Services;
Services::example('Peter', 'PHP', false, false)->doFoo();
```

Share instance but don't serialize.

```php 
<?php 
use \Luminova\Application\Services;
Services::example('Peter', 'PHP', false, true)->doFoo();
```

###

Optionally you can initialize service from factory, using global function `factory` to initialize service and call your class method in service.

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

Using factory instance.

```php 
<?php 
use \Luminova\Application\Factory;
Factory::service(true, false)->example('Peter', 'PHP')->doFoo();
```