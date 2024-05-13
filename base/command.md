# Base Command

***

## Overview

Base command, Luminova&#039;s Framework for command-line operations - controller base class for NovaKit CLI Tool.

***

## Introduction

Base Command is Luminova's comprehensive framework designed for managing command-line operations. As a controller base class, it offers a structured approach to building and executing command-line commands, providing a solid foundation for developing robust and extensible command-line tool (CLIs).

***

* Class namespace: `\Luminova\Base\BaseCommand`
* Parent class: [\Luminova\Command\Terminal](/commands/terminal.md)
* This class is an **Abstract class**

***

## Properties

Define your command group name. The group will be used as a router capture authentication to ensure that commands belonging to same group are executed accordingly.

```php
protected string $group
```

Give your command a name.

```php
protected string $name
```

Describe your command usages, it can either be a `string` or an `array` of usages examples.

```php
protected string|array $usage
```

More expressive way to describe your command options and it descriptive message.
make the array key your option while the value can be a description of the option.

```php
protected array<string,mixed> $options
```

Tell more stories about your command by writing a clear description message to help users understand.

```php
protected string $description
```

***

### Features

- Define custom commands with specific functionalities, each associated with a unique name and description.
- Handle command execution, including parsing command-line arguments, validating inputs, and invoking the appropriate command handler.
- Organize commands into logical modules or groups for improved code reusability and maintainability.
- Customize and extend functionality by creating custom command handlers, adding new command-line options, or integrating with third-party libraries.
- Access controller class functionality just as you would in a web browser, providing seamless integration between command-line and browser-based operations.

***

### Methods

The base command extends the [Luminova Terminal class](/commands/terminal.md), making all the methods available and ready to use in command controller class. 

***

###  Usages

Initialize command line instance before running any commands if you plan to do anything in your Command Controller class.

```php
<?php 
namespace App\Controllers;
use \Luminova\Base\BaseCommand;

class PayStackCommand extends BaseCommand 
{
	//...
	public function foo(): int
	{
		$command = $terminal::getCommand();
		$var = $this->getOption('var');
		//..
		return STATUS_SUCCESS;
	}
}
```