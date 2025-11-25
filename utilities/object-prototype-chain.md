# PHP Object Inheritance and Prototype Chain

***

## Overview

Enables JavaScript-like prototypal behavior in PHP. The Prototypeable trait allows you to dynamically add, modify or remove methods and properties at runtime, offering a clean type-safe implementation

***

## Introduction

The **Prototypeable** trait provides a lightweight *prototype-based inheritance* mechanism in PHP, inspired by [JavaScript’s Prototype Model](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain).
It allows objects to dynamically define or modify properties and methods at runtime, enabling flexible object extension without subclassing.

This trait is useful when you need objects that can evolve their behavior during execution — for example, adding temporary helpers, testing dynamic features, or extending class capabilities in runtime environments.

---

## Features

* Add or override properties dynamically.
* Attach new methods to existing objects.
* Access prototype-defined members like normal class properties or methods.
* Maintain a registry of dynamic properties and methods.

---

## How It Works Internally

The `Prototypeable` trait mimics JavaScript’s *prototypal inheritance* model in PHP, letting you dynamically attach methods and properties to an object instance at runtime.
Internally, it uses a private registry array named `$_prototypes_` that keeps track of two categories:

* **`methods`** — for dynamically bound closures (runtime methods)
* **`properties`** — for dynamically assigned values

Each instance of a class using `Prototypeable` has its own isolated prototype registry:

```php
private array $_prototypes_ = [
    self::PROTO_PROPERTY => [],
    self::PROTO_METHOD   => []
];
```

This ensures prototype extensions are per-instance — not shared between objects (unlike JavaScript prototypes, which are shared by default).

---

### Implementing Prototypes

To enable prototypal behavior, your class should implement the `PrototypeableInterface` and use the `Prototypeable` trait.
This combination provides the runtime property and method binding functionality.

```php
// /app/Utils/Example.php
namespace App\Utils;

use Luminova\Interface\PrototypeableInterface;
use Luminova\Utility\Object\Prototypeable;

class Example implements PrototypeableInterface
{
    use Prototypeable;
}
```

#### Usage Example

```php
use App\Utils\Example;

$example = new Example();

// Add a dynamic property
$example->prototype('nickname', 'Johnny');

// Add a dynamic method
$example->prototype('sayHello', function (): string {
    return "Hello from {$this->nickname}";
});

// Access them like native members
echo $example->nickname;   // Outputs: Johnny
echo $example->sayHello(); // Outputs: Hello from Johnny
```

***

## Class Definition

* Trait namespace: `\Luminova\Utility\Object\Prototypeable`

### When to Use

Use `Prototypeable` when:

* You need to extend object functionality dynamically.
* You’re experimenting with behaviors without altering base classes.
* You want to simulate JavaScript-like prototyping within PHP.

Avoid it for performance-critical code paths or strict type systems, as dynamic bindings can introduce runtime overhead.

---

## Constants

| Constant | Type | Value | Description |
|:---------|:-----|:------|:------------|
| `PROTO_PROPERTY` | string |**properties** | Used internally to group all user-defined properties within the prototype system. |
| `PROTO_METHOD` | string |**methods** | Used internally to group all user-defined methods within the prototype system. |

---

## Methods

### extend

Add or override a dynamic method for the current instance.

This method allows you to dynamically attach a callable (closure or method) to the object at runtime. The callable will be bound to the current instance context, giving it access to private and protected members.

```php
public extend(string $prototype, callable $fn): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prototype` | **string** | The prototype method name to register or override. |
| `$fn` | **callable** | The function or closure to bind to this instance. |

**Return Value:**

`static` - Returns the current instance for method chaining.

**Example:**

```php
use Luminova\Utility\String\Str;

$str = new Str('Hello');

// Add a new method dynamically
$str->extend('greet', fn($name) => "{$this->valueOf} from {$name}!");

echo $str->greet('Peter'); // Output: Hello from Peter!
echo $str; // Hello
```

***

### prototype

Add a dynamic method or property to the current instance.

If `$valueOf` is a callable, it is registered as a method using `extend()`.
Otherwise, it is stored as a dynamic property.

```php
public prototype(string $prototype, mixed $valueOf): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prototype` | **string** | The prototype method or property name to register. |
| `$valueOf` | **mixed** | A callable (method) or any value (property). |

**Return Value:**

`static` - Returns the current instance for method chaining.

**Examples:**

```php
use Luminova\Utility\String\Str;

$str = new Str('Apple,Banana,Mango,Orange');

// Add a new property
$str->prototype('category', 'Fruits');
echo $str->category; // Output: Fruits

// Prototype a new array method
$str->prototype('toArray', fn() => explode(',', $this->valueOf));
$str->toArray(); // Output: Array('Apple', 'Banana', 'Mango', 'Orange')
```

**See Also:**

* `extend()` - To explicitly add methods only.

***

### unprototype

Remove a dynamically registered prototype method or property.

If no `$type` is specified, the method attempts to remove the entry
from both prototype collections (methods and properties). If `$type` is provided, it must be either `\T::PROTO_METHOD` or `\T::PROTO_PROPERTY`.

```php
public unprototype(string $prototype, ?string $type = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prototype` | **string** | The name of the prototype method or property to remove. |
| `$type` | **string&#124;null** | Optional. The prototype type to target (`\T::PROTO_METHOD` or `\T::PROTO_PROPERTY`). |

**Return Value:**

`bool` - Returns true if the entry was removed or attempted successfully,false if `$type` is invalid.

**Example:**

```php
$str = new Str('Luminova');

$str->prototype('greet', fn() => "Hello {$this->value}");

$str->hasPrototype('greet'); // true
$str->hasPrototype('greet', Str::PROTO_METHOD); // true
$str->hasPrototype('greet', Str::PROTO_PROPERTY); // false
```

***

### getPrototypes

Retrieve all dynamically added methods and properties, or a specific type.

If no type is specified, the entire prototype registry is returned.
If a type is specified (`T::PROTO_METHOD` or `T::PROTO_PROPERTY`), only that subset will be returned.

```php
public getPrototypes(?string $of = null): array|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$of` | **string&#124;null** | Optional. Specify `\T::PROTO_METHOD` or `\T::PROTO_PROPERTY` to fetch a specific set. |

**Return Value:**

`array|null` - Returns the full registry if `$of` is null, the specific subset if `$o`f is valid, or null if the type does not exist.

***

### getPrototype

Get a single dynamically added method or property.

If the prototype type is not provided, it defaults to methods.
Use `\T::PROTO_METHOD` or `\T::PROTO_PROPERTY` to specify what to retrieve.

```php
public getPrototype(string $prototype, string $of = self::PROTO_METHOD): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prototype` | **string** | The prototype property or method name to retrieve. |
| `$of` | **string** | The type of prototype to fetch (default: `T::PROTO_METHOD`). |

**Return Value:**

`mixed` - Return the stored callable or value, or null if not found

***

### hasPrototype

Check if a dynamic prototype method or property exists.

When `$type` is specified, the check is limited to that prototype category —
either `\T::PROTO_METHOD` or `\T::PROTO_PROPERTY`. If no type is given, both methods and properties are checked.

```php
public hasPrototype(string $prototype, ?string $type = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prototype` | **string** | The name of the dynamic method or property. |
| `$type` | **string&#124;null** | Optional. The prototype type to check<br />(`\T::PROTO_METHOD` or `\T::PROTO_PROPERTY`). |

**Return Value:**

`bool` - Returns true if the prototype entry exists, false otherwise.

---

## Internal Dynamic Accessors

The magic methods (`__get`, `__set`, `__call`, etc.) are implemented to make prototype items behave like real class members:

* `__get()` - Retrieves properties from the prototype registry first.
* `__call()` - Executes registered prototype methods.
* `__isset()` and `__unset()`- Support native PHP operations on dynamic entries.

So this works transparently:

```php
$object->nickname = 'Jay'; // __set()
echo $object->sayHello();  // __call()
```

***

### __set

Magic setter for dynamic properties.

```php
public __set(string $prototype, mixed $value): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prototype` | **string** | The property name to set value to. |
| `$value` | **mixed** | The prototype property value to set. |

***

### __get

Magic getter for dynamic properties.

```php
public __get(string $prototype): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prototype` | **string** | The prototype property name to get. |

**Return Value:**

`mixed` - Returns the property value or null if not set

***

### __isset

Magic isset to check dynamic properties.

```php
public __isset(string $prototype): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prototype` | **string** | The prototype property name to check. |

**Return Value:**

`bool` - Return true if property exists, otherwise false.

***

### __unset

Magic unset to remove dynamic properties.

```php
public __unset(string $prototype): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prototype` | **string** | The prototype property name to unset. |

***

### __call

Magic call to invoke dynamically added methods.

```php
public __call(string $prototype, array<int,mixed> $args): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prototype` | **string** |  The prototype method name to call. |
| `$args` | **array<int,mixed>** | An optional arguments to pass to prototype method. |

**Return Value:**

`mixed` - Returns the prototype method result.

**Throws:**
- [Luminova\Exceptions\BadMethodCallException](/error-handlers/exceptions.md#badmethodcallexception) - If the prototype method does not exist.
