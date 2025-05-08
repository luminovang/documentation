# Configuration for Client-Side Cookies and Security

***

## Overview

Configurations to various properties to adjust and customize the behavior of cookies in your web application, including cookie expiration time, paths, and more particularly client-side cookies.

***

## Introduction

The `Cookie Configuration` class provides properties to configure various aspects of cookies used in web applications, particularly for client-side cookie management. These properties allow developers to customize settings such as the save path, expiration, cookie path, domain, SameSite attribute, security flags, and more.

***

* Class namespace: `\App\Config\Cookie`
* File path: `/app/Config/Cookie.php`
* This class is marked as **final** and can't be subclassed

***

## Properties

### savePath

The path where session files are stored on the server.

```php
public string $savePath = '';
```

***

### expiration

The lifetime of the session in seconds.

```php
public int $expiration = 365 * 24 * 60 * 60;
```

***

### cookiePath

The path to use for the session cookie.

```php
public string $cookiePath = '/';
```

***

### cookieDomain

The domain to use for the session cookie.

```php
public string $cookieDomain = '.localhost';
```

***

### sameSite

Set the session cookie security level.

Possible attributes: `None`, `Lax`, `Strict`

```php
public string $sameSite = 'Lax'
```

***

### secure

Cookie will only be set if a secure HTTPS connection exists.

```php
public bool $secure = false;
```

***

### httpOnly

Cookie will only be accessible via HTTP(S) (no JavaScript).

```php
public bool $httpOnly = true;
```

***

### cookieRaw

This flag allows setting a 'raw' cookie, i.e., its name and value are not URL encoded using `rawurlencode()` leaving the cookie as it was.

```php
public bool $cookieRaw = false;
```

> If this is set to `true`, cookie names should be compliant of RFC 2616's list of allowed characters.

**See Reference:**

- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#attributes](#)
- [https://tools.ietf.org/html/rfc2616#section-2.2](#)

