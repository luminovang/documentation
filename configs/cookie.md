# Client Cookies &amp; Security Configurations

***

## Overview

Manage cookie settings such as expiration, path, and security flags to control how your application stores and protects client-side data.

***

## Introduction

The `Cookie Configuration` class allows you to manage and customize cookies in your web application. You can control client-side cookie behavior, including save path, expiration, path, domain, `SameSite` policy, and security flags, giving you full control over how cookies are stored and used.

***

## Class Definition

* Class namespace: `\App\Config\Cookie`
* File path: `/app/Config/Cookie.php`
* This class is marked as **final** and can't be subclassed

***

## Properties

### savePath

The directory where cookie-related data may be stored on the server.

```php
public string $savePath = '';
```

> Leave empty for default PHP cookie storage path.

***

### expiration

The cookie's expiration time in seconds.

```php
public int $expiration = 365 * 24 * 60 * 60;
```

> This determines how long the cookie remains valid.

***

### cookiePath

The path where the cookie is accessible.

```php
public string $cookiePath = '/';
```

> This restricts the availability of this cookie to a specific URI path.

***

### cookieDomain

The domain for which the cookie is valid.

```php
public string $cookieDomain = '.localhost';
```

> This determines which domains can access the cookie (e.g., `'.' . APP_HOSTNAME`).

***

### sameSite

The SameSite policy for cookies.

Defines how cookies are sent with cross-site requests.

**Options:** `None`, `Lax`, `Strict`

```php
public string $sameSite = 'Lax'
```

***

### secure

Enforce secure cookies by requiring an HTTPS connection.

```php
public bool $secure = false;
```

> If enabled, cookies will not be sent over an insecure HTTP connection.

***

### httpOnly

Restrict cookie access to HTTP(S) requests only.

If enabled, JavaScript cannot access the cookie via `document.cookie`.

```php
public bool $httpOnly = true;
```

***

### cookieRaw

Enable setting raw (non-URL-encoded) cookies.

If enabled, cookie names and values must comply with **RFC 2616**, i.e., its name and value are not URL encoded using `rawurlencode()` leaving the cookie as it was.

```php
public bool $cookieRaw = false;
```

**See Reference:**

- [Web HTTP Headers Set-Cookie Attributes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#attributes)
- [RFC 2616 Section 2.2](https://tools.ietf.org/html/rfc2616#section-2.2)