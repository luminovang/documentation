# Background Task Queue - Novakit CLI Management

***

## Overview

Novakit commands to run, monitor, and manage background tasks. Like sending emails or cleaning up files without affecting your app’s performance. Tasks are executed safely in the background.

***

## Introduction

The **Background Task Queue** in Novakit provides helper commands for working with the `Luminova\Base\Queue as BaseTaskQueue` class. It allows you to stage and run background tasks efficiently using a queue system that supports:

* Delayed execution
* Retries on failure
* Task priority levels
* Recurring tasks (e.g., daily or hourly)

The **CLI TaskQueue** commands let you manage queued tasks directly from the terminal. You can:

* Add new tasks to the queue
* Pause, retry, or inspect existing tasks
* Run a background task worker using cron
* Monitor task execution and performance

For setup instructions and full setup examples, see the [Background Task Queue System](/base/task-queue.md) documentation.

---

### List of Commands

```bash
task:init       Initialize the task queue system and create the required table in your database.
task:deinit     Remove the task queue table and all tasks.
task:queue      Add a new task to the queue.
task:list       Show tasks in the queue. You can filter by status or ID.
task:info       Show full information of a task by ID.
task:delete     Delete a task from the queue.
task:purge      Clear all tasks with a specific status (like completed).
task:pause      Pause a task. It will not run until resumed.
task:resume     Resume a paused task.
task:retry      Set a failed task back to pending.
task:status     Manually change a task\'s status.
task:run        Start a worker that processes tasks in the queue.
task:sig        Send control signals to the task worker (stop or resume).
task:listen     Show real-time logs while tasks are being processed.
task:export     Export all tasks from the queue with optional status filtering.
```

---

### Task Status List

You can use these values to filter or update task status:

| Status      | Type   | Description                                             |
| ----------- | ------ | ------------------------------------------------------- |
| `all`       | string | Includes tasks of all statuses.                         |
| `forever`   | string | Task runs again and again. It will not be auto-deleted. |
| `pending`   | string | Task is waiting in the queue.                           |
| `paused`    | string | Task is paused. It will not run until resumed.          |
| `running`   | string | Task is now being processed.                            |
| `failed`    | string | Task failed. Usually caused by an error or exception.   |
| `completed` | string | Task ran successfully. It may be deleted if configured. |

---

### Custom Task Handler Class

You can use `--class` (or `-c`) in any command to choose your own task handler class. If not set, it will use this default class:

```php
namespace App\Tasks\TaskQueue;
```

Examples:

```bash
php novakit task:list --class=App\\Tasks\\MyTask
php novakit task:list -c=App\\Tasks\\MyTask
```

---

### Command Help

To show all task-related commands:

```bash
php novakit task --help
```

To see help for one command:

```bash
php novakit task:list --help
```

---

## Task Table Setup

### Create the Table

Use this the first time you use the task system:

```bash
php novakit task:init
```

### Delete the Table

This removes all tasks and deletes the task table:

```bash
php novakit task:deinit
```

---

## Adding Tasks to the Queue

Add a task from your default tasks method:

```bash
php novakit task:queue
```

Add a specific class method:

```bash
# Using instance method (@)
php novakit task:queue \
  --task=App\\Utils\\Worker@clean \
  --args='["temp"]' \
  --priority=1 \
  --schedule='+10 minutes'

# Using static method (::)
php novakit task:queue \
  --task=App\\Utils\\Worker::clean \
  --args='["temp"]' \
  --priority=1 \
  --schedule='+10 minutes'
```

Option guide:

* `--task` or `-t`: The method to run (example: `Class@method` or `Class::method`)
* `--args` or `-a`: List of parameters as a JSON string
* `--schedule` or `-s`: When to run (e.g. `+10 minutes`)
* `--priority` or `-p`: Number from 0–100 (higher runs earlier)

---

### Adding Tasks in Code (Programmatic Usage)

You can also add tasks inside your code:

```php
$arguments = ['temp'];
$schedule = '+10 minutes';
$priority = 1;

// Static method
TaskQueue::enqueue(App\Utils\Worker::class . '::clean', $arguments, $schedule, $priority);

// Instance method
TaskQueue::enqueue(App\Utils\Worker::class . '@clean', $arguments, $schedule, $priority);

// Or Invokable Class that implements Interface (Luminova\Interface\QueueableInterface)
TaskQueue::enqueue(App\Utils\Worker::class, $arguments, $schedule, $priority);
```

---

### CLI vs Code Usage

| Feature          | CLI Example                          | Code Example                             |
| ---------------- | ------------------------------------ | ---------------------------------------- |
| Context          | Terminal, cron jobs                  | PHP scripts, controllers, events         |
| Method format    | `-t Class@method` or `Class::method` | `'Class@method'` or `'Class::method'`    |
| Arguments format | JSON string                          | PHP array                                |
| Schedule format  | String (e.g. `+10 minutes`, `UNIX timestamp`)          | Same as string (`DateTimeInterface\|string\|int\|null`)                           |
| When to use      | Manual or scheduled queue            | Queue tasks inside your application flow |

---

## Running the Task Worker

To start processing queued tasks:

```bash
php novakit task:run
```

Or use a custom PHP script:

```bash
php path/to/project/bin/TaskRunner.php
```

---

### Using Cron to Run Tasks Automatically

To run the worker every minute:

```bash
* * * * * /usr/bin/php /path/to/project/novakit task:run >> /path/to/logs/task.log 2>&1
```

You can also add more options:

```bash
* * * * * \
  /usr/bin/php /path/to/project/novakit task:run \
  --class=App\\Tasks\\UserTaskQueue \
  --limit=10 \
  --sleep=200000 \
  --idle=3 \
  --output=debug >> /var/log/task_worker.log 2>&1
```

Explanation:

* `--limit=10`: Process 10 tasks at most in one loop
* `--sleep=200000`: Wait 0.2 seconds between each loop
* `--idle=3`: Stop after 3 loops with no tasks
* `--output=debug`: Show debug logs or write to file

To check your PHP path:

```bash
which php
```

---

## Avoid Running the Worker Twice

Sometimes a task worker may still be running when another one starts. To stop this, use a locking system.

### Using `flock` with Cron

This prevents overlapping (recommended):

```bash
* * * * * flock -n /tmp/task.lock /usr/bin/php /path/to/project/novakit task:run
```

**Using `--flock-worker` with Novakit**

This will create a custom flock file preventing overlapping.

```bash
* * * * * /usr/bin/php /path/to/project/novakit task:run --flock-worker
```

### Using a Stop Signal File

You can also create a stop signal file that pauses execution until manually deleted.

Or by running `sig` command to create signal or remove lock:

```bash
php novakit task:sig --stop-worker
```

```bash
php novakit task:sig --resume-worker
```

> **Note:** You can define your own stop file by setting `$stopSignalFile` in your task handler.