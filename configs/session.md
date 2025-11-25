# Backend Session and Security Configuration

***

## Overview

Manage how server-side sessions are stored, secured, and validated to ensure reliable authentication.

***

## Introduction

The `Session Configuration` class allows you to customize how server-side sessions are handled in your application. You can configure session cookies, save paths, expiration times, and other key session behaviors to ensure secure and reliable session management. To get started see [System Handling](/sessions/session.md) documentation.

***

## Class Definition

* Class namespace: `\App\Config\Session`
* File path: `/app/Config/Session.php`
* This class is marked as **final** and can't be subclassed

## Properties

### cookieName

Name of the session cookie sent to the browser.

```php
public string $cookieName = 'PHPSESSID';
```

> - Default is usually `PHPSESSID`.
> - You can rename it for security or branding.

***

### tableIndex

Identifier used to store and index session data.

The property controls the session storage name, where the value is used as the default storage index when storing client's information in session.

```php
public string $tableIndex = '__server_side_session_table';
```

> **Note:**
> - Should be a fixed, unique string (e.g., an MD5 hash).
> - Do not generate dynamically, or sessions may break.

***

### savePath

Directory path where session files are stored (server-side).

```php
public string $savePath = '';
```

> It doesn't affect the cookie sent to the client.
> If empty, PHP’s default session save path will be used.

***

### expiration

Default session lifetime in seconds.

- After this period, sessions will expire.
- Example: **365 days** → `365 * 24 * 60 * 60`.

```php
public int $expiration = 365 * 24 * 60 * 60;
```

***

### sessionPath

Path under which the session cookie is valid.

It controls the URL path under which the cookie is available on the server.

- `/` means the cookie is available site-wide.
- You can limit it to a subdirectory if needed.

```php
public string $sessionPath = '/'
```

***

### sessionDomain

Domain where the session cookie is valid.

```php
public string $sessionDomain = '.localhost';
```

> - Example: `.example.com` will work on all subdomains.
> - For local development: `.localhost` is typical.

***

### sameSite

SameSite policy for session cookies.

**Options:**

`None`, `Lax`, `Strict`

> `None` requires cookies to be sent over HTTPS (Secure flag).

```php
public string $sameSite = 'Lax';
```

***

### strictSessionIp

Enable strict session IP authentication.

When `true`, it tie client login sessions to a fixed IP address, forcing client to login again if ip address is changed.

```php
public bool $strictSessionIp = false;
```

***

### encryptCookieData

Whether to encrypt session data stored in cookies.

If `true`, it enables data encryption when managing session over `Luminova\Session\Managers\Cookie`.

```php
public bool $encryptCookieData = false;
```

> The data encryption and decryption uses default application encryption configuration.
> - Recommended if you store sensitive data client-side.

***

### csrfStorage

Storage method for CSRF tokens.

This allows you to set which storage engine to manage **CSRF** protection.

**Options:** 

- `cookie` - token stored in a cookie.
- `session` - token stored in session data.

```php
public string $csrfStorage = 'session';
```