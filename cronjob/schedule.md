# Cron Scheduling

***

## Overview

Luminova's cron scheduling provides a robust and flexible way to define and manage tasks for cron job execution.

***

## Introduction

Luminova Cron Scheduling provides a robust framework for defining and managing cron jobs in your application. 
It includes various methods to specify execution intervals (e.g., seconds, minutes, days), logging, and handling completion or failure events.
This guide will introduce you to the key methods and how to use them to create a comprehensive cron job setup.

### Defining a Service

To define a specific service to execute in cron, use the `service` method to specify your command controller namespace and the method to call for job execution.

***

## Examples

In this example, the `dailyBackup` method from the `DailyCommand` class is scheduled to run every day with log level set to 'info'.

```php
<?php
$this->service('\\App\\Controllers\\DailyCommand::dailyBackup')
    ->days()
    ->log('info');
```

***

### Logging Output

You can specify the log output file for the service using the `output` method.

```php
<?php
$this->service('\App\Controllers\DailyCommand::dailyBackup')
    ->days()
    ->output('path/to/log/daily/output/file.log');
```

***

### Handling Job Completion

The `onComplete` method allows you to define actions to take when a job completes successfully.

```php
<?php
$this->service('\\App\\Controllers\\DailyCommand::dailyBackup')
    ->days()
    ->onComplete(static function(array $task){
        // Code to execute upon successful completion of the task
    });
```

***

### Handling Job Failure

The `onFailure` method allows you to define actions to take when a job fails to execute.

```php
<?php
$this->service('\\App\\Controllers\\DailyCommand::dailyBackup')
    ->days()
    ->onFailure(static function(array $task){
        // Code to execute upon failure of the task
    });
```

***

### Pinging URLs

You can configure the service to ping specific URLs upon job completion or failure.

**On Completion**

```php
<?php
$this->service('\\App\\Controllers\\DailyCommand::dailyBackup')
    ->days()
    ->pingOnComplete('https://example.com/ping/daily/success');
```

**On Failure**

```php
<?php
$this->service('\\App\\Controllers\\DailyCommand::dailyBackup')
    ->days()
    ->pingOnFailure('https://example.com/ping/daily/failure');
```

***

### Adding Descriptions

Use the `description` method to add a description to your cron job, which can be helpful for documentation and debugging purposes.

```php
<?php
$this->service('\App\Controllers\DailyCommand::dailyBackup')
    ->days()
    ->description('This is my daily command description');
```

***

### Full Example

Here is a full example that demonstrates all available methods,

```php
<?php
$this->service('\\App\\Controllers\\DailyCommand::dailyBackup')
    ->seconds()
    ->minutes()
    ->hours()
    ->days()
    ->weeks()
    ->months()
    ->years()
    ->cronTime('* * * * *')
    ->pingOnComplete('https://example.com/ping/daily/success')
    ->pingOnFailure('https://example.com/ping/daily/failure')
    ->onComplete(static function(array $task){
        // Code to execute upon successful completion of the task
    })
    ->onFailure(static function(array $task){
        // Code to execute upon failure of the task
    })
    ->output('path/to/log/daily/output/file.log')
    ->log('info')
    ->description('This is my daily command description');
```

> *Note:*
> 
> This is an example of all methods configuration, you cannot use it with all the time method together in one `sechedul`.