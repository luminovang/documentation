# Lazy Load Object Initialization

***

## Overview

The lazy object provides a proxy for lazily instantiating an object. The actual object is not initialized until one of its methods or properties is accessed.

***

## Introduction

The Luminova **LazyObject** class provides a straight-forward mechanism for implementing lazy initialization in your applications. It allows you to defer the instantiation of objects until they are accessed for the first time. This approach helps optimize resource usage and improve application performance by avoiding unnecessary object creation, especially for objects that may not always be used during execution.

The `LazyObject` class supports both class-based and closure-based initialization, making it highly flexible for a variety of use cases. It seamlessly integrates with native lazy-loading system and advanced PHP's `ReflectionClass::newLazyGhost` method (introduced in **PHP 8.4**), ensuring efficient and modern lazy-loading capabilities. 

***

### Key Features:

- **Lazy Initialization**: Objects are instantiated only when accessed, reducing memory and processing overhead.
- **Class and Closure Support**: Accepts a class name or a closure for creating objects dynamically.
- **PHP Compatibility**: Leverages modern PHP features, while maintaining fallback support for older versions.
- **Type Safety**: Ensures compatibility with object type casting through the use of marker interface `LazyInterface`.
- **Customizable Behavior**: Allows passing arguments to constructors or custom initialization logic using closures.
- **PHP Magic Methods**: Supports for PHP magic methods such as `__get`, `__set`, `__call`, `__clone`, `__isset`, `__unset`, `__toString`, `__serialize`, `__unserialize`, `__debugInfo` and `__destruct`.

***

### Use Cases:

- **On-Demand Resource Loading**: Ideal for scenarios where heavy objects, such as database connections or large datasets, are only needed conditionally.
- **Improving Performance**: Helps streamline application startup by deferring unnecessary computations or object creation.
- **Decoupled Architecture**: Enables dynamic and flexible design patterns where dependencies are resolved and loaded only when required.

The `LazyObject` class is designed to align with Luminova's philosophy of providing powerful, efficient, and developer-friendly utilities, ensuring that your applications remain scalable and performant.

***

### Usage Examples

**Creating and Using a Lazy Object**  

Below is an example of a `Person` class:  

```php
class Person 
{
   public function __construct(
      public string $name, 
      public int $age,
      public bool $underAge = false
   ) {}

   public function getDetails(): string
   {
      return "{$this->name}, Age: {$this->age}";
   }
}
```  

**Creating a Lazy Object (Dynamic Handling for PHP Versions)**  

The `LazyObject` will use lazy ghosting if the PHP version is `8.4.0` or later; otherwise, it defaults to the standard lazy-loading mechanism.  

```php
use Luminova\Utils\LazyObject;

$person = new LazyObject(Person::class, fn(): array => ['Peter', 33]);

echo $person->getDetails(); // Outputs: Peter, Age: 33
```  

---

**Early Object Instantiation for PHP 8.4.0+**  

This method creates a lazy object that performs an early return of the object instance.  

```php
use Luminova\Utils\LazyObject;

$person = LazyObject::newObject(Person::class, fn(): array => ['Peter', 33]);

echo $person->getDetails(); // Outputs: Peter, Age: 33
```  

---

**Using a Closure for Custom Initialization**  

Creating a new instance with arguments from a callable initializer argument:

```php 
use Luminova\Utils\LazyObject;
use Person;

$person = LazyObject::newObject(
   function(mixed ...$arguments): Person {
      // Custom logic to manipulate arguments
      $arguments[2] = ($arguments[1] < 18);

      return new Person(...$arguments);
   }, 
   function(): array {
      return ['Peter', 33]; // Arguments: Name, Age
   }
);

echo $person->age; // Outputs: 33
echo $person->name; // Outputs: Peter
echo $person->underAge ? 'yes' : 'no'; // Outputs: no
```

---

You can use a closure to define custom logic for initializing the object lazily. 

```php
use Luminova\Utils\LazyObject;
use Person;

$person = LazyObject::newObject(function (): Person 
{
   // Custom logic to fetch initialization data
   [$name, $age] = loadPersonInformation();

   return new Person($name, $age);
});

echo $person->getDetails(); // Outputs: Peter, Age: 33
```  

---

**Handle Initialization Arguments**  

Optionally manipulate initialization arguments in the closure.  

```php
use Luminova\Utils\LazyObject;
use Person;

$person = LazyObject::newObject(function (int $age): Person 
{
   $isUnderAge = ($age < 18);
   return new Person($age, $isUnderAge);
}, fn(): array => [random_int(15, 65)]);

echo $person->isUnderAge() ? 'Yes' : 'No';
```  

---

**Creating a Lazy Ghost Object**  

For PHP `8.4.0` and above, you can create a lazy ghost object that defers instantiation until the object is accessed.  

```php
use Luminova\Utils\LazyObject;

$person = LazyObject::newLazyGhost(Person::class, fn() => ['Peter', 33]);

echo $person->getDetails(); // Outputs: Peter, Age: 33
``` 

***

## Class Definition

* Class namespace: `\Luminova\Utils\LazyObject`
* This class implements:
[\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface), [\Stringable](https://www.php.net/manual/en/class.stringable.php)

***

## Methods

### constructor

Initializes a new instance of `LazyObject`.

The constructor accepts either a class string (e.g., `Example::class`) or a closure responsible for creating and initializing the object. Lazy initialization postpones object creation until it is first accessed, optimizing resource usage.

For PHP versions `8.4.0` and later, this method utilizes `ReflectionClass::newLazyGhost`, which leverages PHP's magic methods to access properties or methods dynamically without early object instantiation. For earlier versions, a fallback approach ensures compatibility.

```php
public __construct(\Closure|class-string<\T> $initializer, ?callable $arguments = null): mixed
```

**Parameters:**

| Parameter      | Type                    | Description                                                                 |
|----------------|-------------------------|-----------------------------------------------------------------------------|
| `$initializer` | **\Closure&#124;class-string<\T>** | A class string or closure that creates the lazily initialized object.       |
| `$arguments` | **callable\|null**              | Optional arguments to pass to the class constructor or closure initializer argument.<br/>Must be a callable that returns a list array of arguments to pass to the constructor.                   |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the class does not exist or an error occurs during initialization.

**Example:**

The object `Person` is instantiated only when its properties or methods are accessed:

```php
$person = new LazyObject(fn() => new Person(33, 'Peter'));

echo $person->getName(); // Outputs: Peter
```

***

### newObject

Creates a lazily initialized instance of a class using a static method.

This method enables early object creation when required and utilizes `ReflectionClass::newLazyGhost` for PHP `8.4.0` or later. Unlike the constructor, this method ensures no further delegation of property or method access by the `LazyObject` class. For earlier PHP versions, it falls back to a custom lazy initialization mechanism.

```php
public static newObject(\Closure|class-string<\T> $initializer, ?callable $arguments = null): object
```

**Parameters:**

| Parameter      | Type                    | Description                                                                 |
|----------------|-------------------------|-----------------------------------------------------------------------------|
| `$initializer` | **\Closure&#124;class-string<\T>** | A class string or closure that creates the lazily initialized object.       |
| `$arguments` | **callable\|null**              | Optional arguments to pass to the class constructor or closure initializer argument.<br/>Must be a callable that returns a list array of arguments to pass to the constructor.                    |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the class does not exist or an error occurs during initialization.

**Example:**

The object `Person` is lazily loaded and its properties can be accessed immediately:

```php
$person = LazyObject::newObject(Person::class);

$person->age = 33;

echo $person->age; // Outputs: 33
```

***

### newLazyGhost

Creates a new lazy ghost object for the specified class.  

This method utilizes PHP 8.4+'s `ReflectionClass::newLazyGhost` to create a lazily loaded instance of the specified class. The actual object initialization is deferred until its properties or methods are accessed, optimizing resource usage.

```php
public static newLazyGhost(class-string<\T> $class, ?callable $arguments = null): object
```

**Parameters:**

| Parameter       | Type                   | Description                                               |
|-----------------|------------------------|-----------------------------------------------------------|
| `$class`        | **class-string<\T>**  | Fully qualified class name for which to create a lazy ghost. |
| `$arguments` | **callable\|null**             | Optional arguments to pass to the class constructor.<br/>Must be a callable that returns a list array of arguments to pass to the constructor.      |

**Return Value:**

`object` - Returns a lazy ghost object of the specified class.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the lazy ghost creation fails.

***

### newLazyInstance

Creates a new instance with arguments return the class object.

This method takes an arguments, and creates a new instance of lazy loaded class.

```php
public newLazyInstance(mixed ...$arguments): object
```

**Parameters:**

| Parameter       | Type                   | Description                                               |
|-----------------|------------------------|-----------------------------------------------------------|
| `$arguments`        | **mixed**  | The arguments to create a new class object with. |

**Return Value:**

`object` - Return a new instance of the lazy loaded class or null.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If error occurs while initializing class.

**Examples:**

Recrating a new instance with arguments:

```php 
$parent = LazyObject::newObject(Person::class, fn() => ['Peter', 33]);
echo $parent->getDetails(); // Outputs: Peter, Age: 33

$child = $parent->newLazyInstance('John Deo', 34);
echo $child->getDetails(); // Outputs: John Deo, Age: 34
```

***

### setLazyInstance

Override the current lazy-loaded instance with a new class object.

```php
public setLazyInstance(object $instance): void
```

**Parameters:**

| Parameter       | Type                   | Description                                               |
|-----------------|------------------------|-----------------------------------------------------------|
| `$instance`        | **object**  | The new class object to replace the existing lazy-loaded instance. |

***

### getLazyInstance

Creates and return the lazy-loaded class object.

```php
public getLazyInstance(): ?object
```

**Return Value:**

`object|null` - Return lazy-loaded instance of the specified class or null.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the class does not exist or error occurs.

***

### isInstanceof

Checks if the lazy-loaded instance is of a specific class type. 

This method verifies whether the lazily instantiated object is an instance of the specified class or implements the specified interface.

```php
public isInstanceof(string $class): bool 
```

**Parameters:**

| Parameter       | Type                   | Description                                               |
|-----------------|------------------------|-----------------------------------------------------------|
| `$class`        | **class-string<\T>**  | The fully qualified class or interface name to check against. |

**Return Value:**

`object` - Returns true if the lazy-loaded instance is of the specified class type, false otherwise.

**Examples:**

Check if the lazy-loaded instance `Person`:

```php
if($object instanceof LazyObject && $object->isInstanceof(Person::class)){
    echo $object->getName();
}
```

***

## Implementing the Lazy Interface

The `LazyInterface` in **Luminova** is a marker interface with no abstract methods. It allows your classes to support lazy initialization while maintaining compatibility with object type casting. 

### Problem Scenario

Imagine you have an existing class defined like this:

```php
// /app/Utils/Example.php

namespace App\Utils;

class Example {
    // Class implemenation
}
```

And your controller initializes this class as shown:

```php
// /app/Controllers/Http/ExampleController.php

namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use App\Utils\Example;

class ExampleController extends BaseController 
{
    protected ?Example $example = null;

    protected function onCreate(): void 
    {
        $this->example = new Example();
    } 
}
```

If you decide to use lazy loading for the `Example` class by assigning a lazy instance like this:

```php
$this->example = LazyObject::newObject(Example::class);
```

You'll encounter an **error**. The property `$example` expects an instance of the `Example` class, but `LazyObject::newObject` returns an object or a class object that implements `LazyInterface`. This type mismatch causes the issue.

***

### Solution

To resolve this, you need to modify your `Example` class and your controller. Follow these steps:

#### 1. Object Type

If you need to accommodate lazy-loading while maintaining compatibility with your class property type, you might consider redefining the property to use the `object` type:

```php
protected Example|object|null $example = null;
```

However, the above approach will result in an error because PHP does not support union types that combine a specific class type (like `Example`) with the generic `object` type. Your only alternative is to use `object` alone, as shown below:

```php
protected ?object $example = null;
```

This approach works as expected but has a notable drawback: it is not **IDE-friendly**. With this definition, IDEs will not provide auto-completion or suggestions for methods and properties defined within the `Example` class. This can hinder development efficiency and code readability. 

For a more better solution, consider using interfaces or `Closure` to support lazy-loading while maintaining type hints.

---

#### 2. Implement the LazyInterface

Update the `Example` class to implement the `LazyInterface`:

```php
// /app/Utils/Example.php

namespace App\Utils;

use Luminova\Interface\LazyInterface;

class Example implements LazyInterface {}
```

---

#### 3. Update the Property Type
In the controller, update the property definition to include a union type:

```php
protected ?LazyInterface $example = null;
```

This allows `$example` to accept both `Example` and objects implementing `LazyInterface`.

---

#### Updated Controller Example

Here's the complete updated controller code:

```php
// /app/Controller/Http/FooController.php

namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Interface\LazyInterface;
use Luminova\Utils\LazyObject;
use App\Utils\Example;

class FooController extends BaseController 
{
    /**
     * @var Example<LazyInterface>|null $example
     */
    protected ?LazyInterface $example = null;

    protected function onCreate(): void 
    {
        $this->example = LazyObject::newObject(Example::class);
    }
}
```

***

### Why Use the Lazy Interface?

Type Compatibility, to ensure that lazy-loaded instances work seamlessly with type hints. By implementing `LazyInterface` and using the correct type casting, you can harness the benefits of lazy loading without compromising type safety.