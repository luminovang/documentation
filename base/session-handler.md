# Abstract Base Class for Backend Session Handlers

***

## Overview

Luminova built-in development server, leveraging PHP's development server functionality. This is useful for development and testing your application in a controlled environment.

***

## Introduction

The **BaseSessionHandler** class serves as an abstract foundation for implementing custom session handlers for backend sessions. It provides the structure for managing session data, including options for encryption, IP binding, and session ID handling. By extending this class, developers can create their own session management class that integrate seamlessly with the Luminova framework [Session Management Class](/sessions/session.md).

**See Also:**

* [Database Session handler](/sessions/database-handler.md) - Session handler for managing session in database.
* [Filesystem Session Handler](/sessions/file-handler.md) - Session handler for managing session in filesystem.

---

### Implementing a Custom Session Handler

To create a custom session handler, extend the `BaseSessionHandler` class and implement the necessary abstract methods, including those from `SessionHandlerInterface` and `SessionIdInterface`. This allows you to define how session data is stored, retrieved, validated, and destroyed.

```php
// /app/Utils/CustomHandler.php
namespace App\Utils;

use Luminova\Base\SessionHandler as BaseSessionHandler;

class CustomHandler extends BaseSessionHandler
{
    public function __construct(array $options = [])
    {
        parent::__construct($options);
        // Your initialization logic here
    }

    // Implement Abstract methods for SessionHandlerInterface and SessionIdInterface
}
```

---

### Example Usage

Below is an example showing how to use the `CustomHandler` class in conjunction with the `Session` class to manage session data.

```php
use Luminova\Sessions\Session;
use App\Utils\CustomHandler;

$session = new Session();
$session->setHandler(new CustomHandler([
    'encryption'    => true,  // Enable session data encryption
    'session_ip'    => true,  // Bind session to client IP for security
]));
$session->start();

// Set and retrieve session data
$session->set('foo', 'Hello World');
echo $session->get('foo'); // Outputs: Hello World
```

This setup demonstrates the flexibility of the abstract session handler while ensuring that session management can be easily customized to meet specific application requirements.

---

## Class Definition

* Class namespace: `Luminova\Base\SessionHandler as BaseSessionHandler`
* This class implements:[\SessionHandlerInterface](https://www.php.net/manual/en/class.sessionhandlerinterface.php), [\SessionIdInterface](https://www.php.net/manual/en/class.sessionidinterface.php)
* This class is an **Abstract class**

---

## Properties

### config

Session config.

```php
protected ?App\Config\Session $config = null
```

***

### fileHash

Hold the last session data hashed-value.
This can be used to compare the session date and determine if should update the data or ignore if no changes have been made.

```php
protected string $fileHash = '';
```

***

### pattern

Session ID validation pattern (e.g, `[0-9a-z]{20}`).

```php
protected string $pattern = '';
```

***

### options

Configuration options for session handling.

```php
protected array<string,mixed> $options = [
    'encryption'        => false,
    'session_ip'        => false,
    'debugging'         => false,
    'cacheable'         => false,  // for database handler only
    'sid_entropy_bits'  => 160,
    'dir_permission'    => 0777,  // for file handler only
    'columnPrefix'      => null,  // for database handler only
    'onValidate'        => null,
    'onCreate'          => null,
    'onClose'           => null
]
```

***

## Methods

### constructor

Constructor to initialize the session handler.

```php
public __construct(array<string,mixed> $options = []): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<string,mixed>** | Configuration options for session handling. |

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - if an error occurred.

***

### setConfig

Set session configuration object.

```php
public setConfig(App\Config\Session<Luminova\BaseLuminova\Base\Configuration> $config): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **\App\Config\Session** | The session configuration object. |

***

### open

Opens the session storage mechanism.

```php
abstract public open(string $path, string $name): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The save path for session files (unused in this implementation). |
| `$name` | **string** | The session name. |

**Return Value:**

`bool` - Return bool value from callback `onCreate`, otherwise always returns true for successful initialization.

***

### close

Closes the session storage mechanism.

```php
abstract public close(): bool
```

**Return Value:**

`bool` - Return bool value from callback `onClose`, otherwise always returns true for successful cleanup.

***

### create_sid

Creates a new session unique identifier.

```php
abstract public create_sid(): string
```

**Return Value:**

`string` - Return new generated unique session ID.

***

### validate_sid

Validates a session ID.

```php
abstract public validate_sid(string $id): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **string** | The session ID to validate. |

**Return Value:**

`bool` - Return bool value from `onValidate` callback, otherwise returns true if id is valid and exists else false.

***

### destroy

Deletes a session by ID.

```php
abstract public destroy(string $id): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **string** | The session ID. |

**Return Value:**

`bool` - Return true on success, false on failure.

***

### gc

Performs garbage collection for expired sessions.

```php
abstract public gc(int $max_lifetime): int|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$max_lifetime` | **int** | The maximum session lifetime in seconds. |

**Return Value:**

`int|false` - Return the number of deleted sessions, or false on failure.

***

### read

Reads session data by ID.

```php
abstract public read(string $id): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **string** | The session ID. |

**Return Value:**

`string` - Return the session data or an empty string if not found or invalid.

***

### write

Writes session data.

```php
abstract public write(string $id, string $data): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **string** | The session ID. |
| `$data` | **string** | The session data. |

**Return Value:**

`bool` - Return true on success, false on failure.

***

### update_timestamp

Updates the session timestamp.

```php
abstract public update_timestamp(string $id, string $data): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **string** | The session ID. |
| `$data` | **string** | The session data. |

**Return Value:**

`bool` - Return true on success, false on failure.

***

### initSessionIDPattern

Initializes the session ID pattern and updates session length settings if required.

This method determines the appropriate session ID length based on the specified number of bits and the `session.sid_bits_per_character` setting. If the current `session.sid_length` is insufficient to meet the required entropy, the method updates it dynamically. It also generates a regex pattern that reflects the character set and length of the session ID for validation purposes.

```php
protected static initSessionIDPattern(int $bits): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$bits` | **int** | The total number of bits to be used for the session ID (e.g., `160`, `130`). |

**Return Value:**

`string` - Returns the generated regex pattern for the session ID. The pattern ensures compatibility with the character set and length.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an unsupported value for `session.sid_bits_per_character` is encountered.

***

### base32Encode

Encodes a string into `Base32`, typically used for session IDs. The encoding process adheres to the value of session.`sid_bits_per_character` when set to `5`.

```php
protected static base32Encode(string $input): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | The input string to be encoded.<br />It is expected to be ASCII or binary data. |

**Return Value:**

`string` - Return the Base32 encoded output string. The result will only contain characters from the a-v Base32 alphabet.

***

### destroySessionCookie

Destroy the session cookie. This method ensures that the cookie session `name` is destroyed.

```php
protected destroySessionCookie(): bool
```

**Return Value:**

`bool` - Return true on success, false on failure.

***

### log

Log error messages for debugging purposes.

```php
protected log(string $level, string $message): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level to use. |
| `$message` | **string** | The log error message. |

***

## Configuration Options

This section outlines the available options for configuring a session database handler. These options provide flexibility in managing session data storage and behavior.

### encryption

Enables or disables encryption for session data. When set to `true`, the session data is encrypted before storage and decrypted upon retrieval. The encryption utilizes the default application encryption configuration based on `App\Config\Encryption`.

**Type:** `(bool)`  
**Default:** `false`  
**Example:**  
```php
'encryption' => true
```

---

### debugging

Determines whether to log error messages during session operations, useful for troubleshooting. 

**Type:** `(bool)`  
**Default:** `false`  
**Example:**  
```php
'debugging' => true
```

---

### sid_entropy_bits

Specifies the number of entropy bits used when generating the session ID, impacting the randomness and security of session IDs.  

**Type:** `(int)`  
**Default:** `160`  
**Example:**  
```php
'sid_entropy_bits' => 130
```

---

### dir_permission

Defines the permissions for the session storage directory, applicable only when using the filesystem handler.  

**Type:** `(int)`  
**Default:** `0777`  
**Example:**  
```php
'dir_permission' => 0700
```

---

### session_ip

When enabled, this option ties the session to the IP address of the client, enhancing security by preventing session hijacking from different IPs.  

**Type:** `(bool)` 
**Default:** `false`  
**Example:**  
```php
'session_ip' => true
```

---

### columnPrefix

Specifies a prefix to be added to all session-related database columns. This can help avoid column name collisions in shared database tables.  

**Type:** `(string|null)`  
**Default:** `null`  
**Example:**  
```php
'columnPrefix' => 'user_session_'
```

---

### cacheable

Determines whether session data should be cached in memory for faster access. When `true`, caching mechanisms (if available) are utilized. This is applicable only when using the filesystem handler. 

**Type:** `(bool)`   
**Default:** `false`  
**Example:**  
```php
'cacheable' => true
```

---

### onValidate
 
A custom callback function executed during session ID validation. This can be used to perform additional checks, such as validating user-agent strings or custom tokens.  

**Type:** `(callable|null)`
**Default:** `null`  
**Example:**  
```php
'onValidate' => function (string $id, bool $exists): bool {
    // Custom validation logic
    return $exists && doExtraCheck($id);
}
```

---

### onCreate

A custom callback function triggered when a new session is created. This is useful for initializing session-specific resources or logging creation events.  

**Type:** `(callable|null)`  
**Default:** `null`  
**Example:**  
```php
'onCreate' => function (string $path, string $name): bool {
    // Logic for new session creation
    return true;
}
```

---

### onClose
 
A custom callback function triggered when a session is closed or destroyed. This can be used to clean up resources or log session closure events.  

**Type:** `(callable|null)` 
**Default:** `null`  
**Example:**  
```php
'onClose' => function (): bool {
    // Logic for session closure
    return true;
}
```