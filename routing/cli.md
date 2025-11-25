# CLI Controller Routing

***

## Overview

Learn how to define and route CLI commands in Luminova using command controllers or closures, with support for groups, middleware guards, and dependency injection.

***

## Introduction

The **CLI Routing** documentation explains how to route commands for **CLI command controllers** classes that extend [Luminova Base BaseCommand](/controllers/cli-command-controller.md).
Unlike `Luminova\Base\Console`, which commands are handles by **novakit** CLI tool, **BaseCommand** uses Luminova’s routing system, just like HTTP controllers to register and execute commands.

This approach lets you:

* **Define commands cleanly** using a routing structure
* **Group related commands logically** for better organization
* **Apply middleware authentication** using guards to protect specific commands

> **To define commands:**
>
> * Use the `group()` method to organize commands.
> * Use the `command()` method to register each command.
> * Use the `guard()` method to attach middleware authentication to commands.

---

## Controller Method Handlers

When mapping CLI commands to handlers, you can use either a **Closure** or a **string reference** pointing to a controller method. This works the same way as HTTP routing.

### Controller Class Handler

The routing system supports controller handlers using either:

* **Static-style:** `Controller::method`
* **Notation-style:** `Controller@method`

You **don’t need to write the full namespace** — just the controller’s base name and method. The routing system resolves the correct namespace automatically from the registered controller list.

```php
// /app/Controllers/Cli/Command.php

namespace App\Controllers\Cli;

use App\Utils\SomeClass;
use Luminova\Base\Command as BaseCommand;
use Luminova\Attributes\Group;
use Luminova\Attributes\Route;

#[Group('demo')]
class Controller extends BaseCommand
{
   /**
    * CLI group name for this controller.
    */
   protected string $group = 'demo';

   /**
    * Display help message.
    * 
    * @return int STATUS_ERROR
    */
   public function help(array $helps): int
   {
      return STATUS_ERROR; // Return STATUS_ERROR to use default help style
   }

   #[Route('hello', group: 'demo')]
   public function hello(SomeClass $object): int
   {
      $name = $this->getOption('name', 'World');
      $this->write("Hello, {$name}!");

      return STATUS_SUCCESS; // return appropriate status exit code
   }
}
```

---

### Closure Handler

CLI routes are defined in `/routes/cli.php`.

Within this file, `Luminova\Routing\Router $router` and `App\Application $app` are automatically available, so you don't need `use` statements unless explicitly required (e.g., when calling `group()`).

**Example using a callback function:**

```php
// /routes/cli.php

use Luminova\Routing\Router;
use Luminova\Foundation\Core\Application as CoreApplication;

/**
 * @var Router $router
 * @var CoreApplication $app
 */

$router->group('demo', static function(Router $router): void {
   $router->command('hello', 'Controller::hello');
});
```

---

## Usages

### Registering a Command

All CLI commands must be wrapped in a `group()` method. The group name acts as the base command, and inside its closure you define subcommands using the `command()` method.

**With Controller Handler:**

```php
// /routes/cli.php

use Luminova\Routing\Router;

$router->group('demo', static function(Router $router): void {
    $router->command('hello', 'Controller::hello');
});
```

**Execute the command:**

```bash
php index.php demo hello
```

---

**Multiple Commands in One Group:**

```php
// /routes/cli.php

use Luminova\Routing\Router;
use Luminova\Foundation\Core\Application as CoreApplication;

$router->group('blog', static function(Router $router): void {
    $router->command('list', 'Controller::list');
    $router->command('id/(:int)', 'Controller::get');
});
```

**Execute the commands:**

To list all blogs:

```bash
php index.php blog list
```

To show a single blog by its ID:

```bash
php index.php blog id=2
```

---

### CLI Middleware

You can apply middleware to CLI commands using the `guard()` method. This works like HTTP middleware but is specific to command authentication or validation.

```php
// /routes/cli.php

use Luminova\Routing\Router;

$router->group('blog', function(Router $router){
   $router->guard('blog', 'Controller::middleware');
   $router->command('list', 'Controller::list');
});
```

---

### Dependency Injection

Luminova’s CLI routing supports **automatic dependency injection**. You can type-hint dependencies in either controller methods or closures, and the framework will automatically inject them.

**Using a Controller Method:**

```php
// /app/Controllers/Cli/Controller.php

namespace App\Controllers\Cli;

use Luminova\Base\Command as BaseCommand;
use Luminova\Command\Utils\Image;
use App\Utils\ImageColor;

#[Group('demo')]
class Controller extends BaseCommand
{
   #[Route('draw/image/(:mixed)', group: 'demo')]
   public function drawImageArt(?string $image, ImageColor $color): void 
   {
      $art = Image::draw(
         $image ?? '/path/to/image.jpg', 
         ascii: Image::ASCII_CLASSIC, 
         width: 50, 
         height: 50, 
         grayscale: true, 
         colors: $color->getArrayColors(), // ['red', 'green', 'blue']
         lazyPrint: ['pixel' => 50000, 'pixels' => 200000]
      );

      $this->write($art);

      return STATUS_SUCCESS;
   }
}
```

**Execute the commands:**

To draw image using **ASCII**

```bash
php index.php demo draw image="/path/to/image.jpg"
```

The `ImageColor` object is automatically injected, allowing you to access command-line arguments directly without manual instantiation.

> **More Examples and Guides:**
> See additional patterns in the [Routing Example Documentation](/examples/cli-routing-integration.md).

***

## Class Definition

* Class namespace: `Luminova\Routing\Router`
* This class implements: [Luminova\Interface\RouterInterface](/interface/classes.md#routerinterface)
* This class is marked as **final** and can't be subclassed

***

## Methods

### group

Organizes CLI commands into a specific group for execution.

Similar to the HTTP `bind` method, the `group` method simplifies the organization of CLI commands by grouping related commands under a common name. This is especially useful for commands related to the same controller, making it easier to execute and maintain them.

```php
public group(string $group, Closure $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The name of the command group (e.g., `blog`, `user`). |
| `$callback` | **Closure** | A callback function that defines the commands for the group. |

**Example:**

Grouping CLI commands under a `blog` group:

```php
$router->group('blog', static function (Router $router) {
   $router->command('list', 'BlogController::blogs');     // Command to list all blogs
   $router->command('id/(:int)', 'BlogController::blog'); // Command to fetch a blog by ID
});
```

**Executing Commands:**

```bash
# Navigate to the project's public directory
cd path/to/project/public/

# Execute commands within the `blog` group
php index.php blog list       # Lists all blogs
php index.php blog id=4       # Fetches the blog with ID 4
```

> **Note:** Ensure that the group name matches the value of the `$group` property defined in the command controller class to avoid routing mismatches or execution errors.

***

### command

Registers a CLI command and its corresponding callback or controller method.

This method is used to define CLI commands, specifying the command name and the callback function or controller method to execute when the command is run in the terminal. Unlike HTTP routes, CLI commands are defined using this method specifically within a `group` method and does not support regular regex patterns. Instead, it uses a set of filter rules to capture command arguments.

```php
public command(string $command, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The command group name or a command group name with filters (e.g., `foo`, `foo/(:int)/bar/(:string)`). |
| `$callback()` | **Closure&#124;string** | The callback function or controller method to execute (e.g., `ClassBaseName::methodName`). |

**Example Usage:**

Suppose you have a command group `blogs` and a blog command controller class with two methods: `list` and `get`. The `list` method accepts an `int` argument to determine the number of blogs to list, while the `get` method accepts a `string` as the blog ID. You can register these commands with filters like `/list/limit/(:int)` for the list method and `/get/id/(:string)` for the get method.

**List Command With Method-Based:**

```php
// /routes/cli.php

$router->group('blog', function(Router $router){
	$router->command('/list/limit/(:int)', 'Controller::list');
});
```

**List Command With Attribute:**

```php
// /App/Controllers/Cli/Controller.php

#[Route('list/limit/(:int)', group: 'blog')]
public function list(int $limit = 10): int
{}
```

**Get Command:**

```php
// /routes/cli.php

$router->group('blog', function(Router $router){
	$router->command('/get/id/(:string)', 'Controller::get');
});
```

**Get Command With Attribute:**

```php
// /App/Controllers/Cli/Controller.php

#[Route('get/id/(:string)', group: 'blog')]
public function get(string $id): int
{}
```

> **Note:**
> For commands that don't expect any arguments, you don't need to add any filters, just pass the name like `foo`.
> 
> Additionally, execution of the command controller must follow this pattern: `php index.php <command-group> <command> <argument>`.

***

### guard

Registers a CLI `before` middleware guard to authenticate commands within a specific group.

The `guard` method provides middleware security for `CLI` implementation. It applies middleware logic to CLI commands within a specified group. The middleware is executed before any command in the group. If the response code returns `STATUS_SUCCESS`, the routing process will continue to execute other commands. If the middleware callback returns `STATUS_ERROR`, the routing process will terminate, preventing further commands from executing.

Unlike the `middleware` method, which handles only `HTTP` method authentication and accepts regular `regex` patterns, the `guard` method accepts only the command group name or `global` for global middleware security.

```php
public guard(string $group, Closure|string $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The command group name or default `global` for middleware that applies to all commands. |
| `$callback` | **Closure&#124;string** | The callback function or controller method to execute (e.g., `ControllerClass::methodName`). |

**Throws:**
- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - Thrown if the method is used outside a CLI context.

> **Note:** To register middleware with the group name, you must define your `guard` method within the `group` method callback closure.