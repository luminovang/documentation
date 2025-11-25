# Backend Session &amp; User Login Management

***

## Overview

The Session class provides a set of methods to simplify session management in your Luminova application. It offers convenient methods for storing, retrieving, and manipulating session data.

***

## Introduction

The **Luminova Backend Session Class** makes it easier to handle user logins and session data for both **Web** and **CLI** applications. Instead of writing low-level session logic yourself, you can use this class to store, retrieve, and manage session data reliably—whether you want to use PHP sessions, cookies, files, or even a database.

With Luminova, you get two main types of **session storage managers**:

* `Luminova\Sessions\Managers\Session` – uses PHP’s built-in session handling (`$_SESSION`).
* `Luminova\Sessions\Managers\Cookie` – stores session data directly in cookies.

And you also get three ready-to-use **session handlers**:

* `Luminova\Sessions\Handlers\Database` – stores sessions in a database.
* `Luminova\Sessions\Handlers\Filesystem` – stores sessions in files.
* `Luminova\Sessions\Handlers\ArrayHandler` – keeps session data in memory only.

This flexibility means you can pick the storage method that best fits your application, whether it’s a high-traffic website needing a central database, or a CLI tool where temporary in-memory storage is plenty.

---

### Storage Managers

**Session Manager (`Luminova\Sessions\Managers\Session`)**
This manager uses PHP’s normal `$_SESSION` superglobal. If you’re already familiar with standard PHP sessions, this will feel natural—it’s just cleaner and more configurable.

**Cookie Manager (`Luminova\Sessions\Managers\Cookie`)**
This manager stores everything directly in cookies using PHP’s `setcookie` and `$_COOKIE`. It’s handy when you want the browser to carry session data around without relying on PHP’s built-in session files.

> Both managers let you customize where your session data is stored using `App\Config\Session->tableIndex`. You can also set your own storage name so multiple apps don’t clash in the same table or namespace.

---

### Session Handlers

Sometimes you need more control than PHP’s default session behavior. Luminova provides custom **session handlers** that completely replace PHP’s internal storage logic:

* **Database Handler (`Luminova\Sessions\Handlers\Database`)**
  Stores session data in a database table—perfect for scaling across multiple servers.

* **Filesystem Handler (`Luminova\Sessions\Handlers\Filesystem`)**
  Saves session files on disk—simple and reliable for small or single-server apps.

* **Array Handler (`Luminova\Sessions\Handlers\ArrayHandler`)**
  Keeps session data in memory only. This is blazing fast, but doesn’t persist between requests. It’s great for testing, CLI tools, or anything that doesn’t need permanent session storage.

> All handlers can optionally encrypt data and bind it to an IP address for extra security. Encrypted sessions can only be read by apps using the same encryption key.

---

### Custom Handlers

Need something different? Luminova provides a base class, `Luminova\Base\SessionHandler as BaseSessionHandler`, so you can write your own session storage logic while still plugging into the same system.

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

* Class namespace: `Luminova\Sessions\Session`
* This class implements:  [Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)

***

## Methods

### constructor

Initializes session class with your preferred manager class, such as ` Luminova\Sessions\Managers\Session` or ` Luminova\Sessions\Managers\Cookie`.

This constructor sets up the session manager to handle user login and backend session management.
It allows for an optional custom session manager and session handler to be provided or defaults to the standard manager.

If not provided, the default `Luminova\Sessions\Managers\Session` will be used as default.

```php
public __construct(?Luminova\Interface\SessionManagerInterface $manager = null): mixed
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
public static getInstance(?Luminova\Interface\SessionManagerInterface $manager = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$manager` | **Luminova\Interface\SessionManagerInterface&#124;null** | Optional. A custom session manager instance.<br />If not provided, the default `Luminova\Sessions\Managers\Session` will be used. |

**Return Value:**

`static` - Return a shared instance of session class.

***

### getManager

Retrieves the current session storage manager instance.

This method returns the session manager instance responsible for handling
session data, either `Luminova\Sessions\Managers\Cookie` or `Luminova\Sessions\Managers\Session`.

```php
public getManager(): ?Luminova\Interface\SessionManagerInterface
```

**Return Value:**

`Luminova\Interface\SessionManagerInterface|null` - Return the current session manager instance, or `null` if not set.

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

This method returns a randomly generated token when `login()` is called.
The returned token can be used to track the online session state, validate session integrity or prevent session fixation attacks.

```php
public getToken(): ?string
```

**Return Value:**

`string|null` - Return the login session token, or `null` if not logged in.

***

### getDatetime

Retrieves the client login session date and time in ISO 8601 format.

The session datetime is generated automatically when `login()` is called, marking the moment the session login was established.

```php
public getDatetime(): string
```

**Return Value:**

`string` - Return the session login datetime in ISO 8601 format, or `null` if not logged in.

***

### getTimestamp

Retrieves the client login session creation timestamp.

The session timestamp is generated automatically when `login()` is called, marking the moment the session login was established.

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

### getRoles

Get the list of roles assigned to the current session user.

Retrieves roles from the session metadata. Returns an empty array if no roles are set.

```php
public getRoles(): array<int, string|int>
```

**Return Value:**

`array<int,string|int>` - Return a list of assigned roles or an empty array if none.

***

### setHandler

Sets the session save handler responsible for managing session storage.

This method allows specifying a custom session save handler, such as a database array-handler,or filesystem-based handler, to control how session data is stored and retrieved.

The session save handler can be either:
- `Luminova\Sessions\Handlers\Database`: Stores session data in a database.
- `Luminova\Sessions\Handlers\Filesystem`: Saves session data in files.
- `Luminova\Sessions\Handlers\ArrayHandler`: Stores session data temporarily in an array.

```php
public setHandler(Luminova\Base\SessionHandler as BaseSessionHandler $handler): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **Luminova\Base\SessionHandler as BaseSessionHandler** | The session save handler instance. |

**Return Value:**

`static` - Returns the instance of session class.

**Reference**  

For more details on session handlers, refer to the following documentation:  

- [Database Session Handler](/sessions/database-handler.md) – Stores session data in a database for persistent storage.  
- [Filesystem Session Handler](/sessions/file-handler.md) – Saves session data in files on the server.  
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
public setManager(Luminova\Interface\SessionManagerInterface $manager): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$manager` | **Luminova\Interface\SessionManagerInterface** | The session manager instance to set. |

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

This method verifies whether the `login()` method has been called, meaning the session user is considered online. It optionally checks a specific session storage; otherwise, it defaults to the current storage.

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

- `Session::DISABLED` (PHP_SESSION_DISABLED): Sessions are disabled.
- `Session::NONE` (PHP_SESSION_NONE): Sessions are enabled but no session exists.
- `Session::ACTIVE` (PHP_SESSION_ACTIVE): A session is currently active.

```php
public static is(int $status = Session::ACTIVE): bool 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | The session status to check (default: `Session::ACTIVE`). |

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

### toSessionId

Convert a string into a valid PHP session ID.

This method generates a session ID that conforms to the current PHP session configuration (`session.sid_bits_per_character` and `session.sid_length`).

Character sets based on `session.sid_bits_per_character`:
- 4: Hexadecimal characters [0-9a-f]
- 5: Base32 characters [0-9a-v]
- 6: Extended base64 characters [0-9a-zA-Z,-]

```php
public static toSessionId(string $input, string $algo = 'sha256'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | The input string to convert. |
| `$algo` | **string** | The hashing algorithm to use if input is not already hashed (default `sha256`). |

**Return Value:**

`string` - Return a valid PHP session ID based on the current configuration.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If `session.sid_bits_per_character` is unsupported.

**Examples:**

Convert CLI System Id to php session Id:

```php
$sid = Session::toSessionId(Luminova\Command\Terminal::getSystemId());
```
Convert string to session Id:

```php
$sid = Session::toSessionId('user-id');
```

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

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an invalid session ID is provided.

**Example:**

Starting a session with a specified session ID:

To start a session, simply call the `start()` method. It initializes the session if it's not already started.

```php
namespace App;

use Luminova\Foundation\Core\Application as CoreApplication;
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

### login

Starts a user's online login session and synchronizes session data.

This method is called once after a successful login to initialize and persist session-related data, marking the user as logged in. If strict IP validation is enabled, it associates the session with a specific IP address. Session data is synchronized and stored using the configured session manager and save handler.

```php
public login(?string $ip = null, array<int, string|int> $roles = []): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | Optional IP address to associate with login session (default: null). <br />If not provided, the client's current IP address will be used if strict IP validation is enabled. |
| `$roles` | **array** | Optional list of roles to assign (e.g., `['admin', 'editor']`). |

**Return Value:**

`bool` - Returns true if session login was started, otherwise false.

**Throws:**

- [Luminova\Exceptions\LogicException](/error-handlers/exceptions.md#logicexception) - If strict IP validation is disabled and IP address is provided.

**Example:**

Synchronizing user login session:

```php
// /app/Controllers/Http/AdminController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use function Luminova\Funcs\response;

class AdminController extends Controller
{
    #[Route('admin/login', methods: ['POST'])]
    public function loginAction(): int 
    {
        if($this->app->session->online()){
            return response()->json(['success' => true]);
        }

        $username = $this->request->getPost('username');
        $password = $this->request->getPost('password');

        // Perform login authentication
        if($username === 'admin' && $password === 'password'){
            // Set user session data
            $this->app->session->set('username', $username);
            $this->app->session->set('email', 'admin@example.com');

            // Login user
            $this->app->session->login();

            // Set roles
            $this->app->session->roles(['admin', 'editor']);

            return response()->json(['success' => true]);
        }

        return response()->json(['success' => false, 'error' => 'Invalid credentials']);
    }
}
```

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

### roles

Assign roles to the current session user.

This method stores the specified roles in the session metadata, allowing you to associate access levels or permissions with the session.

```php
public roles(array<int, string|int> $roles): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$roles` | **array<int,string\|int>** | A list of roles (e.g., `['admin', 'editor']`). |

**Return Value:**

`self` - Returns the instance of Session class.

**Example:**

Setting user roles after successful login.

```php
$session->roles(['admin', 'editor']);
```

***

### guard

Checks if the current session user has the specified roles.

This method guards routes or logic by evaluating the session roles against the required ones.

**Supported modes:**

- `Session::GUARD_ANY` (default): At least one required role must exist in user roles.
- `Session::GUARD_ALL`: All required roles must be present in user roles (but extras allowed).
- `Session::GUARD_EXACT`: Exact match — all and only the specified roles must exist.
- `Session::GUARD_NONE`: None of the given roles should be present (e.g., guest access only).

```php
public guard(array<int, string|int> $roles, int $mode = Session::GUARD_ANY, ?callable $onDenied = null): bool
```

> **Note:**
> The guard method uses (early-exit). 
> Returns `true` if access is denied (guard failed), and `false` if access is granted.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$roles` | **array** | A list of required role(s) to validate against the session (e.g., `['admin', 'editor']`). |
| `$mode` | **int** | Guard match mode. One of: (`Session::GUARD_*`), (default: `Session::GUARD_ANY`). |
| `$onDenied` | **callable\|int** | Optional handler to call if access is denied. |

**Return Value:**

`bool` -  Returns `true` if access is denied, `false` if access is allowed.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If an invalid mode was provided.

**Example:**

Controller implementation

```php
// /app/Controllers/Http/AdminController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;

class AdminController extends Controller
{
    #[Route('admin/payment/info', methods: ['GET'])]
    public function paymentInfo(): int 
    {
        if($this->app->session->guard(['admin', 'manager'])){
            return $this->view('denied');
        }

        return $this->view('payment.info');
    }
}
```

Allow access if user has any of the roles:

```php
if ($session->guard(['admin', 'editor'])) {
    // access denied
}
```
Require all roles:

```php
if ($session->guard(['admin', 'editor'], Session::GUARD_ALL)) {
    // access denied
}
```

Require exact match:

```php
if ($session->guard(['admin', 'editor'], Session::GUARD_EXACT)) {
    // access denied
}
```

Deny access if user has any of the listed roles:

```php
if ($session->guard(['banned', 'suspended'], Session::GUARD_NONE)) {
    // access denied
}
```

With custom failure handler:

```php
$session->guard(['admin'], Session::GUARD_ANY, 
    function(array $expected, array $roles): void 
    {
        throw new AccessDenied('Not allowed.');
    }
);

// OR

$session->guard(['admin']) && throw new AccessDenied('Not allowed.');
```

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
// /app/Application.php
namespace App;

use Luminova\Foundation\Core\Application as CoreApplication;
use Luminova\Sessions\Session;

class Application extends CoreApplication
{
    protected ?Session $session = null;

    protected function onCreate(): void 
    {
        $this->session = new Session();
        $this->session->start();
        $this->session->onIpChanged(function (Session $session, string $ip, array $changes): bool {
            // Handle the IP address change event manually
            // Destroy the session, or return false to keep it or indication that it been handled
            return true; 
        });
    }
}
```