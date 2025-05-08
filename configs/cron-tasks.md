# Configuration for Cron Tasks and Job Scheduling

***

## Overview

The CronJob configuration class enables you to define services and their execution intervals. When registered with `crontab` and paired with the novakit command-line tool, it facilitates the period

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
* Parent Class: `Luminova\Core\CoreCronTasks`
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

use Luminova\Core\CoreCronTasks;

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

- [Core Class for CronJob Tasks Management](/core/cron-tasks.md)
- [Running and Executing Cron Jobs  with Nova-Kit](/cronjob/execution.md)
- [Defining Cron Services and Scheduling Task for Execution](/cronjob/schedule.md)