# Job Queuing

***

## Overview

Luminova's queue class is a flexible and efficient solution for managing asynchronous tasks in (FIFO) order.

***

## Introduction

The Queue Class in Luminova provides a mechanism for managing asynchronous tasks, to handle background processes efficiently. Asynchronous processing is essential for improving application responsiveness and scalability, as it enables tasks to be executed independently of the main application flow.

Luminova's Queue Class facilitates the organization and execution of tasks in a first-in, first-out (FIFO) manner. This means that tasks are processed in the order they are added to the queue, ensuring fairness and predictability in task execution.

* Class namespace: `\Luminova\Utils\Queue`

***

## Eexamples

 Initialize a new Queue instance.
 
```php
$queue = new Queue();
```

Enqueueing tasks directly during initialization.
```php
$initialTasks = [
    function() {
        // Task 1: Perform another operation
    },
    "Task 2"
];
$queue = new Queue($initialTasks);
```

Define tasks as closures and enqueue them.

```php
$queue->push(function() {
    // Task 1: Perform some operation
});
```

Alternatively, enqueue a string task.

```php
$queue->push("Task 2");
```

You can enqueue any callable item.

```php
$queue->push([$object, 'methodName']);
```

Run the queue and execute all enqueued tasks.

```php
$queue->run();
```

Remove a specific task from the queue.

```php
$queue->remove("Task 3");

// Remove a task by index
$queue->remove($initialTasks[1]);
```

Get the current task and create a new instance.

```php
$current = $queue->current();
```

Get the next task and create a new instance.

```php
$next = $queue->next();
```

Get the last task and create a new instance.

```php
$lastTask = $queue->last();
```

***

## Methods

### constructor

```php
public __construct(array|null $jobs = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$jobs` | **array&#124;null** | Array of jobs to initialize the queue. |

***

### push

Push a closure or item to the queue.

```php
public push(\Closure|string|callable $item): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$item` | **\Closure&#124;string&#124;callable** | The item to enqueue. |

***

### run

Run the queue by executing all jobs.

```php
public run(callable|null $callback = null): void
```

And execute a callback function

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **callable&#124;null** | Optional Callback function to execute after running the queue. |

***

### hasQueue

Check if the queue has registered callable jobs.

```php
public hasQueue(): bool
```

**Return Value:**

`bool` - True if the queue has registered callable jobs.

***

### size

Get the size of the queue.

```php
public size(): int
```

**Return Value:**

`boo` - The size of the queue.

***

### delete

Delete the queue.

```php
public delete(): void
```

***

### remove

Remove an job from the queue.

```php
public remove(mixed $job): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$job` | **mixed** | The job name to remove. |

***

### free

Free resources

```php
public free(): void
```

***

### getInstance

Get the current job from queue and return a new instance.

```php
public getInstance(int $index): \Luminova\Utils\Queue
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **int** | Current job index. |

**Return Value:**

`Queue` - A new Queue instance.

***

### current

Get the current job from queue and return a new instance.

```php
public current(): \Luminova\Utils\Queue
```

**Return Value:**

`Queue` - A new Queue instance.

***

### next

Get the next job from queue and return a new instance.

```php
public next(): \Luminova\Utils\Queue
```

**Return Value:**

`Queue` - A new Queue instance.

***

### last

Get the last job from queue and return a new instance.

```php
public last(): \Luminova\Utils\Queue
```

**Return Value:**

`Queue` - A new Queue instance.