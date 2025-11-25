# Session Handler to Store Data in File

***

## Overview

Installation guides for the PHP Luminova framework highlight the benefits of using Composer for easy maintenance and ensuring you're always up to date with the latest framework and dependency.

***

## Introduction

The `Filesystem` session handler stores PHP session data in files, offering enhanced control, security, and scalability compared to PHP's default session handling. This approach is ideal for applications that require encrypted session storage or need to integrate with filesystem-based features.

Key features include:

- **Data Encryption (Optional)**: Secures session data by encrypting it before storage, protecting it from unauthorized access.
- **IP Binding**: Associates sessions with client IP addresses for additional security.
- **Session ID Entropy**: Controls the length and complexity of generated session IDs.
- **Custom Callbacks**: Allows hooking into session lifecycle events, such as validation, creation, and closure.

---

### Usage Example

The following example demonstrates how to configure and use the `Filesystem` with the `Session` class to manage sessions.

```php
use Luminova\Sessions\Session;
use Luminova\Sessions\Handlers\Filesystem;

$session = new Session();
$session->setHandler(new Filesystem('path/to/sessions', [
    'encryption'    => true,         // Enable encryption for session data
    'session_ip'    => true,         // Bind sessions to client IPs
    'dir_permission'  => 0777,       // Session storage directory permission
    'sid_entropy_bits' => 160        // The total bits for session id generation
]));
$session->start();

// Set and retrieve session data
$session->set('foo', 'Hello World');
echo $session->get('foo'); // Outputs: Hello World
```

***

## Class Definition

* Class namespace: `Luminova\Sessions\Handlers\Filesystem`
* Parent class: [Luminova\Base\SessionHandler as BaseSessionHandler](/base/session-handler.md)
* Class implements: [\SessionHandlerInterface](https://www.php.net/manual/en/class.sessionhandlerinterface.php), [\SessionIdInterface](https://www.php.net/manual/en/class.sessionidinterface.php)

***

## Methods

### constructor

Constructor to initialize the session handler.

```php
public __construct(?string $filePath = null, array $options = [])
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filePath` | **string\|null** | Optional directory for session files (e.g, `/writeable/session/`). |
| `$options` | **array<string,mixed>** | Configuration options for session handling. |

**Options**

This section outlines the available options for configuring a session filesystem handler. These options provide flexibility in managing session data storage and behavior for more information visit [Abstract Base Session handler](/base/session-handler#lmv-docs-configuration-options) documentation.

```php
[
    'encryption'    => false, // Enables encryption for session data to enhance security. 
    'session_ip'    => false, // Binds sessions to the client's IP address to prevent session hijacking.  
    'debugging'     => null, // Indicates whether log error messages.
    'sid_entropy_bits'  => 160, // Session id entropy bits.
    'dir_permission'    => 0777, // Session storage directory permissions.
    'cacheable'     => true, // Enables in-memory caching for faster session data retrieval.
    'onValidate'    => null, // A callback executed during session validation, useful for custom checks.
    'onCreate'      => null, // A callback triggered upon session creation.  
    'onClose'       => null  // A callback triggered when a session is closed or destroyed. 
]
```