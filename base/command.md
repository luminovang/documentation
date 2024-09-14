# Base Class for Command-Line Routing Controllers

***

## Overview

Base command, Luminova's Framework for command-line operations - controller base class for NovaKit CLI Tool.

***

## Introduction

Base Command is Luminova's comprehensive framework designed for managing command-line operations. As a controller base class, it offers a structured approach to building and executing command-line commands, providing a solid foundation for developing robust and extensible command-line tool (CLIs).

***

### Features

- Define custom commands with specific functionalities, each associated with a unique name and description.
- Handle command execution, including parsing command-line arguments, validating inputs, and invoking the appropriate command handler.
- Organize commands into logical modules or groups for improved code reusability and maintainability.
- Customize and extend functionality by creating custom command handlers, adding new command-line options, or integrating with third-party libraries.
- Access controller class functionality just as you would in a web browser, providing seamless integration between command-line and browser-based operations.

***

* Class namespace: `\Luminova\Base\BaseCommand`
* Parent class: [\Luminova\Command\Terminal](/commands/terminal.md)
* This class is an **Abstract class**

***

## Properties

#### group

The group name for the current command controller class. This group is used for router capture authentication to ensure that commands within the same group are executed accordingly (e.g., `php index.php <command-group-name> <command> <arguments>`).

```php
protected string $group = '';
```

***

#### name

The execution command name for the current controller class. This name is used internally to build information about your command, which can be utilized in displaying command help.

```php
protected string $name = '';
```

***

#### usage

A description of your command usages. This can be either a `string` or an `array` of usage examples.

```php
protected string|array $usage = [];
```

***

#### options

A more expressive way to describe your command options and their descriptive messages. The array key represents the command option, while the value is a description of the option (e.g., `['-f, --foo' => 'Foo description']`).

```php
protected array<string,mixed> $options = [];
```

***

#### examples

Examples of how to use the command. This allows you to provide full examples demonstrating command usage.

```php
protected array<string,mixed> $examples = [];
```

***

#### description

A detailed description of your command to help users understand its purpose and functionality.

```php
protected string $description = '';
```

***

### Methods

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

###  Usages

Initialize command line instance before running any commands if you plan to do anything in your Command Controller class.

```php
<?php 
namespace App\Controllers;
use \Luminova\Base\BaseCommand;

class PayStackCommand extends BaseCommand 
{
	public function help(array $helps): int
	{
	    return STATUS_ERROR;
	}
}
```