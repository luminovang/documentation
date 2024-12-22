# Promise for Asynchronous Task Management

***

## Overview

A Promise represents a task that resolves or rejects asynchronously.

***

## Introduction

A `Promise` represents the eventual result of an asynchronous operation. It provides a structured way to handle the success or failure of an operation through registered callbacks. Promises can manage fulfillment, rejection, or actions to be taken regardless of the outcome.

The primary interaction with a promise is through the `then` method, which allows you to register handlers for both fulfilled and rejected states, while the `catch` method specifically handles failure scenarios.

In the Luminova promise implementation, additional functionality is available: 
- The `canceled` method enables registering a handler that is executed when the promise is canceled by invoking `cancel` method.
- The `error` method allows for handling logic errors or implementation issues. If no error handler is registered, these errors are thrown as exceptions.

### Catch vs Error Methods

- **`catch`:** This method is triggered and executes the callback when the promise is rejected, typically due to an expected failure or external issue.
- **`error`:** This method is invoked and executes the callback only when a logic error or incorrect promise implementation occurs. This is intended for internal errors rather than standard rejection cases. 

---

## Usage Examples

Promises in Luminova implementation support two styles of initialization, each suited to different use cases. Below are examples demonstrates the use of promise to handle asynchronous success and failure outcomes.

**Using an Executor for Asynchronous Operations**

This implementation Mimicking JavaScript Promise. Use this approach when you need to handle asynchronous operations, providing a function that defines how to resolve or reject the promise.

```php
use Luminova\Utils\Promise\Promise;

$promise = new Promise(function (callable $resolve, callable $reject) {
    $data = rand(1, 10);
    if ($data > 5) {
        $resolve("Success with data: $data");
    } else {
        $reject("Failure with data: $data");
    }
});

$promise->then(function (string $result) {
    return "First Is: {$result}\n";
})->then(function (string $value) {
    echo "Next Is: " . $value;
})->catch(function (\Throwable $error) {
    echo "Error: " . $error->getMessage() . PHP_EOL;
})->finally(function(){
    echo "All done";
});
```

---

**Handling Resolved and Rejected States Directly**

Use this approach when you already have the resolved or rejected state and want to directly provide callbacks to handle the outcomes.

```php
$promise = new Promise(
    function ($value) {
        echo "Hello " . $value;
    },
    function (\Throwable $reason) {
        echo "Rejected with reason: " . $reason->getMessage();
    }
);

$promise->resolve('World!');
```

---

### Error Propagation

This example shows how to handle and propagate errors through the `catch` method.

```php
$promise = new Promise();

$promise->then(function ($value) {
    throw new \Exception((string) ($value + 1));
})->catch(function (\Throwable $e) {
    // Propagate rejection
    throw $e;
})->catch(function (\Throwable $e) use ($promise) {
    // Optionally return or throw another rejection
    // Or return $e->getMessage() + 1;
    // Or return new \Exception($e->getMessage() + 1);
    throw new \Exception($e->getMessage() + 1);
})->catch(function (\Throwable $e) {
    echo 'Reject ' . $e->getMessage(); // Outputs: 3
});

$promise->resolve(1); 
```

---

### ThirdParty Promises with the Luminova Promise System

This example demonstrates how to use promises in Luminova with third-party promise systems. When an object is resolved, if it implements Luminova's `PromiseInterface` or is an object with a `.then()` method, the Luminova promise system will attempt to resolve or reject it. The system will return a new `PromiseInterface` instance containing the resolved or rejected result of the original promise. This ensures compatibility with third-party promises in your Luminova application.

```php
$promise->then(function (string $result) {
    // Return a new promise object
    $p = new \Some\ThirdParty\Promise();
    $p->resolve('ThirdParty Promise Resolved With: ' . $result);

    // Alternatively, return a new Luminova promise
    // return new \Luminova\Utils\FulfilledPromise('Resolved for: ' . $result);
    // Or return a rejected promise
    // return new \Luminova\Utils\RejectedPromise(new \Error('Rejected for: ' . $result));

    return $p;
})->then(function (\Luminova\Interface\PromiseInterface $p) {
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

**Another Example**

This example demonstrates how to wrap a third-party promise system within Luminova's Promise implementation.

```php
$promise = new Promise(function (callable $resolve, callable $reject) {
    Foo\Bar\Async::promise()
        ->then(fn($value) => $resolve($value)) // Resolve the Luminova promise
        ->otherwise(fn($error) => $reject(
            $error instanceof Throwable ? $error : new \Exception($error)
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

### Wait till Fulfillment or Rejection

This example demonstrates how to transform values sequentially and waits for final result.

```php
$promise = new Promise();

$wait = $promise->then(function ($value) {
    // $value === 10
    return $value * 2;
})->then(function ($value) {
    // $value === 20
    return $value + 5;
});

$promise->resolve(10);
echo $wait->wait(); // Final value is (10 * 2) + 5 = 25
```

---

### Handling Mixed Resolutions

This example demonstrates a `Promise` chain where values and exceptions are handled and mixed.

```php
$promise = new Promise();

$promise->then(function ($value) {
    return $value + 1;
})->then(function ($value) {
    throw new \Exception($value + 1);
})->catch(function (\Throwable $e) {
    return $e->getMessage() + 1;
})->finally(function ($value) {
    echo 'Total ' . $value; // Outputs: Total 4
});

$promise->resolve(1);  // Resolves to value "1"
```

---

### Sequential Value Resolution

This example shows how values are sequentially resolved in a promise chain.

```php
$promise = new Promise();
$promise->then(function ($x) {
    return $x + 1; // Adds 1
})->then(function ($x) {
    // $x === 2
    return $x + 1; // Adds 1
})->then(function ($x) {
    // $x === 3
    return $x + 1; // Adds 1
})->then(function ($x) {
    // $x === 4
    echo 'Resolve ' . $x; // Outputs: Resolve 4
});

$promise->resolve(1); // Resolves to value "1"
```

---

### Advanced Handling

This example demonstrates how to handle different promise states, including logic errors, fulfillment, rejection, and cancellation. The logic incorporates conditional behavior based on dynamic data values.

```php
use Luminova\Utils\Promise\Promise;

// Create a promise and define a logic error handler
$promise = (new Promise())->error(function (\Throwable $e) {
    echo 'Logic Error: ' . $e->getMessage() . PHP_EOL;
});

// Chain multiple handlers for fulfillment, rejection, and cancellation
$chain = $promise->then(function ($value) {
    echo "Resolved with value: {$value}" . PHP_EOL;
})->catch(function (\Throwable $e) {
    echo "Error: {$e->getMessage()}" . PHP_EOL;
})->canceled(function ($reason, string $state) {
    echo "Canceled with reason: {$reason}, in state: {$state}" . PHP_EOL;
});

// Simulate dynamic data behavior
$data = rand(0, 15);

if ($data >= 5 && $data < 10) {
    // Resolve the promise
    $promise->resolve("Value: {$data}");
} elseif ($data === 0) {
    // Simulate a logic error: resolve and reject the same promise
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

***

## Class Definition

* Class Namespace: `\Luminova\Utils\Promise\Promise`
* This class implements:
[\Luminova\Interface\PromiseInterface](/interface/classes.md#promiseinterface)

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

Initializes a new Promise object with optional callback handlers.

```php
public __construct(?callable $onResolve = null, ?callable $onReject = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onResolve` | **callable&#124;null** | Optional callback invoked when the promise is resolved. |
| `$onReject` | **callable&#124;null** | Optional callback invoked when the promise is rejected. |

**Initialization Behavior**

- If `$onResolved` is provided and the argument is not a callable type, it is treated as a direct resolution handler. In this case, `$onResolved` and `$onRejected` are assigned directly.
- Otherwise, if `$onResolved` is callable, it is invoked with two arguments (`resolve` and `reject`) to allow external resolution or rejection of the promise.
- If `$onResolved` is `null`, the constructor assigns `$onRejected` as the rejection callback.
- If the callback argument types do not align with the expected promise behavior, the promise is immediately rejected with a `RuntimeException`.

***

### deferred

Returns a new instance of the PromiseInterface.
This method can be used to create a new singleton instance of a promise.

```php
public static deferred(?callable $executor = null): \Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$executor` | **callable&#124;null** | Invoked when the promise fulfills.<br />Receives the resolved value. |

**Return Value:**

`\Luminova\Interface\PromiseInterface` - Return a new static promise instance.

***

### getState

Retrieves the current state of the promise.

```php
public getState(): string
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
public then(?callable $onResolve = null, ?callable $onReject = null): \Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onResolve` | **callable&#124;null** | Invoked when the promise fulfills.<br />Receives the resolved value. |
| `$onReject` | **callable&#124;null** | Invoked when the promise is rejected.<br />Receives the rejection reason. |

**Return Value:**

`\Luminova\Interface\PromiseInterface` - Return a new promise resolved with the handler's return value.

***

### finally

Appends a handler to be executed regardless of the promise's outcome.

This handler will be executed whether the promise is fulfilled or rejected, making it useful for performing finalization tasks.

```php
public finally(callable $onAlways): \Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onAlways` | **callable** | Invoked when the promise is either fulfilled or rejected.<br />Receives the fulfillment or rejection value. |

**Return Value:**

`\Luminova\Interface\PromiseInterface` - Return a new promise resolved with the handler's return value.

> **Note:** The `finally` callback is executed only when the promise is fulfilled or rejected.  
> If the `cancel` method is invoked, only the `canceled` callback will run; the `finally` callback will not be called.

***

### catch

Registers an error handler that will be called to handle promise rejection.

This method will invoke `$onCatch` when an exception is thrown during the fulfillment or rejection of the promise. It allows for handling errors specifically within the promise's execution.

```php
public catch(callable $onCatch): \Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onCatch` | **callable** | Invoked when the promise is rejected.<br />Receives the rejection reason. |

**Return Value:**

`\Luminova\Interface\PromiseInterface` - Return a new promise resolved with either the rejection handler's value or the original value if the promise was fulfilled.

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

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - if the promise has already been resolved.

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

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - if the promise has already been resolved.

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

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - if the promise cannot settle after waiting or lacks a `wait` function.

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

### allSettled

Resolves when all the promises in the array have settled.

Settled means the promise has either resolved or rejected. Returns an array of results with their states.

```php
public static allSettled(\Luminova\Interface\PromiseInterface[] $promises): \Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$promises` | **\Luminova\Interface\PromiseInterface[]** | An array of promises. |

**Return Value:**

`\Luminova\Interface\PromiseInterface` - Return a promise that resolves with an array of results.

***

### try

Executes a callable within a promise.

The callable is executed immediately, and its result is used to resolve or reject the promise.

```php
public static try(callable $fn): \Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fn` | **callable** | A callable function to execute. |

**Return Value:**

`\Luminova\Interface\PromiseInterface` - Return a promise that resolves with the result of the callable.

***

### race

Resolves or rejects as soon as any promise in the array resolves or rejects.

Returns a promise that resolves or rejects with the value or reason of the first promise to settle.

```php
public static race(\Luminova\Interface\PromiseInterface[] $promises): \Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$promises` | **\Luminova\Interface\PromiseInterface[]** | An array of promises. |

**Return Value:**

`\Luminova\Interface\PromiseInterface` - Return a promise that resolves or rejects with the first settled promise.

***

### all

Resolves when all promises in the array resolve.

If any promise rejects, the returned promise immediately rejects with the reason of the first rejection.

```php
public static all(\Luminova\Interface\PromiseInterface[] $promises): \Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$promises` | **\Luminova\Interface\PromiseInterface[]** | An array of promises. |

**Return Value:**

`\Luminova\Interface\PromiseInterface` - Return a promise that resolves with an array of values.

***

### any

Resolves with the value of the first resolved promise.

If all promises reject, the returned promise rejects with a reason indicating the failure.

```php
public static any(\Luminova\Interface\PromiseInterface[] $promises): \Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$promises` | **\Luminova\Interface\PromiseInterface[]** | An array of promises. |

**Return Value:**

`\Luminova\Interface\PromiseInterface` - Return a promise that resolves with the first fulfilled value.

***

### Promise Utility Classes

Luminova provides `FulfilledPromise` and `RejectedPromise` utility classes to manage specific promise states efficiently. 
These classes enable direct handling of resolved or rejected promises without requiring additional chaining or logic. 
Both classes implement the [\Luminova\Interface\PromiseInterface](/interface/classes.md#promiseinterface).

---

### FulfilledPromise

Represents a promise that is already resolved with a specific value.

* **Class Namespace**: `Luminova\Utils\Promise\FulfilledPromise`

**Usage**

```php
use Luminova\Utils\Promise\FulfilledPromise;

$promise = new FulfilledPromise('Resolved Value');

$promise->then(function ($value) {
    echo "Resolved: " . $value; // Output: Resolved: Resolved Value
});
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | The value with which the promise is resolved. |

---

### RejectedPromise

Represents a promise that is already rejected with a specific error.

* **Class Namespace**: `Luminova\Utils\Promise\RejectedPromise`

**Usage**
```php
use Luminova\Utils\Promise\RejectedPromise;

$promise = new RejectedPromise(new \Exception('An error occurred'));

$promise->error(function (\Throwable $error) {
    echo "Error: " . $error->getMessage(); // Output: Error: An error occurred
});
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$reason` | **Throwable** | The reason for the rejection, typically an instance of `Throwable`. |

---

### Benefits of Using These Classes

1. **Immediate Resolution/Rejection**: Useful for creating mock promises or handling pre-known states.
2. **Error Handling**: Ensures rejections are properly encapsulated in `Throwable` objects.