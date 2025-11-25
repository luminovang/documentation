# Abstract Base Class for Background Task Queue Controllers

***

## Overview

BaseTaskQueue is an abstract base class for managing background tasks in a queue system. It supports managing and running a worker using the Novakit CLI helper.

***

## Introduction

The Luminova **BaseTaskQueue** is an abstract base class used for handling background tasks in a queue system.

Background tasks are jobs that take time to complete or are not urgent. These include things like sending a welcome email, generating a sitemap, or sending push notifications. Since the user doesn't need to wait for the result right away, they are placed in a queue and processed in the background.

You can extend the `Luminova\Base\Queue as BaseTaskQueue` class to add your own logic or use a different storage system instead of the default database. The class allows you to add tasks, update their status, retry failed tasks, pause or resume them, and run a worker that processes them.

### Task Handler Formats

A task handler is the method or function that the queue will run. You can define the handler using any of the formats below:

* A plain function name
  Example: `sendEmail`

* A static method
  Example: `App\Utils\Worker::process`

* A class and method using instance style
  Example: `App\Utils\Worker@clean`

* An invokable class that implements `Luminova\Interface\QueueableInterface`
  The class must define a `__invoke()` method
  Example: `App\Utils\Worker`

* An anonymous class or closure that was serialized using `Opis\Closure\Serializer`
  The anonymous class must implement `QueueableInterface` and define `__invoke()` method

> **Note:** To enable support for serialized closures or anonymous classes, set the `$supportOpisClosure` property to `true` **before** running the `php novakit task:init` command or calling the `init()` method.

---

### CLI Integration

Task queue works with the `novakit` command-line tool, which lets you manage tasks directly from the terminal. You can add, list, delete, or run tasks using CLI commands.

To learn how to use the CLI tool, check the [Task Queue Management Commands](/task-queue-system/execution.md) documentation.

---

### Usages 

**Default Application Task Queue:**

When Luminova is installed or updated, a default task queue class will be created in your application under `app/Tasks` if not already exists. To start using background task queue setup crontab in production and run the following CLI command once:

```bash
php novakit task::init
```

After initialization and proper crontab configuration, you can queue tasks either from the CLI or directly in your PHP code.

**Example Task Queue Class:**

```php
// app/Tasks/TaskQueue.php
namespace App\Tasks;

use Luminova\Base\Queue as BaseTaskQueue;

class TaskQueue extends BaseTaskQueue 
{
    public function onCreate(): void
    {
        // Optional: Code to run when task is created
    }

    public function tasks(): ?array
    {
        // Return your task map here if needed
        return null;
    }
}
```

---

**Adding a Task:**

You can queue any valid callable, such as a named function:

```php
function mySendMailFunction(string $to): bool
{
    // send email...
    return true;
}

$task = new TaskQueue();
$task->enqueue('mySendMailFunction', ['peter@example.com']);
```

**Static Access Support:**

```php
use App\Tasks\TaskQueue;

TaskQueue::enqueue('mySendMailFunction', ['peter@example.com']);
TaskQueue::list(TaskQueue::ALL);
```

---

### Anonymous Classes and Closures

You can enqueue either an anonymous class or a closure, but anonymous classes **must implement** the `QueueableInterface`.

**Anonymous Class Example:**

```php
use Luminova\Interface\QueueableInterface;
use function \Opis\Closure\serialize;

$class = serialize(new class() implements QueueableInterface {
    public function __invoke(mixed ...$arguments): mixed
    {
        return $this->greet(...$arguments);
    }

    public function deleteOnCompletion(): bool
    {
        return false;
    }

    public function greet(string $message): string
    {
        return $message;
    }
});

App\Tasks\TaskQueue::enqueue($class, ['hello from anonymous class!']);
```

**Closure Example:**

```php
use function \Opis\Closure\serialize;

$closure = serialize(function (int $loop): int {
    $i = 0;
    do {
        $i++;
    } while ($i < $loop);
    
    return $i;
});

App\Tasks\TaskQueue::enqueue($closure, [10]);
```

---

### Ensuring Task Reliability

For background queue tasks to work reliably, the system needs clear success/failure signals. Since queues can't automatically determine task outcomes, follow these best practices:

**Implementation Guidelines:**
1. **Explicit Success**: Return a success response *only* when the task fully completes as expected
2. **Explicit Failure**: Throw an exception for any failure scenario
3. **Pro Tip:** For non-critical tasks, implement a maximum retry limit to prevent infinite loops.

This way, the system can retry the task automatically until it succeeds.

**Example**

```php
function sendWelcomeEmail(string $to): bool 
{
    $mailer = Luminova\Utility\Email\Mailer::to($to)
        ->subject('Welcome!')
        ->body('Thanks for joining...');

    if ($mailer->send()) {
        return true; // Explicit success
    }

    throw new Exception('Email delivery failed to: ' . $to); // Explicit failure
}
```

> **Why This Matters:**
> - Failed tasks automatically retry until successful
> - Prevents silent failures from going unnoticed
> - Guaranteed eventual completion (or proper failure logging)
> This is especially useful when dealing with temporary issues or poorly implemented task handlers.

---

## Class Definition

* Class namespace: `Luminova\Base\Queue as BaseTaskQueue`
* This class is an **Abstract class**

---

## Constants

### Task Status & Filter Constants

These constants define possible statuses for queued tasks, as well as filter types used when querying the queue.
They help standardize task state handling across the system.

#### Task Execution Statuses

| Constant    | Type   | Description                                                      |
| ----------- | ------ | ---------------------------------------------------------------- |
| `PENDING`   | string | Task is waiting to be picked up by the worker.                   |
| `PAUSED`    | string | Task is paused and will not run until resumed.                   |
| `RUNNING`   | string | Task is currently being processed.                               |
| `FAILED`    | string | Task execution failed. Typically includes an error or exception. |
| `COMPLETED` | string | Task finished successfully and may be deleted if configured.     |

---

#### Task Result Filters

| Constant     | Type   | Description                                                            |
| ------------ | ------ | ---------------------------------------------------------------------- |
| `ALL`        | string | Special filter to match tasks of all statuses.                         |
| `FOREVER`    | string | Task is scheduled to run repeatedly and is never auto-removed.         |
| `EXCEPTION`  | string | Used to identify tasks that ended by throwing an exceptions.           |
| `PAUSEABLE`  | string | Filters tasks that can be paused (e.g., running or pending).           |
| `EXECUTABLE` | string | Filters tasks that are ready for execution (pending or forever task).  |

---

## Properties

### onError

Task error callback.

This callback will execute if any error occur while executing task.

```php
protected ?Closure $onError = null;
```

**Callback Signature:**

```php
$this->onError = static function(int $id, string $status, Throwable $e){
    // Handle error
};
```

***

### table

Task database table name used for persistence.

```php
protected string $table = 'task_queue';
```

***

### group

A unique identifier for this task queue group.

This value is used to identify and manage tasks, especially when multiple queue classes share the same database table. By setting a different value, you can group and execute tasks that belong to a specific class or type.

```php
protected ?string $group = null;
```

> **Note:** The group name should not exceed 150 characters.

***

### deleteOnCompletion

Automatically delete tasks after successful completion.

When true, completed tasks and their output will be removed immediately after execution.
These tasks will no longer appear in task listings.

```php
protected bool $deleteOnCompletion = false;
```

***

### supportOpisClosure

Flag to enable or disable support for [Opis\Closure](https://opis.io/closure/4.x/) serialized handlers.

When set to true, the system will allow serialized handlers and deserialized using `Opis\Closure\Serializer`. This enables storing closures (anonymous functions) as strings and restoring them later.

If false, only standard callable formats (e.g., `Class@method` or `function` names) are allowed.

```php
protected bool $supportOpisClosure = false;
```

***

### stopSignalFile

File path used to signal background workers to stop.

When this file is present, active workers will shut down gracefully.

```php
protected ?string $stopSignalFile = null;
```

***

### eventLogPath

Enable event logging for task execution monitoring.

Set to false to disable logging. This flag is used when running: `php novakit task:listen`.

```php
public bool $eventLogging = false;
```

***

### running

Worker is currently running.

When SIGTERM or SIGINT is sent stop worker.

```php
protected bool $running = true;
```

***

## Methods

### constructor

Initialize a new task queue instance.

By default, the task system uses `App\Task\TaskQueue` as the base queue class.
You can extend this class to create and manage custom task queues per domain or module.

```php
public __construct(): mixed
```

> **Note:**  
> This constructor initializes the internal database connection.  
> If you're extending this class and overriding `__construct()`, you must either call `parent::__construct()`  
> or use the `onCreate()` method for custom initialization logic.

**CLI Usage:**

```bash
php novakit task:queue         # Loads and enqueues tasks from tasks() method
php novakit task:queue -t=App\\Utils\\Worker@clean -a='["temp"]' -s='+10 minutes''
```

**Programmatic Usage:**

```php
$queue = new \App\Task\TaskQueue();
$queue->enqueue('App\Utils\Worker@clean', ['temp'], '+10 minutes');

// Or static access
\App\Task\TaskQueue::enqueue('App\Utils\Worker@clean', ['temp'], '+10 minutes');
```

***

### onCreate

Hook method for custom initialization logic during construction.

This method is called automatically after the database connection is initialized.
It can be overridden by child classes to perform setup logic without needing
to override `__construct()` directly.

```php
protected onCreate(): void
```

**Example:**

```php
// /app/Tasks/TaskQueue.php

namespace App\Tasks;

use Luminova\Base\Queue as BaseTaskQueue;

class TaskQueue extends BaseTaskQueue
{
    protected function onCreate(): void
    {
        $this->ignores = ['App\Tasks\DebugTask@run'];
    }
}
```

***

### isConnected

Check if the database connection is active and ready.

```php
protected isConnected(): bool
```

**Return Value:**

`bool` - Return true if connected; otherwise false.

***

### isLocked

Check if the task is currently locked.

```php
public isLocked(): bool
```

**Return Value:**

`bool` - Return true if another instance is holding the lock.

***

### isOpisClosure

Checks if the given handler string is a serialized `Opis\Closure`.

This method verifies whether the provided handler string is a serialized instance of `Opis\Closure\Serializer`. It performs the following checks:

```php
public isOpisClosure(string $handler): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **string** | The serialized string to check. |

**Return Value:**

`bool` - Return true if the string appears to be an Opis closure, false otherwise.

- Returns false if Opis closure support is disabled or the handler is empty.
- Returns false if the `Opis\Closure\Serializer` class is not available.
- Checks if the handler starts with a serialized format marker matching either object (`O:`) or custom-serialized (`C:`) Opis closures.

***

### getInstance

Get the singleton instance of the called class.

This method ensures that only one instance of the class is created and reused throughout the application. Useful for lightweight service classes or static facades that require shared state.

```php
public static getInstance(): static
```

**Return Value:**

`static<Luminova\Base\Queue as BaseTaskQueue>` - Returns the singleton instance of the class.

***

### getGroup

Get the task group name.

If a custom group name is set in the `$group` property, it will return that. Otherwise, it falls back to using the short class name (without the namespace).

```php
public getGroup(): string
```

**Return Value:**

`string` - Return the group name used for this task queue.

> The group name is also used internally for identifying or grouping tasks when multiple task classes share the same queue system storage.

***

### getResult

Get the results of executed tasks.

When task is executed in non-cli mode, this method will return all collected task results including status, handler, and response.

```php
public getResult(): array
```

**Return Value:**

`array` - Returns an associative array of task results.

***

### getStagedTasks

Get all staged tasks, combining those defined in the subclass (`tasks()`) and those explicitly queued via the `stage()` method.

This method is used to collect all pending tasks before they are persisted using the `task:queue` command. Tasks returned from `tasks()` may be defined in lifecycle methods like `__construct()` or `onCreate()`.

```php
public getStagedTasks(): array
```

**Return Value:**

`array<int,array>` - Returns a merged array of all staged tasks.

***

### getPathInfo

Get the resolved directory and full file path for a specific worker context.

**Supported contexts:**

- `event`  : Path to the task event log file.
- `signal` : Path to the stop/resume signal file.
- `flock`  : Path to the task lock file for preventing duplicate workers.

```php
public getPathInfo(string $context): ?array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$context` | **string** | The context to resolve. One of: 'event', 'signal', 'flock'. |

**Return Value:**

`array` - Returns an array with two values:
    - [0] string: Resolved directory path (base path + dirname).
    - [1] string: Full resolved file path (directory + filename).
    Or null if context is invalid or required config is missing.

**Example:**

```php
[$path, $file] = $this->getPathInfo('event')  ?? [null, null];
[$path, $file] = $this->getPathInfo('signal') ?? [null, null];
[$path, $file] = $this->getPathInfo('flock')  ?? [null, null];
```

***

### tasks

Define tasks to preload into the queue when `php novakit task:queue` is executed.

This method should return an array of task definitions. Each task specifies the handler
and optional metadata such as arguments, priority, schedule, and forever. These tasks
will be queued with a status of `pending`.

Each task must be an associative array with the following keys:
- `handler`  (string, required): A valid callable, e.g. `Class@method`, `Class::method`, or `function_name`.
- `arguments` (array, optional): Arguments to pass to the handler.
- `schedule`  (DateTimeInterface|string|int|null, optional): Optional. Delay task executing (e.g, `DateTime`, Unix  timestamp, or relative time).
- `priority`  (int, optional): Task priority (0 = highest, 100 = lowest). Defaults to 0.
- `forever` (int|null) – Interval in minutes between repeats for forever tasks (e.g, 1440 once/day).
- `retries` (int) - The number of times to retry failed task.

```php
abstract public tasks(): ?array
```

**Return Value:**

`array<int,array<string,mixed>|null` - Returns an array of task definitions to enqueue, or null to skip.

```php
// /app/Tasks/TaskQueue.php

namespace App\Tasks;

use Luminova\Base\Queue as BaseTaskQueue;

class TaskQueue extends BaseTaskQueue
{
    public function tasks(): ?array 
    {
        return [
            [
                'handler'  => 'App\Services\Mailer@sendWelcomeEmail',
                'arguments' => ['user@example.com']
            ],
            [
                'handler'  => 'App\Utils\Cleanup::purgeTempFiles',
                'schedule' => '+5 minutes',
                'priority' => 5,
                'forever'   => 1440
            ],
        ];
    }
}
```

***

### init

Initialize the task queue task queue system.

This method creates the queue table if it does not already exist. The table will be used to stores task metadata including priority, status, handler information, timestamps and execution response and any output.

If the table already exists, no changes will be made.

```php
public init(): bool
```

**Return Value:**

`bool` - Returns true if the table was created successfully or already exists.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database connection is denied.

***

### deinit

Destroy the task queue system.

This method removes the task queue table from the database. It also resets internal task states if the operation succeeds.

```php
public deinit(): bool
```

**Return Value:**

`bool` Returns true if the table was dropped.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database is not connected or an error occurs during drop.

***

### close

Close the active database connection, if connected.
This method safely disconnects from the database and clears the local connection reference.

```php
public close(): bool
```

**Return Value:**

`bool` - Return true if connection was closed, false otherwise.

***

### shutdown

Gracefully stop task execution when a termination signal is received.

This method sets the `$running` flag to false, allowing the worker to exit cleanly.
If a signal is provided, it logs the signal and process ID.

```php
public shutdown(?int $signal = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$signal` | **int&#124;null** | Optional POSIX signal number (e.g. SIGTERM, SIGINT). |

***

### enqueue

Queue a new task for background execution.

```php
public enqueue(
    string $handler, 
    array $arguments = [], 
    \DateTimeInterface|string|int|null $schedule = null, 
    int $priority, 
    ?int $forever = null,
    int $retries = 0
): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **string** | The task handler reference (e.g., function name, `Class@method`, or `Class::method`).<br/>Or class that implements `Luminova\Interface\QueueableInterface`. |
| `$arguments` | **array** | Optional arguments to pass to the task handler on execution. |
| `$schedule` | **DateTimeInterface&#124;string&#124;int&#124;null** |Optional delay task execution time (default: null).<br />Accepts: `DateTime`, Unix  timestamp, or relative time.<br/>If `null`, the task will run immediately.  |
| `$priority` | **int** | Optional task priority (0 = highest, 100 = lowest). Default is 0. |
| `$forever` | **int\|null** | Interval in minutes between repeated executions for a forever task (default: null).<br/>(e.g, `1440` once per day). |
| `$retries` | **int** | Optional number of times to retry the task if it fails (default: `0` no retry). |

**Return Value:**

`int` - Returns the inserted task ID on success, or 0 if failed or ignored.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If a database error occurs.
- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If an invalid forever interval is provided.

**Example:**

```php
TaskQueue::enqueue(
    handler:   App\Utils\Mailer::class . '@send',
    arguments: ['user@example.com', 'welcome-template'],
    schedule:  '+5 minutes',
    priority:   1
);
```

**Forever task example:**
```php
TaskQueue::enqueue(
    handler:  'App\Utils\Cleaner::purgeTemp',
    schedule: '+5 minutes',
    priority: 10,
    forever:  720   // Run forever, every 12 hours
);
```

***

### stage

Stage a task for queuing, to be pushed later using `php novakit task:queue`.

This method does not immediately add the task into the queue system storage. Instead, it stores the task in an internal queue list which is later processed when the `task:queue` command is executed. Typically used within subclasses of `BaseTaskQueue` (e.g., in `__construct`, `onCreate`, or `tasks()`).

```php
public stage(
    string $handler, 
    array $arguments = [], 
    \DateTimeInterface|string|int|null $schedule = null, 
    int $priority, 
    ?int $forever = null,
    int $retries = 0
): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **string** | The task handler reference (e.g., function name, `Class@method`, or `Class::method`).<br/>Or class that implements `Luminova\Interface\QueueableInterface`. |
| `$arguments` | **array** | Optional arguments to pass to the task on execution. |
| `$schedule` | **DateTimeInterface&#124;string&#124;int&#124;null** | Optional delay time for task execution (default: `null`).<br />Accepts `DateTime`, Unix timestamp, or relative time string (e.g., `+5 minutes`).<br/>If null, the task is scheduled to run immediately. |
| `$priority` | **int** | Optional priority for the task (e.g, 0 = highest, 100 = lowest) (default: `0`). |
| `$forever` | **int\|null** | Optional interval in minutes for repeated execution (e.g., `1440` = run once per day).<br/>If set, the task is marked as a **forever** task. |
| `$retries` | **int** | Optional number of times to retry the task if it fails (default: `0` no retry). |

**Return Value:**

`self` - Returns the current instance of class.

**Example:**

Queue a one-time task:

```php
protected function onCreate(): void 
{
    $this->stage(
        handler: App\Tasks\NotifyAdmin::class . '@dispatch',
        arguments: ['User 42 registered'],
        schedule: '+2 minutes'
    );
}
```

**Queue a repeating task:**

```php
protected function onCreate(): void 
{
    $this->stage(
        handler: 'App\Tasks\Jobs\SystemTasks::run',
        arguments: ['API_STATUS'],
        forever: 1440, // Run once every 24 hours
        priority: 3
    )->stage(
        handler: 'App\Tasks\Jobs\SystemTasks::run',
        arguments: ['CLEAR_CACHES'],
        forever: 1440,
        priority: 5
    );
}
```

***

### batchEnqueue

Queues multiple tasks for background execution in a batch.

Each task must define a `handler`, and can optionally define:

- `handler` (**string**): The callable string, e.g., `Class@method`, `function_name`.
- `arguments` (**array**): Parameters to pass to the handler.
- `schedule` (**DateTimeInterface|string|int|null**) – Optional. Delay task execution (e.g, `DateTime`, Unix  timestamp, or relative time).
- `priority` (**int**) – Priority of execution (0 = highest). Default is 0.
- `forever` (**int|null**) – Interval in minutes between repeats for forever tasks (e.g, 1440 once/day).
- `retries` (**int**) - The number of times to retry failed task.

Tasks whose handlers are listed in `$this->ignores` will be skipped.

```php
public batchEnqueue(array $tasks): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$tasks` | **array<int,array<string,mixed>>** | Array of task definitions (each an associative array). |

**Return Value:**

`int` - Return the number of tasks successfully inserted.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If a database connection or execution error occurs.
- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If an invalid forever interval is provided.

**Example:**

```php
TaskQueue::batchEnqueue([
    ['handler' => 'App\Utils\Mailer@send', 'arguments' => ['user@example.com']],
    ['handler' => 'App\Utils\Webhook::send', 'priority' => 10, 'schedule' => '+5 minutes'],
    ['handler' => 'App\Utils\Cleaner::purgeTemp', 'forever' => 1440]
]);
```

***

### get

Retrieve task information by task ID.

```php
public get(int $id): ?array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **int** | The task's unique ID. |

**Return Value:**

`array<string,mixed>|null` - Returns the task data as an associative array, or null if not found.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database connection fails or a query error occurs.

***

### count

Count the number of tasks matching a given status.

```php
public count(string $status = self::ALL): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **string** | Task status to count (e.g., `pending`, `failed`, `all`).<br />Use `TaskQueue::ALL` to count all tasks. |

**Return Value:**

`int` - Return the number of matching tasks based on status.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the given status is invalid.
- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database connection fails or a query error occurs.

***

### status

Update the status of a task (and optionally its output).

```php
public status(int $id, string $newStatus): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **int** | The task ID to update its status. |
| `$newStatus` | **string** | The new status to update. |

**Return Value:**

`bool` - Return true if task was updated.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the status name is invalid.
- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database is not connected or an error occur.

***

### delete

Delete a task by ID.

```php
public delete(int $id): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **int** | The task ID to delete. |

**Return Value:**

`bool` - Return true if the task was deleted.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database is not connected or an error occur.

***

### purge

Delete all tasks by a given status.

```php
public purge(string $status = self::ALL): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **string** | Status to match. Use `all` to delete everything. |

**Return Value:**

`int` - Return the number of tasks deleted.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the status name is invalid.
- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database is not connected or an error occur.

***

### retry

Reset a failed task back to 'pending'.

```php
public retry(int $id): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **int** | The task ID to mark as retry. |

**Return Value:**

`bool` - Return true if the task was updated.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database is not connected or an error occur.

***

### resume

Resume a paused task by setting its status to 'pending'.

```php
public resume(int $id): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **int** | The task ID to resume. |

**Return Value:**

`bool` - Return true if task was successfully resumed, otherwise false.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database is not connected or an error occur.

***

### pause

Pause a task by setting its status to 'paused', only if currently pending or failed.

```php
public pause(int $id): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **int** | The task ID to pause. |

**Return Value:**

`bool` - Return true if the task was paused, otherwise false.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database is not connected or an error occur.

***

### lock

Acquire an exclusive lock to prevent multiple concurrent task queue instances.

Creates an flock file. If another process already holds the lock, this method returns false.
The lock is determined by file-based advisory locking using `flock`.

```php
public lock(?int $permission = 0775): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$permission` | **int&#124;null** | Optional. Directory permission mode (e.g, 0775).<br />If null use the default application directory permission from `App\Config\Files`. |

**Return Value:**

`bool` - Return true if the lock was successfully acquired; false if already locked or on failure.

***

### unlock

Release the lock by deleting the lock file.

```php
public unlock(): bool
```

**Return Value:**

`bool` - Return true if lock file was deleted.

***

### list

Get a list of tasks from the database, optionally filtered by status, offset, and limit.

```php
public list(string $status = self::ALL, ?int $limit = null, int $offset = 0, bool $withTotal = false): ?array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **string** | Task status to filter by. Use `all` to fetch all tasks (default: `all`). |
| `$limit` | **int&#124;null** |  Maximum number of tasks to retrieve. `Null` for no limit (default: `null`). |
| `$offset` | **int** | The limit offset to start from (default: `0`). |
| `$withTotal` | **bool** | Whether to include total task count with result (default: `false`). |

**Return Value:**

`array<string,mixed>|null` - Returns an array of tasks or null if no tasks found.
- Return structure if `$countOver` is true `['total' => int, 'count' => int, 'tasks' => array]`.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database connection fails or a query error occurs.

***

### export

Exports the list of tasks with the given status to a file.

The exported file will contain an array of task definitions, including:
- priority
- forever
- handler
- arguments
- schedule
- status

If no file path is provided, null or empty, the method will defaults to `writeable/exports/TaskHandlerClass.txt` in the `writeable` directory.

```php
public export(string $status = self::ALL, ?string &$path = null, ?string &$metadata = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **string** | Task status to filter by (e.g., `PENDING`, `FAILED`). (default: `ALL`). |
| `$path` | **string&#124;null** | Optional path to write the exported file. (default: `null`). |
| `$metadata` | **string\|null** | Optional output variable for export metadata info (default: `null`). |

**Return Value:**

`bool` - Return true if export was successful, otherwise false.

**Throws:**

- [Luminova\Exceptions\FileException](/error-handlers/exceptions.md#fileexception) - If the directory cannot be created or written to.

***

### run

Start the task worker loop and continuously process executable tasks.

This method runs in a loop until stopped by one of the following:
- A stop signal (SIGTERM, SIGINT) is detected.
- A stop signal file is detected.
- The maximum number of idle cycles is reached (no new tasks).

The worker checks for new tasks at each cycle and executes them if available.
Adaptive sleep delay is applied based on the number of completed tasks per cycle.

```php
public run(int $sleep = 100, ?int $limit = null, int $maxIdle = 10): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$sleep` | **int** | Base sleep time (in microseconds) between task cycles. |
| `$limit` | **int&#124;null** | Optional. Maximum number of tasks to fetch per cycle. Null for no limit. |
| `$maxIdle` | **int** | Maximum consecutive idle cycles allowed before automatic shutdown. |

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an unrecoverable error occurs during execution.
- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database is not connected or a query fails.

***

### update

Update task status and output, with optional status filter check.

```php
final protected update(int $id, string $status, ?string $filter = null, ?string $output = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **int** | Task ID. |
| `$status` | **string** | New status to set. |
| `$filter` | **string&#124;null** | Only update where current status matches this.<br />Special case: 'pauseable' allows 'pending' and 'failed'. |
| `$output` | **string&#124;null** | Optional task output JSON string (e.g, `{'response': ..., 'output': ...}`). |

**Return Value:**

`bool` - Return true if task was updated success, otherwise false.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If status or filter is invalid.

***

### clean

Releases the task lock and deletes the stop signal file if it exists.

This method is used to clean up any active stop signals that prevent
the task worker from executing. It first unlocks the process, then
checks if a stop signal file is defined and removes it from the filesystem.

```php
protected clean(): void
```

***

### exportResult

Export a result as a string, scalar, or null depending on type.

- Scalars (int, float, bool, string): returned as-is
- Null: returned as null
- Resources: returned as a string with type info
- Arrays/Objects: JSON-encoded
- Fallback: var_export representation

```php
protected exportResult(mixed $result): string|int|float|bool|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$result` | **mixed** | The value to export. |

**Return Value:**

`string|int|float|bool|null` - Return the string representation of the result.

> This method can be override to implement how task response should be exported before saving to database.

***

### getCallable

Resolve a callable handler string into an actual PHP callable.

**Supported handler formats:**

- `Class@method` → Instantiates Class and calls method.
- `Class::method` → Static method call.
- `Class` → Instantiates the class if it exists.
- `Function` name → Returns the function if it exists.
- Serialized `Opis\Closure\Serializer` → Deserialized and returned.

```php
protected getCallable(string $handler): ?callable
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **string** | The handler string to resolve. |

**Return Value:**

`callable|null` - Return a valid callable or null if not resolvable.

> This method can be override to implement how task resolves handler before execution.

---

### Background Task Comparison

**`Luminova\Foundation\core\CronTasks` vs. `Luminova\Base\Queue as BaseTaskQueue`**

**`CoreCronTasks` (Legacy Task Scheduler)**

* Designed for **static cron-based scheduling**, using a familiar `cron` time format.
* Task definitions are **file-based**, no database required.
* **No support** for adding tasks dynamically from code.
* **Limited commands**:

  * `cron:create` – Register or define a new cron task.
  * `cron:run` – Execute due cron tasks.
* Good for simple, predictable schedules but not flexible.

**Limitations**:

* Static schedules only.
* Cannot schedule tasks dynamically at runtime.
* No runtime state tracking or fine-grained control.

---

**`BaseTaskQueue` (Modern Task Manager)**

* Supports **code-defined tasks** using `enqueue()` and `batchEnqueue()` method or via CLI.
* Allows scheduling using:

  * `scheduleAt` – Absolute timestamp or relative time (`+5 minutes`, `tomorrow`, etc.).
* **Database-based queueing**, but designed with flexibility in mind.
* Advanced runtime management features:

  * **Flock locks** to prevent double execution.
  * **Signal handling** for clean termination or interruption.
  * **Status monitoring**, including queue size and memory control.

**CLI Commands**:

* `task:queue` – Enqueue a task.
* `task:run` – Run due tasks.
* `task:list`, `task:purge`, etc. – Full queue management suite.

---

### Summary

If you're building something fixed and periodic, `CoreCronTasks` works — but it's rigid.
If you need **dynamic, programmatic task scheduling**, runtime safety, and monitoring — `BaseTaskQueue` is the practical and extensible choice.

> **Use BaseTaskQueue going forward.** `CoreCronTasks` is for legacy support and basic use cases only.