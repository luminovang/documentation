# Group Attributes for CLI Command Controllers

***

## Overview

Defines a class-level group for CLI command controllers, allowing consistent command organization within a single class for more efficient routing and execution.

***

## Introduction

The **Group** attribute is designed for CLI command controller classes.  
It allows you to organize and unify commands within a single controller class, making it easier for the routing system to detect and execute commands efficiently.

The `Group` attribute was introduced in **Luminova version 3.5.6** as part of improvements to the routing attribute compiler. It is applied at the **class level** and is **non-repeatable**.

> **Note:**  
> - The `Group` attribute is **required** when using **attribute-based routing** for commands.  
> - For **method-based routing**, attributes are **not required**.

---

## Usages

### Command Grouping

The following example demonstrates how to apply the `Group` attribute to define a command group name for handling CLI command execution.

**Basic Example**

```php
// /app/Controllers/Cli/ListCommand.php

namespace App\Controllers\Cli;

use Luminova\Base\Command as BaseCommand;
use Luminova\Attributes\Group;
use Luminova\Attributes\Route;

#[Group(name: 'list')]
class ListCommand extends BaseCommand
{
   // Group name matches the Group attribute
   protected string $group = 'list';

   #[Route('users', group: 'list')]
   #[Route('users/limit/(:int)', group: 'list')]
   public function listCommand(int|string $limit = 10): int
   {
      // Method implementation
      return STATUS_SUCCESS;
   }

   // Additional methods handling list-related routes
}
```

In this example, the `ListCommand` class handles all commands routes starting with `list *`, such as `index.php list users`.

---

## Class Definition

* Class namespace: `Luminova\Attributes\Group`
* This class is marked as **final** and can't be subclassed

---

### Attribute Constructor

Defines a unique command group to CLI controllers.

This attribute assigns a group name to a routable CLI controller, so all commands within that group share a common name during command routing execution. Each controller can only have **one group**.

```php
public __construct(string $name)
```

**Parameters:**

| Parameter   | Type        | Description                           |
|-------------|-------------|---------------------------------------|
| `$name`     | **string**  | The command group name (e.g., `foo`).<br/>A valid command group must start with a letter and can contain only lowercase letters, numbers, hyphens (`-`), underscores (`_`), or colons (`:`). |

**Throw:**

- [Luminova\Exceptions\RouterException](/error-handlers/exceptions.md#routerexception) - If an invalid command group name is provided.

> **Note:** Only one `Group` attribute can be assigned to a controller class.