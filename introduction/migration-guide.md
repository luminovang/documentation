# Migration Guide for Version 3.6.8

***

## Overview

Step-by-step instructions for upgrading your application to Luminova version 3.6.8, covering changes, renames, new features, and required adjustments.

***

## Introduction

Version **3.6.8** introduces broad cleanup, optimizations, renames, and new features across Luminova’s core modules. This guide walks you through the key changes and what you need to adjust when migrating.

## Core Changes

* Significant renaming and restructuring of core classes for consistency.
* Optimizations and bug fixes across all modules.
* Improved template view engine performance.
* Enhanced exception handling system.

---

## Before You Start

Before upgrading, **backup your project**.
A migration always has the chance of breaking custom code, so you want a safe way back if something goes wrong.

---

## Step 1: Install or Update

### Option A: Fresh Installation

Create a new folder for your project and run:

```bash
composer create-project luminovang/luminova .
```

### Option B: Update an Existing Project

Inside your current project directory:

```bash
composer update
```

This pulls in the latest 3.6.8 changes.

---

## Step 2: Enable Debug Output

After installation or update, open your `.env` file and temporarily enable debugging:

```ini
debug.display.errors = true
debug.show.tracer = true
```

This makes it easier to spot issues during migration.
Turn them off later when everything is stable.

---

## Step 3: Move Your Files Back (For Fresh Installs)

If you installed a clean copy of the framework, now copy your project files into the new structure.

### Resources

Move your old view files:

```
old-project/resources/Views/* → new-project/resources/Views/*
```

Then apply these changes:

* Rename `404.php` → `4xx.php`
* Rename `500.php` → `5xx.php`
* Delete the old `system_errors` folder and any files inside it

**If you updated instead of reinstalling:**
Make sure the view directory is now named **Views** (capital V).
The old lowercase `views` is no longer valid.

---

## Step 4: Update Your Controllers

Copy your controllers:

```
old-project/app/Controllers/* → new-project/app/Controllers/*
```

### Controller Namespace Change

The base controller class has moved:

Old:

```php
Luminova\Base\BaseController // for HTTP Controller
Luminova\Base\BaseCommand    // for CLI Controller
```

New:

```php
Luminova\Base\Controller // for HTTP Controller
Luminova\Base\Command    // for CLI Controller
```

Update any affected controllers.

### HTTP Controllers Update

**Before:**

```php
namespace App\Controller\Http;

use Luminova\Base\BaseController;

class YourController extends BaseController
{}
```

**After:**

```php
namespace App\Controller\Http;

use Luminova\Base\Controller;

class YourController extends Controller
{}
```

---

### CLI Controllers Update

**Before:**

```php
namespace App\Controller\Cli;

use Luminova\Base\BaseCommand;

class YourCommand extends BaseCommand
{}
```

**After:**

```php
namespace App\Controller\Cli;

use Luminova\Base\Command;

class YourCommand extends Command
{}
```

### If Your Controller Name Is Already "Controller"

If your file/class is literally named `Controller` or `Command` for CLI controller, you have two options:

**Option A: Rename your class/file**
Simple and clean.

**Option B: Use an alias**

in HTTP controllers:

```php
use Luminova\Base\Controller as BaseController;

class Controller extends BaseController
{}
```

in CLI controllers:

```php
use Luminova\Base\Command as BaseCommand;

class Command extends BaseCommand
{}
```

This avoids naming conflicts.

---

### If Your HTTP Controller Used Validation

The validation system in Luminova 3.6.8 has been updated.
Controllers using the older validation methods must be adjusted.

#### 1. Property Renamed

The validation handler has been renamed:

* **Old:** `$validate`
* **New:** `$input`

Any controller referring to `$this->validate` must now use `$this->input`.

---

#### 2. Validation Method Signature Changed

The old `validate()` method accepted an array of data.
This is no longer supported.

**New behavior:**

* `validate()` now **requires the full request object**.
* If you still want to validate from an array, use the new `setBody()` method before calling `validate()`.

---

#### 3. New Validation Rules Added

The validation engine now supports additional rules, including:

`between`, `lat`, `lng`, `latlng`, `length`, `limit`, `size`, and others.

You can extend your validation logic with these improved checks.

---

### Before (Old Version)

```php
namespace App\Controller\Cli;

use Luminova\Base\BaseCommand;

class YourCommand extends BaseCommand
{
    public function yourMethod(): int
    {
      if($this->validate->validate($this->request->getBody())){
        // ...
      }
    }
}
```

---

### After (Updated Version: Using Request Object)

```php
namespace App\Controller\Cli;

use Luminova\Base\BaseCommand;

class YourCommand extends BaseCommand
{
    public function yourMethod(): int
    {
      if$this->input->validate($this->request){
        // ...
      }
    }
}
```

---

### After (Updated Version: Using Array Body)

If your logic depends on validating a raw array:

```php
namespace App\Controller\Cli;

use Luminova\Base\BaseCommand;

class YourCommand extends BaseCommand
{
    public function yourMethod(): int
    {
      $this->input->setBody($this->request->getBody());
      if($this->input->validate()){
        // ...
      }
    }
}
```

---

# Step 5: Update Your Application Class

The core Luminova application class has been moved to a new namespace.
Any project extending it must update its import path.

### Application Namespace Change

**Old location:**

```php
Luminova\Core\CoreApplication
```

**New location:**

```php
Luminova\Foundation\Core\Application
```

### Required Update

**Before:**

```php
namespace App;

use Luminova\Core\CoreApplication;

class Application extends CoreApplication
{}
```

**After:**

```php
// /app/Application.php

namespace App;

use Luminova\Foundation\Core\Application as CoreApplication;

class Application extends CoreApplication
{}
```

The alias keeps your class name the same while pointing it to the updated framework class.

---

# Step 6: Stop and Check

At this point you should have updated:

* Your controllers
* Your application class
* Your view structure

Open the project in your browser and make sure it loads without errors.
If everything runs, you can continue following the latest Luminova documentation for any new features you want to adopt.

If you do see errors, they usually come from:

* Old namespaces
* Moved classes
* Methods that have been relocated to dedicated modules

Update them as needed, then continue with the remaining migration steps.

---

# Other Important Changes

Several helper classes were moved into new namespaces.
You will need to update your `use` statements accordingly.

## Helper Function Changes

Luminova 3.6.8 reorganized how helper functions are accessed.

Most helper functions now require importing from the `Luminova\Funcs\*` namespace unless they are core PHP functions or part of the limited set of Luminova global helpers listed below.

### Global Helper Functions (No Namespace Required)

These functions remain globally available and do **not** require any namespace import.

| Function                   | Purpose                                                                        |
| -------------------------- | ------------------------------------------------------------------------------ |
| `json_validate()`          | Polyfill for PHP versions earlier than 8.3. Validates if a string contains valid JSON.  |
| `array_is_list()`          | Polyfill for PHP versions earlier than 8.1. Determines if an array is a list.  |
| `array_first()`            | Polyfill for PHP versions earlier than 8.5. Get first element of array.        |
| `array_last()`             | Polyfill for PHP versions earlier than 8.5. Get last element of array.         |
| `set_function()`           | Executes a PHP function dynamically, checking if the function is disabled before running it. |
| `set_max_execution_time()` | Sets the script’s execution time limit only if the new timeout exceeds the current limit. |
| `to_numeric()`             | Converts a numeric string to the correct numeric type (int or float).          |
| `setenv()`                 | Sets an environment variable and can optionally write it into the `.env` file. |
| `env()`                    | Retrieves the value of an environment variable.                                |   

Any other Luminova helper functions **must** be accessed using the `Luminova\Funcs` namespace.

---

#### Using Namespaced Helper Functions

Update all reference to helper function with correct namespace import.

**Direct Fully-Qualified Call**

```php
Luminova\Funcs\href('about');
```

**Import a Single Function**

```php
use function Luminova\Funcs\href;

href('about');
```

**Import Multiple Functions**

```php
use function Luminova\Funcs\{href, root};

href('about');
root('writeable/tmp/', 'file.txt');
```

This pattern keeps your code cleaner and avoids repeating long namespace paths.

---

## Helper Class Changes

| Old Namespace                   | New Namespace                               |
| ------------------------------- | ------------------------------------------- |
| `Luminova\Base\BaseConfig`       | `Luminova\Base\Configuration`                   |
| `Luminova\Base\Base*`       | Drop prefix **Base** `Luminova\Base\*`                   |
| `Luminova\Errors\ErrorHandler`       | `Luminova\Foundation\Error\Guard`                   |
| `Luminova\Application\*`       | `Luminova\Foundation\Module\*`                   |
| `Luminova\Library\Modules`       | `Luminova\Foundation\Module\Autoloader`                   |
| `Luminova\Functions\Func`       | `Luminova\Common\Helpers`                   |
| `Luminova\Functions\Maths`      | `Luminova\Common\Maths`                     |
| `Luminova\Functions\IP`         | `Luminova\Utility\IP`                       |
| `Luminova\Functions\Tor`        | Replace with `Luminova\Utility\IP::isTor()` |
| `Luminova\Functions\Escape`     | `Luminova\Security\Escaper`                 |
| `Luminova\Functions\Normalizer` | `Luminova\Http\Helper\Normalizer`           |
| `Luminova\Storages\*` | `Luminova\Utility\Storage\*`           |
| `Luminova\Seo\*` | `Luminova\Component\Seo\*`           |
| `Luminova\Languages\*` | `Luminova\Component\Languages\*`           |
| `Luminova\Optimization\Minification` | `Luminova\Component\Seo\Minifier`           |
| `Luminova\Builder\*` | `Luminova\Component\Widget\*`           |
| `Luminova\Email\*` | `Luminova\Utility\Email\*`           |
| `Luminova\Arrays\ArrayUtil` | `Luminova\Utility\Collections\Arr`           |
| `Luminova\Arrays\Listify` | `Luminova\Utility\String\Listify`           |
| `Luminova\Utils\*` | `Luminova\Utility\*`           |
| `Luminova\Utils\LazyObject` | `Luminova\Utility\LazyObject`           |
| `Luminova\Utils\Interval` | `Luminova\Time\Interval`           |
| `Luminova\Utils\Promise\FulfilledPromise` | `Luminova\Utility\Promise\Fulfilled`           |
| `Luminova\Utils\Promise\RejectedPromise` | `Luminova\Utility\Promise\Rejected`           |
| `Luminova\Utils\Promise\Promise::deferred()` | Standalone helper class `Luminova\Utility\Promise\Deferred`           |
| `Luminova\Ai\*` | `Luminova\Utility\Ai\*`           |
| `Luminova\Http\Client\Curl` | `Luminova\Http\Client\Novio`           |
| `Luminova\Interface\AiInterface` | `Luminova\Interface\AIModelInterface`           |
| `Luminova\Interface\ValidationInterface` | `Luminova\Interface\InputValidationInterface`           |
| `Luminova\Interface\LazyInterface` | `Luminova\Interface\LazyObjectInterface`           |
| `Luminova\Cookies\BaseCookie` | `Luminova\Base\Cookie`           |
| `Luminova\Cookies\CookieFileJar` | `Luminova\Cookies\FileJar`           |

Update any old imports in your project.

---

# Encryption Changes

The encryption module has been reorganized.

### Crypter Namespace Change

**Old:**

```php
use Luminova\Security\Crypter;
```

**New:**

```php
use Luminova\Security\Encryption\Crypter;
```

No behavior changes, only location.

---

# Password Handling Changes

Password functions were removed from `Crypter` and placed into a dedicated class.

### Before:

```php
use Luminova\Security\Crypter;

Crypter::password();
Crypter::isPassword();
```

### After:

```php
use Luminova\Security\Password;

Password::hash();
Password::verify();   // same as isValid()
```

This keeps password handling clean and independent.

---

# Key Management Changes

Key generation and validation have also been moved out of `Crypter`.

### Before:

```php
use Luminova\Security\Crypter;

Crypter::generateKey();
Crypter::isKeyMatch();
Crypter::isPrivateKey();
```

### After:

```php
use Luminova\Security\Encryption\Key;

Key::newPublic();
Key::newPrivate();
Key::newKeyPair();
Key::newRandom();

Key::isMatch();
Key::isPrivate();
```

This separates encryption keys from password utilities, making both clearer and easier to maintain.

---

### Exception Handling

Luminova 3.6.8 introduces a dedicated class for all exception and error codes.
Older exception classes previously carried their own constants. That is no longer the case.

A new `ErrorCode` class centralizes these values, and all framework exceptions now reference it.

### Before

```php
use Luminova\Exceptions\RuntimeException;

throw new RuntimeException('Error', RuntimeException::INVALID);
```

### After (Updated)

```php
use Luminova\Exceptions\RuntimeException;
use Luminova\Exceptions\ErrorCode;

throw new RuntimeException('Error', ErrorCode::INVALID);
```

Every exception type that previously used its own code constants must now use `ErrorCode::*` instead.

---

### Cookies

The framework dropped the longer name `CookieFileJar` to keep the cookie storage classes consistent. If your project used the old class, you must update the import.

### Before

```php
use Luminova\Cookies\CookieFileJar;

$jar = new CookieFileJar();
```

### After

```php
use Luminova\Cookies\FileJar;

$jar = new FileJar();
```
