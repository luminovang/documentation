# v3.2.6 Full Changelog 

***

## Overview

Provides a detailed record of all changes in Luminova's v3.2.6, updates, and improvements made to the Luminova Framework over time.

***

## Introduction

## General

Luminova **3.2.6** includes significant updates that enhance performance and error handling mechanisms, as well as improved debugging capabilities with debug tracing for all error occurrences.

This update also features enhanced official documentation, with clearer titles and detailed examples of usage to better illustrate features and implementations. Additionally, we've optimized method and parameter descriptions for improved visibility in IDEs.

***
## New Features

- **[Luminova\Storages\Stream](/files/stream.md)**: A stream handler for efficiently reading and writing large data volumes, integrated with the latest `cURL` network client response updates.
- **[Luminova\Config\Enums\ErrorCodes](/running/error-codes.md)**: A comprehensive list of exceptions and error codes used throughout the framework for various error handling scenarios.
- **[NovaKit Command](/commands/novakit.md)**: Introduction of the `novakit log` command for managing error logs.
- **[View Content Caching](/cache/view-caching.md)**: utilizes `app.version` to store and retrieve page caching.
- **`array_extend_default:`** A global helper function, it allows you to extend an array retaining the default values, it only apply new element that doesn't exist in default.

***
## Optimizations & Updates

- **[Luminova\Debugger\Profiling](/running/debugging.md)**: Enhanced performance profiling now includes total database query execution time, command line performance profiling, and API performance logging.
- **[Luminova\Datable\Builder](/database/query-builder.md)**: Optimized performance, introduced new cache management methods, and replaced expired cache content instead of deleting it. Fixed issues with the `in` method and added new arguments for the `insert` method.
- **[\Luminova\Exceptions\AppException](/running/exceptions.md#appexception)**: Improved error handling with support for string error codes and a new `getName` method.
- **[Luminova\Seo\Sitemap](/seo/sitemap.md)**: Optimized to support URL prefix definitions to limit scanning.
- **[Luminova\Seo\Schema](/seo/schema.md)**: Enhanced scheme builder and configuration optimizations.
- **[Luminova\Http\Network](/http/network.md)**: Improved network operations and implemented necessary interfaces for the `Guzzle` client.
- **[Luminova\Http\Client\Curl](/http/network#lmv-docs-configuration-options)**: Optimized for various configurations, including multipart uploads, header callbacks, and large stream handling.
- **[Luminova\Http\Message\Response](/http/response.md)**: Enhanced response handling with new methods, such as `getBody`, to return a stream object.
- **`Command Executor`**: Improved command executor now suggests commands when an unknown `novakit` command is run.
- **[Luminova\Command\Terminal](/commands/terminal.md)**: Optimized methods, resolved issues with `watcher` and `progress`, and introduced `spinner` and `terminate` methods, along with enhancements to other methods.

***
## Changes & Renames

- **`Luminova\Base\BaseApplication`** renamed to **[Luminova\Core\CoreApplication](/core/application.md)** to indicate it can only be extended once for `App\Application`.
- **`Luminova\Base\BaseDatabase`** renamed to **[Luminova\Core\CoreDatabase](/core/database-backups.md)** for single inheritance in `App\Config\Database`.
- **`Luminova\Base\BaseCron`** renamed to **[Luminova\Core\CoreCronTasks](/core/cron-tasks.md)** for single inheritance in `App\Config\Cron`.
- **`Luminova\Base\BaseFunction`** renamed to **[Luminova\Core\CoreFunction](/core/functions.md)** for single inheritance in `App\Utils\Functions`.
- **`Luminova\Base\BaseServices`** renamed to **[Luminova\Core\CoreServices](/core/services.md)** for single inheritance in `App\Config\Services`.
- **`Luminova\Command\TextUtil`** renamed to **[Luminova\Command\Utils\Text](/commands/text-util.md)** for better code organization.
- **`Luminova\Command\Colors`** renamed to **[Luminova\Command\Utils\Color](/commands/color-util.md)** for better code organization.
- **`Luminova\Interface\HttpClientInterface`** renamed to **[\Luminova\Interface\NetworkClientInterface](/interface/classes.md#networkclientinterface)**.

***
## Deprecated or Removed

- The method **`Luminova\Http\Request->isCommand()`** is now deprecated. Use the global function **`is_command()`** instead.

***

## To-Dos

### 1. Update Class Inheritance

In your application configuration, change all classes that extend from `\Luminova\Base\*` to extend from `\Luminova\Core\*`.

#### Application

```php
// /app/Application.php
<?php
namespace App;

use Luminova\Core\CoreApplication;

class Application extends CoreApplication
{
    //...
}
```

#### Services

```php
// /app/Config/Services.php
<?php
namespace App\Config;

use Luminova\Core\CoreServices;

class Services extends CoreServices
{
    //...
}
```

#### Database

```php
// /app/Config/Database.php
<?php
namespace App\Config;

use Luminova\Core\CoreDatabase;

class Database extends CoreDatabase
{
    //...
}
```

#### Functions

```php
// /app/Utils/Functions.php
<?php
namespace App\Utils;

use Luminova\Core\CoreFunction;

class Functions extends CoreFunction
{
    //...
}
```

#### Cron Tasks

```php
// /app/Config/Cron.php
<?php
namespace App\Config;

use \Luminova\Core\CoreCronTasks;

final class Cron extends CoreCronTasks
{
    //...
}
```

### 2. Add `scanUrlPrefix` to Sitemap Configuration

In your application sitemap configuration, add a new variable named `$scanUrlPrefix`.

```php
// /app/Config/Sitemap.php
<?php
namespace App\Config;

use Luminova\Base\BaseConfig;

final class Sitemap extends BaseConfig
{
    public string $scanUrlPrefix = '';
}
```