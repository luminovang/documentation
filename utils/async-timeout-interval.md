# Asynchronous Executions using Intervals

***

## Overview

Interval class mimics the behavior of JavaScript's setInterval using PHP Fibers.

***

## Introduction

The `Interval` class built with PHP `Fiber`, to ensure asynchronous operation and efficient task management. It provides a way to execute a callback function repeatedly at a specified interval, similar to `setInterval` in JavaScript. You can use it to perform tasks at regular intervals while maintaining precise control over when the interval starts, stops, or clears.

***

### Usage Examples

#### Basic Usage

Executes every `2000` milliseconds (2 seconds).

```php
$interval = Interval::setInterval(function () {
    echo "Interval executed at: " . date('H:i:s') . "\n";
}, 2000); 

// Clear the interval conditionally.
if ($foo === $bar) {
    $interval->clear();
    echo "Interval cleared due to condition.\n";
}
```

--- 

#### Execution the Interval from new Instance

Create an interval and manually handle interval execution.
```php
$interval = new Interval(function () {
    echo "Executing at: " . date('H:i:s') . "\n";
}, 1000);
$interval->run();
```

#### Dynamic Interval Adjustments

Create an interval and manually handle interval execution.

```php
$interval = new Interval(function (int $index) {
    echo "Count: $index at " . date('H:i:s') . "\n";

    // Change behavior dynamically.
    if ($index === 3) {
        echo "Adjusting logic after third tick.\n";
    }
}, 1000);

for ($i = 0; $i < 6; $i++) {
    usleep(500_000); // Simulates a delay (500ms).
    $interval->tick($i);
}

$interval->clear();
```

---

#### Executing for a Fixed Number of Ticks

```php
$maxTicks = 5;
$ticks = 0;

$interval = new Interval(function () use (&$ticks, $maxTicks, &$interval) {
    $ticks++;
    echo "Tick {$ticks} at " . date('H:i:s') . "\n";

    if ($ticks >= $maxTicks) {
        $interval->clear();
        echo "Reached maximum ticks. Interval cleared.\n";
    }
}, 1000);
$interval->run();
```

---

> **Notes:**
> Use `tick()` in conjunction with a timer or loop to manage interval execution.
> When use `setInterval` or `run` is called, the interval persists until manually cleared using the `clear()` method.

***

## Class Definition

* Class Namespace: `\Luminova\Utils\Interval`

## Methods

### constructor

Initializes the interval and starts the execution of the given callback at specified intervals.

```php
public __construct(callable $callback, int $milliseconds): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **callable** | The callback function to execute at each interval. |
| `$milliseconds` | **int** | The interval duration in milliseconds. |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throws if error occurs during execution.

***

### setInterval

Creates and returns an `Interval` instance, allowing a callback to execute repeatedly asynchronously at the specified interval in milliseconds.

```php
public static setInterval(callable $callback, int $milliseconds): \Luminova\Utils\Interval
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **callable** | The callback function to execute on each interval. |
| `$milliseconds` | **int** | The interval duration in milliseconds. |

**Return Value:**

Return new created interval instance.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throws if an error occurs during execution.

***

### run

Starts or resumes the execution of the interval.

This method ensures the interval is marked as running and resumes the internal Fiber if it is currently suspended. The interval will execute its callback repeatedly until explicitly stopped using `clear()`.

```php
public run(mixed $value = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | An optional value to pass to the callback (default: null). |

***

### clear

Stops the interval and clears any further execution.

This method gracefully terminates the interval loop and prevents the callback from being executed again.

```php
public clear(mixed $value = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | An optional value to pass to the callback during the clear operation (default: null). |

***

### tick

Resumes the interval execution manually.

This method should be called periodically to resume the interval fiber and allow the next execution of the callback.

```php
public tick(mixed $value = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | An optional value to pass to the callback when resuming (default: null). |

**Return Value:**

`bool` - Return true if interval is running and invocation succeed, false otherwise.

***

### isRunning

Checks if the interval is currently running.

This method returns a boolean indicating whether the interval is active and its callback is being executed at regular intervals.

```php
public isRunning(): bool
```

**Return Value:**

`bool` - Return `true` if the interval is running, otherwise `false`.