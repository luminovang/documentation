# NovaKit Commands

***

## Overview

Need to manage environment variables or create a sitemap? NovaKit has got you covered. Perform database operations, execute scripts, or start a development server seamlessly—all from the command line.

***

## Introduction

NovaKit Command Line Tool is a powerful tool provided by Luminova framework, for simplifying command-line operations and facilitating the development as well as building command-line applications. It offers a wide range of functionalities such as generating boilerplate code, scaffolds, templates, environment variables, sitemap, database operations, executing scripts, starting development server, and more.

***

## Building CLI Tools in Luminova

Creating command-line tools in Luminova is straightforward, thanks to its intuitive routing system, which mirrors HTTP routing. This makes it easy to apply familiar logic to your CLI commands.

### Command Controllers

To build a new command controller in Luminova:

1. **Create Your Command Controller**: Place your command controller classes in the `/app/Controllers/` directory. Ensure these classes extend `Luminova\Base\BaseCommand`.

2. **Command Execution Format**: To execute a command, use the following format:

   ```bash
   php index.php <group-name> <command> <argument>
   ```

   The group name should come immediately after `php index.php`, followed by the command name and any parameters. Arguments can be passed in any order after the command group name.

### Examples

**Example: Blog Command with Code-Based Routing**

Define your commands in the routing configuration:

```php
$router->group('blog', static function(Router $router){
    $router->command('list', 'BlogCommand::listBlog');
});
```

**Example: Blog Command with Attribute-Based Routing**

Add the `Route` attribute directly before the `listBlog` method:

```php
#[Route('list', group: 'blog')]
```

**Using Arguments**

You can specify arguments for your commands in different ways:

- **Using Options:**

  ```bash
  php index.php blog list --limit=3
  ```

  Corresponding controller method:

  ```php
  public function listBlog(): int 
  {
      $limit = $this->getAnyOption('limit', 'l', 50);
      echo $limit;
  }
  ```

- **Using Direct Arguments:**

  ```bash
  php index.php blog list limit=3
  ```

  Corresponding controller method:

  ```php
  public function listBlog(int $limit = 50): int 
  {
      echo $limit;
  }
  ```

***

### Controller Help

Print help information related to a command controller group.

```bash
php index.php <command-group> --help
```

Print help for example `blog` command.

```bash
php index.php blog --help
```

***

### Catch Exceptions

Exceptions are handled by the framework in the `CLI`. To catch exceptions, you can enable it in your environment file by setting `throw.cli.exceptions` to `true`. Alternatively, you can enable it temporarily for the current script execution by calling the following function before your script or within the controller's `__construct` or `onCreate` method:

```php
setenv('throw.cli.exceptions', true);
```

***

### Novakit Help

To show help information related to `novakit` commands.

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
php novakit create:controller --help
```

***

## Generators
The generator commands allow you to easily generate a skeleton for a controller class, utils class, or a view file.

### Utils Class

To create a utility class for your application use the below example command.
The class will be saved in `/app/Controllers/Utils/`

```bash
php novakit create:class "myClass" --extend "baseClassName" --implement "myClassInterface"
```

***

### Model Class

To create a database model class for your application use the below example command.
The class will be saved in `/app/Controllers/Models/`

```bash
php novakit create:model "myModel" 
```

***

### Controllers

To create a new controller class for your application use the below example command.
The class will be saved in `/app/Controllers/`

```bash
php novakit create:controller "PayStackController"  --type "view"
```

> You can pass the type of `API` to extend `\Luminova\Base\BaseController` or `view` to extend `\Luminova\Base\BaseViewController`.
> 
> For command controller class extend and implement will ignore, use flag `--type "command"`

***

### View File

To create a utility class for your application use the below example command.
The class will be saved in `/resources/views/`

```bash
php novakit create:view "blog" 
```

> To create a view file in a subdirectory in `/resources/views/` folder, use flag `--dir "mySubDir"` 

***

### Routing Context

To install a new routing context use the below example command.

```bash
php novakit context "test"'
```

> The above command will create a file in `/routes/` directory and update your `/public/index.php` with a new context.
> 
> To disable adding an error handler in `index.php` use flag `--no-error`
> > *Note:* If the error handler is not disabled, you need to manually create the method name in your View errors class in `/app/Controllers/Config/ViewErrors.php`.

***

### Sitemap

To generate your website sitemap use the below command.

```bash
php novakit generate:sitemap
```

> To configure the sitemap domain and ignore list, it can be done here `/app/Controllers/Config/Sitemap.php`.

***

### App Key

To generate your application encryption key use the below command.

```bash
php novakit generate:key
```

> If you wish to generate without saving the key to env file, use flag `--no-save`.

***

### Add/Update Env Key

To add new or update value to your env file use the below command.

```bash
php novakit env:add --key="my_new_key" --value="my key value"
```

> If you wish to generate without saving the key to env file, use flag `--no-save`.

***

### Remove Env Key

To remove key from your env file use the below command.

```bash
php novakit env:remove --key="my_old_key"
```

### Build Project

To package your application for publishing, use the below command to archive the project files required for production. 

```bash 
php novakit build:project --type zip
```