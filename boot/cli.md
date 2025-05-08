# Bootstrapping PHP Executable Bash Script for CLI

***

## Overview

Autoloading Luminova framework for creating a custom executable PHP bash script. 

***

## Introduction

In this guide, we will discuss how to autoload the Luminova framework when creating a PHP executable bash script. When creating a custom PHP executable in Luminova, it is recommended to place your script within the `/bin/` directory. However, you can also create it in your project root directory at the same level as the `novakit` script.

---

## Implementation

When creating your script, it’s important to ensure that the Luminova framework is accessible within your executable. This allows you to leverage global functions, load necessary modules, and include classes required for your script’s functionality. 

### Example:

```php
// /bin/script

#!/usr/bin/env php
<?php
use Luminova\Boot;

/**
 * Autoload Luminova framework
 */
require __DIR__ . '/system/Boot.php';

/**
 * Sets up the CLI (Command Line Interface) environment.
 * Define CLI-related constants, finishes bootstrapping.
 */
Boot::cli();

// Your script implementation
```

---

### See Also

- [Command Executor Helpers](/commands/terminal.md) - Learn more about CLI command execution and handling.
- [Base Command Classes](/base/command.md) - Learn how to extend and implement a CLI command controller class.
- [Routing Attributes](/routing/route-attribute.md) - Learn about PHP attribute routing and its usage.