# Novakit Luminova Command-Line Interface

***

## Overview

Novakit is the built-in CLI tool in Luminova that handles everything from code generation to environment setup and custom command execution.

***

## Introduction

The **Novakit** is the built-in **Command Line Interface (CLI)** tool for the **Luminova Framework**. It simplifies development tasks by allowing you to execute and manage various operations directly from the terminal.

The NovaKit CLI is powered by a `novakit` PHP [Shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) file located at the **project root**. This is a special PHP script with a shebang line (`#!/usr/bin/env php`) that makes it executable like a native terminal command.

---

### What Does Novakit Do?

Novakit acts like a **routing system for CLI commands**. It prepares the command input and passes it to the correct controller, just like Luminova’s HTTP routing system does for web requests.

With Novakit, you can:

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

For a full reference, see the list of [available Novakit commands](/commands/novakit-commands.md) in the documentation.

---

## Novakit Class

The **Novakit class** provides a collection of methods for managing CLI commands internally.
It includes utilities for registering custom console commands, verifying whether a command exists, and handling other command-related operations.

All methods in this class can be called **statically** from anywhere in your application when needed.

---

### Novakit Console Commands

To build your own CLI commands using NovaKit console:

1. Extend the `Luminova\Base\Console` class.
2. Define your logic in `run(...)` method.
3. Register your commands in CLI novakit command file (`/bin/.novakit-console.php`).

Once created, you can run the command via:

```bash
php novakit your-command
```

Unlike `BaseCommand` (used via `index.php`), `BaseConsole` commands:
- Run **faster** because they skip routing and attribute parsing.
- Are better suited for **utility based ** CLI tools.

For details on creating custom commands, see: [Base Console Controller](/controllers/cli-novakit-controller.md)

***

## Class Definition

* Class namespace: `Luminova\Command\Novakit`

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
(new Novakit())->run($_SERVER['argv']);
```

***

### find

Find the fully qualified controller class for a given command.

This method attempts to resolve the command to a known controller class. It first checks predefined system commands, then searches registered console commands based on the provided mode (`system` or `global`).

```php
public static find(string $command, string $mode = 'global'): ?class-string<Luminova\Base\Console>
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
public static command(string $name, class-string<Luminova\Base\Console> $class, array $properties = []): bool
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