# Services Configuration

***

## Overview

Register and autoload services that should be discoverable throughout your application.

***

## Introduction

The `Service Configuration` class allows you to register services to be autoloaded and discoverable anywhere in your application. It allows you specify initialization arguments, and optionally specify whether the class instance should be shared, and whether it should be serialized and stored as cache.

***

* Class namespace: `\App\Config\Services`
* File path: `/app/Config/Services.php`
* Parent class: [\Luminova\Base\BaseServices](/base/services.md)

***

### ToDo

Enable the service feature by setting `feature.app.services = enable` in your environment configuration file.

***

## Methods
To get started with service usages and method read the [Application Service Guide](/introduction/service.md).

### bootstrap

Register services to autoload and make them discoverable.

```php
public bootstrap(): void
```

> To register a classes, use the static `newService` method available in `BaseServices` within the `bootstrap` method as shown below.
> 
> Do not create a duplicate of the `bootstrap` method or the `Service` class. To register multiple classes, simply add each class in a new line within the `bootstrap` method using `newService`.

### Example 

```php
<?php
namespace App\Config;

use Luminova\Base\BaseServices;
use App\Utils\YourServiceClassName;

class Services extends BaseServices
{
    public function bootstrap(): void
    {
        static::newService(
            YourServiceClassName::class, // Class name to register.
            null, // Class name alias (defaults to the class basename if null).
            true, // Should return shared instance.
            true, // Should serialize and store the class object.
			[ // Initialization arguments, must be array list (int keys)
				'foo', 
				'bar', 
				true, 
				new fooClass(), 
				['my', 'key' => 'array']
			]
        );
        //...
    }
}
```