# CLI Commands Routing Implementation Examples

***

## Overview

Learn how to define and handle CLI command routes in Luminova. See examples of command registration, argument handling, and controller execution for terminal-based applications.

***

## Introduction

This page shows **different examples of using Luminova CLI command routing with a Command Controller**.
For deeper understanding, see these references first:

* [Base Command Controller](/controllers/cli-command-controller.md)
* [Routing System Overview](/routing/system.md)
* [CLI Controller Routing](/routing/cli.md)
* [Attribute Routing](/attributes.md)
* [Dynamic Placeholders](/routing/dynamic-uri-placeholder.md)

---

## Examples

### Method-Based Routes

When you are **not using attribute routing**, you define your CLI prefixes in `/public/index.php`.

**Example:**

The following setup maps any request CLI prefix for all command:

```php
// /public/index.php

use Luminova\Boot;
use Luminova\Routing\Prefix;

Boot::http()->router->context(
    new Prefix(Prefix::CLI),
    // Other routes
)->run();
```

> **Note:**
> Do not remove your website prefixes, just add cli prefix if not already added.

---

#### Route context file

For example, the `cli` prefix above corresponds to `/routes/cli.php`.
Inside this file, you define all routes under `/cli.php` using the global `$router` instance.

```php
// /routes/cli.php

/**
 * Available in file scope
 * 
 * @var Luminova\Routing\Router $router
 */

$router->group('demo', static function(Router $router){
   $router->command('hello', 'Command::hello');
});
```

**Execute:**

```bash
php index.php demo hello
```

---

### Attribute-Based Routes

When **attribute routing is enabled**, you no longer need to manually register CLI prefix in `/public/index.php`.
Instead, you define them directly inside your controller class using the `#[Group]` attribute.

**Controller Example:**

The following controller automatically handles all command related to group (except `demo`).

```php
// /app/Controllers/Cli/Command.php

namespace App\Controllers\Cli;

use Luminova\Base\Command as BaseCommand;
use Luminova\Attributes\Group;

#[Group(name: 'demo')]
class Command extends BaseCommand
{
    // Define controller methods here
}
```

---

Same as HTTP routing, with attribute routing, your `index.php` setup stays clean because the router automatically reads prefixes from attributes:

```php
// /public/index.php

use Luminova\Boot;

Boot::http()->router->context()->run();
```

***

## Middlewares

Middleware lets you intercept incoming commands **before they reach your controllers**.
This is useful for tasks like authentication, access control, or pre-processing.

If middleware fails (returns an error), **the controller will never be called** — instead, the defined error handler will run.

Middleware should return:

* `STATUS_SUCCESS` (allow request to proceed)
* `STATUS_ERROR` (block request and trigger error)

---

### Using middleware with a route attribute

In this example, we use **session authentication**.
We assume that the session object is available as `$this->app->session` inside your `App\Application` class.

```php
// /app/Controllers/Cli/Controller.php

use Luminova\Attributes\Route;

#[Route('demo', group: 'demo', middleware: Route::)]
public function middleware(): int
{
    if ($this->app->session->isOnline()) {
        return STATUS_SUCCESS;
    }

    return STATUS_ERROR;
}
```

---

### Using middleware with method-based routing

If you’re not using attributes, you can attach middleware directly in your route file.

In CLI, middlewares are define using `guard`:

```php
// /routes/cli.php

$router->group('demo', function(Router $router, Application $app){
    $router->guard('demo', 'Command::middleware');
    $router->command('list', 'Command::list');
    $router->command('list/limit/(:int)', 'Command::list');
});
```

**Execute:**

Running below command will trigger middleware authentication first before command is executed.

```bash
php index.php demo list
php index.php demo list limit=10
```

***

## Handling Command

Luminova uses the `Luminova\Command\Terminal` class for all CLI related helper functions. All method are static making it easy to access. But before calling method ensure you initialize by calling `Terminal::init()` or use object `new Terminal()`.
Once initialized you call the methods (e.g, `Terminal::write()`) to handle command.

Here’s how to set up a simple command route.

---

### Handling commands with an attribute

Define a controller method with a `#[Route]` attribute:

```php
// /app/Controllers/Http/Controller.php

use Luminova\Attributes\Route;

#[Route('message', group: 'demo')]
public function message(): int
{
    $message = $this->getAnyOption('message', 'm');

    $this->write($message);
    return STATUS_SUCCESS;
}
```

**Execute:**

```bash
php index.php demo message="Hello world!"
```

---

### Handling commands with method

You can also handle commands inside a route context file such as `/routes/cli.php`.

**Using a controller method handler:**

```php
// /routes/cli.php

$router->group('demo', function(Router $router){
    $router->command('message', 'Command::message');
});
```

**Execute:**

```bash
php index.php demo message="Hello world!"
```

***

## Dynamic CLI segments

You can capture parts of the command if you choose not to use arguments, the capture is similar to URL using **patterns**, **named placeholders**, or **predefined placeholder patterns**.

This allows your routes to handle Commands like `php index.php demo user=peter1` dynamically without using arguments (e.g, `php index.php demo --user=peter1`).

Arguments may be better choice as they are tied to controller `$options` property making it visible when help command is run and they also support alias (e.g `--foo, -f`).

### Capturing segments with an attribute

```php
// /app/Controllers/Cli/Command.php

use Luminova\Attributes\Route;

#[Route('/user/(:username)', group: 'demo')]
public function profile(string $username): int
{
    $this->write($username);
    return STATUS_SUCCESS;
}
```

**Execute:**

```bash
php index.php demo user username="peter"
```

---

### Capturing segments with method-based routing

```php
// /routes/cli.php

$router->group('demo', function(Router $router){
    $router->command('/user/(:username)', 'Command::profile');
});
```

> These examples capture a dynamic username from the Command and pass it to the controller method to process the user profile.

---

### Uploading Files

When processing form submissions or API calls (e.g., updating a user profile via `POST`), it’s best to use a **dedicated controller** instead of mixing logic into your main web controller.

```php
// /app/Controllers/Cli/Command.php

use Luminova\Attributes\Route;
use App\Models\User;

#[Route('/upload', group: 'demo')]
public function upload(): int
{
    $file = $this->getAnyOption('file', 'f');
    $destination = $this->getAnyOption('destination', 'd');
    $name = $this->getAnyOption('name', 'n', null);

    $message = 'Failed to upload file';
    if($this->upload($file, $destination, $name)){
        $message = 'File was uploaded';
    }

    $this->success($message);

    return STATUS_SUCCESS;
}
```

**Execute:**

```bash
php index.php demo upload --file="path/to/image.png" --destination="path/to/assets/images/"
```

***

## Nested binding and Aliases

You can group related routes together under a shared command segment.
For example, if you want to handle `/about` and want an aliases `/info`, you can bind them using a closure or define them directly in a controller with attributes.

### Route Aliases (Redirects or Alternate Paths)**

Show how to point multiple commands to the same controller method — useful for legacy routes or SEO.

```php
#[Route('about', group: 'demo', aliases: ['info'])]
public function about(): int 
{
    $about = $this->systemInfo();

    var_export($about);
    return STATUS_SUCCESS;
}
```

**Execute:**

```bash
php index.php demo about
php index.php demo info
```

---

### Stack multiple route attributes

Define more than one `#[Route]` attribute on the same method:

```php
// /app/Controllers/Cli/Command.php

use App\Models\User;
use Luminova\Attributes\Route;

#[Route('about', group: 'demo')]
#[Route('info', group: 'demo')]
public function about(): int
{
    $about = $this->systemInfo();

    var_export($about);
    return STATUS_SUCCESS;
}
```

---

### Using method-based routing with bind

You can bind all `about` related commands to a single block in your route file:

```php
// /routes/web.php

use Luminova\Routing\Router;
use App\Application;

$router->group('demo', function(Router $router){
    $router->command('/about', 'Command::about');
    $router->command('/info', 'Command::about');
});
```

---

### CLI Session

This example demonstrates how to maintain a **persistent login session** in CLI applications using the Luminova [Session Class](/sessions/session.md).
It allows commands to store and retrieve user state (like login information) between executions.

```php
// /app/Controllers/Cli/CommandController.php

namespace App\Controllers\Cli;

use Luminova\Base\Command as BaseCommand;
use Luminova\Sessions\Session;
use Luminova\Command\Utils\Color;

class CommandController extends BaseCommand
{
    private ?string $sid = null;
    private ?Session $session = null;

    /**
     * Called when the command is initialized.
     */
    protected function onCreate(): void 
    {
        $this->sid = Session::toSessionId($this->getSystemId());
        $this->session = new Session();
        $this->session->setStorage('cli');

        if (!$this->session->start($this->sid)) {
            $this->error('Unable to start session in CLI');
        }
    }

    /**
     * Display help message.
     */
    public function help(array $helps): int
    {
        return STATUS_ERROR;
    }

    public function login(): int
    {
        if (!$this->session->isStarted()) {
            $this->error('Cannot use session in CLI');
            return STATUS_SUCCESS;
        }

        if ($this->session->isOnline()) {
            $this->write(sprintf(
                "User is already online.\nUsername: %s\nLast Login: %s\nSID: %s",
                $this->session->get('username'),
                $this->session->get('date'),
                $this->session->getId()
            ));
            return STATUS_SUCCESS;
        }

        $username = $this->input('Enter your username to login: ');

        if (!$username) {
            $this->error('Username is required');
            return STATUS_SUCCESS;
        }

        $date = date('d-m-Y H:i:s A');
        $this->session->put('username', $username);
        $this->session->put('date', $date);
        
        if (!$this->session->login()) {
            $this->error('User login failed', 'red');
            return STATUS_SUCCESS;
        }

        $this->write(sprintf(
            "Login successful.\nUsername: %s\nLogin Date: %s\nSID: %s",
            $username,
            $date,
            $this->sid
        ));

        return STATUS_SUCCESS;
    }

    public function logout(): int
    {
        if (!$this->session->isStarted()) {
            $this->error('Cannot use session in CLI');
            return STATUS_SUCCESS;
        }

        if ($this->session->isOnline()) {
            $username = $this->session->get('username');
            $this->session->logout();

            $this->success(sprintf(
                "User '%s' has successfully logged out.",
                $username
            ));

            return STATUS_SUCCESS;
        }

        $this->write('User is already logged out', 'yellow');
        return STATUS_SUCCESS;
    }
}
```

---

#### Register Session Handlers

To register the above commands in your CLI routes:

```php
// /routes/cli.php

use Luminova\Routing\Router;

$router->group('demo', static function (Router $router): void {
    $router->command('login', 'CommandController::login');
    $router->command('logout', 'CommandController::logout');
});
```

**Execute:**

```bash
php index.php demo login
php index.php demo logout
```
