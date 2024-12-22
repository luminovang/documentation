# Async Class with Fiber: Asynchronous Task Execution 

***

## Overview

The Async class leverages PHP's `Fiber` to execute tasks asynchronously, allowing easy concurrent jobs management. It provides methods for queuing, running, and awaiting the completion of tasks.

***

## Introduction

The `Async` class in Luminova provides a way to handle asynchronous task execution using PHP `Fiber`. This allows for better concurrency management, enabling non-blocking operations without relying on traditional threading or external systems. With `Fiber`, tasks can be suspended and resumed, making it possible to execute multiple operations simultaneously in an efficient, lightweight manner.

It provides methods for queuing, running, and awaiting the completion of tasks and control asynchronous operations, yielding control to other tasks when necessary and continuing execution when the task is completed. 

***

### Base Usage

Below are examples of how to initialize and use it effectively:

#### Task Initialization

**Direct Task Execution**:

With callable tasks.

```php
$async = Async::task([
    new Fiber(fn() => 'Task 1 completed'),
    function () { return 'Task 2 completed'; }
]);
```

**Instance-Based Task Management**:

With Fiber tasks.

```php
$async = new Async([
    function () { return 'Task 1 completed'; },
    function () { return 'Task 2 completed'; }
]);
```

**Awaiting a Single Task**:

```php
use Luminova\Http\Network;
use Fiber;

$results = Async::await(new Fiber(fn() => (new Network())->get('https://example.com')->getContents()));

echo $results;
```
**Another example:**

```php
use Luminova\Http\Network;

$results = Async::await(fn() => (new Network())->get('https://example.com'));

echo $results->getContents();
```

***

#### Controlled Execution with `run`

Control the execution flow and yielding of tasks.

```php
use Luminova\Http\Network;
use Luminova\Http\Message\Response;

$async->enqueue(function () {
    echo "Task 1 started\n";
    Async::next(); // Yield control to other tasks
    echo "Task 1 resumed after yielding\n";
    return "Result of Task 1";
});

$async->enqueue(function () {
    echo "Task 2 started\n";
    Async::sleep(1); // Simulate an asynchronous delay of 1 second
    echo "Task 2 completed\n";
    return "Result of Task 2";
});

$async->run(function (mixed $result, int $id) {
    var_dump($result);
});
```

***

## Class Definition

* Class namespace: `\Luminova\Utils\Async`

## Properties

### result

Executed tasks result.

```php
protected array<string,mixed> $result = [];
```

***

### tasks

Array of Fiber or callable tasks to enqueued for execution.

```php
protected array<string|int,\Fiber|callable> $tasks = [];
```

***

## Methods

### constructor

Initializes a new Async instance with an optional array of tasks.

This constructor initializes the Async instance, optionally accepting an array of tasks (either `Fiber` or callable) to populate the task queue. If no tasks are provided, an empty queue is initialized. Additionally, it checks if PHP Fiber is supported and prepares the task queue for execution.

```php
public __construct(array<string|int,\Fiber|callable> $tasks = []): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$tasks` | **array<int,\Fiber&#124;callable>** | An optional array of tasks (Fiber or callable) to initialize the queue with. |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If PHP Fiber is not supported on the system.

***

### getTasks

Gets the current list of tasks.

```php
public getTasks(): array<string,\Fiber|callable>
```

**Return Value:**

`array<string,\Fiber|callable>` -  Return an array of current tasks.

***

### getResult

Get executed task result.

```php
public getResult(): array<string,mixed>
```

**Return Value:**

`array<string,mixed>` -  Return array of completed task results.

***

### hasTasks

Checks if there are any tasks in the queue.

```php
public isEmpty(): bool
```

**Return Value:**

`bool` -  Return true if there are tasks, false otherwise.

***

### task

Creates a new instance of the Async class, optionally initializing with an array of tasks.

This method allows you to create an Async instance, optionally passing an array of tasks (either `Fiber` or callable) to initialize the queue. If no tasks are provided, an empty queue will be created.

```php
public static task(array $tasks = []): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$tasks` | **array<string\|int,\Fiber\|callable>** | An optional array of tasks (Fiber or callable) to initialize with. |

**Return Value:**

`self` -  Returns a new instance of the Async class.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If PHP Fiber is not supported on the system.

**Example:**

```php
$async = Async::task([
    function () { return 'Task 1 completed'; },
    function () { return 'Task 2 completed'; }
]);
```

***

### next

Suspends the current fiber and yields a value.

If fibers are supported, it suspends the current fiber and yields the given value.
If not, it simply returns the value immediately.

```php
public static next(mixed $value = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | The value to yield. |

**Return Value:**

`mixed` -  Return the value yielded by the fiber or the immediate value if fibers are not supported.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If PHP Fiber is not supported on the system.

***

### sleep

Pauses the execution of the current fiber for a specified duration.

The delay is specified in seconds and can be an integer or a float for precise timing. While paused, other fibers can continue executing.

```php
public static sleep(int|float $seconds = 0.5): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$seconds` | **int&#124;float** | The duration to pause, in seconds (e.g., 1 for one second, 0.5 for half a second). |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If PHP Fiber is not supported on the system.

***

### enqueue

Adds a fiber or callable to the task queue for later execution.

```php
public enqueue(\Fiber|callable $task): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$task` | **\Fiber&#124;callable** | The task to add to queue (e.g, `fiber` or `callable`). |

**Return Value:**

`string` -  Return the unique task ID for reference.

***

### dequeue

Removes a task from the task queue by its ID or index.

```php
public dequeue(string|int $id): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **int** | The task ID or index to remove. |

**Return Value:**

`bool` -  Return true if the task was removed, false otherwise.

***

### reindex

Reindexes the tasks array to remove any gaps in the array keys.

This method is useful after tasks have been removed from the queue,
ensuring that the remaining tasks are indexed sequentially.

```php
public reindex(): bool
```

**Return Value:**

`bool` -  Returns true if the reindexed, otherwise false.

***

### prioritize

Prioritizes a specific task by moving it to the front of the queue.

```php
public prioritize(string|int $id): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **string\|int** | The id or index of the task to prioritize. |

**Return Value:**

`bool` -  Return true if the task was prioritized, false otherwise.

***

### clear

Clears all tasks from the task queue.

```php
public clear(): true
```

**Return Value:**

`true` -  Always return true.

***

### await

Awaits the completion of a fiber or callable. The `await` method ensure that task is completed before returning the response.

```php
public static await(\Fiber|callable $task): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$task` | **\Fiber&#124;callable** | The task to await (e.g, `Fiber` or `callable`).<br />Callback signature: `function(): mixed{ return fooRunAndReturnTaskResult(); }`. |

**Return Value:**

`mixed` -  Return the result of the fiber or callable.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If PHP Fiber is not supported on the system.

**Example:**

```php
use Luminova\Http\Network;
use Luminova\Http\Message\Response;

$requests = [
    fn() => (new Network)->get('https://example.com'),
    fn() => (new Network)->get('https://another.com'),
];

$results = [];
foreach ($requests as $request) {
    $response = Async::await($request);
    
    $results[] = ($response instanceof Response) 
        ? $response->getContents() 
        : $response;
}

print_r($results);
```

***

### awaitPromise

Awaits the completion of a fiber or callable and return promise that resolves to the result.

```php
public static awaitPromise(\Fiber|callable $task): PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$task` | **\Fiber&#124;callable** | The task to await (e.g, `Fiber` or `callable`).<br />Callback signature: `function(): mixed{ return fooRunAndReturnTaskResult(); }`. |

**Return Value:**

`Luminova\Interface\PromiseInterface` -  Return promise object that result to result, otherwise reject reason.

**Example:**

```php
use Luminova\Http\Network;
use Luminova\Utills\Async;
use Psr\Http\Message\ResponseInterface;

$promise = Async::awaitPromise(fn() => (new Network)->get('https://example.com'));

$promise->then(function(ResponseInterface $response){
    echo $response->getContents();
})->catch(function(Throwable $e){
    echo 'error: ' . $e->getMessage();
})->finally(function(){
    echo 'Done';
});
```

***

### setTimeout

Executes a given callback function after a specified timeout in milliseconds.

This method runs the callback asynchronously using a Fiber and handles any errors that occur during execution by throwing a `RuntimeException`.

```php
public static setTimeout(callable $callback, int $milliseconds): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **callable** | The callback function to execute after the timeout. |
| `$milliseconds` | **int** | The delay before the callback is executed, in milliseconds. |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throws if an error occurs during execution.

**Example:**

```php
Async::setTimeout(function () {
    echo "Timeout executed at: " . date('H:i:s') . "\n";
}, 2000);
```

***

### timeout

Creates a Fiber to execute a given callback after a specified timeout.

The returned Fiber can be manually managed to provide fine-grained control over its execution.

```php
public static timeout(callable $callback, int $milliseconds): \Fiber
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **callable** | The callback function to execute after the timeout. |
| `$milliseconds` | **int** | The delay before the callback is executed, in milliseconds. |

**Return Value:**

`Fiber` -  Return the Fiber instance for managing the timeout execution.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throws if an error occurs during execution.

**Example:**

 ```php
echo "Start\n";
$fiber = Async::timeout(function () {
    echo "Timeout executed in fiber at: " . date('H:i:s') . "\n";
}, 2000);

// Simulate 1 second of work
usleep(1000_000);

// Resume the fiber to allow the callback to execute
$fiber->resume();
echo "End\n";
```

**Another Example:**

```php
$fibers = [];
$fibers[] = Async::timeout(function () {
    echo "Task 1 executed after 1 second\n";
}, 1000);

$fibers[] = Async::timeout(function () {
    echo "Task 2 executed after 2 seconds\n";
}, 2000);

foreach ($fibers as $fiber) {
    $fiber->resume();
}
```

***

### run

Runs all enqueued tasks asynchronously. The `run` method allows you to control the execution flow of enqueued tasks, including support for yielding and delays.

```php
public run(?callable $callback = null, int $delay = 5000): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **callable&#124;null** | Optional callback to execute with each result.<br />Callback signature: `function(mixed $result,int $tid): void}`. |
| `$delay` | **int** | Optional delay, the number of seconds to wait after checking for completed task (default: 5000). |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the method is called while another task execution (`run` or `until`) is in progress.

**Examples:**

```php
use Luminova\Http\Network;
use Luminova\Http\Message\Response;

$results = [];

$async->enqueue(fn() => (new Network)->get('https://example.com'));
$async->enqueue(fn() => (new Network)->get('https://another.com'));

$async->run(function(Response $response, string $id) use(&$results) {
    $results[$id] = $response->getContents();
});
```

***

### until

Executes all deferred tasks sequentially until completion.

The `until` method processes all enqueued tasks in sequence by utilizing the `await` method for task execution. However, it does not support custom asynchronous controls such as `Async::next()` or `Async::sleep()`. Attempting to use these controls within tasks executed by `until` will result in an error, as this method does not handle explicit suspension required by such operations.

```php
public until(?callable $callback = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **callable&#124;null** | Optional callback to execute after each task completes, receiving the result and index.<br />Callback signature: `function(mixed $result,int $tid): void}`. |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the method is called while another task execution (`run` or `until`) is in progress.

**Examples:**

```php
use Luminova\Http\Network;
use Luminova\Http\Message\Response;

$async->enqueue(fn() => (new Network)->get('https://example.com')->getContents());
$async->enqueue(fn() => (new Network)->get('https://another.com')->getContents());

$async->until(function(mixed $result, string $id){
     var_dump($result);
});
```