# Pipeline-Based Data Processing and Transformation

***

## Overview

The Pipeline class is a simple utility class that allows data to flow through a series of transformations, operations, and error-handling steps.

***

## Introduction

The **Pipeline** class provides an easy-to-use, chainable interface for processing data in a pipeline pattern. It supports both synchronous and asynchronous execution for non-blocking tasks, enabling efficient handling of complex workflows. The class is ideal for cases where data needs to be processed in stages, and handles errors gracefully, by continuing or stop the flow based on returned value.

---

### Purpose

The Pipeline processes an initial value through a series of operations or transformations. Each operation is executed in sequence, and the output of one operation becomes the input to the next. This allows for easy chaining of operations. Additionally, the class provides built-in mechanisms to handle errors and control flow, such as stopping the pipeline or returning to a previous valid state.

---

### Features

- **Chainable Operations:** You can add multiple operations to the pipeline in sequence using the `pipe()` method.
- **Error Handling:** Errors can be captured using the `catch()` method, with the ability to control the pipeline's flow after an error (stop or continue).
- **Asynchronous Execution:** Supports executing operations in parallel (using PHP Fibers) for better performance in certain cases.
- **Graceful Termination:** Provides mechanisms to stop or continue the pipeline based on error conditions or custom logic.
- **Flexible Input:** The pipeline can accept any data type (objects, arrays, primitive types) as the initial value and process them accordingly.

---

While a variable-based approach works for simple use cases, the **Pipeline class** shines in complex, reusable, and error-prone workflows by improving readability, modularity.

### When to Use the Pipeline

Use the **Pipeline class** when:
1. **Complex Workflows**: If you have multiple transformations or steps that need clear organization.
2. **Error Handling**: When you want a clean way to handle errors across all steps.
3. **Reusability**: If the same process needs to be applied in multiple places.
4. **Asynchronous Tasks**: For non-blocking or background tasks.
5. **Dynamic Pipelines**: When steps may be conditionally added or configured at runtime.

---

### When Not to Use the Pipeline
1. **Simple Transformations**: For 2-3 straightforward operations, variables are sufficient.
2. **Performance-Critical Code**: While the Pipeline class is efficient, the overhead of function calls and dynamic state management might not be ideal in highly performance-sensitive scenarios.

---

### Usage Examples

#### Basic Pipeline Implementation

This example demonstrates how to start a new pipeline with an initial value and apply a series of transformations.

```php
$result = Pipeline::chain(10)
   ->pipe(fn($x) => $x + 5)  // Add 5
   ->pipe(fn($x) => $x * 2)  // Multiply by 2
   ->getResult();

echo $result;  // Output: 30
```

#### Process an Array

In this example, the pipeline processes an array by applying a series of transformations, such as filtering, mapping, and reducing.

```php
$result = Pipeline::chain([1, 2, 3, 4, 5, 6])
   ->pipe(function(array $arr): array {
      // Filter even numbers
      return array_filter($arr, fn($x) => $x % 2 === 0);
   })
   ->pipe(function(array $arr): array {
      // Multiply each number by 10
      return array_map(fn($x) => $x * 10, $arr);
   })
   ->pipe(function(array $arr): int {
      // Sum all the numbers
      return array_sum($arr);
   })
   ->getResult();

echo $result; // Output: 120 (because (2*10) + (4*10) + (6*10) = 120)
```

---

#### String Manipulation Pipeline

In this example, we manipulate a string by applying multiple transformations, including capitalizing the first letter, appending text, and wrapping the result in HTML tags.

```php
$result = Pipeline::chain('hello')
   ->pipe(fn($x) => ucfirst($x))  // Capitalize first letter
   ->pipe(fn($x) => $x . ' World')  // Append " World"
   ->pipe(fn($x) => '<p>' . trim($x) . '!</p>')  // Trim whitespace and wrap in paragraph tags
   ->getResult();

echo $result;  // Output: <p>Hello World!</p>
```

---

#### Error Handling

This example catches an exception and stops the pipeline.

```php
$result = Pipeline::chain(random_int(1, 10))
   ->catch(function(\Throwable $e, int $result): mixed {
      echo "Error occurred: {$e->getMessage()} with last valid value {$result}";
      return Pipeline::STOP;  // Stop the pipeline execution
   })
   ->pipe(function(int $x): int {
      if ($x > 5) {
         return $x + 1;
      }
      
      throw new \Error("{$x} must be greater than 5");
   })
   ->getResult();

echo $result;
```

---

### Error Handling With Last Valid Result

In this example, the `catch()` method is used to manage exceptions, allowing the pipeline to either continue with the last valid result, continue with new result value or stop execution. You can explicitly return `Pipeline::LAST_STATE` or `$result` to use the last valid result from the pipeline execution.

```php
$result = Pipeline::chain(random_int(1, 10))
   ->catch(function(\Throwable $e, int $result): mixed {
      // Return a new value or ``$result`` to allow the pipeline to continue with the value.
      // Or return Pipeline::LAST_STATE to continue with the last valid result
      return Pipeline::LAST_STATE;
   })
   ->pipe(function(int $x): int {
      if ($x > 5) {
         return $x + 1;  // Increment if greater than 5
      }
      
      throw new \Error("{$x} must be greater than 5");  // Trigger error if less than or equal to 5
   })
   ->pipe(function(int $x): mixed {
      return 10 - $x;  // Subtract from 10
   })
   ->getResult();

echo $result;  // Output: The last valid result before error occurred.
```

> **Note:** The second parameter `$result` in the `catch()` callback handler, is the last valid result before an error occurred.

---

#### Stopping Pipeline Execution

This example demonstrates stopping the pipeline execution based on a condition, ensuring that only valid results are processed.

```php
$result = Pipeline::chain(random_int(8, 9))
   ->pipe(function(int $x): int {
      return ($x + 1);  // Increment by 1
   })
   ->pipe(function(int $x): string|int {
      if ($x >= 10) {
         return Pipeline::STOP;  // Stop if the value reaches 10
      }

      return $x;
   })
   ->pipe(function(int $x): int {
      return ($x + 1);  // This operation will not be executed due to stopping
   })
   ->getResult();

echo $result; // Output: 10
```

---

#### Lazy Loading

This approach uses a lazy initialization pattern to defer the creation of the Pipeline object until it is actually needed. Combining `LazyObject` with Pipeline can improve performance when the pipeline construction is expensive, or when the pipeline might not always be used.

```php
use Luminova\Utility\Object\LazyObject;
use Luminova\Utility\Pipeline;

$pipe = LazyObject::newObject(function(): Pipeline {
   return Pipeline::chain(doAsyncOperation(), true)
      ->pipe(fn($x) => expensiveOperation1($x));
      ->pipe(fn($x) => expensiveOperation2($x))
});

// Trigger the lazy initialization, perform pipeline operations and return the result only when needed.
echo $pipe->getResult();
```

#### Use Cases

This approach is particularly useful when:
- **Expensive Operations**: The pipeline involves computationally expensive or resource-intensive steps.
- **Conditional Usage**: The pipeline may not always be required in the execution flow.
- **Dynamic Pipelines**: The pipeline structure or initial value depends on runtime conditions.
- **Deferred Dependencies**: Some dependencies required for constructing the pipeline may not be available at the point of initialization.

Using `LazyObject` to wrap a `Pipeline` is a solid approach when deferred initialization is beneficial. However, if the pipeline is lightweight or always used, this may introduce unnecessary complexity. The decision should be guided by your application's performance and maintainability needs.

---

#### Advantages of Lazy Loading

1. **Lazy Initialization**
   - The `Pipeline` object is created only when it is actually needed. If the pipeline is never used, the resources required to construct it are saved.

2. **Improved Performance**
   - In scenarios where initialization is expensive or dependent on external resources, this approach avoids unnecessary overhead during the early stages of execution.

3. **Clean Separation of Concerns**
   - The construction of the pipeline logic is encapsulated in the callback function. This makes it easier to manage or modify the pipeline logic without affecting other parts of the code.

4. **Reusability**
   - The same initialized pipeline can be reused multiple times without re-creating it, except explicitly called `$pipe->newLazyInstance(...)` to create new childe object of lazy loaded class.

5. **Flexibility**
   - You can replace the pipeline construction logic dynamically at runtime by swapping out the callback function if needed.

---

#### Considerations

1. **Overhead of LazyObject**
   - If the cost of wrapping the `Pipeline` object in a `LazyObject` is higher than the cost of creating the pipeline itself, this might introduce unnecessary complexity.

3. **Readability**
   - While lazy initialization improves performance, it can make the code slightly harder to follow. Developers must be aware that `$pipe` is not the actual `Pipeline` object but a proxy that initializes it lazily.

4. **State Persistence**
   - If `Pipeline` is modified after initialization (e.g., adding more `pipe` calls), you need to ensure that `LazyObject` preserves state correctly or re-initializes the pipeline as expected.

---

## Class Definition

* Class Namespace: `Luminova\Utility\Pipeline`
* This class implements:
[Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)
* This class is marked as **final** and can't be subclassed

---

## Constants

| Constant   | Type   | Value                      | Description                                                                                   |
|:-----------|:-------|:---------------------------|:----------------------------------------------------------------------------------------------|
| `STOP`     | string | `'__PIPELINE_STOP__'`       | This constant is used to indicate that the pipeline execution should stop immediately.        |
| `LAST_STATE` | string | `'__PIPELINE_LAST_STATE__'` | This constant signals that the pipeline should continue execution using the last valid result. |

---

## Methods

### constructor

Initializes the pipeline with an optional initial value.

If `$async` is `true` and the handler is a callable, the handler will execute in a background thread (if supported).
It is invoked in a Fiber (PHP 8.1+). If Fiber is available, otherwise, it will throw an exception.

```php
public __construct(mixed $initializer = null, bool $async = false): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$initializer` | **mixed** | The initial value to be processed through the pipeline. |
| `$async` | **bool** | Whether to execute the handler asynchronously in a background thread (default: false). |

***

### chain

Creates a new instance of the Pipeline class with the provided initial value.

This method is used to start a new pipeline chain with an initial value. The initial value can be any type of data, including objects, arrays, or primitive values.

```php
public static chain(mixed $initializer = null, bool $async = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$initializer` | **mixed** | The initial value to begin the pipeline with. |
| `$async` | **bool** | Whether to execute the handler asynchronously in a background thread (default: false). |

**Return Value:**

`self` - Return a new instance of the Pipeline.

***

### pipe

Adds a value or callable to the pipeline.

If the provided value is callable, it will be invoked with the current value, and its result will be stored. Otherwise, the value itself replaces the current value.

```php
public pipe(mixed $handler): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **callable&#124;mixed** | A callable or a static value to process.<br />Callable signature should be: `function(mixed $result): mixed`. |

**Return Value:**

`self` - Returns the current Pipeline instance.

**Throws:** 

- [RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If a callable throws an exception during execution.
For asynchronous operations, if `Fiber` is not available.

> **Note:** The pipe method can be chained multiple times for different transformations.
> Also if asynchronous operations is enabled, it will utilize async operation if handler is a callable.

***

### catch

Adds an error handler to handle exceptions during pipeline execution.

- If an exception occurs, the error handler determines the pipeline's behavior:
  - Returning `Pipeline::STOP` stops execution, and `getResult` will return `null`.
  - Returning `Pipeline::LAST_STATE` stops execution, and `getResult` will return the last valid result.
  - Returning any other value allows the pipeline to continue execution with that value as the new result.
- If no error handler is defined, the exception is rethrown.

```php
public catch(callable $onError): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onError` | **callable** | A callable to handle the error.<br />Signature: `function(Throwable $e, mixed $result): mixed`. |

**Return Value:**

`self` - Returns the current Pipeline instance for chaining.

> **Note:** The error handler must always return a value: `Pipeline::STOP`, `Pipeline::LAST_STATE`, or any other value to continue execution.
>> The `catch` method can only be invoked once and does not support multiple calls or exception type-hinting.
>>
>> The `catch` method should be called before invoking the `pipe` method, for it to be ready to handle exceptions.

***

### getResult

Retrieves the final result after processing through the pipeline.

```php
public getResult(): mixed
```

**Return Value:**

`mixed` - Return the final processed result or `null` if the pipeline execution was stopped.