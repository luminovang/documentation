# Dependency Injection for Controller Methods

***

## Overview

Luminova’s Dependency Injector will automatically resolve and inject dependencies into your controller methods, while also handling URL parameters as required.

***

## Introduction

Luminova's Dependency Injection (DI) system simplifies the management of class dependencies in your application without any pre-configuration required. By leveraging DI, you can easily inject dependencies into controllers, closures and router methods, promoting modularity and testability.

Your controller methods can accept both request parameters and dependencies. The DI injector will automatically resolve and inject dependencies into your controller methods, while also handling URL parameters as required.
The position of data types and dependencies in the method signature does not affect the injection process.

#### How It Works

1. **Enable Dependance Injection:** In your environment variables file enable the dependency injection feature by setting the value of `feature.route.dependency.injection` to `enable`.
2. **Controller Definition:** Define your controller class as usual. You can specify modules in method parameters that will be injected by the DI injector.
3. **Automatic Injection:** When a controller method is called, Luminova's DI injector automatically resolves and injects the required dependencies based on the method's parameters.

> **Note:** Luminova doesn't support dependency injection in controller constructor methods.

***

### Injection in Controllers

In Luminova, you can inject dependencies directly into controller methods. 
This allows for more flexible and maintainable code by abstracting the creation and management of dependencies away from the controller itself.

***

#### Controllers Example

```php
// /app/Controllers/MyController.php
<?php
namespace App\Controllers;

use Luminova\Base\BaseController;
use App\Utils\SomeService;
use App\Utils\SomeRepository;

class MyController extends BaseController
{
    public function myMethod(SomeService $service, SomeRepository $repository): int
    {
        $service->doSomething();
        $repository->loadSomething();

        return $this->view('myView');
    }
}
```

***

### Injection in Router

Luminova also supports DI in some router methods, allowing you to inject dependencies directly into the route handlers.

#### Router Example

**Closure Controller with Dependency Injection:**

```php
// /routes/web.php
<?php 
use Luminova\Routing\Router;
use App\Utils\SomeService;
use App\Utils\SomeRepository;

$router->get('/example', static function(Router $router, SomeService $service, SomeRepository $repository){
    $service->doSomething();
    $repository->loadSomething();

    echo 'Example';
});
```

**HTTP Bind Method with Dependency Injection:**

```php
// /routes/web.php
<?php 
use Luminova\Routing\Router;

$router->bind('/example', static function(Router $router){
    $router->get('/', 'MyController::myMethod');
});
```

**Command Group Method with Dependency Injection:**

```php
// /routes/cli.php
<?php 
use Luminova\Routing\Router;

$router->group('http-get', static function(Router $router){
    $router->command('request', 'MyController::myMethod');
});
```

***

### Combining Parameters and Dependencies

In Luminova, you can inject dependencies and handle parameters in your controller methods based on the request URL. 
You have the flexibility to mix data types and dependencies in any order within the method signature.

#### Example

Consider a route that provides a URL parameter and requires a service dependency. You can define your controller method to accept both.

**Route Definition:**

Define a route that maps to a controller method.

```php
// /routes/cli.php
<?php 

$router->get('/example/{id}', 'MyController::handle');
```

**Controller Method:**

```php
namespace App\Controllers;

use Luminova\Base\BaseController;
use App\Utils\SomeService;

class MyController extends BaseController
{
    public function handle(int $id, SomeService $service): int
    {
        // Use $id and $service here
    }
}
```

> **Note:** 
> Ensure that the method parameters are correctly typed and that the injected class is available and instantiable to resolve the dependencies.