# NovaKit CLI

***

## Overview

Need to manage environment variables or create a sitemap? NovaKit has got you covered. Perform database operations, execute scripts, or start a development server seamlessly—all from the command line.

***

## Introduction

NovaKit Command Line Tool is a powerful tool provided by Luminova framework, for simplifying command-line operations and facilitating the development as well as building command-line applications. It offers a wide range of functionalities such as generating boilerplate code, scaffolds, templates, environment variables, sitemap, database operations, executing scripts, starting development server and more.

***
## Helps

### Controller Help

Print help information for a command group in controller.

```bash
php index.php blog --help
```

***

### Novakit Help

Print help all novakit help commands.

```bash
php novakit --help
```

Print help a specific novakit help commands.

```bash
php novakit --help create:controller
```

***

## Generators
The generator commands allow you to easily generate a skeleton for a controller class, utils class or a view file.

### Utils Class

To create a utility class for your application use the below example command.
The class will be saved in `/app/Controllers/Utils/`

```bash
php novakit create:class "myClass" --extend "baseClassName" --implement "myClassInterface"
```

***

### Controllers

To create a new controller class for your application use the below example command.
The class will be saved in `/app/Controllers/`

```bash
php novakit create:controller "PayStackController"  --type "view"
```

> You can pass type of `api` to extend `\Luminova\Base\BaseController` or `view` to extend `\Luminova\Base\BaseViewController`.
> 
> For command controller class extend and implement will ignored, use flag `--type "command"`

***

### View File

To create a utility class for your application use the below example command.
The class will be saved in `/resources/views/`

```bash
php novakit create:view "blog" 
```

> To create view file in a sub directory in `/resources/views/` folder, use flag `--dir "mySubDir"` 

***

### Routing Context

To install new routing context use the below example command.

```bash
php novakit context "test"'
```

> The above command will create a file in `/routes/` directory and update your `/public/index.php` with new context.
> 
> To disable adding error handler in `index.php` use flag `--no-error`
> > *Note:* If error handler is not disabled, you need to manually create the method name in your View errors class in `/app/Controllers/Config/ViewErrors.php`.

***

### Sitemap

To generate your website sitemap use the below command.

```bash
php novakit generate:sitemap
```

> To configure sitemap domain and ignore list it can be done here `/app/Controllers/Config/Sitemap.php`.

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