# Promise Utility Classes

***

## Overview

Use Luminova’s Deferred, Fulfilled, and Rejected classes to simplify promise handling, manage pre-resolved states, and improve async logic clarity.

***

## Introduction

Luminova provides a set of utility classes to simplify common [promise](/utilities/promise-object.md) patterns.
These include `Deferred`, `Fulfilled`, and `Rejected`, each designed to handle different promise states or workflows efficiently.

---

## Use Cases

| Class         | Purpose                                        | Typical Use Case                                 |
| ------------- | ---------------------------------------------- | ------------------------------------------------ |
| **Deferred**  | Create and control promise resolution manually | Async callbacks, timeouts, or custom async logic |
| **Fulfilled** | Represent an already completed promise         | Return cached or static data as a promise        |
| **Rejected**  | Represent an already failed promise            | Return a known failure immediately               |

---

### Deferred

The **Deferred** class provides full control over a `Promise` object.
It separates the creation of a promise from its resolution or rejection — ideal when the result of an operation isn’t immediately available, such as waiting for asynchronous tasks, I/O operations, or timed callbacks.

* **Class Namespace**: `Luminova\Utility\Promise\Deferred`

**Constructor:**

Create a new Deferred instance with a pending promise state.

```php
public __construct()
```

**Usage**

```php
use Luminova\Utility\Promise\Deferred;

$deferred = new Deferred();

// Access the promise object
$deferred->promise()
    ->then(function ($result) {
        echo "Done: $result";
    })
    ->catch(function ($error) {
        echo "Failed: " . $error->getMessage();
    });

// Later in your code...
$deferred->resolve('Operation successful');

// or handle failure
// $deferred->reject(new Exception('Something went wrong'));
```

**Common Use Case**

Deferred promises are commonly used in custom async wrappers:

```php
use Luminova\Utility\Async;
use Luminova\Utility\Promise\Deferred;

function delayedMessage(): PromiseInterface {
    $deferred = new Deferred();
    Async::setTimeout(function () use ($deferred) {
        $deferred->resolve('Done after delay');
    }, 1000);
    return $deferred->promise();
}

delayedMessage()->then(fn($msg) => echo $msg);
```

---

### Fulfilled

The **Fulfilled** class represents a promise that is already resolved with a known value.
This is helpful when you want to return a precomputed result as a promise, without additional asynchronous logic.

* **Class Namespace**: `Luminova\Utility\Promise\Fulfilled`
* **Implements**: [Luminova\Interface\PromiseInterface](/interface/classes.md#promiseinterface)

**Constructor:**

Creates a promise that is already fulfilled (resolved).

```php
public __construct(mixed $value)
```

**Parameters**

| Parameter | Type      | Description                                        |
| --------- | --------- | -------------------------------------------------- |
| `$value`  | **mixed** | The value used to immediately resolve the promise. |

**Usage**

```php
use Luminova\Utility\Promise\Fulfilled;

$promise = new Fulfilled('Resolved Value');

$promise->then(function ($value) {
    echo "Resolved: " . $value; // Output: Resolved: Resolved Value
});
```

---

### Rejected

The **Rejected** class represents a promise that is already rejected with a specific error or exception.
It's useful for returning an immediate failure state from a function that normally returns a promise.

* **Class Namespace**: `Luminova\Utility\Promise\Rejected`
* **Implements**: [Luminova\Interface\PromiseInterface](/interface/classes.md#promiseinterface)

**Constructor:**

Creates a promise that is already rejected.

```php
public __construct(\Throwable $reason)
```

**Parameters**

| Parameter | Type          | Description                                                    |
| --------- | ------------- | -------------------------------------------------------------- |
| `$reason` | **Throwable** | The reason for the rejection, typically an exception or error. |

**Usage**

```php
use Luminova\Utility\Promise\Rejected;

$promise = new Rejected(new \Exception('An error occurred'));

$promise->catch(function (\Throwable $error) {
    echo "Error: " . $error->getMessage(); // Output: Error: An error occurred
});
```