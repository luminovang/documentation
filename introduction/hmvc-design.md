# Hierarchical Model-View-Controller (HMVC) Design Patterns

***

## Overview

HMVC provides a simple way to build scalable and modular applications. By organizing your app into logical modules, it enhance reusability, maintainability, and performance.

***

## Introduction

HMVC (Hierarchical Model-View-Controller) is an advanced architectural pattern that extends the standard MVC (Model-View-Controller) design by adding modularity. In Luminova, each module follows the traditional `MVC` components (Models, Views, and Controllers), but operates independently within its own namespace and structure. This modular approach makes it easier to develop, maintain, and scale complex applications.

#### Implementing HMVC in Luminova

Adopting the HMVC design pattern in Luminova is simple and straightforward. It doesn't require additional learning curves. You can easily convert your existing MVC structure into HMVC by organizing your controllers into module specific directories. Simply place your existing MVC controllers, models, and views into the `/app/Modules/` directory, update namespaces and Luminova will handle the routing and module management for you.

---

### Enabling HMVC in Luminova

To enable HMVC support in Luminova, you need to switch from the default MVC mode to HMVC by updating your environment configuration.

1. Open the `.env` file in the root of your project.
2. Locate the `feature.app.hmvc` setting.
3. Set its value to `enable` like this:

```env
feature.app.hmvc = enable
```

> This tells Luminova to use the HMVC structure for loading controllers, views, and models from module folders.

---

### Important Naming Convention

In the Luminova HMVC architecture, module directories must follow a strict naming convention* by using **PascalCase**.
The **module name must start with an uppercase letter** (e.g., `Blog`), not lowercase (`blog`).

**Why it matters:**
Luminova uses autoloading and class resolution that are case-sensitive. If your module directory name doesn’t follow the correct casing, it may not be recognized properly.

**Example:**

If your URL is:

```bash
https://example.com/blog
```

And `blog` is a sub-HMVC module, then the corresponding module directory must be named with an uppercase first letter:

Correct:

```
/app/Modules/
    Blog/
        Controllers/
        Models/
```

Incorrect:

```
/app/Modules/
    blog/
        Controllers/
        Models/
```

> Always use `PascalCase` (first letter uppercase) for module directory names to ensure compatibility and consistency within the framework.

***

## Key Concepts

### Modules

A **module** is a self-contained component that encapsulates its own controllers, models, views, and other necessary resources. Modules in Luminova HMVC are designed to be reusable and scalable, often representing a distinct part of your application (e.g., user management, blog, etc.).

---

### Controllers

Controllers in Luminova HMVC are responsible for handling requests and directing the flow of the application. Controllers can be created within either:
- **HTTP Controllers**: Located in `app/Modules/Controllers/Http/` or `app/Modules/<Module>/Controllers/Http/` and handle HTTP requests.
- **CLI Controllers**: Located in `app/Modules/Controllers/Cli/` or `app/Modules/<Module>/Controllers/Cli/` and handle command-line interface requests.

> **Note:** You must call `$this->app->setModule('Foo')` within the controller's `onCreate` or `__construct` method to register your custom module name. Only the module's directory name (e.g., `Foo`) is required, not the full path.

---

### Views

Views handle the presentation logic of the application. Each module can have its own views, and the path to these views is scoped to the module in which they are defined. Views are generally located in the `app/Modules/Views/` or `app/Modules/<Module>/Views/` directory within each module.

---

### Models

Models are responsible for handling the data logic of your application. Each module can have its own set of models, allowing each module to manage its own data independently. Models are typically stored in the `app/Modules/Models/` or `app/Modules/<Module>/Models/` directory within each module.

***

### Folder Structure

The Luminova HMVC structure organizes code into modules, each containing its own set of MVC components. Below is a typical file structure for a Luminova HMVC application:

```
/app/
    /Controllers/         (Main controllers for non-modular MVC architecture)
        /Http/            (HTTP controllers)
        /Cli/             (CLI controllers)

    /Modules/              (Modules for HMVC)
        /Controllers/      (Shared HMVC Controllers)
            /Http/         (Shared HTTP controllers for HMVC)
            /Cli/          (Shared CLI controllers for HMVC)
        /Models/           (Shared HMVC Models)
        /Views/            (Shared HMVC Views)

        /Blog/              (Example Module: Blog)
            /Controllers/   (Controllers specific to Blog module)
                /Http/      (HTTP controllers)
                /Cli/       (CLI controllers)
            /Models/        (Models specific to Blog module)
            /Views/         (Views specific to Blog module)

        /User/              (Example Module: User)
            /Controllers/   (Controllers specific to User module)
                /Http/      (HTTP controllers)
                /Cli/       (CLI controllers)
            /Models/        (Models specific to User module)
            /Views/         (Views specific to User module)
```

**Directory Structure Overview**

* **Modules/**: Root container for all application modules, including the default (root) module.
  * **Module/**: Each module represents a self-contained feature or section of the application.
     * **Controllers/**: Handles incoming requests and business logic specific to the module.
     * **Models/**: Defines data structures and database interactions used by the module.
     * **Views/**: Contains the presentation layer, HTML or template files rendered by the module.

***

### Namespaces

In Luminova's HMVC architecture, proper namespace registration is essential for organizing and autoloading your application components. Namespace patterns vary slightly depending on whether you're using standard MVC or modular HMVC.

**MVC Namespace Conventions**

* `\App\Controllers\Http\`
  For standard HTTP controllers in a non-modular structure.

* `\App\Controllers\Cli\`
  For command-line controllers (CLI) in a non-modular structure.

**HMVC Namespace Conventions**

* `\App\Modules\<ModuleName>\Controllers\Http\`
  For HTTP controllers within a specific HMVC module.

* `\App\Modules\<ModuleName>\Controllers\Cli\`
  For CLI controllers within a specific HMVC module.

---

### Namespace Registration

In Luminova, controller namespaces should be registered during application initialization, typically within the `onCreate` or `__construct` method, using the router object in application. This ensures that route resolution can correctly locate controllers across your modules.

**Registering Controller Namespaces**

```php
// /app/Application.php

namespace App;

use Luminova\Core\CoreApplication;

class Application extends CoreApplication
{
    /**
     * Boot and register controller namespaces.
     */
    protected function onCreate(): void 
    {
        // Register namespaces for module-based controllers
        $this->router->addNamespace('\\App\\Modules\\Blog\\Controllers\\')
            ->addNamespace('\\App\\Modules\\User\\Controllers\\');
    }
}
```

> **Note:**
>
> * Do **not** include the `Http` or `Cli` subdirectories when registering namespaces. Only register up to `Controllers` (e.g., `App\Modules\Blog\Controllers`) instead of (`App\Modules\Blog\Controllers\Http`). Luminova will resolve the correct sub-namespace automatically based on request type.
> * The namespace for the **root module** is registered by default, so there is no need to register it manually.

***

### Routing in Luminova HMVC

Routing in Luminova's HMVC architecture works just like in standard MVC. You can define routes using either:

* [PHP Attributes](/routing/route-attribute.md) — for declarative, annotation-style route definitions.
* [Routing System Methods](/routing/url-routing.md) — using the `Luminova\Routing\Router` for programmatic route registration.

Both methods are fully supported within HMVC and can be used interchangeably across your modules.

---

### Route Loading

Luminova automatically resolves and loads the correct controller based on:

* The **request type**: `HTTP` or `CLI`
* The **active module**: as defined by `$this->app->setModule(...)`

The routing system also ensures that view rendering corresponds to the active module, allowing for clean separation and isolation of logic per module.

---

### HMVC Controllers

In Luminova's HMVC architecture, controllers function similarly to those in traditional MVC but are organized within individual modules. Each controller is responsible for handling incoming requests, coordinating with models for data processing, and returning the appropriate response via views.

**Example: Blog Controller**

```php
// /app/Modules/Blog/Controllers/Http/BlogController.php

namespace App\Modules\Blog\Controllers\Http;

use Luminova\Base\BaseViewController;
use Luminova\Attributes\Route;
use Luminova\Attributes\Prefix;
use App\Errors\Controllers\ErrorController;

#[Prefix(
    pattern: '/blog/(:root)',
    onError: [ErrorController::class, 'onWebError']
)]
class BlogController extends BaseViewController 
{
    protected function onCreate(): void 
    {
        // Set the current module name for view and template resolution
        $this->app->setModule('Blog');
    }

    #[Route('/blog', methods: ['GET'])]
    public function index(): int 
    {
        // Logic to display the blog index page
        return $this->view('blogs');
    }
}
```

> **Note:** Always register the module name using `$this->app->setModule('YourModuleName')` inside `onCreate()`. This enables the template engine to correctly locate views within the module context.

---

### HMVC CLI Controllers

CLI controllers in Luminova allow you to define commands that can be executed through the command line interface. These controllers are typically used for background tasks, maintenance commands, or other non-HTTP operations.

**Example: Blog CLI Controller**

```php
// /app/Modules/Blog/Controllers/Cli/BlogController.php

namespace App\Modules\Blog\Controllers\Cli;

use Luminova\Base\BaseCommand;
use Luminova\Attributes\Group;
use Luminova\Attributes\Route;

#[Group(name: 'blog')]
class BlogController extends BaseCommand 
{
    protected string $group = 'blog';
    protected string $name  = 'blog-command'; 
    protected array $usages  = [
        'php index.php blog sync',
    ];

    #[Route('sync', group: 'blog')] 
    public function sync(): int 
    {
        // Logic to handle blog synchronization from the CLI
        return STATUS_SUCCESS;
    }
}
```

> **Note:** In CLI controllers, `Route` attributes define specific command names, and the `Group` attribute is used to group related commands together. The command is then executed via the CLI using `php index.php <group> <command>`.

---

### Advantages of HMVC in Luminova

1. **Modularity**:
   Modules are self-contained and can be independently developed, tested, and managed. This reduces complexity, especially in large applications.

2. **Reusability**:
   Modules can be reused across different projects or parts of the same application, promoting code reuse and reducing duplication.

3. **Maintainability**:
   By organizing the application into logical, isolated modules, maintenance becomes easier. Updates and fixes can be made to specific modules without affecting the entire system.

4. **Scalability**:
   The application can grow more efficiently. New modules can be added without disrupting the core structure, allowing the application to scale seamlessly.
