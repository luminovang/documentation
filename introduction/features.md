## Features

***

## Overview

Framework&#039;s features customization offers numerous useful features, but we understand that not all may be necessary for some developers or applications, while some may add extra performance overhead.

***

Luminova offers a numerous powerful features, yet we recognize that not all may be essential for every application. Understanding and leveraging these features can sometimes pose challenges, especially for beginners who may feel overwhelmed by their complexity.

To address this, we provide a method for enabling or disabling specific features through environment configuration. By doing so, you have the flexibility to customize the framework to your specific needs, ensuring that only the necessary components are included in your application.

> Many of these features are loaded during the bootstrap process and are readily available throughout the framework's execution, making them easily accessible whenever you require them.

***

## Setups

You can configure the features in your environment variable file `/.env`, by simply specifying `enable` or `disable` as the value for the variable key.

***

### Class Alias

`feature.app.class.alias` When enabled, the framework will create name alias for the listed classes in your controller configuration file `/app/Controllers/Config/Modules.php`.

```php 
<?php 
return [
    //...,
    'alias' => [
        'Foo' => 'SomeClass\ExampleFoo'
    ]
];
```

***

### Services

`feature.app.services` When enabled, the framework will register all services listed in your controller Services configuration located in `/app/Controllers/Config/Services.php`.

```php 
public static function bootstrap(): void
    {
        static::addService(Adapter::class, "Test Argument", true, true);
		static::addService(Example::class, true);
		//...
    }
```

> The last two arguments should be boolean values to indicate whether the instance is shared or serialized cached.
> 
> `static::addService(Example::class, $shared, $serialized);`

***

### PSR4 Autoloading

`feature.app.autoload.psr4` When enabled, the framework will autoload all the listed classes using the psr-4 standard. To list your classes, locate the file in the controller configuration file `/app/Controllers/Config/Modules.php`.

```php 
<?php 
return [
    //...,
    'psr-4' => [
        'Example\MyNamespace'    => '/example/MyClass/',
        'Example\AnotherClass' => '/example/anotherclass/',
	    'Example' => '/example/'
    ]
];
```

> Note: Luminova is all about code organization. To maintain good practice and application maintainability.
> 
>  All your class files must be placed in the `/libraries/libs/` directory to load them.

##### Single File Import

Alternatively, you can load a single file without the need to enable the `psr-4` feature by using the import method. This method allows you to include a specific file directly into your application without relying on the `psr-4` autoloading feature.

To import a single file, use the `import` method in your controller or any relevant file:

```php
<?php 
use Luminova\Library\Modules;
Modules::import('example/MyClass');

//Or global function 
import('example/MyClass');
```

***

### Dev Functions

`feature.app.dev.functions` When enabled, the framework will automatically include your development global PHP file `/app/Controllers/Utils/Global.php`. This goal file allows you to define your own custom functions or overwrite PHP procedural functions to replace them with your own.

**Example**

```php
<?php
if (!function_exists('my_string_count')) {
    function my_string_count(string $string): int 
    {
        return strlen($string);
    }
}
```

***

### Dependency Injection

`feature.route.dependency.injection` By default, dependency injection in the router is disabled to optimize performance. You can enable it in the environment file. Once enabled, you can specify a type hint for your router parameter with the desired class to be loaded.

```php 
<?php
use \Luminova\Security\Crypter;
 
$router->get('/users', function (Crypter $crypt) {
    $crypt->encrypt("My data");
});
```

Using dependency injection with your routes to accept a URI parameter in the callback.

URL Example: `https://example.com/users/token/some-token-to-encrypt`

```php 
<?php
use \Luminova\Security\Crypter;
 
$router->get('/users/token/([a-zA-Z0-9-]+)', function (Crypter $crypt, String $token) {
    $crypt->encrypt($token);
});
```

Here's how you can achieve the same result in a controller class.

```php 
<?php
$router->get('/users/token/([a-zA-Z0-9-]+)', 'UserController::hashToken');
```

And the corresponding controller class will look like below example.

```php
<?php
namespace App\Controllers;

use \Luminova\Base\BaseController;
use \Luminova\Security\Crypter;

class UserController extends BaseController {
	public function hashToken(Crypter $crypt, String $token): void 
	{
		$crypt->encrypt($token);
	}
}