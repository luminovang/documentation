# Backend Session Handling and Client Login

***

## Overview

The Session class provides a set of methods to simplify session management in your Luminova application. It offers convenient methods for storing, retrieving, and manipulating session data.

***

## Introduction

The Luminova backend `Session` manager is a useful component designed to simplify managing user data and logins of your in PHP session or cookie storage for Luminova web applications. It provides methods to enhance the storage and retrieval of session data, enabling applications to maintain stateful interactions with users across multiple requests. The session manager supports two distinct storage managers:  
- `Luminova\Sessions\Managers\Session`  
- `Luminova\Sessions\Managers\Cookie`  

Additionally, it offers two session handler implementations:  
- `Luminova\Sessions\Handlers\Database`  
- `Luminova\Sessions\Handlers\Filesystem`  

This flexibility allows developers to choose the most suitable storage mechanism and handler based on their application's requirements.

---

### Storage Managers

The storage managers in Luminova `Luminova\Sessions\Managers\Session` and `Luminova\Sessions\Managers\Cookie`, handle the storing and retrieval of session data. They provide features to define a base storage table name through the session configuration class (`App\Config\Session->tableIndex`), where all application session data is stored. These managers also allow customization of the session storage name, which controls where the session instance stores, retrieves, and manages data in application session table `tableIndex`.

#### Session Storage Manager

The `Luminova\Sessions\Managers\Session` manager uses PHP's default `$_SESSION` superglobal to store and retrieve session data, ensuring seamless integration with standard PHP session handling.

#### Cookie Storage Manager

The `Luminova\Sessions\Managers\Cookie` manager uses PHP's `setcookie` and `$_COOKIE` mechanisms to manage session data, offering an alternative for applications that prefer cookie-based storage.

---

### Session Handlers

Luminova provides two session handler implementations [Luminova\Sessions\Handlers\Database](/sessions/database-handler.md) and [Luminova\Sessions\Handlers\Filesystem](/sessions/filesystem-handler.md). These handlers override PHP's default session handling, enabling the storage of session data in a database or filesystem. They also support ip address binding, data encryption and decryption, allowing secure transfer and management of session data. Encrypted session data can only be decrypted by applications using the same encryption key.

#### Database Handler

The `Luminova\Sessions\Handlers\Database` allows session data to be stored in a database, providing scalability and centralized session management.

#### Filesystem Handler

The `Luminova\Sessions\Handlers\Filesystem` stores session data in the filesystem, offering simplicity and ease of use for smaller-scale applications.

#### Array Handler

The `Luminova\Sessions\Handlers\ArrayHandler` provides an in-memory session storage mechanism using a static array. This approach offers fast read/write operations and is ideal for lightweight applications, unit testing, or scenarios where persistence is unnecessary.  

Unlike file-based or database-backed session handlers, `ArrayHandler` does not retain session data across requests, making it suitable for ephemeral session storage needs. It supports encryption, custom validation, and lifecycle event callbacks for flexible session handling.

#### Custom Session Handlers

In addition to the built-in handlers, Luminova provides a [Luminova\Base\BaseSessionHandler](/base/session-handler.md) class. Developers can extend this abstract base class to implement custom session handlers to meet their specific application needs.

> **Note:** The `Luminova\Sessions\Session` class is designed for backend session management, prioritizing enhanced security and scalability.  

***

### Examples

Initializes the session storage driver of your choice.

```php
use Luminova\Sessions\Session;
use Luminova\Sessions\Managers\Session as SessionManager;

$session = new Session(new SessionManager());
$session->start();
```

#### With Cookie Manager 

Cookie manager, this manager will store your user information in cookie storage which is client-side storage.

```php
use Luminova\Sessions\Session;
use Luminova\Sessions\Managers\Cookie as CookieManager;

$session = new Session(new CookieManager());
$session->start();
```

#### With Database Handler

This will overwrite the default PHP session storage with a database handler. For more information about the database handler, refer to the [Database Session Handler Documentation](/sessions/database-handler.md).

```php
use Luminova\Sessions\Session;
use Luminova\Sessions\Handlers\Database;

$session = new Session();
$session->setHandler(new Database('tableName'));
$session->start();
```

> **Note:** By default access to `JavaScript` is not allowed on and cookie should only be transmitted over secure `HTTPS`  connection.

***

## Class Definition

* Class namespace: `\Luminova\Sessions\Session`
* This class implements:  [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface)

***

## Methods

### constructor

Initializes session class with your preferred manager class, such as ` Luminova\Sessions\Managers\Session` or ` Luminova\Sessions\Managers\Cookie`.

This constructor sets up the session manager to handle user login and backend session management.
It allows for an optional custom session manager and session handler to be provided or defaults to the standard manager.

If not provided, the default `\Luminova\Sessions\Managers\Session` will be used as default.

```php
public __construct(?\Luminova\Interface\SessionManagerInterface $manager = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$manager` | **SessionManagerInterface&#124;null** | Optional. A custom session manager instance.. |

> **Note:** When no custom manager is provided, the default session manager is automatically initialized and configured using the session configuration settings.

***

### getInstance

Singleton method to return an instance of the Session class.

```php
public static getInstance(?\Luminova\Interface\SessionManagerInterface $manager = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$manager` | **\Luminova\Interface\SessionManagerInterface&#124;null** | Optional. A custom session manager instance.<br />If not provided, the default `\Luminova\Sessions\Managers\Session` will be used. |

**Return Value:**

`static` - Return a shared instance of session class.

***

### getManager

Retrieves the current session storage manager instance.

This method returns the session manager instance responsible for handling
session data, either `Luminova\Sessions\Managers\Cookie` or `Luminova\Sessions\Managers\Session`.

```php
public getManager(): ?\Luminova\Interface\SessionManagerInterface
```

**Return Value:**

`\Luminova\Interface\SessionManagerInterface|null` - Return the current session manager instance, or `null` if not set.

***

### getStorage

Retrieves the current session storage name.

This method returns the current storage name used to store session data.

```php
public getStorage(): string
```

**Return Value:**

`string` - Return the current storage name.

***

### getName

Retrieves the session cookie name.

This method returns the name of the session cookie used for session management.
If a custom cookie name is set in the configuration, it will be returned; otherwise, the default PHP session name is used.

```php
public getName(): string
```

**Return Value:**

`string` - Return the session cookie name.

***

### getResult

Retrieves all session data in the specified format.

```php
public getResult(string $format = 'array'): array|object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$format` | **string** | The data format, either `object` or `array` (default: `array`). |

**Return Value:**

`array|object` - Return the stored session data in the requested format.

***

### get

Retrieves a value from the session storage.

```php
public get(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key used to identify the session data. |
| `$default` | **mixed** | The default value returned if the key does not exist. |

**Return Value:**

`mixed` - Returns the retrieved session data or the default value if not found.

***

### getFrom

Retrieves a session data from a specific session storage name.

```php
public getFrom(string $storage, string $key): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string** | The storage name where the data is stored. |
| `$key` | **string** | The key used to identify the session data. |

**Return Value:**

`mixed` - Returns the retrieved session data or `null` if not found.

***

### getId

Retrieves the PHP session identifier.

This method returns the active PHP session ID, which uniquely identifies
the session within the server.

```php
public getId(): string
```

**Return Value:**

`string` - Return the current PHP session identifier.

***

### getIp

Retrieves the IP address associated with the session.

```php
public getIp(): ?string
```

**Return Value:**

`string|null` - Return the stored IP address or null if not set.

***

### getUserAgent

Retrieves the user agent associated with the session.

This method returns the browser or client identifier used when the session was created.

```php
public getUserAgent(): ?string
```

**Return Value:**

`string|null` - Return the user agent string or null if not set.

***

### getToken

Retrieves the client's online session login token.

This method returns a randomly generated token when `synchronize()` is called.
The returned token can be used to track the online session state, validate session integrity or prevent session fixation attacks.

```php
public getToken(): ?string
```

**Return Value:**

`string|null` - Return the login session token, or `null` if not logged in.

***

### getDatetime

Retrieves the client login session date and time in ISO 8601 format.

The session datetime is generated automatically when `synchronize()` is called, marking the moment the session login was established.

```php
public getDatetime(): string
```

**Return Value:**

`string` - Return the session login datetime in ISO 8601 format, or `null` if not logged in.

***

### getTimestamp

Retrieves the client login session creation timestamp.

The session timestamp is generated automatically when `synchronize()` is called,
marking the moment the session login was established.

```php
public getTimestamp(): int
```

**Return Value:**

`int` - Return he Unix timestamp when the session was created.

***

### getExpiration

Retrieves the session expiration timestamp.

This method returns the Unix timestamp at which the session is set to expire.

```php
public getExpiration(): int
```

**Return Value:**

`int` - Return the expiration timestamp or 0 if not set.

***

### getMeta

Retrieves session login metadata key value from session storage.

```php
public getMeta(string $key, ?string $storage = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The metadata key to retrieve. |
| `$storage` | **string&#124;null** | Optional storage name. |

**Return Value:**

`mixed` - Return the metadata value or null if not exist.

***

### getMetadata

Retrieves session login metadata information from session storage.

```php
public getMetadata(?string $storage = null): array<string,mixed>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | Optional storage name. |

**Return Value:**

`array<string,mixed>` - Return an associative array containing session metadata.

***

### getFingerprint

Retrieves the session fingerprint.

The fingerprint is a unique identifier used to track session consistency.

```php
public getFingerprint(): ?string
```

**Return Value:**

`string|null` - Return the session fingerprint or null if not set.

***

### getIpChanges

Retrieves a list of IP address changes during the session.

This method returns an array of previously recorded IP addresses if they changed
during the session lifetime.

```php
public getIpChanges(): array<int,string>
```

**Return Value:**

`array<int,string>` - Return the list of IP address changes.

***

### setHandler

Sets the session save handler responsible for managing session storage.

This method allows specifying a custom session save handler, such as a database array-handler,or filesystem-based handler, to control how session data is stored and retrieved.

The session save handler can be either:
- `Luminova\Sessions\Handlers\Database`: Stores session data in a database.
- `Luminova\Sessions\Handlers\Filesystem`: Saves session data in files.
- `Luminova\Sessions\Handlers\ArrayHandler`: Stores session data temporarily in an array.

```php
public setHandler(Luminova\Base\BaseSessionHandler $handler): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **\Luminova\Base\BaseSessionHandler** | The session save handler instance. |

**Return Value:**

`static` - Returns the instance of session class.

**Reference**  

For more details on session handlers, refer to the following documentation:  

- [Database Session Handler](/sessions/database-handler.md) – Stores session data in a database for persistent storage.  
- [Filesystem Session Handler](/sessions/filesystem-handler.md) – Saves session data in files on the server.  
- [Abstract Base Session Handler](/base/session-handler.md) – Provides a common interface for custom session handlers.  

**Example**

Using the database handler:

```php
use Luminova\Sessions\Handlers\Database;

$session->setHandler(new Database('tableName'));
```

Using the filesystem handler:

```php
use Luminova\Sessions\Handlers\Filesystem;

$session->setHandler(new Filesystem('/writeable/session/'));
```

***

### setManager

Sets the session manager that controls the underlying storage engine for session data.

Unlike a session handler `setHandler()`, which is only applicable when using `Luminova\Sessions\Managers\Session`, this method allows specifying a session manager to determine where session data is stored.

The session manager can be either:
- `Luminova\Sessions\Managers\Cookie`: Stores session data securely in client-side cookies.
- `Luminova\Sessions\Managers\Session`: Uses PHP's default `$_SESSION` storage.

```php
public setManager(\Luminova\Interface\SessionManagerInterface $manager): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$manager` | **\Luminova\Interface\SessionManagerInterface** | The session manager instance to set. |

**Return Value:**

`self` - Returns the instance of session class.

***

### setStorage

Sets the storage name for storing and retrieving session data.

This method allows you to define or override the session name under which session data will be managed.

```php
public setStorage(string $storage): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string** | The session storage key to set. |

**Return Value:**

`self` - Returns the instance of session class.

***

### set

Sets a value in the session storage by key.

This method saves or updates a value in the session using the specified key.
If the key already exists, its value will be overwritten with the new value.

```php
public set(string $key, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to identify the session data. |
| `$value` | **mixed** | The value to associate with the specified key. |

**Return Value:**

`self` - Returns the instance of session class.

***

### setTo

Stores a value in a specific session storage name.

```php
public setTo(string $key, mixed $value, string $storage): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key used to identify the session data. |
| `$value` | **mixed** | The value to be stored. |
| `$storage` | **string** | The storage name where the value will be saved. |

**Return Value:**

`self` - Returns the instance of session class.

**Similar:**

This method is similar to using `setStorage` except that it will change the entire storage of current session instance.

```php
$session->setStorage('new-storage')->set('foo', 'bar');
```

To prevent that behavior, clone the main storage name  and restore it afterwards:

```php
$mainStorage = $session->getStorage(); // Main storage name
$session->setStorage('new-storage')
    ->set('foo', 'bar')
    ->setStorage($mainStorage); // Restore the main storage
```

***

### add

Adds a value to the session storage without overwriting existing keys.

This method will attempts to add a new key-value pair to the session.
If the specified key already exists in the session storage, the method does not modify the value and sets the status to `false`. Otherwise, it adds the new key-value pair and sets the status to `true`.

```php
public add(string $key, mixed $value, bool &$status = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to identify the session data. |
| `$value` | **mixed** | The value to associate with the specified key. |
| `$status` | **bool** | A reference variable to indicate whether the operation succeeded (`true`) or failed (`false`). |

**Return Value:**

`self` - Returns the instance of session class.

***

### put

Queues multiple items for batch storage when `save` is called.

This method allows adding multiple key-value pairs to a temporary stack, which can later be saved to session storage using `$session->save()`. If a key already exists in the stack or storage, its value will be overwritten.

```php
public put(string $key, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to identify the session data. |
| `$value` | **mixed** | The value to associate with the specified key. |

**Return Value:**

`self` - Returns the instance of session class.

***

### save

Saves all stacked items to the session storage.

This method moves all previously stacked items (added via `put()`) to session storage. If a storage name is provided, the items are saved under that specific session storage. Once saved, the stack is cleared.

```php
public save(?string $storage = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string\|null** | Optional storage name where stacked data will be saved. |

**Return Value:**

`bool` - Returns true if data was successfully saved, otherwise false.

***

### commit

Commits the current session data.

This method finalizes the session write process by committing any changes made to the session data. Once committed, the session is considered closed and cannot be modified until restarted.

```php
public commit(): void
```

***

### dequeue

Clears all stacked session data without saving.

This method removes all temporarily stored session data before it is saved. Use it if you want to discard changes before calling `save()`.

```php
public dequeue(): true
```

**Return Value:**

`true` - Always return true.

***

### online

Determines if the client has successfully logged in.

This method verifies whether the `synchronize()` method has been called, meaning the session user is considered online. It optionally checks a specific session storage; otherwise, it defaults to the current storage.

```php
public online(?string $storage = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | Optional session storage name. |

**Return Value:**

`bool` - Returns true if the session user is online, false otherwise.

***

### is

 
Checks if the current session status matches the given status.

**Available Status:**

- `self::DISABLED` (PHP_SESSION_DISABLED): Sessions are disabled.
- `self::NONE` (PHP_SESSION_NONE): Sessions are enabled but no session exists.
- `self::ACTIVE` (PHP_SESSION_ACTIVE): A session is currently active.

```php
public static is(int $sessionStatus = self::ACTIVE): bool 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$sessionStatus` | **int** | The session status to check (default: `Session::ACTIVE`). |

**Return Value:**

`bool` - Returns `true` if the current session status matches the given status, otherwise `false`.

***

### isOnline

Checks if the session user is currently online.

This method acts as an alias for `online()`, maintaining naming consistency.

```php
public isOnline(): bool
```

**Return Value:**

`bool` - Returns true if the session user is online, false otherwise.

***

### isExpired

Checks if the session is still valid based on elapsed time.

This method determines whether the session has expired based on the last recorded online time. By default, a session is considered expired after 3600 seconds (1 hour).

```php
public isExpired(int $seconds = 3600): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$seconds` | **int** | The time threshold in seconds before the session is considered expired (default: 3600). |

**Return Value:**

`bool` - Returns true if the session is still valid, false if it has expired.

***

### isStrictIp

Checks if strict IP validation is enabled in the session configuration.

```php
public isStrictIp(): bool
```

**Return Value:**

`bool` -Returns true if strict session IP enforcement is enabled, false otherwise.

***

### isSessionIp

Validates whether the session IP remains unchanged when strict IP enforcement is enabled.

This method ensures that the user's IP address matches the stored session IP, preventing session hijacking if strict IP validation is enabled.

```php
public isSessionIp(): bool
```

**Return Value:**

`bool` -Returns true if strict IP validation is enabled and the IP is unchanged, false otherwise.

***

### toArray

Retrieves session data as an associative array.

```php
public toArray(?string $key = null): array<string,mixed>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | Optional key to retrieve specific data. If null, returns all session data. |

**Return Value:**

`array<string,mixed>` - Return the session data as an associative array.

***

### toObject

Retrieves session data as an object.

```php
public toObject(?string $key = null): object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | Optional key to retrieve specific data. If null, returns all session data. |

**Return Value:**

`object<string,mixed>` - Return the session data as a standard object.

***

### remove

Remove a key from the session storage by passing the key.

```php
public remove(string $key): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to identify the session data to remove. |

**Return Value:**

`Session` - Returns the instance of session class.

***

### clear

Clear all data from session storage by passing the storage name or using the default storage.

```php
public clear(?string $storage = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | Optionally session storage name to clear. ||

**Return Value:**

`Session` - Returns the instance of session class.

***

### has

Check if item key exists in session storage.

```php
public has(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to identify the session data to check. |

**Return Value:**

`bool` - Return true if key exists in session storage else false.

***

### attempt

Tracks the number of session login attempts.

This method increments the number of session login attempts unless reset is requested.
The attempt count is stored in session metadata.

Tracks the number of session login attempts.

```php
public attempt(bool $reset = false): true
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$reset` | **bool** | If true, resets the attempt count to zero. |

**Return Value:**

`true` - Always returns true.

***

### attempts

Retrieves the number of session login attempts.

```php
public attempts(): int
```

**Return Value:**

`int` - Return the number of recorded login attempts.

***

### regenerate

Regenerate session identifier and delete the old ID associated session file.

```php
public regenerate(): string|bool
```

**Return Value:**

`string|bool` - Return the new generated session Id on success, otherwise false.

***

### start

Initializes PHP session configurations and starts the session if it isn't already started.

This method replaces the default PHP `session_start()`, with additional configuration and security implementations.

```php
public start(?string $sessionId = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$sessionId` | **string&#124;null** | Optional specify a valid PHP session identifier (e.g,`session_id()`). |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If an invalid session ID is provided.

**Example:**

Starting a session with a specified session ID:

To start a session, simply call the `start()` method. It initializes the session if it's not already started.

```php
namespace App;

use Luminova\Core\CoreApplication;
use Luminova\Sessions\Session;

class Application extends CoreApplication
{
    protected ?Session $session = null;
    protected function onCreate(): void 
    {
        $this->session = new Session();
        $this->session->start('optional_session_id');
    }
}
```

***

### synchronize

Starts a user's online login session and synchronizes session data.

This method is called once after a successful login to initialize and persist session-related data, marking the user as logged in. If strict IP validation is enabled, it associates the session with a specific IP address. Session data is synchronized and stored using the configured session manager and save handler.

```php
public synchronize(?string $ip = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | Optional IP address to associate with login session. If not provided,<br />the client's current IP address will be used if strict IP validation is enabled. |

**Return Value:**

`bool` - Returns true if session login was started, otherwise false.

**Throws:**

- [\Luminova\Exceptions\LogicException](/running/exceptions.md#logicexception) - If strict IP validation is disabled and IP address is provided.

**Example:**

Synchronizing user login session:

```php
namespace App\Controllers\Http;

use Luminova\Base\BaseController;
class AdminController extends BaseController
{
    public function loginAction(): int 
    {
        if($this->app->session->online()){
            return response()->json(['success' => true]);
        }

        $username = $this->request->getPost('username');
        $password = $this->request->getPost('password');

        if($username === 'admin' && $password === 'password'){
            $this->app->session->set('username', $username);
            $this->app->session->set('email', 'admin@example.com');
            $this->app->session->synchronize();
            return response()->json(['success' => true]);
        }

        return response()->json(['success' => false, 'error' => 'Invalid credentials']);
    }
}
```

***

### login

Logs in the user by synchronizing session login metadata.

This method serves as an alias for `synchronize()`, which initializes and maintains the session state after a successful login. If IP validation is enabled,  the session will be linked to the provided IP address.

```php
public login(?string $ip = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | Optional IP address to associate with the session. |

**Return Value:**

`bool` - Returns true if the session was successfully started, otherwise false.

***

### terminate

Terminates the user's online session and clears session metadata.

This method removes only the session's online status and metadata, ensuring the user is logged out.
It does not delete any stored session data but forces the application to recognize the session as inactive.
If strict session IP validation is enabled, the associated IP address will also be removed.

```php
public terminate(): bool
```

**Return Value:**

`bool` - Returns true if session was terminated.

***

### logout

Logs out the user by terminating the session login metadata.

This method acts as an alias for `terminate()`, ensuring the session metadata is cleared and marking the user as logged out. The session data itself remains intact, but the session state will no longer be recognized as active.

```php
public logout(): bool
```

**Return Value:**

`bool` - Returns true if the session was successfully terminated, otherwise false.

***

### destroy

Deletes session data stored in the session table `$tableIndex`, based on the active session configuration or the table set via `setTable` in the session manager.

If `$allSessions` is `true`, all session and cookie data for the application will be cleared.

```php
public destroy(bool $allSessions = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$all_session` | **bool** | Whether to destroy clear all application session or cookie data, based on session manager in use (default: false). |

**Return Value**

`bool` - Returns `true` if the session data was successfully cleared; `false` otherwise.

***

### ipChanged

Checks if the user's IP address has changed since the last login session.

```php
public ipChanged(?string $storage = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | Optional session storage name to perform the check. |

**Return Value:**

`bool` - Returns false if the user's IP address matches the session login IP, otherwise returns true.

***

### onIpChanged

IP Address Change Listener to detect and respond to user IP changes.

This method monitors the user's IP address during a session and `$strictSessionIp` is enabled. If the IP address changes,
the specified callback is executed. Based on the callback's return value:
- `true`: The session is terminate the client login session.
- `false`: The session remains active, allowing manual handling what happens on IP change event.

```php
public onIpChanged(callable $onChange): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onChange` | **callable** | A callback function to handle the IP change event.<br/>The function receives the `Session` instance, the previous IP, and array list of IP changes as arguments. |

**Return Value:**

`self` - Returns the current `Session` instance.

**Example**

Session IP address change event:

```php
namespace App;
use Luminova\Core\CoreApplication;
use Luminova\Sessions\Session;

class Application extends CoreApplication
{
    protected ?Session $session = null;

    protected function onCreate(): void 
    {
        $this->session = new Session();
        $this->session->start();
        $this->session->onIpChanged(function (Session $instance, string $sessionIp, array $ipChanges): bool {
            // Handle the IP address change event manually
            // Destroy the session, or return false to keep it or indication that it been handled
            return true; 
        });
    }
}
```