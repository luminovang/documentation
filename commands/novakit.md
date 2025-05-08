# Novakit Commands and Integration Patterns

***

## Overview

Novakit is Luminova's command-line tool for managing application development, such as database, generating sitemaps, or executing custom-defined console-based commands using the Novakit CLI handler.

***

## Introduction

**NovaKit** is the built-in **Command Line Interface (CLI)** tool for the **Luminova** framework. It simplifies development tasks by allowing you to execute and manage various operations directly from the terminal.

The NovaKit CLI is powered by a `novakit` file located at the **project root**. This is a special PHP script with a [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) line (`#!/usr/bin/env php`) that makes it executable like a native terminal command.

---

### What Does NovaKit Do?

NovaKit acts like a **router for CLI commands**. It prepares the command input and passes it to the correct controller, just like Luminova’s HTTP routing system does for web requests.

With NovaKit, you can:

- Generate boilerplate code, controllers, and scaffolds.
- Manage environment variables and config files.
- Run database tasks like:
  - Migrations
  - Versioning
  - Seeding
- Schedule and execute cron jobs.
- View or clear application logs.
- Start a local PHP development server.
- Run Composer commands more easily.
- And more — fully customizable to your application needs.

---

### NovaKit Console Commands

To build your own CLI commands using NovaKit console:

1. Extend the `Luminova\Base\BaseConsole` class.
2. Define your logic in `run(...)` method.
3. Register your commands in CLI novakit command file (`/bin/.novakit-console.php`).

Once created, you can run the command via:

```bash
php novakit your-command
```

Unlike `BaseCommand` (used via `index.php`), `BaseConsole` commands:
- Run **faster** because they skip routing and attribute parsing.
- Are better suited for **utility based ** CLI tools.

For details on creating custom commands, see: [Base Console Controller Class](/base/console.md)

---

## NovaKit Command Reference

### List Available Commands

To view all available NovaKit commands:

```bash
php novakit list
```

---

### View Help Information

NovaKit provides flexible help options for understanding available commands:

**General Help:**

```bash
php novakit --help
```

**Detailed Help for All Commands:**

```bash
php novakit --help --all
```

**Help for a Specific Command:**

Replace `log` with any available command:

```bash
php novakit log --help
```

---

### Show System Information

To display system details relevant to your terminal environment:

```bash
php novakit --system-info
```

***

### Default Command Flags

Use these flags with commands to modify output formatting.

**No Header:**

Omits the header from the output.

```bash
php novakit <command> --no-header
```

- **No Color:**

Disables colored output.

```bash
php novakit <command> --no-color
```

**No ANSI:** 

Disables ANSI formatting for terminals that don't support it.

```bash
php novakit <command> --no-ansi
```

---

## Basic NovaKit Generator Commands

NovaKit includes helpful commands to quickly generate boilerplate files for common components in your application—like utility classes, models, controllers, and views.

---

### Create a Utility Class

Generate a class in the `/app/Utils/` directory:

```bash
php novakit create:class "MyClass" \
    --extend "Foo\Bar\BarClassName" \
    --implement "Foo\Interface\FooClassInterface"
```

- `--extend`: (optional) Base class to extend.  
- `--implement`: (optional) Interface to implement.

---

### Creating a Model Class

You can generate a database model class for both MVC and HMVC applications.

**Default Locations:**

* **MVC Application**: Stored in `/app/Models/`
* **HMVC Application**: Stored in `/app/Modules/Models/`
  Use the `--module <ModuleName>` flag to specify a custom module name.

```bash
php novakit create:model "MyModel" \
    --implement "Optional\Interface\FooClassInterface"
```

**Example for a Custom HMVC Module:**

```bash
php novakit create:model "MyModel" --module "Info"
```

---

### Create a Controller Class

Generate a controller in `/app/Controllers/Http/`, or in `/app/Modules/<module>/Controllers/Http/` for HMVC apps:

**For MVC or root HMVC**
```bash
php novakit create:controller "PayStackController" --type "view"
```

**For Custom HMVC Module**

```bash
php novakit create:controller "PayStackController" --type "view" --module="Blog"
```

**Types:**

- `view`: Extends `Luminova\Base\BaseController`  
- `command`: Extends `Luminova\Base\BaseCommand`  
- `console`: Extends `Luminova\Base\BaseConsole`

> **Note:** When using the HMVC design pattern, specify `--module=<moduleName>` to install the controller in the appropriate module directory.  
>
> If the module is not provided, the controller will be installed in the root module controller directory.  
> The module name is case-sensitive and must match the directory name exactly.

---

### Create a View File

Generate a view file in `/resources/Views/`, or in a module path like `/app/Modules/<module>/Views/`:

```bash
php novakit create:view "blog"
```

To create the view inside a subdirectory:

```bash
php novakit create:view "blog" --dir "articles"
```

***

#### Route URI Prefix Context

To install a new route URI prefix context use the below example command.

```bash
php novakit context "test"
```

> The above command will create a file in `/routes/` directory and update your `/public/index.php` with a new context.
> 
> To disable adding an error handler in `index.php` use flag `--no-error`
> > **Note:** If the error handler is not disabled, you need to manually create the method name in your View errors class in `/app/Errors/Controllers/ErrorController.php`.

---

## Basic NovaKit Utility Commands

NovaKit includes several utility commands to help you manage your project, environment, and deployment.

---

### Generate Sitemap

Creates a `/public/sitemap.xml` for your website:

```bash
php novakit generate:sitemap
```

- Configure sitemap domain and ignored routes in:  
  `/app/Config/Sitemap.php`

---

### Generate Application Key

Creates a secure encryption/decryption key for your app:

```bash
php novakit generate:key
```

To generate the key without saving it to your `.env` file:  

```bash
php novakit generate:key --no-save
```

---

### Add or Update Environment Variable

Adds or updates a key-value pair in your `.env` file:

```bash
php novakit env:add --key="MY_NEW_KEY" --value="Some value"
```

To for runtime and skip saving to `.env`:

```bash
php novakit env:add --key="KEY" --value="Value" --no-save
```

---

### Remove Environment Variable

Deletes a key from your `.env` file:

```bash
php novakit env:remove --key="MY_OLD_KEY"
```

---

### Build Project for Deployment

Packages your application for production:

```bash
php novakit build:project --type zip
```

> Supported types: `zip`, `tar`, or `none` (default).

---

### Managing Log Files

NovaKit allows you to view and manage application log files by specifying the log level you want to interact with (e.g., `debug`, `error`, `info`).

---

#### View Recent Log Entries

Display the 5 most recent entries in the `debug` log:

```bash
php novakit log --level=debug
```

---

#### View Log Entries with Offset and Limit

Display log entries from line `5` to `20` in the `debug` log:

```bash
php novakit log --level=debug --start=5 --end=20
```

---

#### Clear a Log File

Clear all entries in the `debug` log:

```bash
php novakit log --level=debug --clear
```

---

> **Note:** Replace `debug` with the appropriate log level for your application. Supported levels typically include `info`, `error`, `warning`, `debug`, etc., depending on your configuration.

---

## Novakit Class

The `Novakit` helper class provides a set of static methods for registering custom console commands, checking if a command exists, and performing various other console-related tasks. These methods can be called statically from anywhere in your application when needed.

***

## Class Definition

* Class namespace: `\Luminova\Command\Novakit`

***

## Methods

### constructor

Initialize the Novakit console terminal instance.

```php
public __construct()
```

***

### run

Entry point for executing Novakit CLI commands.

```php
public run(array<string,mixed> $commands): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$commands` | **array<string,mixed>** | The raw command-line arguments, typically from `$_SERVER['argv']` or `$argv`. |

**Example:**

```php
(new Console())->run($_SERVER['argv']);
```

***

### find

Find the fully qualified controller class for a given command.

This method attempts to resolve the command to a known controller class. It first checks predefined system commands, then searches registered console commands based on the provided mode (`system` or `global`).

```php
public static find(string $command, string $mode = 'global'): ?class-string<\Luminova\Base\BaseConsole>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The command string (e.g., `create:controller`, `db:migrate`, `my-command`). |
| `$mode` | **string** | The lookup mode: `system` for internal commands, or `global` for custom/console commands. |

**Return Value:**

`class-string<BaseConsole>|null` - Returns the fully qualified class name if found, or `null` if not.

***

### command

Registers a new console command with an optional metadata definition.

This method maps a command name to its controller class and optionally stores command metadata such as group, description, usage examples, options, and more.

```php
public static command(string $name, class-string<Luminova\Base\BaseConsole> $class, array $properties = []): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The command name (e.g., 'foo'). |
| `$class` | **class-string<BaseConsole>** | The fully qualified class name that handles the command. |
| `$properties` | **array** | (optional) Additional metadata for the command based on protected properties. |

**Return Value:**

`bool` - Returns true if the command was successfully registered, false if it already exists.

***

### get

Get a specific property from a registered command's metadata.

```php
public static get(string $command, string $name): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The command name. |
| `$name` | **string** | The property name to retrieve (e.g., `group`, `description`). |

**Return Value:**

`mixed` - Returns the value of the specified property if it exists, or `null` otherwise.

***

### getCommand

Retrieve full metadata for a command based on its protected controller properties.

The returned array may include the following keys:
`name`, `group`, `description`, `usages`, `options`, `examples`, etc.

```php
public static getCommand(string $command, string $mode = 'global'): array<string,mixed>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The command name (e.g, `my-command`, `env`) etc... |
| `$mode` | **string** | The command mode to check within (supported: `system` or `global`). |

**Return Value:**

`array<string,mixed>` - Returns an associative array of command metadata.

***

### has

Check if a command exists globally (i.e., `user-defined` or `console` command).

```php
public static has(string $command): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The name of the command to check. |

**Return Value:**

`bool` - Returns true if the command exists globally, otherwise, false.