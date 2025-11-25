# Dependency Injection in Routable Controller Methods

***

## Overview

Luminova’s Dependency Injection system resolves and passes objects to your routable controller methods, while also handling any URI segment parameters at the same time.

***

## Introduction

Luminova's **Dependency Injection (DI)** system automatically creates and passes the objects your routable methods need. You don't have to manually set them up, which keeps your code cleaner and easier to manage.

Instead of instantiating classes inside your controllers or route closures, DI delivers them for you, and also it allows you to use **custom arguments** when initializing these objects, before they are injected into your method. This makes your code easier to test, reuse, and extend without the usual boilerplate.

## What it does

* **Creates and injects dependencies automatically** — no manual wiring required.
* **Supports passing arguments when creating objects** before injection.
* **Works with controller methods and route closures right out of the box.**
* **Handles dependencies and URL parameters at the same time** within a single method.

---

### Enabling Routing System Dependency Injection

Dependency injection is disabled by default. To enable it, open your `.env` file and add:

```env
feature.route.dependency.injection = enable
```

> **Note:** DI only works with *routable methods* — not with controller constructors.

---

## Using Dependency Injection

### 1. Create an Injectable Class

First, let's make a simple class called **Test** that we'll inject later.

```php
// /app/Utils/Test.php
namespace App\Utils;

class Test
{
   public function __construct(private string $message) {}

   public function getMessage(): string
   {
      return $this->message;
   }
}
```

---

### 2. Bind the Class to the DI Container

Next, we tell Luminova how to create this class. You can bind it from the application class helper method or using the static `DI::bind()` method, depending on your preference.

```php
// /app/Application.php
namespace App;

use App\Utils\Test;
use Luminova\Foundation\Core\Application as CoreApplication;

class Application extends CoreApplication
{
   protected function onPreCreate(): void 
   {
      // Bind Test class with a custom message
      $this->bind(Test::class, fn(): Test => new Test('Hello world!'));

      // Alternative way (also valid)
      // Luminova\Routing\DI::bind(Test::class, fn(): Test => new Test('Hello world!'));
   }
}
```

---

### 3. Inject into a Controller Method

Controller methods can accept both **URI parameters** and **type-hinted dependencies** in any order. The DI engine figures out what belongs where.

```php
// /app/Controllers/Http/Controller.php
namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;
use App\Utils\Test;

class MyController extends Controller
{
   #[Route('/test/(:username)', methods: ['GET'])]
   public function test(Test $test, string $username): int
   {
      return $this->view('test', [
         'username' => $username,
         'message'  => $test->getMessage(), /* Outputs: Hello world! */
      ]);
   }
}
```

* `$test` is automatically resolved and injected by Luminova.
* `$username` comes from the URI segment.

> No container lookups. No manual wiring. It just works.

---

### 4. Inject into a Route Closure

DI also works inside closures. Just type-hint your dependencies, and Luminova provides them.

```php
// /routes/web.php
use Luminova\Foundation\Core\Application;
use App\Utils\Test;

$router->get('/test/(:username)', static function(Application $app, Test $test, string $username): int {
   return $app->view->view('test', [
      'username' => $username,
      'message'  => $test->getMessage(), /* Outputs: Hello world! */
   ]);
});
```

---

### 5. Works with CLI Command Groups Too

Dependency injection isn't limited to HTTP routes — you can use it in CLI routes as well.

```php
// /routes/cli.php
use Luminova\Interface\RouterInterface;

$router->group('http-get', static function(RouterInterface $router) {
   $router->command('request', 'Command::test');
});
```

> **Reminder:** Make sure any class you inject is type-hinted correctly and can actually be created by Luminova (either automatically or via a bind).

---

## Dependency Injections

Luminova's DI system lets you map abstract classes or interfaces to real, concrete implementations. This makes it simple for the framework to resolve and inject dependencies automatically when routing requests.

You can configure bindings in your application using either:

* **The static class method:** `Luminova\Routing\DI::bind()`
* **The application helper method:** `$this->bind()` inside your `Application` class (`onCreate()` or `__construct()`).

---

### Binding Dependencies

You can bind services directly by class name or use closures for custom initialization — depending on what your object requires.

---

#### Resolving an Interface to a Concrete Class

Bind an interface to the class that implements it:

```php
use Luminova\Routing\DI;

// Using the DI static bind method directly
DI::bind(\ExampleInterface::class, \Example::class);

// Using the Application helper method
// Inside \App\Application::onCreate() or \App\Application::__construct()
$this->bind(\ExampleInterface::class, \Example::class);
```

**Usage inside a routable method:**

```php
public function myExample(\ExampleInterface $example): int
{
   // Handle example
}
```

> By type-hinting the interface, you can swap implementations later without changing your controller method.

---

#### Binding with a Closure (Custom Instantiation)

Use a closure when your class needs extra setup or its own constructor arguments.

```php
use Luminova\Routing\DI;

// Using the DI static bind method directly
DI::bind('custom_service', function () {
   return new \Example(new SomeDependency());
});

// Using the Application helper method
// Inside \App\Application::onCreate() or \App\Application::__construct()
$this->bind(\ExampleInterface::class, function () {
   return new \Example(new SomeDependency());
});
```

**Guidelines:**

* Use **class names** for simple, stateless services.
* Use **closures** for services that need special initialization or other dependencies.
* Bound services are resolved automatically in controller methods and route closures — no manual container fetch required.

---

> **Important Notes:**
>
> * Do not bind the **same interface** multiple times — only the last binding will take effect.
> * If only one class exists, you can bind directly using the class name instead of an interface.
> * Always use unique bindings so the DI system can resolve classes predictably.

---

## Useful Methods

Luminova's DI container provides helper methods to inspect and manage your bindings.
These are handy when you need to verify if a dependency can be resolved, or to remove or replace a service during testing, debugging, or runtime overrides.

---

### unbind

Removes a previously registered class or interface from the DI container.

```php
use Luminova\Routing\DI;
use App\Utils\Test;

// Later, unbind it
DI::unbind(Test::class);

// Now resolving will return null (unless a default mapping exists)
$service = DI::resolve(Test::class); // null
```

> * Safe to call even if the binding doesn't exist — it fails silently.
> * Useful when swapping out implementations or cleaning up after tests.
> * Default mappings (if any) are unaffected by unbinding.

---

### has

Use `has()` when you just want to confirm that a class or interface **can** be resolved by the DI system — whether it's explicitly bound or automatically handled by default mappings.

```php
use Luminova\Routing\DI;

if (DI::has(Luminova\Utility\Email\Mailer::class)) {
    // Safe to inject or resolve MailService
    $mail = DI::resolve(Luminova\Utility\Email\Mailer::class);
    $mail->to('user@example.com')->send('Welcome!');
} else {
    // Handle cases where the service isn't available
    Logger::error('MailService not available in DI container');
}
```

---

### isBound

Use `isBound()` when you only care about **manual bindings** (via `DI::bind()` or `$this->bind()` in your Application class). This does not consider any default mappings.

```php
use Luminova\Routing\DI;

if (DI::isBound(\App\Contracts\StorageInterface::class)) {
    // Class was explicitly bound — we know exactly what implementation will be used
    $storage = DI::resolve(\App\Contracts\StorageInterface::class);
} else {
    // Either not bound, or relying on defaults (if any)
    Logger::warning('StorageInterface not explicitly registered');
}
```