## REMOVE Session Configuration

***

## Overview

Configuration for your application server-side session behavior.

***

Class details various properties that can be adjusted to customize the behavior of sessions in your application, including cookie settings, expiration time, session paths, and more particularly for server-side session management.

* Class namespace: `\App\Controllers\Config\Session`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

***
## Properties

These properties are **public static**.

***

### Cookie Name

The name of the session cookie.

```php
public static string $cookieName = 'PHPSESSID'
```

***

### Save Path

The path where session files are stored on the server.

```php
public static string $savePath = ''
```

***

### expiration

The lifetime of the session in seconds.

```php
public static int $expiration = 365 * 24 * 60 * 60; 
```

***

### Session Path

The path to use for the session cookie.

```php
public static string $sessionPath = '/'
```

***

### Session Domain

The domain to use for the session cookie.

```php
public static string $sessionDomain = '.localhost'
```

***

### Same Site

Set the session cookie security level.

```php
public static string $sameSite = 'Lax'
```

None, Lax, Strict

***

### Strict Session Ip Address

Enable strict session IP authentication.

```php
public static bool $strictSessionIp = 'false'
```

> If set to true, the user will be logged out if their IP address changes.

***

### CSRF Storage

Set the csrf storage engine
cookie, session,

```php
public static string $csrfStorage = 'session'
```