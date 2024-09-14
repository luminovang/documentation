# Nova-Kit Command Patterns and Integrations

***

## Overview

Need to manage environment variables or create a sitemap? NovaKit has got you covered. Perform database operations, execute scripts, or start a development server seamlessly—all from the command line.

***

## Introduction

The Luminova framework includes **NovaKit**, a Command Line Utility Tool designed to simplify application development. This tool allows for the extension and implementation of custom command-line logic tailored to your application's needs through the [Base Command Class](/base/command.md) and `Base Console` classes. By extending `Base Command`, you can create command-line tools that operate similarly to HTTP `Controller` methods, supporting command routing using [Route Attributes](/routing/route-attribute.md).

### What Is NovaKit?

NovaKit is a powerful tool provided by the Luminova framework to simplify command-line operations. It facilitates the development of console-level tools that can be executed directly as `novakit` commands. NovaKit offers various functionalities, including:

- Generating boilerplate code.
- Creating scaffolds and templates.
- Managing environment variables.
- Handling database operations like migration, versioning and seeding.
- Managing application logs.
- Executing cron tasks on production environment.
- Running a PHP development server.
- And more.

***

### Listing Available NovaKit Commands

To display the list of available NovaKit commands, open your command line tool, navigate to your project directory where the `novakit` file is located, and run:

```bash
php novakit list
```

### Novakit Help Command

To show general help information related to `novakit` commands.

Print `novakit` help information.

```bash
php novakit --help
```

Print help all `novakit` commands help information.

```bash
php novakit --help --all
```

To print help information related to a specific `novakit` command.

```bash
php novakit log --help
```

***

### Basic NovaKit Generators Commands

The generator commands allow you to easily generate a skeleton for a controller class, utils class, or a view file.

#### Utils Class

To create a utility class for your application use the below example command.
The class will be saved in `/app/Utils/`

```bash
php novakit create:class "myClass" --extend "baseClassName" --implement "myClassInterface"
```

***

#### Model Class

To create a database model class for your application use the below example command.
The class will be saved in `/app/Models/`

```bash
php novakit create:model "myModel" 
```

***

#### Controllers

To create a new controller class for your application use the below example command.
The class will be saved in `/app/Controllers/`

```bash
php novakit create:controller 'PayStackController'  --type 'view'
```

> You can pass the type of `API` to extend `\Luminova\Base\BaseController` or `view` to extend `\Luminova\Base\BaseViewController`.
> 
> For command controller class extend and implement will ignore, use flag `--type "command"`

***

#### View File

To create a utility class for your application use the below example command.
The class will be saved in `/resources/views/`

```bash
php novakit create:view "blog" 
```

> To create a view file in a subdirectory in `/resources/views/` folder, use flag `--dir "mySubDir"` 

***

#### Routing Context

To install a new routing context use the below example command.

```bash
php novakit context "test"'
```

> The above command will create a file in `/routes/` directory and update your `/public/index.php` with a new context.
> 
> To disable adding an error handler in `index.php` use flag `--no-error`
> > *Note:* If the error handler is not disabled, you need to manually create the method name in your View errors class in `/app/Controllers/Errors/ViewErrors.php`.

***

#### Sitemap

To generate your website sitemap use the below command.

```bash
php novakit generate:sitemap
```

> To configure the sitemap domain and ignore list, it can be done here `/app/Config/Sitemap.php`.

***

#### Application Cryptic Key

To generate your application encryption and decryption key use the below command.

```bash
php novakit generate:key
```

> If you wish to generate without saving the key to env file, use flag `--no-save`.

***

#### Add/Update Environment Variables

To add new or update value to your env file use the below command.

```bash
php novakit env:add --key='my_new_key' --value='my key value'
```

> If you wish to generate without saving the key to env file, use flag `--no-save`.

***

#### Remove Environment Variable

To remove key from your env file use the below command.

```bash
php novakit env:remove --key='my_old_key'
```

#### Build Exportable Project Files

To package your application for publishing, use the below command to archive the project files required for production. 

```bash 
php novakit build:project --type zip
```

***

### Managing Application Log Files

To manage your application log files, you will need to specify the log level you want to view or manage.
In this example we will be using `debug`.

** Display the 5 Most Recent Entries**

To view the 5 most recent entries in the debug log:

```bash
php novakit log --level=debug
```

** Display Log Entries Using offset and limit** 

To display debug log entries starting from line `5` and showing only the next `20` entries:

```bash
php novakit log --level=debug --start=5 --end=20
```

** Clear the Entire Log File** 

To clear all entries from the debug log file:

```bash
php novakit log --level=debug --clear
```

***

## Building Command Line Tools

Creating command-line tools in Luminova is straightforward, thanks to its intuitive routing system, which resembles HTTP routing. This familiarity makes it easy to apply known logic to your CLI commands.

#### Command Controllers

To create a new command controller in Luminova:

1. **Create Your Command Controller**: Place your command controller classes in the `/app/Controllers/` directory, alongside your HTTP controller classes. Ensure these classes extend `Luminova\Base\BaseCommand`.
2. **Define Your Method**: Use either PHP Attributes or code-based routing with the Router class methods for command implementations.
3. **Optional Help Display**: Implement a command help display with styling and formatting as desired, or use the default display implementation.

**Command Execution Pattern**:

To execute a custom controller command, open your command line tool, navigate to your application's public directory with `cd path/to/your-project/public`, and run the command following this pattern:

```bash
php index.php <group-name> <command-name> <arguments>
```

Print help information related to a command controller group.

```bash
php index.php <group-name> --help
```

The `group-name` should be immediately after `php index.php`, followed by the `command-name` and any parameters. Arguments can be passed in any order after the command group name.

***

### Routing Command Controllers

#### Example: Blog Command with Code-Based Routing

To define your commands using code-based routing, you can configure your routes in the routing file as follows:

```php
// /routes/cli.php

<?php 
$router->group('blog', static function(Router $router) {
    // Command with argument
    $router->command('list', 'BlogCommand::blogs');

    // Command with method argument
    $router->command('list/limit/(:int)', 'BlogCommand::blogs');
});
```

***

#### Example: Blog Command with Attribute-Based Routing

For attribute-based routing, you can directly annotate the `blogs` method in your command class:

```php
// app/Controllers/BlogCommand.php

<?php
namespace App\Controllers;

use Luminova\Base\BaseCommand;

class BlogCommand extends BaseCommand
{
    // Command with argument
    #[Route('list', group: 'blog')]
    public function blogs(): int {
        $limit = $this->getAnyOption('limit', 'l', 10);
        echo $limit;
        return STATUS_SUCCESS;
    }

    // Command with method argument
    #[Route('list/limit/(:int)', group: 'blog')]
    public function blogs(int $limit = 10): int {
        echo $limit;
        return STATUS_SUCCESS;
    }
}
```

> **Note:** Both implementations achieve the same result.

***

### Executing Commands with Arguments

You can specify arguments for your commands in various ways. For example, to list blogs with a limit of 3:

```bash
php index.php blog list --limit=3
```

***

### Displaying Command Help

To print help for the `blog` command, use the following command:

```bash
php index.php blog --help
```

***

### Catching Exceptions

In the Luminova framework, exceptions in CLI operations are managed effectively. To enable exception handling, follow these guidelines:

#### Enable Exception Handling

1. **Environment Configuration**: 
   You can enable exception handling globally by setting the following option in your environment file:

   ```plaintext
   throw.cli.exceptions = true
   ```

2. **Temporary Enablement**: 
   If you want to enable exception handling temporarily for the current script execution, you can use the following global function. Call it before your script runs or within the controller's `__construct`, `onCreate`, or any executing controller method:

   ```php
   setenv('throw.cli.exceptions', 'true');
   ```