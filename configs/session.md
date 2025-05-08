# Configurations for Backend Session Management

***

## Overview

Session configuration class provides flexibility and control over server-side session management, ensuring the security and reliability of the application's session handling.

***

## Introduction

The `Session Configuration` class provides properties to configure various aspects of server-side sessions in your application, including session cookies, save path, expiration, and more.

***

* Class namespace: `\App\Config\Session`
* File path: `/app/Config/Session.php`
* This class is marked as **final** and can't be subclassed

## Properties

### cookieName

The name of the session cookie.

```php
public string $cookieName = 'PHPSESSID';
```

***

### tableIndex

The session storage index name, this will be used as the default storage index for user's information in session or cookie.

```php
public string $tableIndex = '__server_side_session_table';
```

> Since the `Session` is primarily designed for back-end user session management, we need a unique `ID` to differentiate the user session from any other sessions  stored in session variable. 
> 
> Use a unique identifier possibly `md5` hash string.
> > Do not dynamically generate the index always, you must use specific default ID that will be used always to lookup users data in session.

***

### savePath

Set the directory where session files are stored on the server.
It doesn't affect the cookie sent to the client.

```php
public string $savePath = '';
```

> Pass a blank string to use the default server configuration path.

***

### expiration

The lifetime of the session in seconds.

```php
public int $expiration = 365 * 24 * 60 * 60;
```

***

### sessionPath

Set the path on the server where the cookie will be available. 
It controls the URL path under which the cookie is available on the server.

```php
public string $sessionPath = '/'
```

***

### sessionDomain

The domain to use for the session cookie.

```php
public string $sessionDomain = '.localhost';
```

***

### sameSite

Set the session cookie security level.

Possible attributes: `None`, `Lax`, `Strict`

```php
public string $sameSite = 'Lax';
```

None, Lax, Strict

***

### strictSessionIp

Enable strict session IP authentication.

```php
public bool $strictSessionIp = false;
```

If set to true, the user will be logged out if their IP address changes.

***

### encryptCookieData

Enable cookie data encryption when managing session over `Luminova\Session\Managers\Cookie`.
If set to true, the cookie data will be encrypted using the application encryption key and algorithm.

```php
public bool $encryptCookieData = false;
```

***

### csrfStorage

Set the storage engine to use for CSRF protection.
Option: `cookie`, `session`,

```php
public string $csrfStorage = 'session';
```