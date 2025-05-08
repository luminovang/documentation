# Client IP Address and Proxy Helper

***

## Overview

Manage and retrieve the client’s IP address, detect proxies or Tor exit nodes, and fetch detailed info via IPHub or IPApi.

***

## Introduction

The `IP` class helps retrieve and manage client IP addresses from request variables and headers, accounting for proxies and load balancers. It supports detailed IP lookups using third-party APIs (`ipapi.co` or `iphub.info`) and caches results locally to reduce network requests and improve performance.

***

### Usages

**1. Get User IP Address**

Use the global helper function to retrieve the user's IP address:

```php
echo ip_address();
```

**2. Get User IP Address Information**

To fetch detailed information about the user's IP address, use the helper function with a `true` parameter:

```php
$info = ip_address(true);

// IP Info
var_export($info);
```

**3. Get IP Address Instance**

To obtain an instance of the IP address class, use the global helper function in one of the following ways:

```php
$instance = func('ip');

// Or 
$instance = func()->ip();
```

> This instance allows you to interact with various IP-related methods provided by the class.

***

## Class Definition

* Class namespace: `\Luminova\Functions\IP`

---

## Methods

### get

Retrieve the client's IP address.

This method first checks if the CloudFlare `HTTP_CF_CONNECTING_IP` header is set and uses it if available. If not, it iterates through other potential headers to find the client's IP address. It also validates the IP address to ensure it is neither a private IP nor a reserved IP range.

```php
public static get(): string
```

**Return Value:**

`string` - Return the client's IP address, if failed returns `0.0.0.0` on production and `REMOTE_ADDR` on development.

***

### getLocalAddress

Get the local IP address of the machine.

This method first attempts to retrieve the machine's IP address via its hostname. 
If that fails or returns an invalid IP address, it falls back to using platform-specific shell commands (`ipconfig` on Windows or `ifconfig` on Linux/macOS) to retrieve the IP.

```php
public static getLocalAddress(): string|false
```

**Return Value:**

`string|false` - Returns the local IP address as a string, or false if unable to retrieve it.

***

### getLocalNetworkAddress

Get the local network IP address (not the loopback address).

This method attempts to retrieve the machine's IP address on the network, avoiding the loopback IP (127.0.0.1). It first checks platform-specific network interfaces using shell commands (`ipconfig` on Windows, `ifconfig` on Linux/macOS).

```php
public static getLocalNetworkAddress(): string|false
```

**Return Value:**

`string|false` - Returns the local network IP address as a string, or false if unable to retrieve it.

***

### info

Fetches and stores information about an IP address from third-party `APIs`, either `ipapi` or `iphub`. An API key is required for both providers which can be configured in `app/Config/IPConfig.php`. 

The method caches the IP address information in the `filesystem` to avoid repeated network requests. The cache is stored in the directory `/writeable/caches/ip/` with filenames in the format `ip_info_<IP_ADDRESS>.json` and does not expire automatically. The cached data remains until the cache file or directory is manually deleted.

```php
public static info(?string $ip = null, array $options = []): ?object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to look up. If `null`, uses the current IP address. |
| `$options` | **array** | Additional options for storing IP address information. |

**Return Value:**

`object|null` - Return address information as an object, or `null` if not found.

***

### isTrustedProxy

Check if the request origin IP is a trusted proxy IP or subnet. 
This method used the configuration in `app/Config/IPConfig.php` variable `trustedProxies` to determine if the ip can be trusted or not. If no proxies are set for `trustedProxies`, this will always return `false`

```php
public static isTrustedProxy(?string $ip = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to check. If `null` defaults to the current IP address. |

**Return Value:**

`bool` - Return true if the request origin IP matches the trusted proxy `IPs`, false otherwise.

***

### isValid

Validate an IP address for a specific IP version (e.g., `4`, `6`, or `0` for any). 

```php
public static isValid(?string $ip = null, int $version = 0): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to validate. |
| `$version` | **int** | The IP version to validate (e.g., `4`, `6`, or `0` for any). |

**Return Value:**

`bool` - Return true if the IP address is valid for the specified version, otherwise false.

***

### equals

Compare two IP addresses to determine if they are equal.

This method checks the equality of two IP addresses (IPv4 or IPv6) by converting them to their numeric binary representations. If either IP address is invalid, the method returns `false`. Otherwise, it compares the numeric forms to check for equality.

```php
public static equals(string $ip1, ?string $ip2 = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip1` | **string** | The first IP address to compare. |
| `$ip2` | **string&#124;null** | The second IP address to compare (default: null). |

**Return Value:**

`bool` - Returns `true` if both IP addresses are valid and equal; `false` otherwise.

> **Note:** If the second IP address (`$ip2`) is not provided, it defaults to the current client IP
     * obtained via the `self::get()` method.

***

### toNumeric

Convert an IP address to its numeric long integer representation (`IPv4` or `IPv6`).

```php
public static toNumeric(?string $ip = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to convert (default: null).<br> If null use the current client Ip. |

**Return Value:**

`string|false` - Return long integer representation of the IP address, otherwise false.

***

### toAddress

Convert a numeric or hexadecimal IP address representation to its original (`IPv4` or `IPv6`).

```php
public static toAddress(string|int|null $numeric = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$numeric` | **string&#124;int&#124;null** | The ipv4 numeric or ipv6 hexadecimal representation to convert. |

**Return Value:**

`string|false` - Return original IP address from numeric representation, otherwise false on error.

***

### toBinary

Convert IP address to binary representation (`IPv4` or `IPv6`).

```php
public static toBinary(?string $ip = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to convert. |

**Return Value:**

`string|false` - Return binary representation of an IP address, otherwise false on error.

***

### toIpv4

Convert an `IPv6` address to its IPv4-mapped representation.

```php
public static toIpv4(?string $ipv6 = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ipv6` | **string** | The IPv6 address to convert (e.g, `::ffff:192.16.0.1`). |

**Return Value:**

`string|false` - Return the IPv4-mapped address, or false if the input is not a valid IPv6 address.

***

### toIpv6

Convert an `IPv4` address to its IPv6-mapped representation.

```php
public static toIpv6(?string $ipv4 = null, bool $binary = false): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ipv4` | **string** | The IPv4 address to convert. |
| `$binary` | **bool** | Weather to return the binary representation (default: false). |

**Return Value:**

`string|false` - Return the IPv6-mapped address, or false if the input is not a valid IPv4 address.

***

### fromBinary

Convert a binary representation of an IP address to its original IP address.

```php
public static fromBinary(string $binary, bool $ipv6 = false): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$binary` | **string** | The binary representation of the IP address. |
| `$ipv6` | **bool** | Optional flag to specify if the IP address is IPv6 (default: false).<br/>If true, assumes the address is IPv6. |

**Return Value:**

`string|false` - Returns the original IP address as a string, or false if the conversion fails.

> **Note**
> If the binary is a representation of an IPV6, it will return the `IPV4` representation of the binary if you didn't specify true in the second parameter indicating it's an IPV6.

***

### isTor

Check if an IP address is a Tor exit node. If `$ip` is `null`, it uses the current IP address. 

This method performs the following actions:

1. **Network Request**: Retrieves the Tor bulk exit list from `https://check.torproject.org/torbulkexitlist`.
2. **Check IP**: Verifies if the given IP address is in the Tor exit list.
3. **Caching**: Stores the bulk list for future use to avoid repeated network requests. The cached list expires after the specified period, the default is 30 days.

```php
public static isTor(?string $ip = null, int $expiration = 2592000): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to check. |
| `$expiration` | **int** | The expiration time to request for new exit nodes from tor api (default: `2592000`). |

**Return Value:**

`bool` - Return true if the IP address is a Tor exit node, otherwise false.

**Throws:**

- [\Luminova\Exceptions\FileException](/running/exceptions.md#fileexception) - Throws if error occurs or unable to read or write to directory.

> Note: You can also use the global helper function `is_tor` to check if an IP address is a Tor exit node.