# Legacy Cron Scheduling and Job Execution Configuration

***

## Overview

Define recurring tasks and execution intervals for your application. Works with crontab and the Novakit CLI to automate background jobs reliably.

***

## Introduction

The `Cron` class is the central configuration for defining and scheduling tasks to be executed by the cron task manager. It extends the `CoreCronTasks` class provided by Luminova Core and allows you to schedule services and their corresponding execution intervals.

### Overview

The `Cron` class is located in the `/app/Config/` namespace and defines tasks to be executed by implementing the `schedule` method. This method specifies the services (controller methods) to be executed at defined intervals.

***

## Class Definition

***

* Class namespace: `\App\Config\Cron`
* File path: `/app/Config/Cron.php`
* Parent Class: `Luminova\Foundation\core\CronTasks`
* This class is marked as **final** and can't be subclassed

***

## Methods

### schedule

The `schedule` abstract method is the primary method where tasks are defined and scheduled. It uses the `service` method to register services and allows chaining additional configuration methods such as `seconds` and `output`.

```php
protected function schedule(): void
```

**Responsibilities**

- Define services to be executed by cron.
- Set the execution interval for each service.
- Specify log file paths for task output.

**Example Implementation**

```php
protected function schedule(): void
{
    $this->service('\App\Controllers\Cli\ServiceOneCommand::fooMethod')
        ->seconds(5) 
        ->output(root('/writeable/log/') . 'cron-one.log');

    $this->service('\App\Controllers\Cli\ServiceTwoCommand::barMethod')
        ->minutes(10)
        ->log('info');
}
```

***

## Configuration Options

### service

The `service` method specifies the task to be executed. It accepts a combination of a controller class and a method name. The controller can be an `Http` controller or a `Cli` controller class designed for execution.

**Usage**

The service definition must adhere to the following pattern: a fully qualified class name (FQCN) followed by `::` and the method name, resembling the syntax for invoking a static method.

```php
$this->service('\Namespace\Controllers\Cli\Controller::method');
```

***

## Full Example

Below is a complete example of the `Cron` class configuration:

```php
// /app/Config/Cron.php
namespace App\Config;

use Luminova\Foundation\core\CronTasks as CoreCronTasks;

final class Cron extends CoreCronTasks
{
    /**
     * Define and schedule tasks for cron execution.
     */
    protected function schedule(): void
    {
        $this->service('\App\Controllers\Cli\ServiceOneCommand::fooMethod')
            ->seconds(5) 
            ->output(root('/writeable/log/') . 'cron-one.log');

        $this->service('\App\Controllers\Cli\ServiceTwoCommand::barMethod')
            ->minutes(10)
            ->log('info');
    }
}
```

***

## Additional Resources

- [Core Class for CronJob Tasks Management](/foundation/cron-queue.md)
- [Running and Executing Cron Jobs  with Nova-Kit](/task-queue-system/cron-task-execution.md)
- [Defining Cron Services and Scheduling Task for Execution](/task-queue-system/cron-scheduling.md)