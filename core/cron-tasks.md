# Core Class for Cron Job Task Management

***

## Overview

The Luminova Base Cron class offers advanced scheduling capabilities, allowing you to define specific controllers and methods for cron job executions.

***

## Introduction

The `CoreCronTasks` class provides useful methods for creating cron jobs, allowing you to define task execution times using simple predefined methods like `seconds` and `years`, as well as custom cron expressions via the `cronTime` method.

### Usages

To use the `CoreCronTasks` class, follow these steps:

1. **Extend the CoreCronTasks Class**: Create a new class that extends `CoreCronTasks`.
2. **Register Your Tasks**: Define your tasks within the protected `schedule` method.

Once your jobs are registered, you can schedule the cron execution in your `cPanel` or server's crontab by using the `NovaKit` command:

```bash
* * * * * php novakit cron:run
```

This command ensures that the job execution times are properly monitored and executed.

> **Note:**
>
> **Cron Time in `cPanel`**: Set the cron job in your `cPanel` to run every minute to ensure that your jobs are executed promptly. You can adjust this based on your specific needs.
>
> **Execution Time in `CoreCronTasks`**: The execution time set in the `service` time methods within `CoreCronTasks` does not depend on the `cPanel` cron time. It is used only to determine when your commands should be executed.

By following these steps, you can effectively manage and schedule your cron jobs using the `CoreCronTasks` class.

***

**Example**

Here is an example of cron job scheduler for managing scheduled tasks.
By default a `Cron` configuration class is added to your application configuration directory `app/Controller/Config/Cron.php`.

```php
<?php 
namespace App\Config;

use \Luminova\Core\CoreCronTasks;
final class Cron extends CoreCronTasks
{
    /**
     * Schedule the task for execution.
    */
    protected function schedule(): void 
    {
        $this->service('\App\Controllers\DailyCommand::fooMethod')
            ->days()
            ->log(root('/writeable/log/') . 'cron.log');

        $this->service('\App\Controllers\CerateCacheCommand::clear')
            ->minutes(5)
            ->onComplete(function(array $task){

        });
    }
}
```

***

* Class namespace: `\Luminova\Core\CoreCronTasks`
* This class is an **Abstract class**

***

## Methods

### constructor

Initialize constructor, additionally you can set a custom location to store cron lock file and the filename.
If not set, the default location will be used which is `writeable/cron/schedules.json`.

```php
public __construct(string|null $path = null, ?string $filename = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string&#124;null** | Path to store cron job configuration files. |
| `$filename` | **?string** | The filename to store lock file. |

***

### schedule

To define scheduled cron jobs within the extended `CoreCronTasks` class, use this method.
Called method `service` withing the `schedule` method, each service should have its own configurations.

```php
protected schedule(): void
```

***

### Service

To define a specific service for execution in a cron job, use this method to specify the namespace of your command controller and the method to be called for the job execution.

**Note**: The parameter must be the full class namespace of your command class, followed by the method name (e.g., `\Path\To\CommandClass::methodName`).

```php
protected service(class-string $controller): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$controller` | **class-string<BaseCommand>** | The controller class name string and method. |

**Return Value:**

`self` - Return cron class instance.

> *Note:*
>
> Your controller class must implement the `Luminova\Base\BaseCommand`, using other class will cause an error.

***

### onComplete

To set a callback to eacute on a successful task completion.

```php
protected onComplete(\Closure $onComplete): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onComplete` | **\Closure** | The callback function to execute on completion.<br />- Closure with one array parameter of task details. |

**Return Value:**

`self` - Return cron class instance.

***

### onFailure

To set a callback to eacute on a failure to complete task.

```php
protected onFailure(\Closure $onError): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onError` | **\Closure** | The callback function to execute on failure.<br />- Closure with one array parameter of task details. |

**Return Value:**

`self` - Return cron class instance.

***

### pingOnComplete

To set a callback URL to ping on a successful task execution.
The request payload will contain the executed task details.

```php
protected pingOnComplete(string $url): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The URL to ping on completion. |

**Return Value:**

`self` - Return cron class instance.

***

### pingOnFailure

To set a callback URL to ping on a failure task execution.
The request payload will contain the executed task details.

```php
protected pingOnFailure(string $url): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The URL to ping on failure. |

**Return Value:**

`self` - Return cron class instance.

***

### log

To set a log level to use in logging the execution response messages and exceptions that my occur during execution of task.

```php
protected log(string $level): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level to use while logging execution response. |

**Log levels**

You can choose any of the following log levels.

- emergency
- alert
- critical
- error
- warning
- notice
- info
- debug
- exception
- php_errors

**Return Value:**

`self` - Return cron class instance.

***

### output

To set a log path where all output like error messages or other exceptions that my occur during execution of task.

```php
protected output(string $path): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The output full path and filename. |

**Return Value:**

`self` - Return cron class instance.

> This log is not related to your method, it mainly other errors that may occur due to code errors or probably PHP or framework errors.

***

### description

Set a description for the cron task, the description will be used in logging your response.

```php
protected description(string $description): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$description` | **string** | The description of the cron job. |

**Return Value:**

`self` - Return cron class instance.

***

### Create Method

This method is essential for the framework to initialize and create your defined services, locking them in the cron lock file. The `force` parameter enforces service recreation. It can also be used as a CLI flag `php novakit cron:run --force`. When the flag is added, your service will be recreated each time the cron is executed. Use this option with caution, as it can be useful for renewing the lock file when adding new services to your `Cron` configuration.

```php
final public create(bool $force = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$force`  | bool | Whether to force the creation (default: false). |

**Return Value:**

`bool` - Returns true if the cron jobs were successfully created and written to the file, false otherwise.

***

### seconds

To eacute cron task at a specific seconds.

```php
final protected seconds(int $seconds = 5): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$seconds` | **int** | Interval in seconds (default: 5). |

**Return Value:**

`self` - Return cron class instance.

> The minimum execution time which can be set in cron is `1 minute`, using this method can only be useful for debugging in development environments, if you use it in production then your task will always be executed on every call except if use set a longer seconds.

***

### minutes

To eacute cron task at a specific minutes.

```php
final protected minutes(int $minutes = 1): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$minutes` | **int** | Interval in minutes. |

**Return Value:**

`self` - Return cron class instance.

***

### hours

To eacute cron task at a specific hour(s).

```php
final protected hours(int $hours = 1): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$hours` | **int** | Interval in hours. |

**Return Value:**

`self` - Return cron class instance.

***

### days

To eacute cron task at a specific day.

```php
final protected days(int $days = 1): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$days` | **int** | Interval in days. |

**Return Value:**

`self` - Return cron class instance.

***

### weeks

To eacute cron task at a specific week.

```php
final protected weeks(int $weeks = 1): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$weeks` | **int** | Interval in weeks. |

**Return Value:**

`self` - Return cron class instance.

***

### months

To eacute cron task in specific month(s).

```php
final protected months(int $months = 1): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$months` | **int** | Interval in months. |

**Return Value:**

`self` - Return cron class instance.

***

### years

To eacute cron task in specific year(s).

```php
final protected years(int $years = 1): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$years` | **int** | Interval in years. |

**Return Value:**

`self` - Return cron class instance.

***

### cronTime

To eacute cron task using the cron timestamp expression (e.g `* * * * *`).

```php
protected cronTime(string $expression): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$expression` | **string** | The cron expression. |

**Return Value:**

`self` - Return cron class instance.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If invalid expression is provided.

***

**What Next?**

- [Cron Scheduling ](/cronjob/schedule.md) - See the documentation for cron scheduling.
- [Cron Execution](/cronjob/execution.md) - Or see the documentation for cron execution