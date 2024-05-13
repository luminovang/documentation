## Session Configuration

***

## Overview

Session configuration class provides flexibility and control over server-side session management, ensuring the security and reliability of the application&#039;s session handling.

***

The `Session Configuration` class provides properties to configure various aspects of server-side sessions in your application, including session cookies, save path, expiration, and more.

***

* Class namespace: `\App\Controllers\Config\Session`
* File path: `/app/Controllers/Config/Session.php`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

## Properties

### cookieName

The name of the session cookie.

```php
public static string $cookieName = 'PHPSESSID';
```

***

### savePath

Set the directory where session files are stored on the server.
It doesn't affect the cookie sent to the client.

```php
public static string $savePath = '';
```

> Pass a blank string to use the default server configuration path.

***

### expiration

The lifetime of the session in seconds.

```php
public static int $expiration = 365 * 24 * 60 * 60;
```

***

### sessionPath

Set the path on the server where the cookie will be available. 
It controls the URL path under which the cookie is available on the server.

```php
public static string $sessionPath = '/'
```

***

### sessionDomain

The domain to use for the session cookie.

```php
public static string $sessionDomain = '.localhost';
```

***

### sameSite

Set the session cookie security level.

Possible attributes: `None`, `Lax`, `Strict`

```php
public static string $sameSite = 'Lax';
```

None, Lax, Strict

***

### strictSessionIp

Enable strict session IP authentication.

```php
public static bool $strictSessionIp = false;
```

If set to true, the user will be logged out if their IP address changes.

***

### csrfStorage

Set the storage engine to use for CSRF protection.
Option: `cookie`, `session`,

```php
public static string $csrfStorage = 'session';
```