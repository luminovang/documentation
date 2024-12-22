# Hierarchical Model-View-Controller (HMVC) Design Patterns

***

## Overview

The HMVC offers a simple way to build scalable, modular applications. By organizing the application into logical modules, which can improve reusability, maintainability, and application performance.

***

## Introduction

HMVC (Hierarchical Model-View-Controller) is an advanced architectural pattern that extends the standard MVC (Model-View-Controller) structure by supporting modularity. Each module in Luminova `HMVC` has its own `MVC` components (Models, Views, and Controllers). This modular approach helps in providing a cleaner, more maintainable, and scalable approach to developing complex applications.

In an HMVC application, modules operate independently, making it easier to divide the system into logical sections that can be worked on in parallel or reused across different parts of the application.

***

### Key Concepts

#### 1. Modules
A **module** is a self-contained component that encapsulates its own controllers, models, views, and other necessary resources. Modules in Luminova HMVC are designed to be reusable and scalable, often representing a distinct part of your application (e.g., user management, blog, etc.).

### 2. Controllers
Controllers in Luminova HMVC are responsible for handling requests and directing the flow of the application. Controllers can be created within either:
- **HTTP Controllers**: Located in `app/Modules/Controllers/Http/` or `app/Modules/<Module>/Controllers/Http/` and handle HTTP requests.
- **CLI Controllers**: Located in `app/Modules/Controllers/Cli/` or `app/Modules/<Module>/Controllers/Cli/` and handle command-line interface requests.

 > **Note:** You must call `$this->app->setModule('Foo')` within the controller's `onCreate` or `__construct` method to register your custom module name. Only the module's directory name (e.g., `Foo`) is required, not the full path.

#### 3. Views
Views handle the presentation logic of the application. Each module can have its own views, and the path to these views is scoped to the module in which they are defined. Views are generally located in the `app/Modules/Views/` or `app/Modules/<Module>/Views/` directory within each module.

#### 4. Models
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

**Directory Structure Breakdown:**

- **Modules**: Contains all application modules.
  - **<Module>**: Each module is a self-contained section of the application.
    - **Controllers**: Contains all controller logic for that module.
    - **Models**: Contains all model logic for that module.
    - **Views**: Contains all view templates for that module.

***

### Namespaces

In Luminova HMVC, namespace registration is important for managing modules. The namespace conventions follow a specific pattern based on whether it's a regular MVC application or HMVC module.

**MVC Namespace:**
- `\App\Controllers\Http\` for HTTP controllers.
- `\App\Controllers\Cli\` for CLI controllers.

**HMVC Namespace:**
- `\App\Modules\<ModuleName>\Controllers\Http\` for HTTP controllers in the module.
- `\App\Modules\<ModuleName>\Controllers\Cli\` for CLI controllers in the module.

**Namespaces Registration:**

In Luminova, namespaces are typically registered within the application's `onCreate` or `__construct` methods, using the router object. This ensures that controller namespaces are properly set up when the application is initialized.

**Example: Registering Namespaces**

```php
// /app/Application.php
<?php
namespace App;

use Luminova\Core\CoreApplication;

class Application extends CoreApplication
{
    /**
     * Initialize namespaces for the application.
     */
    protected function onCreate(): void 
    {
        // Register namespaces for different modules
        $this->router->addNamespace('\\App\\Modules\\Blog\\Controllers\\')
                     ->addNamespace('\\App\\Modules\\User\\Controllers\\');
    }
}
```

> **Note:** When registering the namespace for routing, omit the `Http` and `Cli` suffixes. Instead, register your namespace module and end it in `Controllers` (e.g., `App\Modules\Blog\Controllers`), not `App\Modules\Blog\Controllers\Http` or `App\Modules\Blog\Controllers\Cli`.

***

### Routing in Luminova HMVC

Routing in Luminova HMVC works the same way as in regular MVC. You can use either [PHP Attributes](/routing/route-attribute.md) for route definition or the method-based routing provided by the [Luminova\Routing\Router](/routing/url-routing.md) class. Both approaches are fully supported within the HMVC architecture.

***

### Route Loading

In Luminova's HMVC architecture, the routing system automatically loads the appropriate controller based on the request type (`HTTP` or `CLI`) and the module structure. This process also takes into account the module name defined in `$this->app->setModule(...)` for rendering views.

***

### Controllers in HMVC

Controllers in Luminova HMVC are very similar to those in traditional MVC structures, but they are organized within modules. Each controller handles incoming requests, processes the necessary data (using models), and returns the appropriate response (using views).

**Example Controller:**
```php
// /app/Modules/Blog/Controllers/Http/BlogController.php

<?php
namespace App\Modules\Blog\Controllers\Http;

use Luminova\Base\BaseViewController;
use Luminova\Attributes\Route;
use Luminova\Attributes\Error;
use App\Controllers\Errors\ViewErrors;

#[Error(onError: [ViewErrors::class, 'onWebError'])]
class BlogController extends BaseViewController 
{
    protected function onCreate(): void 
    {
        $this->app->setModule('Blog');
    }

    #[Route('/blog', methods: ['GET'])]
    public function index(): int 
    {
        // Logic to handle showing the blog index page

        return $this->view('blogs');
    }
}
```

**For CLI controllers:**
```php
// /app/Modules/Blog/Controllers/Cli/BlogController.php
<?php
namespace App\Modules\Blog\Controllers\Cli;

use Luminova\Base\BaseCommand;
use Luminova\Attributes\Route;

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
        // Logic to handle CLI-based blog synchronization

        return STATUS_SUCCESS;
    }
}
```

***

## Advantages of HMVC in Luminova

1. **Modularity**: Each module can be independently developed and managed, reducing complexity in large applications.
2. **Reusability**: Modules can be reused across multiple applications or different parts of the same application.
3. **Maintainability**: By breaking down the application into logical modules, the code becomes easier to maintain and update.
4. **Scalability**: The application can grow by adding more modules without affecting the core structure.