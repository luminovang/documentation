# Events Emitter

***

## Overview

Whether you need to listen for user actions, manage system events, or trigger custom workflows, The Event class offers a simple yet powerful solution to orchestrate event handling across your project.

***

## Introduction

The Luminova `Event` class provides a static event triggering method, allowing you to attach callbacks to events and trigger them throughout your application. This utility supports adding, removing, and triggering event hooks, making it an essential component for managing event-driven behavior.

### Class Overview

The `Event` class is designed to handle events via static methods, meaning all event callbacks are stored in a static container shared across the application. You can use this class to:
- Bind callbacks to specific events.
- Trigger events and execute their associated callbacks.
- Remove specific callbacks or entire events.
- Retrieve all event callbacks.
- Clear all events.

***

### Usage Example

```php
<?php
use Luminova\Utils\Event;

// Add a callback to an event
Event::on('user.register', 'sendWelcomeEmail', function($user) {
    // Send welcome email to the user
    echo "Welcome email sent to {$user->email}";
});

// Trigger the event
Event::emit('user.register', [$user]);

// Remove a specific callback
Event::off('user.register', 'sendWelcomeEmail');
```

This example demonstrates how to bind, trigger, and remove callbacks from events using the `Event` class.

***

### Advanced Usage

#### Event Binding Example

The `Events` class extends the base `Event` system, allowing you to define and bind event listeners in your application.

```php
// /app/Utils/Events.php
<?php
namespace App\Utils;

use Luminova\Utils\Event;

class Events extends Event
{   
    public function __construct()
    {
        parent::__construct();
        $this->onEvent();
    }

    /**
     * Bind event listeners for various events in your application.
     * 
     * Define callbacks for specific events, such as sending a welcome email
     * upon user registration or compressing files after an upload.
     */
    private function onEvent(): void
    {
        parent::on('user.register', 'sendWelcomeEmail', static function(object $user) {
            echo "Welcome email sent to {$user->email}";
        });

        parent::on('file.upload', 'compressFile', static function (string $file) {
            echo "Compressing file: {$file}\n";
        });
    }
}
```

**Usage Example**

You can trigger events anywhere in your application by calling the event name and passing the necessary arguments. For instance, to trigger the `user.register` event:

```php
use App\Utils\Events;

Events::emit('user.register', [
    (object)['email' => 'peter@example.com']
]);
```

#### Key Benefits:
- **Centralized Event Management**: By binding events in one place, you ensure maintainability and clear event management.
- **Flexible Triggers**: Emit events from anywhere in your code, making your application more modular and decoupled.

***

* Class namespace: `\Luminova\Utils\Event`

***

## Methods

### constructor

Initialize a new event class.

```php
public __construct(): mixed
```

***

### getInstance

Retrieves the singleton instance of the `Event` class.

```php
public static getInstance(): static
```

**Return Value:**

`static` - Return a new static event object.

***

### emit

Triggers an event, executing all the callbacks associated with it. You can optionally target a specific hook within the event.

```php
public static emit(string $name, array $arguments = [], ?string $hook = null): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The event name to trigger. |
| `$arguments` | **array** | Arguments to pass to each callback. |
| `$hook` | **string&#124;null** | Optional hook name to trigger specific hooks. |

**Return Value:**

`int` - Return the number of callbacks triggered.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if an invalid event name was passed.

***

### on

Binds a callback function to a specific event and hook. This method allows you to define a unique callback that will be invoked when the event is triggered.

```php
public static on(string $name, string $hook, callable $callback): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the event to bind the callback to. |
| `$hook` | **string** | A unique identifier for the callback. |
| `$callback` | **callable** | The function to invoke when the event is triggered. |

**Throws:**

- [InvalidArgumentException](/Exceptions/InvalidArgumentException.md) - Throws if an invalid event name, hoot or callback was passed.

***

### off

Removes a specific callback from an event or clears all callbacks if no hook is specified.

```php
public static off(string $name, ?string $hook = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The event name to unbind. |
| `$hook` | **string&#124;null** | Optional. The unique identifier of the callback to remove. |

**Return Value:**

`bool` - Return true if successful, false if the event or callback does not exist.

**Throws:**

- [InvalidArgumentException](/Exceptions/InvalidArgumentException.md) - Throws if an invalid event name was passed.

***

### events

Retrieve all event callbacks, or callbacks for a specific event.

```php
public static events(?string $name = null): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string&#124;null** | Optional. The event name to filter by. |

**Return Value:**

`array` - Return the list of event callbacks.

***

### detach

Clears all events and their associated callbacks.

```php
public static detach(): void
```
