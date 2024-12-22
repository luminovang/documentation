# Client Side Cookie Management

***

## Overview

The cookie class enhanced client-side data storage without Default JavaScript or HTTP-only restrictions, allowing you to configure your restrictions as needed.

***

## Introduction

Luminova's Cookie manager provides a convenient way to store data on the client-side, offering greater flexibility in managing cookie data compared to the `SessionManager` and `CookieManager` classes. With the Cookie class, you have the ability to configure the security access of the stored information according to your specific requirements.

The Cookie class stores data directly on the client-side, within cookies. This approach is particularly useful for scenarios where client-side storage is preferred or necessary, such as persisting user preferences, session tokens, or other application-specific data.

***

## Class Definition

* Class namespace: `\Luminova\Cookies\Cookie`
* This class implements:
[\Luminova\Interface\CookieInterface](/interface/classes.md#cookieinterface), [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface), [\Stringable](https://www.php.net/manual/en/class.stringable.php)

***

## Properties

The default cookie options.

```php
protected array $default = [
    'prefix' => '',
    'expires'  => 0,
    'path'     => '/',
    'domain'   => '',
    'secure'   => false,
    'httponly' => true,
    'samesite' => 'Lax',
    'raw'      => false,
];
```

***

## Constants

These constants define common cookie attributes and formatting options used for setting and managing cookies. 

| Constant         | Type   | Value                        | Description                                      |
|------------------|--------|------------------------------|--------------------------------------------------|
| `NONE`           | string | `'none'`                    | Represents the `SameSite=None` policy, allowing cookies to be sent in all contexts, including cross-site requests. |
| `LAX`            | string | `'lax'`                     | Represents the `SameSite=Lax` policy, allowing cookies in top-level navigations and some cross-site requests. |
| `STRICT`         | string | `'strict'`                  | Represents the `SameSite=Strict` policy, restricting cookies to the same site only. |

***

## Methods

### constructor

Cookie class initialization. This class cannot be extended.

```php
new Cookie(string $name, mixed $value = '', array $options = []): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | Cookie name |
| `$value` | **mixed** | cookie value |
| `$options` | **array** | Optional cookie options if not passed the default will be used |

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws cookie exception.

***

### newFromString

Create a new Cookie instance from a `Set-Cookie` header.

```php
public newFromString(string $cookie, bool $raw = false, Cookie|array $options = []): \Luminova\Interface\CookieInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$cookie` | **string** | The cookie header string. |
| `$raw` | **bool** | Indicates if the cookie is raw. |
| `$options` | **App\Config\Cookie\|array<string,mixed>** | An array of default cookie options or cookie config class object. |

**Return Value:**

`Luminova\Interface\CookieInterface` - Return a new Cookie instance.

***

### newFromArray

Create a new Cookie instance from an array or json object.

```php
public newFromArray(array|object $cookies, Cookie|array $options = []): \Luminova\Interface\CookieInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$cookie` | **array<string,mixed>\|object** | An associative array or json object of cookies. |
| `$options` | **App\Config\Cookie\|array<string,mixed>** | An array of default cookie options or cookie config class object. |

**Return Value:**

`Luminova\Interface\CookieInterface` - Return a new Cookie instance.

***

### set

Set a cookie.

```php
public set(mixed $name, mixed $value, array $options = []): \Luminova\Interface\CookieInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **mixed** | The name of the cookie. |
| `$value` | **mixed** | The value of the cookie. |
| `$options` | **array** | An array of cookie options. |

**Return Value:**

`CookieInterface` - Return new Cookie instance.

***

### setValue

Set the value of a cookie.

```php
public setValue(mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | The value to set. |

**Return Value:**

`self` - Return the instance of Cookie class.

***

### setOptions

Set cookie options.

```php
public setOptions(Cookie|array $options): \Luminova\Interface\CookieInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **App\Config\Cooke&#124;array<string,mixed>** | An array of cookie options or cookie config class object. |

**Return Value:**

`CookieInterface` - Return instance of Cookie class.

***

### toString

Convert the cookie object to a string.

```php
public toString(): string
```

**Return Value:**

`string` - The string representation of the Cookie object.

***

### toArray

Convert the cookie to an array.

```php
public toArray(): array<string, mixed>
```

**Return Value:**

`array<string, mixed>` - The array representation of the Cookie object.

***

### get

Retrieve the value of a cookie, if `key` is specified it will return value of the key .

```php
public get(?string $key = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | Optional key of the cookie to retrieve. |

**Return Value:**

`mixed` - The value of the cookie.

***

### getName

Get the name of the cookie.

```php
public getName(): string
```

**Return Value:**

`string` - The name of the cookie.

***

### getOptions

Get the options associated with the cookie.

```php
public getOptions(): array
```

**Return Value:**

`array` - The options associated with the cookie.

***

### getValue

Get the value of the cookie.

```php
public getValue(): mixed
```

**Return Value:**

`mixed` - The value of the cookie.

***

### getDomain

Get the domain associated with the cookie.

```php
public getDomain(): string
```

**Return Value:**

`string` - The domain associated with the cookie.

***

### getPrefix

Get the prefix associated with the cookie.

```php
public getPrefix(): string
```

**Return Value:**

`string` - The prefix associated with the cookie.

***

### getMaxAge

Get the maximum age of the cookie.

```php
public getMaxAge(): int
```

**Return Value:**

`int` - The maximum age of the cookie.

***

### getPath

Get the path associated with the cookie.

```php
public getPath(): string
```

**Return Value:**

`string` - The path associated with the cookie.

***

### getSameSite

Get the SameSite attribute of the cookie.

```php
public getSameSite(): string
```

**Return Value:**

`string` - The SameSite attribute of the cookie.

***

### getId

Get the ID of the cookie.

```php
public getId(): string
```

**Return Value:**

`string` - The ID of the cookie.

***

### getPrefixedName

Get the prefixed name of the cookie.

```php
public getPrefixedName(): string
```

**Return Value:**

`string` - The prepended prefix name of the cookie.

***

### getExpiry

Get the cookie expiration time.

```php
public getExpiry(bool $return_string = false): string|int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$return_string` | **bool** | Return cookie expiration timestamp or string presentation. |

**Return Value:**

`string|int` - The expiration time.

***

### isSecure

Check if the cookie is secure.

```php
public isSecure(): bool
```

**Return Value:**

`bool` - True if the cookie is secure, false otherwise.

***

### isHttpOnly

Check if the cookie is HTTP-only.

```php
public isHttpOnly(): bool
```

**Return Value:**

`bool` - True if the cookie is HTTP-only, false otherwise.

***

### isRaw

Check if the cookie value is raw.

```php
public isRaw(): bool
```

**Return Value:**

`bool` - True if the cookie value is raw, false otherwise.

***

### isExpired

Check if the cookie has expired.

```php
public isExpired(): bool
```

**Return Value:**

`bool` - Return true if the cookie has expired otherwise false.

***

### hasPrefix

Check if a cookie name has a prefix.

```php
public hasPrefix(?string $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string&#124;null** | The name of the cookie. |

**Return Value:**

`bool` - True if the cookie name has a prefix, false otherwise.

***

### has

Check if a cookie exists, if `key` is passed `NULL`, it checks if the current cookie object name exists.

```php
public has(?string $key = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | The key of the cookie to check. |

**Return Value:**

`bool` - True if the cookie exists, false otherwise.

***

### delete

Remove a cookie, If `key` is null or empty, delete the entire cookie.

```php
public delete(?string $key = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | The key of the cookie to remove. |

**Return Value:**

`bool` - Return true if the cookie was removed, false otherwise.

