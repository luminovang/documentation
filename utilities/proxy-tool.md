# Proxy Testing Utility

***

## Overview

Easily parse, validate, and test HTTP/SOCKS proxies. Handle IPv4/IPv6, check latency, resolve public IPs, verify anonymity, and format proxies for use in any application.

***

## Introduction

The Luminova **Proxy** class provides a complete set of tools to work with proxy servers. It allows you to:

* Parse and format proxy strings.
* Validate proxy formats and ports.
* Detect proxy types (HTTP, HTTPS, SOCKS4, SOCKS5).
* Test proxies for connectivity, latency, and anonymity.
* Resolve the public IP as seen through a proxy.

This class is designed to simplify proxy handling for applications that need to route HTTP requests through external servers.

---

## Examples

### Proxy Parsing and Formatting

Breaks a proxy string into parts like host, port, username, password, and type.

```php
$parts = Proxy::parse("john:pass@10.0.0.1:8080");
// ['host'=>'10.0.0.1','port'=>8080,'username'=>'john','password'=>'pass','type'=>null]
```

Breaks a proxy string into parts like in array list format.

```php
$parts = Proxy::parts("john:pass@10.0.0.1:8080");
// [10.0.0.1', 8080, 'john:pass', null]
```

Converts an array of proxy components back into a normalized string.

```php
$proxyString = Proxy::format($parts);
// "john:pass@10.0.0.1:8080"
```

---

### Proxy Validation

Checks if the proxy string is in a valid format.

```php
Proxy::validate("10.0.0.1:8080"); // true
```

Checks if a port is within the valid TCP/UDP range (1–65535).

```php
Proxy::isPort(8080); // true
```

Detects if a proxy contains login credentials.

```php
Proxy::isAuthenticated("john:pass@10.0.0.1:8080"); // true
```

Detects the proxy type (http, https, socks4, socks5).

```php
Proxy::type("socks5://1.2.3.4:1080"); // "socks5"
```

Checks if a proxy matches a specific type.

```php
Proxy::is("http://10.0.0.1:8080", "http"); // true
```

---

### Proxy Testing

Performs a full proxy test and returns detailed results including latency, HTTP status, body size, and error if any.

```php
$result = Proxy::check("10.0.0.1:8080");
// [
//     'proxy' => '10.0.0.1:8080',
//     'blocked' => false,
//     'latency' => 0.23,
//     'status' => 200,
//     'body_size' => 15,
//     'body' => ['ip'=>'10.0.0.1'],
//     'error' => null
// ]
```

Measures latency with a quick HEAD request.

```php
$latency = Proxy::ping("10.0.0.1:8080"); // 0.234
```

Resolves the public IP seen through the proxy.

```php
$ip = Proxy::resolve("10.0.0.1:8080"); // "10.0.0.1"
```

Checks if the proxy hides your IP.

```php
$isAnon = Proxy::isAnonymous("10.0.0.1:8080"); // true
```

---

### IPv6 Support

All parsing, formatting, and validation methods support IPv6 addresses. IPv6 hosts are automatically wrapped in brackets when formatting.

```php
$parts = Proxy::parse("[2001:db8::1]:8080");
// ['host'=>'2001:db8::1','port'=>8080,'username'=>null,'password'=>null,'type'=>null]

$proxyString = Proxy::format($parts);
// "[2001:db8::1]:8080"
```

---

## Class Definition

* Class namespace: `\Luminova\Utility\Proxy`
* This class is marked as **final** and can't be subclassed

---

## Methods

### validate

Validate a proxy string and confirm it follows supported formats.

The method checks:
- Host (IPv4 or IPv6)
- Optional authentication
- Optional proxy type (must match self::$types)
- Valid port number

```php
public static validate(string $proxy): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | Proxy string to validate. |

**Return Value:**

`bool` - Returns bool True when valid, false when invalid.

***

### normalize

Normalize a proxy string into a consistent and predictable format.

The normalization process:
- Removes all whitespace.
- Ensures IPv6 hosts are wrapped in brackets.
- Preserves authentication (`user:pass@`).
- Preserves proxy type prefixes (e.g. `http://`).

```php
public static normalize(string $proxy): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | The raw proxy string to normalize. |

**Return Value:**

`string` - Returns string Normalized proxy in a clean, consistent format.

***

### parts

Break a proxy string into its core components.

```php
public static parts(string $proxy): array
```

Extracts and returns:
- Host (IPv4, IPv6, or hostname)
- Port
- Authentication section (user:pass), if present
- Proxy type prefix (http, socks5, etc), if present

All whitespace is stripped automatically.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | The raw proxy string break into parts. |

**Return Value:**

`array` - Returns proxy parts as a list array in format `[host, port, auth, type]`.

***

### parse

Parse a proxy string into its components.

Extracted parts include:
- host
- port
- username (if authentication is provided)
- password (if authentication is provided)
- type (optional, e.g., http, socks5)

```php
public static parse(string $proxy): array<string,mixed>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | The raw proxy string. to parse. |

**Return Value:**

`array<string,mixed>` - Returns array Associative array with keys: `[host, port, username, password, type]`.

***

### port

Extract the port number from a proxy string.

Supports IPv4, IPv6 (bracketed), and hostname formats.

```php
public static port(string $proxy): ?int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | The raw proxy string to extract port from. |

**Return Value:**

`int|null` - Returns port number if detected, otherwise null.

***

### format

Format proxy parts into a normalized string representation.

Accepts array format from `Luminova\Utility\Proxy::parse()` or `Luminova\Utility\Proxy::parts()`.

Converts an array of proxy components into a standardized proxy string:
- Adds type prefix if present (e.g, `http://`, `socks5://`)
- Adds authentication (`username:password@`) if present
- Wraps IPv6 addresses in brackets
- Appends port if present

```php
public static format(array<string,mixed>|array<int,mixed> $parts): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$parts` | **array<string,mixed>&#124;array<int,mixed>** | List or an associative array with keys: **host, port, username, password, type**. |

**Return Value:**

`string` - Returns string Formatted proxy string suitable for usage or storage.

***

### type

Extract the proxy type from a proxy string.

```php
public static type(string $proxy): ?string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | The raw proxy string to extract type from. |

**Return Value:**

`string|null` - Returns the detected proxy type or `null` if none.

***

### check

Perform a full proxy test and return detailed results.

This method uses `https://api.ipify.org` to test proxy.

The returned array includes:
- `proxy`      **(string)** The proxy tested.
- `blocked`    **(bool)** True if the proxy is blocked or unreachable.
- `latency`    **(float|null)** Response time in seconds, or null on failure.
- `status`     **(int|null)** HTTP status code, or null on failure.
- `body_size`  **(int|null)** Size of the response body in bytes, or null on failure.
- `body`       **(array|null)** Decoded JSON response body, or null on failure.
- `error`      **(string|null)** Error message if the request failed.

```php
public static check(string $proxy, int $timeout = 0): array<string,mixed>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | The proxy string to test. |
| `$timeout` | **int** | Maximum connection timeout in seconds (default: 0 for no limit). |

**Return Value:**

`array<string,mixed>` - Returns array Detailed results of the proxy test.

***

### resolve

Resolve the public IP exposed by a proxy.

Uses `https://api.ipify.org` to detect the IP seen when routing through the proxy.
Useful to confirm the proxy is actually applied.

```php
public static resolve(string $proxy): ?string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | The proxy string to check. |

**Return Value:**

`string|null` - Returns string|null The IP address seen through the proxy, or null on failure.

***

### ping

Measure proxy latency using a quick HEAD request.

Sends a HEAD request to `https://www.google.com/` and measures response time.

```php
public static ping(string $proxy, int $timeout = 10, ?string $useragent = null): ?float
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | Proxy string to test. |
| `$timeout` | **int** | Maximum timeout in seconds (default: 10). |
| `$useragent` | **string&#124;null** | Optional User-Agent header to use. |

**Return Value:**

`float|null` - Returns float|null Latency in seconds, or null if the request failed.

***

### is

Check if a proxy string matches a given type.

> Comparison is case-insensitive.

```php
public static is(string $proxy, string $type): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | Proxy string. |
| `$type` | **string** | Expected proxy type (e.g., `http`, `socks5`). |

**Return Value:**

`bool` - Returns bool True if proxy matches type, false otherwise.

***

### isPort

Validate a TCP/UDP port number.

```php
public static isPort(string|int $port): bool
```

Checks that the port is numeric and within 1–65535.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$port` | **string&#124;int** | The port to validate. |

**Return Value:**

`bool` - Returns bool True if valid, false if invalid.

***

### isAuthenticated

Check if a proxy string contains authentication credentials.

Detects the presence of a username and/or password (`user:pass@`).

```php
public static isAuthenticated(string $proxy): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | Raw proxy string. |

**Return Value:**

`bool` - Returns bool True if proxy contains authentication, false otherwise.

***

### isBlocked

Check if a proxy is blocked, unreachable, or cannot access the given endpoint.

This method uses **Google** to sends a simple **GET** request through the proxy.
A proxy is considered **blocked** when:
- the request fails to connect,
- the endpoint returns a non-200 response,
- the response body is empty.

```php
public static isBlocked(string $proxy, int $timeout = 10, ?string $useragent = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | Proxy address to check. |
| `$timeout` | **int** | Maximum request and connection timeout in seconds (default: `10`). |
| `$useragent` | **string&#124;null** | User agent string. Defaults to a basic browser UA. |

**Return Value:**

`bool` - Returns true when the proxy is blocked or unreachable, false when it works.

***

### isAnonymous

Check if a proxy hides the client IP.

Sends a request to `https://httpbin.org/get` and inspects common leak headers:
- X-Forwarded-For
- Via
- Forwarded

```php
public static isAnonymous(string $proxy, int $timeout = 0, ?array &$headers = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$proxy` | **string** | Proxy string to test. |
| `$timeout` | **int** | Maximum connection timeout in seconds (default: 0 for no limit). |
| `$headers` | **array&#124;null** | Optional reference to capture response headers in lowercase. |

**Return Value:**

`bool` - Returns bool True if the proxy does not leak the client IP, false otherwise.

***

### toString

Convert parsed proxy parts into a formatted proxy string.

```php
public static toString(array<string,mixed> $parts): string
```

Alias of `\Luminova\Utility\Proxy::format()`.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$parts` | **array<string,mixed>** | Associative array with keys: host, port, username, password, type. |

**Return Value:**

`string` - Returns string Formatted proxy string.

---

### Trusted Public Free Proxy Sources

If you need **free proxies purely for testing Luminova’s proxy utilities**, these are the established public sources. They exist for developers, researchers, and QA work, not for anything questionable.
All of them allow you to download raw lists or fetch them directly by URL.

* [https://proxyscrape.com/free-proxy-list](https://proxyscrape.com/free-proxy-list)
* [https://freeproxylist.net](https://freeproxylist.net)
* [https://spys.one](https://spys.one)
* [https://sslproxies.org](https://sslproxies.org)
* [https://us-proxy.org](https://us-proxy.org)
* [https://github.com/monosans/proxy-list](https://github.com/monosans/proxy-list)
* [https://github.com/TheSpeedX/PROXY-List](https://github.com/TheSpeedX/PROXY-List)

> **Warning**
>
> Free public proxies are unstable. They burn out fast, rotate constantly, and are used by half the planet for stress testing.
>
> For **proxy class testing**, they’re good enough.
> For production—don’t even think about it.