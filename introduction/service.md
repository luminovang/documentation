# Services

***

## Overview

Services is a utility class that efficiently manages class initialization across the application&#039;s codebase. It provides shared and serializable class instances, with a centralized location for regist

***

## Introduction

Services is a utility class designed to simplify the management of class instances across your application. Serving as a centralized hub, it allows developers to manage, cache, or return shared instances of a class throughout the codebase. With a centralized location for registering classes, Services optimizes application performance by loading and initializing classes only when called.

#### Key Features:

1. **Class Registration and Access**: [Register classes in service](/configs/service.md) and access their instances anywhere in your codebase.

2. **Shared Instances**: Enables multiple parts of the application to access the same instance of a class.

3. **Serialization and Caching**: Supports serialization of class instances, allowing them to be stored as cache. These cached instances can be reinitialized with new constructor arguments or used with default arguments as they were stored.

***

### Usages:

Suppose you have a class named `YourClassName` that you want to discover using the service class loader. In this example, let's assume that your class is located at `app/Controllers/Utils/YourClassName.php`, with the namespace `App\Controllers\Utils`.

```php
<?php
namespace App\Controllers\Utils;

class YourClassName 
{
    private string $name = '';

    public function __construct(string $name){
        $this->name = $name;
    }

    public function getName(){
        return $this->name;
    }
}
```

**TO DO**

1. Enable the service feature by setting `feature.app.services = enable` in your environment configuration file.
2. In your service bootstrap class located at `/app/Controllers/Config/Services.php`, register your class as follows:

```php
<?php
namespace App\Controllers\Config;

use Luminova\Base\BaseServices;
use App\Controllers\Utils\YourClassName;

class Services extends BaseServices
{
    public function bootstrap(): void
    {
        static::newService(
            YourClassName::class, // Class name to register
            null, // Class name alias (defaults to basename if null)
            'Peter', // Default initialization argument
            true, // Always return shared instance
            true // Serialize and store the class object
        );
        //...
    }
}
```

To dynamically create an instance of a service class, you can use the `Services::YourClassName()` method with the following parameters:

```php
<?php
use \Luminova\Application\Services;
$instance = Services::YourClassName(...$arguments, true, true);
```

**Global Helper Function**

Additionally, there is a global helper function `service()` available, which provides a convenient way to access service instances.

```php
<?php
$instance = service('YourClassName', ...arguments, true, true);
```
Using full qualified class name `FooClass::class`.

```php
<?php
$instance = service(YourClassName::class, ...arguments, true, true);
```

> *Note:*
> 
> You can only use class name `YourClassName::class` if you didn't register your service with a custom name alias.

***

To get the default initialization name from `YourClassName`:

```php
<?php
$instance->getName(); // Peter
```

To reinitialize the instance with a new constructor argument without persisting across the codebase.

```php
<?php
$new = service('YourClassName', 'Farhana', false, false);
$new->getName(); // Farhana
```

To persist the initialization argument across the codebase.

```php
<?php
$persist = service('YourClassName', 'Ujah', true, true);
$persist->getName(); // Ujah
```

**To get instance of service**

```php
<?php
$service = service();
```

***

* Class namespace: `\Luminova\Application\Services`

***

## Methods

### has

Check if service class is available either in cache, shared or ready to initialized.

```php
public static has(class-string|string $service): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$service` | **class-string&#124;string** | The service class name or class name alias. |

**Return Value:**
`bool` - Return true if service class exists, false otherwise.

***

### delete

Delete a service from shared instance and it cached instances.

```php
public static delete(class-string|string $service): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$service` | **class-string&#124;string** | The service class name or alias. |

**Return Value:**
`bool` - Return true if cached service was deleted, false otherwise.

> To fully delete a service, you will need to unregister it from the bootstrap file `/app/Controllers/Config/Services.php`.

***

### clear

Clear all instance of service and it corresponding cached instance.

```php
public static clear(): bool
```

**Return Value:**

`bool` - Return true if cached services was cleared, false otherwise.

***

### add

Add a service class object or class name to services.

```php
public static add(string|object $service, \Luminova\Application\arguments $arguments): ?object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$service` | **string&#124;object** |  |
| `$arguments` | **\Luminova\Application\arguments** | Arguments to initialize class with.<br />-   The last 2 argument should be boolean values to indicate whether to shared instance or cached. |

**Return Value:**

`object|null` - Return object instance service class, null otherwise.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#RuntimeException) - If service already exist or unable to initiate class.

> *Note:*
>
> Using this method to add a service will not make the service visible across your codebase.
>
> However, if you pass `true` to serialization, it will store that service and make it discoverable anywhere.