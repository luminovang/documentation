# Application Custom Service Modules Management

***

## Overview

Services is a utility class that efficiently manages class initialization across the application's codebase. It provides shared and serializable class instances, with a centralized location for regist

***

## Introduction

Services is a utility class designed to simplify the management of class instances across your application. Serving as a centralized hub, it allows developers to manage, cache, or return shared instances of a class throughout the codebase. With a centralized location for registering classes, Services optimizes application performance by loading and initializing classes only when called. See documentation on how you can register your [service bootstrap](/base/services.md).

#### Key Features

1. **Class Registration and Access**: [Register classes in service](/configs/service.md) and access their instances anywhere in your codebase.

2. **Shared Instances**: Enables multiple parts of the application to access the same instance of a class.

3. **Serialization and Caching**: Supports serialization of class instances, allowing them to be stored as cache. These cached instances can be reinitialized with new constructor arguments or used with default arguments as they were stored.

***

### Usages

Suppose you have a class named `YourClassName` that you want to discover using the service class loader. In this example, let's assume that your class is located at `app/Utils/YourClassName.php`, with the namespace `App\Utils`.

```php
// /app/Utils/YourClassName.php

namespace App\Utils;

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

***

To dynamically create an instance of a service class, you can use the `Services::YourClassName()` method with the following parameters:

```php
use Luminova\Application\Services;

$instance = Services::YourClassName(...$arguments, true, true);
```

**Global Helper Function**

Additionally, there is a global helper function `service()` available, which provides a convenient way to access service instances.

```php
$instance = service('YourClassName', true, true, ...arguments);
```
Using full qualified class name `FooClass::class`.

```php
$instance = service(YourClassName::class, true, true, ...arguments);
```

> *Note:*
> 
> You can only use class name `YourClassName::class` if you didn't register your service with a custom name alias.

***

To get the default initialization name from `YourClassName`:

```php
$instance->getName(); // Peter
```

To reinitialize the instance with a new constructor argument without persisting across the codebase.

```php
$new = service('YourClassName', false, false, 'Farhana');
$new->getName(); // Farhana
```

To persist the initialization argument across the codebase.

```php
$persist = service('YourClassName', 'Ujah', true, true);
$persist->getName(); // Ujah
```

**To get instance of service**

```php
$service = service();
```

Clear all services.

```php
$service = service()->clear();
```

***

## Class Definition

* Class namespace: `Luminova\Application\Services`

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

To delete a service from shared instance and it cached instances.

```php
public static delete(class-string|string $service): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$service` | **class-string&#124;string** | The service class name or alias. |

**Return Value:**
`bool` - Return true if cached service was deleted, false otherwise.

> To fully delete a service, you will need to unregister it from the bootstrap file `/app/Config/Services.php`.

***

### clear

To clear all instance of service and it corresponding cached instance.

```php
public static clear(): bool
```

**Return Value:**

`bool` - Return true if cached services was cleared, false otherwise.

***

### add

To add a service class object or class name to services.

```php
public static add(string|object $service, Luminova\Application\arguments $arguments): ?object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$service` | **string&#124;object** |  |
| `$arguments` | **Luminova\Application\arguments** | Arguments to initialize class with.<br />-   The last 2 argument should be boolean values to indicate whether to shared instance or cached. |

**Return Value:**

`object|null` - Return object instance service class, null otherwise.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If service already exist or unable to initiate class.

> *Note:*
>
> Using this method to add a service will not make the service visible across your codebase.
>
> However, if you pass `true` to serialization, it will store that service and make it discoverable anywhere.