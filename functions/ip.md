# IP Address

***

## Overview

Get the user&#039;s IP address or retrieve the IP address information using a third-party API provider for IP address information.

***

## Introduction

The `IPAddress` class is a helper class designed to retrieve user IP address or IP address information using third-party IP information APIs to obtain more details about the user's IP address. Once the IP address information is received, the class stores it for subsequent requests, reducing the number of requests to the third-party API.

***

### Helper Functions

Global helper function to get user ip address.

```php
<?php
echo ip_address();
```

To get user ip address information.

```php
<?php
$info = ip_address(true);
var_export($info);
```

Global helper function to get ip address instance.
```php
<?php
$instance = func('ip');
```

* Class namespace: `\Luminova\Functions\IPAddress`

## Methods

### get

Get the client's IP address.

```php
public static get(): string
```

**Return Value:**

`string` - The client's IP address or '0.0.0.0' if not found.

***

### info

Get IP address information from third party API and store it for later use.

```php
public static info(string|null $ip = null, array $options = []): null|object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | Ip address to lookup if null it will use current ip address |
| `$options` | **array** | Optional information to store user ip address with. |

**Return Value:**

`null|object` - Return ip address information or null if not found.

***

### isTrustedProxy

Check if the request origin IP matches any of the trusted proxy IP addresses or subnets.

```php
public static isTrustedProxy(?string $ip = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The origin IP address |

**Return Value:**

`bool` - Return true if the request origin IP matches the trusted proxy IPs, false otherwise.

***

### isValid

Check if an IP address is valid ip-version (e.g: `ipv4` or `ipv6`).

```php
public static isValid(?string $address = null, int $version): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string&#124;null** | The IP address to validate. |
| `$version` | **int** | The IP version to validate (4 for IPv4, 6 for IPv6 or 0 for any of the ipVersion). |

**Return Value:**

`bool` - True if the IP address is valid, false otherwise.

***

### toNumeric

Convert an IP address to its numeric representation (IPv4 or IPv6).

```php
public static toNumeric(?string $address = null): int|string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string&#124;null** | The IP address to convert. |

**Return Value:**

`int|string` - Numeric IP address or empty string on error.

***

### toAddress

Convert a numeric IP address to its string representation (IPv4 or IPv6).

```php
public static toAddress(int|string $numeric = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$numeric` | **int&#124;string** | The numeric IP address to convert. |

**Return Value:**

`string` - IP address in string format or empty string on error.

***

### isTor

Checks if the given IP address is a Tor exit node

```php
public static isTor(string|null $ip = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | Ip address if null it will use current user's IP. |

**Return Value:**

`bool` - Return true if the IP address is a Tor exit node.

> Global helper function to check if IP address is tor `is_tor()`