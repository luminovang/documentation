# Lazy Load Object Trait Initialization

***

## Overview

The lazy object provides a proxy for lazily instantiating an object. The actual object is not initialized until one of its methods or properties is accessed.

***

## Introduction

```php
// /app/Utils/Example.php

namespace App\Utils;

use Luminova\Utility\Object\LazyObjectTrait;

class Example 
{
    use LazyObjectTrait;

    public string $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }

    public function getName(): string 
    {
        return $this->name;
    }
}
```

---

## Class Definition

* Trait namespace: `\Luminova\Utility\Object\LazyObjectTrait`

---

## Methods

### newLazyGhost

Create a **lazy ghost** instance of this class.

A ghost is an object created without running its constructor.
This is useful if you need to fill private or protected properties manually
before the object is used.

The initializer receives the raw ghost (`$this`) and optional arguments.
It can:
- Mutate $this and return nothing, OR
- Return a fully constructed instance.

```php
final public static newLazyGhost(callable $initializer, ?callable $arguments = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$initializer` | **callable** | The class initializer.<br/>Function Signature `callable(static $ghost, mixed ...$args): static|null|void` |
| `$arguments` | **callable&#124;null** | Optional callback providing init args.<br/>Function Signature `callable(): array`. |

**Return Value:**

`static` - Return unconstructed ghost instance.

**Throws:**
- [Luminova\Exceptions\ExceptionsRuntimeException](/error-handlers/exceptions.md#exceptionsruntimeexception) - if error while invoking class method.

**Examples:**

```php
$ghost = Example::newLazyGhost(function (Example $obj, string $name) {
    return new Example('Alice');
});

echo $ghost->getName(); // Alice
```

```php
$ghost = Example::newLazyGhost(function (Example $obj, string $name) {
    $obj->name = $name; // set private data
});

$ghost->hydrateLazyGhost(['Alice']); // manually hydrate before use
$ghost->getName(); // Alice
```

***

### hydrateLazyGhost

Manually trigger hydration of a lazy ghost object.

If you plan to call a public method immediately, call this first to ensure
the ghost has been initialized. If the initializer returns an object, it is stored;
otherwise we assume $this was mutated in place.

```php
final public hydrateLazyGhost(?array $arguments = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$arguments` | **array&#124;null** | Optional arguments to override lazy initialization arguments. |

**Throws:**
- [Luminova\Exceptions\ExceptionsRuntimeException](/error-handlers/exceptions.md#exceptionsruntimeexception) - if error while initializing class.

**Example:**
```php
$ghost = Example::newLazyGhost(fn(Example $obj): void => $obj->age = 42);

$ghost->hydrateLazyGhost(); // runs initializer

echo $ghost->getAge(); // 42
```

***

### newLazyProxy

Create a **lazy proxy** instance of this class.

A proxy defers real object creation until the first time you access it.
Unlike ghosts, proxies require the initializer to return a *fully constructed object*.
This wrapper only exposes the public API of your class.

```php
final public static newLazyProxy(
    callable $initializer, 
    ?callable $arguments = null
): \Luminova\Interface\LazyObjectInterface<static>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$initializer` | **callable** | Must return an instance.<br/>Function Signature `callable(static $proxy, mixed ...$args): static` |
| `$arguments` | **callable&#124;null** | Optional callback providing initialization arguments.<br/>Function Signature `callable(): array` |

**Return Value:**

`\Luminova\Interface\LazyObjectInterface&lt;static&gt;` - Proxy object that initializes on first use

**Throws:**
- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - if error while invoking class method.

**Example:**

```php
$proxy = Example::newLazyProxy(fn(): Example => new Example('data'));

echo $proxy->getData(); // object is created on first call
```

> **Note:** 
> The initializer must return instance of class.