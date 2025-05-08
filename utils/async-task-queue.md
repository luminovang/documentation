# Asynchronous Background Tasks Queue and Execution

***

## Overview

Luminova's Queue Class is a flexible and efficient module for managing asynchronous tasks in (FIFO) order. It utilizes PHP Fiber if available or alternative methods if not supported.

***

## Introduction

The Luminova Queue Class provides a mechanism for managing asynchronous tasks, allowing for efficient handling of background processes. Asynchronous processing enhances application responsiveness and scalability by enabling tasks to run independently from the main application flow.

This queue class organizes and executes tasks in a **first-in, first-out (FIFO)** manner, ensuring that tasks are processed in the order they are added. This approach guarantees fairness and predictability in task execution.

The Queue Class leverages PHP **Fiber** feature for executing tasks if it is available in the environment. If Fibers are not supported, it attempts to use process forking, `pcntl` function for execution. If neither option is available, the class defaults to a standard asynchronous execution method, which is also optimized for efficient task handling.

***

## Usage Examples

### Asynchronous Executions

Enqueue tasks directly during initialization.

```php
use \Luminova\Utils\Queue;

$jobs = [
    // Tasks with custom identifiers
    ['task' => fn($queue): string => "Executed Task 1\n", 'id' => 1001],
    ['task' => fn($queue): string => "Executed Task 2\n", 'id' => 1002],
    ['task' => fn($queue): string => "Executed Task 3\n", 'id' => 1003],
    ['task' => fn($queue): array => ["Executed Array Task 4"], 'id' => 1004],

    // Tasks without an identifiers
    fn($queue): array => ["Executing Array Task 5"],
    fn($queue) => logger('debug', 'Executed Logger Task 6'),
    'Executed String Task String 7',
    factory()->network()->get('https://example.com')->getContents(),
    function($queue): void {
        echo "Executed Void Return Task\n";
    }
];

$queue = new Queue($jobs);

// Run the queue
$queue->run(function(array|string $result) {
    var_export($result);
});
```

### Pushing Tasks to the Queue

You can dynamically add tasks to the queue after initialization.

```php
// Enqueue a task with an identifier
$queue->push(function() {
    // Task 1: Perform some operation
}, 1001);

// Alternatively, enqueue a simple string task
$queue->push("Task 2");

// Enqueue any callable item, such as a method from a class
$queue->push([SomeClass::class, 'methodName']);
```

### Removing Tasks from the Queue
You can remove specific tasks based on different criteria.

```php
// Remove a task by its string value
$queue->remove("Task 2");

// Remove a task by its identifier
$queue->remove(1001);

// Remove a task by its index in the queue
$queue->remove(1);
```

### Running Specific Tasks
You can retrieve and run specific tasks using their identifiers or indices.

```php
// Run the current task by its identifier
$current = $queue->getInstance(2)->run();

// Run the current task
$current = $queue->current()->run();

// Run the next task in the queue
$next = $queue->next()->run();

// Run the last task in the queue
$lastTask = $queue->last()->run();
```

***

## Class Definition 

* Class namespace: `\Luminova\Utils\Queue`
* This class implements: `\Countable`

***

## Constants

These constants are used to control how messages from the queue are reported during execution.

| Constant | Type | Value | Description |
|:---------|:-----|:------|:------------|
| `E_LOG`  | int  | 1     | Log errors and execution messages using log level `debug`. |
| `E_OUTPUT`| int  | 2     | Display errors and messages directly to the output. |
| `E_SUPPRESS` | int | 0  | Suppress all error and execution messages. |

***

## Methods

Initializes asynchronous queue with optional jobs, output settings, and error reporting mode.

```php
public __construct(
    array<int,\Closure|callable|string> $jobs = [], 
    bool $output = false, 
    int $eReporting = self::E_OUTPUT
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$jobs` | **array<int,\Closure&#124;callable&#124;string>** | An array of jobs to execute. Each job can be a closure, callable, or string. |
| `$output` | **bool** | Whether to display the result of executed jobs if they return a string (default: false). |
| `$eReporting` | **int** | Determines how execution messages and errors are handled (default: `Queue::E_OUTPUT`). |

***

### push

Push a new job queue, either closure, any callable or string to be executed.

```php
public push(\Closure|callable|string $item, string|int|null $id = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$item` | **\Closure&#124;callable&#124;string** | The item to enqueue. |
| `$id` | **string&#124;int&#124;null** | Optional job identifier (default: null). |

***

### run

Run the job queue asynchronously, executing each job's callback function.

If Fibers are supported, it will be used for asynchronous execution.
If Fibers are not available, it attempts process forking (if supported)
or executes jobs directly in a blocking manner.

```php
public run(\Closure|callable|null $callback = null, int $timeout): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **\Closure&#124;callable&#124;null** | Optional callback function executed after the queue finishes processing. |
| `$timeout` | **int** | Maximum time to wait for execution in seconds (default: 0 for no timeout). |

***

### onResponse

Registers a callback to handle the response of each executed task.

This method allows you to specify a callback function that will be called whenever a task completes. The callback receives three parameters:

- **$result**: `mixed` - The result of the most recently completed task.
- **$previous**: `mixed` - Variable containing the cumulative results of all previously completed tasks up to that point.
- **$queue**: `Luminova\Utils\Queue` - The current instance of the queue class, allowing access to its properties and methods.

```php
public onResponse(callable $callback): void 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **callback** | The callback function to be invoked on task completion. |

**Example**

```php
<?php
$queue->onResponse(function (mixed $result, mixed $previous, Luminova\Utils\Queue $queue) {
	echo "Task completed: " . var_dump($result) . "\n";
	echo "Current cumulative results: " . var_dump($previous) . "\n";
});
```

> **Note:** The `onResponse` method should be invoked **before** calling the `$queue->run()` method. 
> To modify the result, declare the `$result` argument in your callback as a reference (e.g., `&$result`). This allows you to update the result within the callback, affecting the overall response.

***

### cancel

Manually cancel all running jobs.

```php
public cancel(): bool
```

**Return Value:**

`bool` - Return true on success, false on failure or if no active running job to cancel.

***

### getResult

Get executed job result.

```php
public getResult(): array|string
```

**Return Value:**

`array<int,mixed>|string` - Return array of job result or string in a new line representing the result.

***

### getStartTime

Get execution start time in seconds.

```php
public getStartTime(): float
```

**Return Value:**

`float` - Return the start execution time in seconds.

***

### getWaitingTime

Get execution waiting time.

```php
public getWaitingTime(): float
```

**Return Value:**

`float` - Return the time waited for jobs to complete in seconds.

***

### isRunning

Check if queue is running any job.

```php
public isRunning(): bool
```

**Return Value:**

`bool` - Return true if the queue is running, otherwise false.

***

### isEmpty

Check if the queue is empty.

```php
public isEmpty(): bool
```

**Return Value:**

`bool` - Return true if the queue is empty, otherwise false.

***

### has

Check a job is in the queue.

```php
public has(string|int $id): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **string&#124;int** | The job identifier or string value to remove. |

**Return Value:**

`bool` - Return true if the job was removed, false otherwise.

***

### hasQueue

Check if the queue has registered callable jobs.

```php
public hasQueue(): bool
```

**Return Value:**

`bool` - Return true if the queue has registered callable jobs.

***

### count

Get the size of jobs in queue.

```php
public count(): int
```

**Return Value:**

`int` - Return the size of the queue.

***

### delete

Delete the current task from the queue.

```php
public delete(): void
```

***

### remove

Remove an job from the queue.

```php
public remove(string|int $id): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **string&#124;int** | The job identifier or string value to remove. |

**Return Value:**

`bool` - Return true if the job was removed, false otherwise.

***

### free

Free all resources by clearing the queue.

```php
public free(): void
```

***

### reindex

Reindexes the tasks array to remove any gaps in the array keys.

This method is useful after tasks have been removed from the queue, ensuring that the remaining tasks are indexed sequentially.

```php
public reindex(): bool
```

**Return Value:**

`bool` -  Returns true if the reindexed, otherwise false.

***

### getInstance

Get new instance Queue for a specific job by it queue index.

```php
public getInstance(int $index): \Luminova\Utils\Queue
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **int** | The job queue index. |

**Return Value:**

`\Luminova\Utils\Queue` - Return new Queue instance.

***

### current

Get new instance of current (first) job from queue.

```php
public current(): \Luminova\Utils\Queue
```

**Return Value:**

`\Luminova\Utils\Queue` - Return new Queue instance.

***

### next

Get new instance of next job from queue.

```php
public next(): \Luminova\Utils\Queue
```

**Return Value:**

`\Luminova\Utils\Queue` - Return new Queue instance.

***

### last

Get new instance of last job from queue.

```php
public last(): \Luminova\Utils\Queue
```

**Return Value:**

`\Luminova\Utils\Queue` - Return new Queue instance.

***

***

### id

Get the job identifier from returned job queue instance.

```php
public id(): string|int|null
```

**Return Value:**

`string|int|null` - Return the current job queue identifier if available.
