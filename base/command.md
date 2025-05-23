# Abstract Base Class for Routable CLI Controllers

***

## Overview

Base command, Luminova's Framework for command-line operations - controller base class for NovaKit CLI Tool.

***

## Introduction

The `BaseCommand` class in Luminova’s framework is for building routable command-line tools (CLIs). It provides a structured way to define, organize, and execute custom CLI commands with flexibility.

Similar to HTTP controllers, `BaseCommand` uses routing system for command handling and supports routing via method names or PHP attributes. You can group related commands using the class-level `#[Group]` attribute, and define individual commands with the shared `#[Route]` attribute, though its configuration differs slightly from its HTTP counterpart.

Unlike [Luminova\Base\BaseConsole](/base/console.md), which handles basic commands interactions using `novakit`, `BaseCommand` supports full routing and is intended for use in front controllers like:

```bash
php /public/index.php <command>
```

> This makes it ideal for integrating CLI features directly into your application, using a familiar structure similar to HTTP controllers, without a new learning curve or the need to learn a separate command framework.

***

### Features

- **Command Metadata**  
  Create commands with clear usages, options, and descriptions to display help (`--help`).

- **Handle Execution and Input**  
  Automatically parse command-line arguments, validate inputs, and run the correct method.

- **Group Commands Logically**  
  Organize related commands into groups or modules to keep your code clean and maintainable.

- **Limit Access by User**  
  Restrict which system users are allowed to run commands defined in the controller.

- **Extend and Customize**  
  Add custom handlers, define new options, or integrate with third-party tools as needed.

- **Reuse Controller Logic**  
  Use your controller class in the same way as you would for web requests, enabling smooth integration between CLI and browser-based functionality.

***

## Class Definition

* Class namespace: `\Luminova\Base\BaseCommand`
* Parent class: [\Luminova\Command\Terminal](/commands/terminal.md)
* This class implements: [\Luminova\Interface\RoutableInterface](/interface/classes.md#routableinterface)

***

## Properties

### group

The group name for the current command controller class. This group is used for router capture authentication to ensure that commands within the same group are executed accordingly (e.g., `php index.php <command-group-name> <command> <arguments>`).

```php
protected string $group = '';
```

***

### name

The execution command name for the current controller class. This name is used internally to build information about your command, which can be utilized in displaying command help.

```php
protected string $name = '';
```

***

### usage

A description of your command usages. This can be either a `string` or an `array` of usage examples.

```php
protected string|array $usage = [];
```

***

### options

A more expressive way to describe your command options and their descriptive messages. The array key represents the command option, while the value is a description of the option (e.g., `['-f, --foo' => 'Foo description']`).

```php
protected array<string,mixed> $options = [];
```

***

### examples

Examples of how to use the command. This allows you to provide full examples demonstrating command usage.

```php
protected array<string,mixed> $examples = [];
```

***

### description

A detailed description of your command to help users understand its purpose and functionality.

```php
protected string $description = '';
```

***

### users

Defines a list of allowed system users who can execute the command. If the array is empty, all users are allowed by default.

```php
protected array<int, string> $users = [];
```

**Example:**

Only the following users are permitted to run this command:

```php
protected array<int, string> $users = ['www-data', 'ubuntu', 'admin'];
```

> **Note:** This is useful for restricting sensitive commands to specific system-level users.

---

### authentication

The `authentication` property allows you to configure login protection for CLI commands using either passwords or public/private key authentication.

**Supported Array Keys / Database Column Names:**

- **`auth`** `(string)` – Type of authentication, such as `password` or `key`.
- **`content`** `(string)` – The hashed password (for password login), public key content, or path to a public key file. Leave empty for public access without a password.
- **`updated_at`** `(datetime)` – Records the last time authentication was used or updated.

```php
protected ?array<string, mixed> $authentication = null;
```

---

**Examples**

You can load authentication data from a database or a PHP file:

**From Database:**
```php
[
   'storage' => 'database',
   'tableName' => 'foo_cli_users'
]
```

**From Filesystem (e.g., `auths.php`):**
```php
[
   'storage' => 'filesystem',
   'storagePath' => 'writeables/auth/cli_users.php'
]
```

> **Note:** When using the filesystem, your PHP file must return an array of users.

***

## Methods

The base command extends the [Luminova Terminal class](/commands/terminal.md), making all the methods available and ready to use in command controller class. 

### onCreate

Command on create method, an alternative method to `__construct()`.

```php
protected onCreate(): void
```

### help

This allows you to override the default help display implementation.
When you implement your custom help information display, you must return `STATUS_SUCCESS` else return `STATUS_ERROR` to tell the framework to keep using the default help implementation.

To call help command: `php index.php groupName --help`

```php
abstract public help(array $helps): int;
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$helps` | **array<string,mixed>** | Receives the helps information about command. |

**Return Value**

`int` - Return status code success as implemented, error using default implementation.

**Help arguments array keys**

- `class` - The class name of the command (Note: this key may not be available if you extend `BaseConsole`).
- `group`  - The group name of the command.
- `name` - The name of the command.
- `description` - The description of the command.
- `usages` - The usages of the command.
- `options` - The available options for the command.
- `examples` - The examples of the command.

***

### app

Initialize and retrieve the application instance.

```php
protected final app(): \App\Application
```

**Return Value**

`\App\Application` - Return the application instance.

***

### getString

Retrieves the full command-line execution string for the current command.

This method returns the entire command string used to run the current script, including the command name, and any arguments or options passed. Useful for logging or debugging purposes to see the exact command structure.

```php
protected final getString(): string
```

**Return Value**

`string` - Return the full command-line string.

**Example:** 

Given a command with: `php index.php foo user=1 --no-nocache`

**Outputs:**

```php
echo $this->getString();
// index.php index.php foo user=1 --no-nocache
```

***

### option

Retrieves an option value of executed command based on the specified key or alias. This method is an alias of the parent `getAnyOption` method.

```php
protected option(string $key, ?string $alias = null, mixed $default = false): mixed 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The primary key for the option. |
| `$alias` | **string\|null** | An optional alias for the option. |
| `$default` | **mixed** | The default value to return if the option is not found. |

**Return Value**

`mixed` - Return the value of the option, or the default if not found.

**Example:** 

Given a command with option `--verbose` or `-v`.

**Outputs:**

```php
$verbose = $this->option('verbose', 'v', false); 

// Returns true if either option is set.
```

Given a command with option `--address=192.160.0.1` or `-a=192.160.0.1`.

**Outputs:**

```php
$address = $this->option('address', 'a', null); 

// Returns 192.160.0.1 if either option is set.
```

***

### argument

Retrieves a command-line argument by its index or name, extending the parent functionality.

This method extends the parent's functionality by allowing arguments to be retrieved based on their name as well as their index. The parent `getArgument` method only supports retrieval by index, whereas this method adds the ability to retrieve arguments by name.

- If the argument is specified as a string (name), it searches all arguments for a match.
- If the argument is in "key=value" format, the value portion (after `=`) is returned.
- If an integer index is provided, it retrieves the argument by position.

```php
protected argument(string|int $index): mixed 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **string\int** | The index (integer) or name (string) of the argument. |

**Return Value**

`mixed` - Return the argument value, or the full argument if no '=' is found, or null if not found.

**Example:** 

Given a command with arguments:

```bash 
php index.php foo file=example.txt mode=write
```

**Outputs:**

```php
$file = $this->argument('file'); // Returns 'example.txt'
$mode = $this->argument('mode'); // Returns 'write'
```

Same as above:

```php
$file = $this->argument(1); // Returns 'example.txt'
$mode = $this->argument(2); // Returns 'write'
```

***

### upload

Uploads a file in command line interface to a specified directory.

This method handles uploading a file from either a local path or a base64 encoded string.
It creates the target directory if it doesn't exist and generates a unique filename if necessary.

```php
protected upload(string $file, string $directory, ?string $name = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **string** | The path to the file, text content or a base64 encoded string of the file content. |
| `$directory` | **string** | The target directory where the file should be uploaded. |
| `$name` | **string\|null** | Optional. The desired name for the uploaded file (e.g, `file.txt`).<br/>If null, a name will be generated. |

**Return Value**

`string|false` - Returns the path of the uploaded file on success, or false on failure.

***

### Help Command

To print help information related to a specific `novakit` command.

```bash
php index.php <command> --help
```

***

### Default Flags

Use these flags with commands to modify output formatting.

- **`--no-header`**: Omits the header from the output.
  ```bash
  php index.php <command> --no-header
  ```

- **`--no-color`**: Disables colored output.
  ```bash
  php index.php <command> --no-color
  ```

- **`--no-ansi`**: Disables ANSI formatting for terminals that don't support it.
  ```bash
  php index.php <command> --no-ansi
  ```
***

***

### Building Command Line Tools

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

**Blog Command with Code-Based Routing**

To define your commands using code-based routing, you can configure your routes in the routing file as follows:

```php
// /routes/cli.php

$router->group('blog', static function(Router $router) {
   // Command with argument
   $router->command('list', 'BlogCommand::blogs');

   // Command with method argument
   $router->command('list/limit/(:int)', 'BlogCommand::blogs');
});
```

***

**Blog Command with Attribute-Based Routing**

For attribute-based routing, you can directly annotate the `blogs` method in your command class:

```php
// /app/Controllers/Cli/BlogCommand.php

namespace App\Controllers\Cli;

use Luminova\Base\BaseCommand;
use Luminova\Attributes\Route;

class BlogCommand extends BaseCommand
{
	public function help(array $helps): int
	{
		return STATUS_ERROR;
	}

   // Command with argument
   #[Route('list', group: 'blog')]
   public function blogs(): int 
   {
      $limit = $this->getAnyOption('limit', 'l', 10);
      echo $limit;
      return STATUS_SUCCESS;
   }

   // Command with method argument
   #[Route('list/limit/(:int)', group: 'blog')]
   public function blogs(int $limit = 10): int 
   {
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

**Environment Configuration**: 

You can enable exception handling globally by setting the following option in your environment file:

```makefile
throw.cli.exceptions = true
```

**Temporary Enablement**: 

If you want to enable exception handling temporarily for the current script execution, you can use the following global function. Call it before your script runs or within the controller's `__construct`, `onCreate`, or any executing controller method:

```php
setenv('throw.cli.exceptions', 'true');
```