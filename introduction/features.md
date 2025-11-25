# Customization of Optional Features and Framework Behavior

***

## Overview

Luminova offers numerous useful features, but we understand that not all may be necessary for some developers or applications, while some may add extra performance overhead.

***

## Introduction

Luminova offers a numerous powerful features, yet we recognize that not all may be essential for every application. Understanding and leveraging these features can sometimes pose challenges, especially for beginners who may feel overwhelmed by their complexity.

To address this, we provide a method for enabling or disabling specific features through environment configuration. By doing so, you have the flexibility to customize the framework to your specific needs, ensuring that only the necessary components are included in your application.

> Many of these features are loaded during the bootstrap process and are readily available throughout the framework's execution, making them easily accessible whenever you require them.

***

## Setups

You can configure the features in your environment variable file `/.env`, by simply specifying `enable` or `disable` as the value for the variable key.

***

### PHP Attribute 

`feature.route.attributes` When enabled, you can define your route before the method using `PHP 8` attribute to simplifies the creation of routes.

```php 
namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;
use Luminova\Attributes\Error;
use App\Errors\Controllers\ErrorController;

#[Error('web', pattern: '/.*', onError: [ErrorController::class, 'onWebError'])]
class MyController extends Controller
{
	#[Route('/', methods: ['GET', 'POST'])]
	public function index(SomeClass $class): int 
	{
		// your code here
	}
}
```

Additionally you can enable `feature.route.cache.attributes` also, in production to cache attributes this is recommended for better performance instead of reading and passing attributes every time request is made.

***

### Class Alias

`feature.app.class.alias` When enabled, the framework will create name alias for the listed classes in your modules configuration file `/app/Config/Modules.php`.

```php 
<?php 
return [
    //...,
    'alias' => [
        'Foo' => 'Some\Class\ExampleFoo'
    ]
];
```

**Usage Example**

Now `ExampleFoo` can be initialized using the alias instead of the original class name.

```php
use Some\Class\Foo;

$foo = new Foo();
```

***

### Services

`feature.app.services` When enabled, the framework will register all services listed in your Services configuration located in `/app/Config/Services.php`.

```php 
public function bootstrap(): void
{
    static::newService(Adapter::class, 'adapter', true, true, []); // Has alias "adapter"
    static::newService(ExampleClass::class, null, true, true, []);  // No alias, it will use class base name instead.
    //...
}
```

**Usage Example**

Initializing with your registered alias.

```php
$adapter = service('adapter');

$adapter->fooBar();
```
Initializing without any registered alias.

```php
$example = service('ExampleClass');

$example->fooBar();
```

***

### PSR4 Autoloading

`feature.app.autoload.psr4` When enabled, the framework will autoload all the listed classes using the psr-4 standard. To list your classes, locate the file in the modules configuration file `/app/Config/Modules.php`.

```php 
return [
    //...,
    'psr-4' => [
        'Example\MyNamespace'    => '/example/MyClass/',
        'Example\AnotherClass' => '/example/anotherclass/',
	    'Example' => '/example/'
    ]
];
```

> Note: 
> Framework is all about code organization. To maintain good practice and application maintainability.
> 
>  All your class files must be placed in the `/libraries/libs/` directory to load them.

***

##### Single File Import

Alternatively, you can load a single file without the need to enable the `psr-4` feature by using the import method. This method allows you to include a specific file directly into your application without relying on the `psr-4` autoloading feature.

To import a single file, use the `import` method in your controller or any relevant file:

```php
import('example/MyClass');
```

***

### Dev Functions

`feature.app.dev.functions` When enabled, the framework will automatically include your PHP procedural global functions file `/app/Utils/Global.php`.

This global file allows you to define your own custom functions or overwrite `PHP` procedural functions to replace them with your own.

**Example**

```php
if (!function_exists('my_string_count')) {
    function my_string_count(string $string): int 
    {
        return strlen($string);
    }
}
```

***

### Dependency Injection

`feature.route.dependency.injection` By default, dependency injection in the controller methods is disabled to optimize performance. 

You can enable it in the environment file. Once enabled, you can specify a type hint for your controller routeable methods or closures parameter with the desired class to be loaded.
This promotes easy initialization and clean code.

```php 
use Luminova\Security\Encryption\Crypter;
 
$router->get('/users', function (Crypter $crypt) {
    $crypt->encrypt("My data");
});
```

Using dependency injection with your routes to accept a URI parameter in the callback.

URL Example: `https://example.com/users/token/some-token-to-encrypt`

```php 
use Luminova\Security\Encryption\Crypter;
 
$router->get('/users/token/([a-zA-Z0-9-]+)', function (Crypter $crypt, String $token) {
    $crypt->encrypt($token);
});
```

Here's how you can achieve the same result in a controller class.

```php 
$router->get('/users/token/([a-zA-Z0-9-]+)', 'UserController::hashToken');
```

And the corresponding controller class will look like below example.

```php
// /app/Controllers/Http/UserController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Security\Encryption\Crypter;

class UserController extends Controller 
{
	public function hashToken(Crypter $crypt, String $token): void 
	{
		$crypt->encrypt($token);
	}
}
```