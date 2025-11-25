# Promise Object for Asynchronous Operations

***

## Overview

Promise object helps you handle asynchronous operations with clean, structured control over success, failure, and cancellation states. Includes support for deferred, chained, and third-party promises.

***

## Introduction

A **Promise** represents the eventual result of an asynchronous operation, **(value that will be available later)**. 
It helps you manage what happens when that operation **succeeds**, **fails**, or **finishes**, by using callback methods.

You mainly interact with a promise using two key methods:

* **`then`** — runs when the operation completes successfully.
* **`catch`** — runs when the operation fails or is rejected.

In Luminova's Promise system, a few extra features make promise handling more flexible:

* **`canceled`** — allows you to attach a callback that runs if the promise is canceled by calling the `cancel()` method.
* **`error`** — handles unexpected logic or implementation errors. If no error handler is attached, these are thrown as exceptions.

---

## Catch vs Error

* **`catch`** handles **normal rejections**, such as network failures, invalid input, or any expected problem during execution.
* **`error`** handles **unexpected internal errors**, such as incorrect code logic or misuse of the promise system. It's mainly for debugging or framework-level issues, not regular promise failures.

---

## Initialization Styles

When using Luminova `Promise`, the way you define your callback determines whether the promise works correctly. There are **direct handlers** (consume a resolved value) and **executor-style callbacks** (control resolution/rejection manually).

### Executor-Style Promises

Executor-style callbacks receive `$resolve` and `$reject` functions. This is the recommended approach.

```php
use Luminova\Utility\Promise;

// Recommended: fully type-hinted
$p = new Promise(function (callable $resolve, callable $reject): void {
    $resolve('Success');

    // or
    // $reject(new Exception('Failed'));
});

// Still valid without type-hinting
$p = new Promise(function ($resolve, $reject): void {
    $resolve('Success');
});
```

**With only a resolver:**

This works only if type-hinted as `callable`.

```php
$p = new Promise(function (callable $resolve): void {
    $resolve('Success');
});
```

**Invalid without type-hint**

This executes the callback but does **not** resolve the promise:

```php
$p = new Promise(function ($resolve): void {
    $resolve('Value'); // Silently fails
});
```

---

### Direct Handlers

Direct handlers expect a resolved value instead of `$resolve/$reject`. They can be used for simple operations or when the promise is already resolved.

```php
$p = new Promise(function (mixed $value): void {
    echo "Resolved value: $value";
});

// With resolved and rejection handlers
$p = new Promise(
    function (mixed $value) {
        echo "Resolved: $value";
    },
    function (Throwable $reason) {
        echo "Rejected: " . $reason->getMessage();
    }
);
```

---

### Invalid/Problematic Cases

These cases will **not work as expected**:

```php
$p = new Promise(
    function (callable $value, callable $reject) {
        // Wrong: cannot resolve properly here
    },
    function (Throwable $reason) {
        // Access rejected reason
    }
);

// Attempting to resolve/reject manually fails
$p->resolve('Bad'); // Nothing happens
$p->reject('Bad');  // Nothing happens

$p->then(function ($val) {
    // Callback will not be executed
});
```

---

## Usage

These examples shows different use cases of Luminova promise system.

> **Key Points:**
>
> 1. Always type-hint `$resolve` and `$reject` in executor-style promises to ensure correct resolution.
> 2. `then()` can return a new `Promise` for chaining operations.
> 3. `catch()` handles errors, and `finally()` executes regardless of outcome.

---

### Nested Chains

Use this style to chain multiple promise operations, where each `then` can return another promise:

```php
use Luminova\Utility\Promise\Promise;

$promise = new Promise(fn(callable $resolve) => $resolve('start'));

$promise->then(function ($val): PromiseInterface {
    echo "First: $val\n";
    return new Promise(fn(callable $resolve) => $resolve('end'));
})->then(function (string $val) {
    echo "Second: $val\n";
});

// Output:
// First: start
// Second: end
```

---

### Asynchronous Operations

Mimicking JavaScript-style promises, you provide a function that receives `$resolve` and `$reject`. Use this approach for operations that may succeed or fail later.

```php
use Luminova\Utility\Promise\Promise;

$promise = new Promise(function (callable $resolve, callable $reject): void {
    $data = rand(1, 10);

    if ($data > 5) {
        $resolve("Success with data: $data");
    } else {
        $reject(new \Exception("Failure with data: $data"));
    }
});

$promise->then(function (string $result) {
    return "First Result: {$result}\n";
})
->then(function (string $value) {
    echo "Next Result: " . $value;
})
->catch(function (\Throwable $error) {
    echo "Error: " . $error->getMessage() . PHP_EOL;
})
->finally(function() {
    echo "All done" . PHP_EOL;
});
```

---

### Handling Resolved and Rejected States

Sometimes you already know the resolved or rejected value and just want to provide callbacks for each outcome. This avoids needing an executor function.

```php
use Luminova\Utility\Promise\Promise;

$promise = new Promise(
    function (mixed $value) {
        echo "Hello " . $value;
    },
    function (\Throwable $reason) {
        echo "Rejected with reason: " . $reason->getMessage();
    }
);

// Resolve the promise directly
$promise->resolve('World!');

// Output: Hello World
```

> **Note:** 
> You can also reject directly using `$promise->reject(new \Exception('Reason'))`.

---

### Error Propagation

Promises allow you to propagate errors down the chain. Each `catch` can handle or rethrow errors for further handling.

```php
use Luminova\Utility\Promise\Promise;

$promise = new Promise();

$promise
    ->then(function (int $value) {
        throw new \Exception((string) ($value + 1));
    })
    ->catch(function (\Throwable $e) {
        // Re-throw to propagate rejection
        throw $e;
    })
    ->catch(function (\Throwable $e) {
        // Optionally transform or propagate
        throw new \Exception($e->getMessage() + 1);
    })
    ->catch(function (\Throwable $e) {
        echo 'Final Reject: ' . $e->getMessage();
        // Output: Final Reject: 3
    });

// Start the chain
$promise->resolve(1);
```

> This enables layered error handling or transformation of errors as they flow through the promise chain.

---

### Third-Party Promises

Luminova promises can seamlessly integrate with third-party promise systems.
When a value passed to `resolve()` is itself a promise—either implementing Luminova's `PromiseInterface` or providing a `.then()` method—the Luminova promise **adopts the state of that promise**.

This means the Luminova promise will resolve or reject depending on the external promise's outcome, returning a new `PromiseInterface` instance that wraps the final result. This ensures full compatibility with other asynchronous libraries or promise-based systems.

**Examples:**

```php
$promise->then(function (string $result) {
    // Return a new promise object
    $p = new \Some\ThirdParty\Promise();
    $p->resolve('ThirdParty Promise Resolved With: ' . $result);

    // Alternatively, return a new Luminova promise
    // return new Luminova\Utility\Promise\Fulfilled('Resolved for: ' . $result);
    // Or return a rejected promise
    // return new Luminova\Utility\Promise\Rejected(new \Error('Rejected for: ' . $result));

    return $p;
})->then(function (Luminova\Interface\PromiseInterface $p) {
    // Decide to return the promise object
    return $p;

    // Or handle it directly here
    // $p->then(function ($value) {
    //   echo $value;
    // })->catch(function (\Throwable $error) {
    //    echo "Error: " . $error->getMessage();
    // });
})->then(function (string $value) {
    // If decided to return the promise object in above then
    // Handle the resolved value here
    echo $value;
})->catch(function (\Throwable $error) {
    echo "Error: " . $error->getMessage();
});

// Resolve the original promise
$promise->resolve('https://example.com');
```

**Wrapping a third-party promise**

This shows how to wrap an external promise system inside a Luminova promise, allowing you to use Luminova's chaining methods:

```php
$promise = new Promise(function (callable $resolve, callable $reject) {
    Foo\Bar\Async::promise()
        ->then(fn($value) => $resolve($value)) // Resolve the value with Luminova promise
        ->otherwise(fn(mixed $error) => $reject(
            ($error instanceof Throwable) ? $error : new \Exception($error)
        )); // Reject with a Throwable or a generic exception
});

// Using Luminova's chaining methods for the promise
$promise->then(function ($value) {
    echo "Resolved Value: " . $value;
})
->error(function (Throwable $e) {
    echo "Error: " . $e->getMessage();
});
```

---

### Wait Until Fulfillment or Rejection

You can chain multiple `then()` handlers to transform values step by step. Using `wait()` allows you to get the final resolved value synchronously.

```php
$promise = new Promise();

$wait = $promise
    ->then(fn(int $value): int => $value * 2)  // Transforms 10 → 20
    ->then(fn(int $value): int => $value + 5); // Transforms 20 → 25

$promise->resolve(10);

echo $wait->wait(); // Outputs: 25
```

> **Note:** 
> Each `then()` receives the result of the previous handler. Calling `wait()` blocks until the promise chain is fully resolved or rejected.

---

### Handling Mixed Values and Exceptions

Promises can mix normal values and exceptions in a chain. Use `catch()` to handle errors and continue the chain, and `finally()` to run cleanup logic.

```php
$promise = new Promise();

$promise
    ->then(fn(int $value): int => $value + 1)           // 1 → 2
    ->then(fn(int $value) => throw new \Exception($value + 1)) // Throws 3
    ->catch(fn(\Throwable $e) => $e->getMessage() + 1)   // 3 + 1 → 4
    ->finally(fn($value) => print('Total ' . $value));     // Outputs: Total 4

$promise->resolve(1);
```

> **Note:** 
> The `finally()` always runs after the promise settles, regardless of success or failure. 
> It can receive the last value or exception if needed.

---

### Sequential Value Resolution

A promise chain resolves each handler in order, passing the result from one `then()` to the next. 
This allows you to transform values step by step.

```php
$promise = new Promise();

$promise
    ->then(fn(int $x): int => $x + 1)   // 1 → 2
    ->then(fn(int $x): int => $x + 1)   // 2 → 3
    ->then(fn(int $x): int => $x + 1)   // 3 → 4
    ->then(fn(int $x) => print('Resolved: ' . $x)); // Outputs: Resolved: 4

$promise->resolve(1); // Starts the chain with value 1
```

---

### Advanced Handling

This example shows how to handle multiple promise states in Luminova, including **fulfillment**, **rejection**, **cancellation**, and **logic errors**. It demonstrates conditional behavior based on dynamic data.

```php
use Luminova\Utility\Promise\Promise;

// Create a promise and define a logic error handler
$promise = (new Promise())->error(function (\Throwable $e) {
    echo 'Logic Error: ' . $e->getMessage() . PHP_EOL;
});

// Chain multiple handlers for fulfillment, rejection, and cancellation
$chain = $promise
    ->then(function ($value) {
        echo "Resolved with value: {$value}" . PHP_EOL;
    })
    ->catch(function (\Throwable $e) {
        echo "Error: {$e->getMessage()}" . PHP_EOL;
    })
    ->canceled(function ($reason, string $state) {
        echo "Canceled with reason: {$reason}, in state: {$state}" . PHP_EOL;
    });

// Simulate dynamic data to trigger different promise states
$data = rand(0, 15);

if ($data >= 5 && $data < 10) {
    // Fulfill the promise
    $promise->resolve("Value: {$data}");
} elseif ($data === 0) {
    // Logic error: attempt both resolve and reject
    $promise->resolve("Resolve Value: {$data}");
    $promise->reject("Rejected: {$data}");
} elseif ($data < 5) {
    // Reject the promise
    $promise->reject("Rejected: {$data}");
} else {
    // Cancel the promise
    $chain->cancel("Canceled: {$data}");
}
```

> **Key Points:**
>
> * `then()` handles fulfilled values.
> * `catch()` handles errors or rejections.
> * `canceled()` handles cancellation events.
> * `error()` handles logic errors, such as invalid callback execution or runtime exceptions.
> * Promises can only be **resolved**, **rejected**, or **canceled** once. Trying to change the state after fulfillment triggers a logic error.

***

## Class Definition

* Class Namespace: `Luminova\Utility\Promise\Promise`
* This class implements:
[Luminova\Interface\PromiseInterface](/interface/classes.md#promiseinterface)

***

### Constants

| Constant  | Type    | Value    | Description                                                                 |
|-----------|---------|----------|-----------------------------------------------------------------------------|
| `PENDING` | string  | `'pending'` | Represents the state of a promise that is still pending fulfillment or rejection. |
| `FULFILLED` | string  | `'fulfilled'` | Represents the state of a promise that has been fulfilled or resolved successfully. |
| `REJECTED` | string  | `'rejected'` | Represents the state of a promise that has been rejected due to an error or failure. |

***

## Methods

### constructor

Create a new Promise instance.
     
A Promise allows you to manage values that are not immediately available, 
such as results from asynchronous operations. It provides a clean way to handle 
success, failure, or cancellation states in an operation.

**How it works:**
- If you pass a function as the first argument (`$onResolved`), it receives two functions: 
  `$resolve` and `$reject`. Call `$resolve($value)` when the operation succeeds, or 
  `$reject($reason)` when it fails.

- If you pass simple callbacks for `$onResolved` and `$onRejected`, 
  they are used as handlers when you manually call `$promise->resolve()` or `$promise->reject()`.

```php
public __construct(?callable $onResolve = null, ?callable $onReject = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onResolve` | **callable&#124;null** | The executor or handler function to call when the promise is created or resolved. |
| `$onReject` | **callable&#124;null** | The callback to call when the promise is rejected. |

**Throws**

[Luminova\Exceptions\RuntimeException][../../RuntimeException.md] - If the provided handlers are invalid or unsupported and no error handler was registered to handler exceptions.

***

### state

Retrieves the current state of the promise.

Alias `getState()`

```php
public state(): string
```

The state will be one of the following constants:

- `PENDING` - The promise is still in progress.
- `FULFILLED` - The promise has been resolved.
- `REJECTED` - The promise has been rejected.

**Return Value:**

`string` - Return the current state of the promise.

***

### then

Attaches fulfillment and rejection handlers to the promise.

When the promise is fulfilled, the `$onResolve` callback is invoked, and if the promise is rejected, the `$onReject` callback is invoked. This method returns a new promise that resolves with the return value of the handler's result.

```php
public then(?callable $onResolve = null, ?callable $onReject = null): Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onResolve` | **callable&#124;null** | Invoked when the promise fulfills.<br />Receives the resolved value. |
| `$onReject` | **callable&#124;null** | Invoked when the promise is rejected.<br />Receives the rejection reason. |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Return a new promise resolved with the handler's return value.

***

### finally

Appends a handler to be executed regardless of the promise's outcome.

This handler will be executed whether the promise is fulfilled or rejected, making it useful for performing finalization tasks.

```php
public finally(callable $onAlways): Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onAlways` | **callable** | Invoked when the promise is either fulfilled or rejected.<br />Receives the fulfillment or rejection value. |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Return a new promise resolved with the handler's return value.

> **Note:** The `finally` callback is executed only when the promise is fulfilled or rejected.  
> If the `cancel` method is invoked, only the `canceled` callback will run; the `finally` callback will not be called.

***

### catch

Registers an error handler that will be called to handle promise rejection.

This method will invoke `$onCatch` when an exception is thrown during the fulfillment or rejection of the promise. It allows for handling errors specifically within the promise's execution.

```php
public catch(callable $onCatch): Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onCatch` | **callable** | Invoked when the promise is rejected.<br />Receives the rejection reason. |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Return a new promise resolved with either the rejection handler's value or the original value if the promise was fulfilled.

***

### cancelled

Registers a promise or global cancellation handler that will be called when promise `cancel` is invoked.
This method will invoke `$onCancelled` whenever promise cancellation method is called.

```php
public canceled(callable $onCancelled): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onCancelled` | **callable** | Invoked when the promise is `cancel` method is triggered. |

**Return Value:**

`self` - Return a instance of the promise.

***

### error

Registers a global error handler that will be called if an exception occurs within the promise due to logic error.

This method will invoke `$onError` when an exception is thrown caused by logic error during the execution, it allows for handling errors specifically within the promise's execution lifecycle.

```php
public error(callable $onError): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onError` | **callable** | Invoked when the an error is encountered. |

**Return Value:**

`self` - Return a instance of the promise.

***

### resolve

Resolves the promise with a given value.

If the promise has already been resolved or rejected, this method will throw a `RuntimeException`. Use this method to resolve the promise when an completes successfully.

```php
public resolve(mixed $value): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | The value to resolve the promise with. |

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - if the promise has already been resolved.

***

### reject

Rejects the promise with a given reason.

If the promise has already been resolved or rejected, this method will throw a `RuntimeException`. Use this method to reject the promise when an operation fails. If passed reason is string it will create an `ErrorException` exception object, otherwise it retain the original reason.

```php
public reject(mixed $reason): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$reason` | **mixed** | The reason for rejection. |

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - if the promise has already been resolved.

***

### cancel

Cancels running promise.

This method allows you to cancel a promise before it settles.

```php
public cancel(mixed $reason = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$reason` | **mixed** | An optional value to pass to the canceled callback handler (default: `null`). |

> **Note:** calling this method doesn't trigger `catch` method in promise chain.
> To handle when cancel is called use the `canceled` method instead.

***

### wait

Waits for the promise to complete and returns the result or throws an error.

The `wait` method waits for execution uto either fulfilled or rejected. If the promise is fulfilled, the resolved value is returned. If it is rejected, the rejection reason is return as an instance of exception.

```php
public wait(int $timeout = 1000): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timeout` | **int** | The timeout in milliseconds to wait for the promise to fulfill or reject (default: `1000`). |

**Return Value:**

`mixed` - Return the resolved value or the rejection reason.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - if the promise cannot settle after waiting or lacks a `wait` function.

***

### is

Check if promise state has fulfilled rejected or pending state.

```php
public is(string $state): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$state` | **string** | The state to check against promise state. |

**Return Value:**

`bool` - Return true if promise current state is same as passed state, otherwise false.

***

## Static Utility Methods

### from

Create a new promise instance, optionally with a task to handle its resolution.

This method is a simple static factory for creating a promise object that can be 
resolved or rejected later.

```php
public static from(?callable $onResolved = null): Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onResolved` | **callable&#124;null** |  Optional callback that runs when the promise is resolved.<br />The resolved value is passed to this callback. |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Returns a new, pending promise instance.

**Examples:**

Basic usage:

```php
use Luminova\Utility\Promise\Promise;

$promise = Promise::from();
$promise->then(function ($result) {
    echo "Got: $result";
});
$promise->resolve('Hello World');
```

With an resolve callback:

```php
use Luminova\Utility\Promise\Promise;
     
$promise = Promise::from(function ($resolve, $reject) {
    echo "Result is: " . $value;
});
```

***

### deferred

Creates and returns a controllable deferred promise instance.

A deferred object lets you manage a promise externally, you can create the promise now 
and resolve or reject it later. This is useful for handling results that depend on 
asynchronous operations or callbacks.

```php
public static deferred(): Deferred
```

**Return Value:**

`Luminova\Utility\Promise\Deferred` - Deferred Returns the deferred promise controller.

**Examples:**

Basic usage:
```php
use Luminova\Utility\Promise\Promise;

$deferred = Promise::deferred();

$deferred->promise()
    ->then(fn($result) => echo "Done: $result")
    ->catch(fn($error) => echo "Failed: {$error->getMessage()}");

// Later resolve
$deferred->resolve('Operation successful');

// or reject
// $deferred->reject(new Exception('Something went wrong'));
```

Async timeout:

```php
use Luminova\Utility\Promise\Promise;
use Luminova\Utility\Async;

function callAsync(): PromiseInterface {
    $deferred = Promise::deferred();

    Async::setTimeout(function () use ($deferred) {
        $deferred->resolve('Time: ' . date('H:i:s'));
    }, 1000);

    return $deferred->promise();
}

callAsync()->then(fn($value) => echo $value);
```

***

### delay

Create a promise that resolves after a specified delay.
     
This method pauses execution for a given number of milliseconds before 
automatically resolving the promise. It is useful for simulating 
asynchronous wait times or delaying operations in sequential tasks.

```php
public static delay(int $ms, mixed $value = null): PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ma` | **int** | The number of milliseconds to wait before resolving the promise. |
| `$value` | **mixed** | An optional value to resolve after delay (default: null). |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Returns a promise that resolves after the specified delay.

**Examples:**

Basic usage:

```php
use Luminova\Utility\Promise\Promise;

Promise::delay(1000)->then(function () {
    echo "Resolved after 1 second.";
});

// With value 

Promise::delay(1000, 'done')
     ->then(fn($v) => echo $v); // outputs "done" after 1 second
```

With chained promises:
```php
use Luminova\Utility\Promise\Promise;

Promise::delay(500)
    ->then(function () {
        echo "Half a second passed.\n";
        return Promise::delay(1000);
    })
    ->then(function () {
        echo "Another second passed.\n";
    });
```

Using inside an async operation:
```php
use Luminova\Utility\Promise\Promise;
use Luminova\Utility\Async;

Async::await(function () {
    echo "Waiting...\n";
    yield Promise::delay(2000);
    echo "Done after 2 seconds.";
});
```

***

### try

Executes a callable within a promise.

The callable is executed immediately, and its result is used to resolve or reject the promise.

```php
public static try(callable $fn): Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fn` | **callable** | A callable function to execute. |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Return a promise that resolves with the result of the callable.

**Example:**

```php
Promise::try(fn() => "Success")->then(function ($value) {
    echo $value; // Outputs: Success
})->catch(function (\Throwable $error) {
   echo "Error: " . $error->getMessage();
});
```

***

### race

Resolves or rejects as soon as any promise in the array resolves or rejects.

Returns a promise that resolves or rejects with the value or reason of the first promise to settle.

```php
public static race(Luminova\Interface\PromiseInterface[] $promises): Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$promises` | **Luminova\Interface\PromiseInterface[]** | An array of promises. |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Return a promise that resolves or rejects with the first settled promise.

**Example:**

```php
use Luminova\Utility\Promise;

$promises = [
    Promise::try(fn() => sleep(2) || 42),
    Promise::try(fn() => 24),
];

Promise::race($promises)->then(function ($value) {
    echo $value; // Outputs: 24
});
```

***

### all

Resolves when all promises in the array resolve.

If any promise rejects, the returned promise immediately rejects with the reason of the first rejection.

```php
public static all(Luminova\Interface\PromiseInterface[] $promises): Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$promises` | **Luminova\Interface\PromiseInterface[]** | An array of promises. |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Return a promise that resolves with an array of values.

**Example:**

```php
use Luminova\Utility\Promise;

$promises = [
    Promise::try(fn() => 10),
    Promise::try(fn() => 20),
];

Promise::all($promises)->then(function ($values) {
    print_r($values); // Outputs: [10, 20]
});
```

Another example.

```php
function getUserAccount() {
  return Luminova\Http\Network::get('/user/12345');
}

function getUserPermissions() {
  return Luminova\Http\Network::get('/user/12345/permissions');
}

Luminova\Utility\Promise\Promise::all([getUserAccount(), getUserPermissions()])
  ->then(function ($results) {
        $account = $results[0];
        $permission = $results[1];
  });
```

***

### any

Resolves with the value of the first resolved promise.

If all promises reject, the returned promise rejects with a reason indicating the failure.

```php
public static any(Luminova\Interface\PromiseInterface[] $promises): Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$promises` | **Luminova\Interface\PromiseInterface[]** | An array of promises. |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Return a promise that resolves with the first fulfilled value.

**Example:**

```php
$promises = [
    Promise::try(fn() => throw new Exception('Error')),
    Promise::try(fn() => 42),
];

Promise::any($promises)->then(function ($value) {
    echo $value; // Outputs: 42
});
```

---

### allSettled

Resolves when all the promises in the array have settled.

Settled means the promise has either resolved or rejected. Returns an array of results with their states.

```php
public static allSettled(Luminova\Interface\PromiseInterface[] $promises): Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$promises` | **Luminova\Interface\PromiseInterface[]** | An array of promises. |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Return a promise that resolves with an array of results.

**Example:**

```php
$promises = [
    Promise::try(fn() => 42),
    Promise::try(fn() => throw new Exception('Error')),
];

Promise::allSettled($promises)->then(function ($results) {
    print_r($results);
});
```

---

## See Also

For related helper classes that provide pre-defined promise states, such as [Deferred](/utilities/promise-utilities#lmv-docs-deferred), [Fulfilled](/utilities/promise-utilities#lmv-docs-fulfilled), and [Rejected](/utilities/promise-utilities#lmv-docs-rejected), see the [Promise Utility Classes](/utilities/promise-utilities.md) documentation.