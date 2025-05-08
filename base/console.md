# Abstract Base Class for Novakit CLI Console Controllers

***

## Overview

Provides a lightweight, extensible base class for defining and executing custom CLI commands using the novakit binary, bypassing traditional routing for faster, direct command handling.

***

## Introduction

The `BaseConsole` is a lightweight command-line helper designed for building CLI commands in Luminova using the `novakit` handler, bypassing the need for the routing system like the `Luminova\Base\BaseCommand` class does.

It provides a fast and efficient way to build developer-defined commands for your application. Unlike `BaseCommand`, which integrates with the routing system and expects execution via `cd /public` and `php index.php`, the `BaseConsole` is executed directly using the `novakit` command handler (e.g., `php novakit hello`), making it faster and simpler for direct CLI tasks.

---

## Key Features

* **Lightweight and fast:** No routing or HTTP kernel required. Uses [Novakit Command Handler Directly](/commands/novakit.md).
* **No need for `cd /public`:** Runs directly from project root.
* **Straightforward API:** Implements a simple `run()` method to handle execution.
* **Custom command parsing:** Gives you full control over parsing command options.
* **Better performance for pure CLI apps.**
* **Compatible with `BaseCommand` logic but optimized for CLI-only use.**

---

### BaseConsole vs BaseCommand

| Feature                 | `BaseConsole`              | `BaseCommand`                          |
| ----------------------- | -------------------------- | -------------------------------------- |
| CLI execution           | Uses `php novakit`         | Uses `php index.php` or routed command |
| Routing required        | No                       | Yes                                  |
| Public directory access | Not needed               | Required                             |
| Command registration    | Manual via console file | Automatic via attributes               |
| Performance             | Faster                  | Slightly slower (routing overhead)  |
| Usage style             | Direct run method          | Controller-like structure              |

---

### Implementation

To define a new CLI command, extend the `BaseConsole` class and implement the `run()` method.

```php
// /app/Console/ConsoleHelloCommand.php

namespace App\Console;

use Luminova\Base\BaseConsole;

class ConsoleHelloCommand extends BaseConsole
{
   // Command metadata
   protected string $name = 'hello';
   protected string $group = 'Demo';
   protected string $description = 'Greets a user by name.';
   protected array|string $usages = [
      'php novakit hello --name=Peter',
      'php novakit hello --help'
   ];
   protected array $options = [
      '-n, --name' => 'The name of the user to greet.'
   ];

   // Handle command execution
   public function run(?array $options = []): int
   {
      $this->term->perse($options);
      $name = $this->term->getAnyOption('name', 'n');

      $this->term->writeln("Hello {$name}!");
      return STATUS_SUCCESS;
   }
}
```

> **Important:**
> When using `BaseConsole`, routing is not automatically handled. If your application uses one controller to handle multiple commands, you'll need to manually route and dispatch based on input data.
> * The `run()` method is the entry point for the command logic.
> * You must call `$this->term->perse($options)` to parse input from CLI arguments.

---

### Command Registration

Once your command class is implemented, you need to register it so it can be executed via the `novakit` CLI tool.

The `.novakit-console.php` file located in the `/bin/` directory is used to register all your custom console commands for terminal usage.

> You can register commands using either the `Console::command()` method or by returning an associative array of command names mapped to their handler classes.

**Using `Console::command()` (Recommended)**

This method allows you to register commands one by one with full support for metadata such as description, options, usages, and examples.

```php
// /bin/.novakit-console.php

use Luminova\Command\Novakit;
use App\Console\ConsoleHelloCommand;
use App\Console\FooCommand;

// Basic command registration
Novakit::command('hello', ConsoleHelloCommand::class);

// Registering with metadata
Novakit::command('foo', FooCommand::class, [
   'group' => 'Bar',
   'description' => 'Executes the foo logic.',
   'usages' => [
      'php novakit foo'
   ],
   'options' => [
      '-b, --bar' => 'Run the command with bar option'
   ],
   'examples' => [
      'php novakit foo -b="bra"' => 'Executes foo with "bra" as value'
   ]
]);
```

---

**Using an Array (Simplified)**

This method returns a plain associative array where command names are mapped to their class names.

```php
// /bin/.novakit-console.php

return [
   'hello' => App\Console\ConsoleHelloCommand::class,
   'foo'   => App\Console\FooCommand::class
];
```

> This format is **simple** but does **not** support registering metadata like description, options, usages, or examples.
> Metadata will be extractly from controller class as needed.

***

## Class Definition

* Class namespace: `\Luminova\Base\BaseConsole`

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
protected array $users = ['www-data', 'ubuntu', 'admin'];
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

---

### app

Provides access to the core application instance.  
It is **lazy-loaded**, meaning it’s only initialized when accessed, helping to reduce memory usage.

```php
protected ?App\Application<Luminova\Instance\LazyInterface> $app = null;
```

---

### term

Gives you access to the terminal command helper.  
Also **lazy-loaded**, this allows you to run shell commands and utilities from within your controller.

```php
protected ?Luminova\Command\Terminal<Luminova\Instance\LazyInterface> $term = null;
```

***

## Methods

### constructor

Initializes the console command controller.

This constructor is responsible for setting up the command environment. It lazily loads required objects such as the terminal handler, application object and invokes the `onCreate()` lifecycle hook automatically.

```php
public function __construct()
```

---

### onCreate

An optional method that acts as a post-construction hook.

You can override this method to perform any setup tasks that would normally go in the constructor.
This approach allows you to keep the constructor clean and maintain separation of logic.

```php
protected function onCreate(): void
```

**Usage Example:**

```php
protected function onCreate(): void
{
   $this->term->writeln('Console command initialized.');
}
```

> If `onCreate()` is defined in your command class, it will be called automatically after the constructor completes.

---

### help

The `help()` method allows you to override the default help output for a command.

When implemented, this method gives you full control over how help information is displayed for your command.
Return `STATUS_SUCCESS` to confirm that your custom help was displayed successfully, or return `STATUS_ERROR` to fall back to the default help renderer.

---

```php
abstract public help(array $helps): int
```

**Parameters:**

| Name     | Type                   | Description                                                         |
| -------- | ---------------------- | ------------------------------------------------------------------- |
| `$helps` | **array<string,mixed>** | An associative array containing detailed help data for the command. |

**Return Value**

`int` — Return `STATUS_SUCCESS` to confirm custom help was displayed, or `STATUS_ERROR` to fall back to the default implementation.

**Help Array Keys**

Below are the available keys in the `$helps` array:

| Key           | Type     | Description                                    |
| ------------- | -------- | ---------------------------------------------- |
| `class`       | **string** | The fully-qualified class name of the command. |
| `group`       | **string** | The group/category of the command.             |
| `name`        | **string** | The name/identifier of the command.            |
| `description` | **string** | A short description of what the command does.  |
| `usages`      | **string\|array**  | A list of example usage strings.               |
| `options`     | **array**  | Key-value pairs of supported CLI options.      |
| `examples`    | **array**  | Key-value pairs of example input and purpose.  |

**Example:**

To display help from the terminal, use:

```bash
php novakit commandName --help
```

Or short named alias

```bash
php novakit commandName --h
```

***

### run

The `run()` method is the entry point for executing your console command logic.
Every console controller class must implement this method to handle how the command should behave when invoked.

```php
abstract public run(?array $params = null): int;
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$params` | **array<string,mixed>\|null** | The parsed arguments and options passed to the command at runtime. |

**Return Value**

`int` -  Return exit code `STATUS_SUCCESS` on success, or `STATUS_ERROR` if the execution fails or aborted.

#### Handling Multiple Commands

To support multiple subcommands within the same controller, use the `getArgument()` method to determine which command was called after the base command name.

For example:  
```bash
php novakit my-command foo --flag
php novakit my-command bar --flag
```

You can handle them like this:

```php
public function run(?array $params = null): int 
{
   $this->term->parse($options); // Parse options from the CLI

   $command = $this->term->getCommand();
   echo $command; // Outputs: my-command

   $called = trim($this->term->getArgument(1) ?? '');
   echo $called; // Outputs: foo or bar

   // Return: true if --flag, -f is used or value if has value
   $this->term->getAnyOption('flag', 'f');
}
```

> This approach lets you route different behaviors based on subcommand names.