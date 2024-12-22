# Logger Preference and Configurations

***

## Overview

Application logger configuration provides flexibility and customization methods to override the default nova-logger module with your preferred PSR-compliant logger interface.

***

## Introduction

The `Logger` configuration class allows you to specify a preferred PSR-compliant logger interface for application logging, replacing the default `NovaLogger`. 

It also enables asynchronous logging using PHP `Fiber`, allowing log messages to be processed in the background without blocking the UI thread.

***

* Class namespace: `\App\Config\Logger`
* File path: `/app/Config/Logger.php`
* This class is marked as **final** and can't be subclassed

***
## Properties

### asyncLogging

Enables asynchronous logging using Fibers to log messages in a background thread without blocking the UI.

```php
public static bool $asyncLogging = false;
```

> **Note:** This feature is only supported for `\Luminova\Logger\NovaLogger`.

***

### maxSize

Specify the maximum size (in bytes) for each log level (e.g., 10485760 for 10 MB). 
When this limit is reached, a backup of the log file is created if `logger.create.backup` or `$autoBackup` is set to true; otherwise, the logs are cleared.

```php
public static int $maxSize = 0;
```

***

### autoBackup

Indicate whether a backup of log file should be created when the `logger.max.size` or `$maxSize` limit is reached. 
Set to 'true' to automatically create a backup and empty the current log file, 'false' to empty the log file only.

```php
public static bool $autoBackup = false;
```

***

## Methods

### getLogger

Returns an instance of the preferred logger class, which must implement the PSR `LoggerInterface`.

```php
public getLogger(): ?\Psr\Log\LoggerInterface
```

**Return Value:**

`\Psr\Log\LoggerInterface|null` - Return preferred logger instance or null to use default logger.

> **Note:** Your logger class must implement psr LoggerInterface.