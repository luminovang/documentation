# IP Address Helper

***

## Overview

Manage and retrieve the client’s IP address, detect proxies or Tor exit nodes, and fetch detailed info via IPHub or IPApi.

***

## Introduction

The **IP address class** is a utility for managing and validating IP addresses. It can retrieve a client's IP from server request data, even when the app is behind proxies or load balancers.

It also supports detailed IP lookups through third-party APIs (`ipapi.co` or `iphub.info`) and caches results locally to avoid unnecessary network calls and improve performance.

***

### Usages

#### Get Client IP Address

Retrieve the client’s IP address using the static `IP::get()` method:

```php
echo IP::get();
```

---

#### Get Client IP Address Information

Fetch detailed information about a specific IP address (or the current client IP if none is given):

```php
var_dump(IP::info('192.168.20.2', ['username' => 'peter']));
```

---

### Using Global Helper Functions

Use the `ip_address()` helper to quickly retrieve the client IP:

```php
echo Luminova\Funcs\ip_address();
```

**Get Client IP Address Information:**

Pass `true` as the first argument to fetch detailed IP information, optionally with extra metadata:

```php
$info = Luminova\Funcs\ip_address(true, ['username' => 'peter']);

var_dump($info);
```

---

#### Get an IP Class Instance

Obtain an instance of the IP class using the global `func()` helpers:

```php
$instance = Luminova\Funcs\func('ip');

// Or:
$instance = Luminova\Funcs\func()->ip();
```

> This instance provides access to all IP-related methods for advanced usage.

***

## Class Definition

* Class namespace: `Luminova\Utility\IP`

---

## Methods

### get

Resolve the real client IP address.

This method checks common proxy and CDN headers (including Cloudflare) to determine the real client IP. It validates that the IP is public (not private or reserved) and returns the first valid match.

- Checks Cloudflare's header first, then other common proxy/CDN headers.
- Skips private and reserved IP ranges to ensure only public addresses are returned.
- Caches the result for subsequent calls during the same request.
- Defaults to `0.0.0.0` in production if no valid IP is found (or `127.0.0.1` in development for easier debugging).
- Returns `0.0.0.0` automatically for CLI environments, since there’s no client.

```php
public static get(): string
```

**Return Value:**

`string` - Return the detected client IP address, or a fallback if unavailable.

***

### info

Retrieve detailed IP address information from a third-party API.

- Uses the current client IP if `$ip` is null.
- Caches responses locally to avoid repeated lookups.
- Merges any additional `$metadata` into the stored result.

Fetches and stores information about client IP address using third-party `APIs`, either **ipapi** or **iphub**, with an API key is required for both providers. 

To configured API provide and key see [App\Config\IPConfig](/configs/ip-filters.md) documentation. 

```php
public static info(?string $ip = null, array $metadata = []): ?object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to query (default: detected client IP). |
| `$metadata` | **array** | Optional metadata to associate with the lookup.. |

**Return Value:**

`object|null` - Return the resolved IP information as an object on success, or `null` on failure.

> This method caches IP lookup results in the filesystem to reduce redundant network calls.
> Cache files are stored in `/writeable/caches/ip/` as `ip_info_<IP_ADDRESS>.json` and do not expire automatically.
> To refresh data, delete the cache files manually or clear the directory during deployment, cron jobs, or maintenance scripts.

---

### expand

Expand an IP address (IPv4 or IPv6) into its full IPv6-style representation.

Converts:
- Compressed IPv6 (e.g. `2001:db8::1`) into full 8-block form  
  → `2001:0db8:0000:0000:0000:0000:0000:0001`
- IPv4 (e.g. `127.0.0.1`) into its IPv4-mapped IPv6 equivalent  
  → `0000:0000:0000:0000:0000:ffff:7f00:0001`
- IPv4-mapped IPv6 (e.g. `::ffff:127.0.0.1`) expands correctly as well.

```php
public static expand(string $ip): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string** | The IPv4 or IPv6 address to expand. |

**Return Value:**

`object|null` - Return the fully expanded IPv6-style address, or false if invalid or malformed addresses.

**Examples:**

```php
IP::expand('2001:db8::ff00:42:8329');
// '2001:0db8:0000:0000:0000:ff00:0042:8329'

IP::expand('127.0.0.1');
// '0000:0000:0000:0000:0000:ffff:7f00:0001'

IP::expand('::1');
// '0000:0000:0000:0000:0000:0000:0000:0001'
```

***

### equals

Compare two IP addresses for equality.

This method normalizes both IPv4 and IPv6 addresses to a consistent binary representation before comparing. It ensures accurate results even when the same address is written in different formats (e.g. `::1` and `0:0:0:0:0:0:0:1` are equal).

IPv4-mapped IPv6 addresses (e.g. `::ffff:192.168.0.1`) are automatically normalized to their native IPv4 form before comparison.

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

**Examples:**

```php
IP::equals('127.0.0.1', '127.0.0.1');         // true
IP::equals('::1', '0:0:0:0:0:0:0:1');         // true
IP::equals('::ffff:192.168.0.1', '192.168.0.1'); // true
IP::equals('192.168.1.1', '192.168.1.2');     // false
IP::equals('invalid', '127.0.0.1');           // false
```

> **Note:** 
> If the second IP address (`$ip2`) is not provided, it defaults to the current client IP obtained via the `IP::get()` method.

***

### isValid

Validate an IP address (IPv4 or IPv6).

This method ensures the IP is valid for the given version. It supports:
- IPv4 (e.g. `192.168.1.1`)
- IPv6 (e.g. `2001:db8::1`)
- IPv4-mapped IPv6 (e.g. `::ffff:192.168.1.1`)

If `$ip` is `null`, it automatically checks the current client IP (`self::get()`).

```php
public static isValid(?string $ip = null, int $version = 0): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to validate. If null, the current IP is used. |
| `$version` | **int** | The IP version: `4` (IPv4), `6` (IPv6), or `0` (any) (default: `0` for any). |

**Return Value:**

`bool` - Return true if the IP is valid for the specified version, false otherwise.

**Examples:**

```php
IP::isValid('192.168.1.1');            // true
IP::isValid('2001:db8::1', 6);         // true
IP::isValid('::ffff:192.168.0.1', 4);  // true
IP::isValid('::1');                    // true
IP::isValid('256.256.256.256');        // false
IP::isValid(null);                     // checks current client IP
```

**IPV4 Specific Check:**

```php
IP::isIpv4('192.168.0.1'); // true
IP::isIpv4('::1');         // false
IP::isIpv4();              // check current client IP
```

**IPV6 Specific Check:**

```php
IP::isIpv6('2001:db8::1');   // true
IP::isIpv6('192.168.1.1');   // false
IP::isIpv6();                // check current client IP
```

***

### isPrivate

Determine whether an IP address is private, loopback, or reserved.
     
Checks whether the given IP (or current client IP if null) falls within a
non-routable or special-use range:
  - Private networks: 10.0.0.0/8, 172.16.0.0–172.31.255.255, 192.168.0.0/16
  - Loopback: 127.0.0.0/8
  - Link-local: 169.254.0.0/16
  - Reserved/special IPv4 and IPv6 ranges

```php
public static isPrivate(?string $ip = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | Optional IP address. If `null`, uses current client IP. |

**Return Value:**

`bool` - Returns true if the IP is private, reserved, loopback, or non-routable; false if it's public.

**Examples:**

```php
IP::isPrivate('127.0.0.1');       // true
IP::isPrivate('192.168.1.100');   // true
IP::isPrivate('8.8.8.8');         // false
IP::isPrivate('::1');             // true
IP::isPrivate();                  // uses current client IP
```

***

### isTor

Check if the given IP address is a known Tor exit node.
     
The method checks the cached Tor exit node list (updated every `$expiration` seconds). If the list is expired or missing, it fetches a new copy.

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
| `$ip` | **string&#124;null** | The IP address to check, if `null` default to client IP.. |
| `$expiration` | **int** | Cache expiration time in seconds (default: `2,592,000` = 30 days). |

**Return Value:**

`bool` - Returns true if the IP is a known Tor exit node, otherwise false.

**Throws:**

- [Luminova\Exceptions\FileException](/error-handlers/exceptions.md#fileexception) - If unable to read or write to the cache directory.

**Examples:**
```php
IP::isTor('185.220.101.1'); // true or false
IP::isTor();                 // check current client IP
```

> **Note:** 
> Global helper function `is_tor` is also available to check if an IP address is a Tor exit node.

***

### isTrustedProxy

Check if IP can be trusted. 

This method checks if an IP address (or the current client IP) is in the list of trusted proxy **IPs** or **subnets**. It checks against the application's trusted proxy configuration (`App\Config\IPConfig->trustedProxies`). Supports both IPv4 and IPv6 addresses.

```php
public static isTrustedProxy(?string $ip = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to check. If `null` defaults to client IP address. |

**Return Value:**

`bool` - Returns true if the IP is a trusted proxy, false otherwise.

***

### getLocalAddress

Retrieve the machine's primary local IP address (IPv4 or IPv6).
     
This method first attempts to resolve the IP from the system hostname using `IP::getPrimaryAddress()`.
If that fails, it falls back to platform-specific commands:

- On Windows: executes `ipconfig`
- On Unix-like systems: executes `ifconfig`
     
The first detected IP address (IPv4 or IPv6) is returned.

```php
public static getLocalAddress(): string|false
```

**Return Value:**

`string|false` - Returns the detected local IP address, or false if it cannot be determined.

***

### getLocalNetworkAddress

Retrieve the machine’s active local network IP address (non-loopback).

This method detects and returns the first non-loopback IP address (IPv4 or IPv6)
from the system network interfaces using platform-specific commands:
- On Windows: uses `ipconfig`
- On Unix-like systems: uses `ifconfig`
     
It first tries to detect an IPv4 address, then falls back to IPv6 if none is found.
If no valid address is detected, it falls back to `IP::getPrimaryAddress()`.

```php
public static getLocalNetworkAddress(): string|false
```

**Return Value:**

`string|false` - Returns the local network IP address, or false if none can be determined.

***

### getMacAddress

Get the MAC (Media Access Control) address of the system.

This method attempts to retrieves the MAC address of the current system's network interface, 
it matches the first non-virtual, non-loopback MAC.
     
Uses system commands depending on the platform:
- Windows: `getmac`
- Unix/Linux/macOS: `ip link` or `ifconfig -a`

```php
public static getMacAddress(): string|false
```

**Return Value:**

`string|false` - Returns the first detected MAC address, or false if not found.

***

### toNumeric

Convert an IP address (IPv4 or IPv6) to its numeric (decimal) representation.
     
For IPv4, this returns a standard unsigned 32-bit decimal number.
For IPv6, this returns a large decimal number using BCMath precision.

```php
public static toNumeric(?string $ip = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to convert, if `null`, the current IP is used. |

**Return Value:**

`string|false` - Returns the numeric string representation, or false on invalid input or failure.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If BCMath extension is not available.

**Examples:**

```php
IP::toNumeric('127.0.0.1'); // "2130706433"
IP::toNumeric('::1'); // "1"
IP::toNumeric('2001:db8::1'); // "42540766411282592856903984951653826561"
IP::toNumeric('invalid-ip'); // false
```

***

### toHex

Convert an IPv4 or IPv6 address to its hexadecimal representation.
     
This method produces a consistent, reversible hex format. IPv4 returns **8** hex chars, IPv6 returns **32** hex chars, both prefixed with `0x`.

```php
public static toHex(?string $ip = null, bool $prefix = true): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to convert, if `null`, the current IP is used. |
| `$prefix` | **bool** | Whether to include prefix `0x`, (default: `true`). |

**Return Value:**

`string|false` - Returns the IP address in hexadecimal format (prefixed with `0x`), or false if the input is invalid.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If BCMath extension is not available.

**Examples:**

```php
IP::toHex('192.168.1.1');        // "0xc0a80101"
IP::toHex('2001:db8::1');        // "0x20010db8000000000000000000000001"
IP::toHex('::ffff:192.168.1.1'); // "0xc0a80101"
IP::toHex('2001:db8::1', false); // "20010db8000000000000000000000001" (no-prefix)
```

***

### toBinary

Convert an IP address to its binary representation.
     
IPv4 addresses are converted to IPv4-mapped IPv6 (::ffff:IPv4) and padded to 16 bytes to provide a consistent binary format for storage or comparison. IPv6 addresses are returned in standard network order.
     
This is useful for storing IPs in binary fields, hashing, or converting to
a hexadecimal string for human-readable or database-safe formats.

```php
public static toBinary(?string $ip = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to convert. |

**Return Value:**

`string|false` - Returns the (4/16) byte binary string, or false if the IP is invalid.

**Examples:**

```php
IP::toBinary('127.0.0.1');
// Returns: "\0\0\0\0\0\0\0\0\0\0\xff\xff\x7f\x00\x00\x01"

IP::toBinary('::1');
// Returns: "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\x01"

IP::toBinary('::ffff:192.168.1.1');
// Returns: "\0\0\0\0\0\0\0\0\0\0\xff\xff\xc0\xa8\x01\x01"
```

***

### toAddress

Convert a numeric, hexadecimal, or binary IP back to a readable (`IPv4` or `IPv6`) address.
     
Supports:
 - Decimal IPv4 (e.g. "2130706433")
 - Hexadecimal with optional "0x" prefix (e.g. "0x20010db8...")
 - Binary input (e.g. output from `IP::toBinary()` or `inet_pton()`)

```php
public static toAddress(string|int|null $data = null, ?int $version = null): string|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **string&#124;int&#124;null** | The IPv4 numeric or IPv6 hex/binary representation to convert. |
| `$version` | **int&#124;null** | The IP version to enforce: `4` for IPv4, `6` for IPv6, or null to auto-detect. |

**Return Value:**

`string|false` - Returns the converted IP address, or false on invalid input.

**Examples:**
```php
IP::toAddress(2130706433); // "127.0.0.1"
IP::toAddress('0x20010db8000000000000000000000001'); // "2001:db8::1"
IP::toAddress('42540766411282592856903984951653826561'); // "2001:db8::1"
IP::toAddress(IP::toBinary('2001:db8::ff00:42:8329')); // "2001:db8::ff00:42:8329"
```

***

### toIpv4

Convert an **IPv6** (or **IPv4-mapped IPv6**) to its IPv4 dotted form when possible.
     
- If the input is already a valid IPv4 string, it is returned as-is.
- If the input is a valid IPv6 that embeds an IPv4 address (mapped or compatible),
  the embedded IPv4 is returned (e.g. `::ffff:127.0.0.1` => `127.0.0.1`, `::127.0.0.1` => `127.0.0.1`).
- Otherwise returns false.

```php
public static toIpv4(?string $ipv6 = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ipv6` | **string** | IPv4/IPv6 string. If `null`, uses client IP (e.g, `::ffff:192.16.0.1`). |

**Return Value:**

`string|false` - Returns IPv4 dotted string or false when conversion not possible.

**Examples:**
```php
IP::toIpv4('127.0.0.1')           // "127.0.0.1"
IP::toIpv4('::ffff:192.168.0.1') // "192.168.0.1"
IP::toIpv4('::127.0.0.1')        // "127.0.0.1"
IP::toIpv4('2001:db8::1')        // false
```

***

### toIpv6

Convert an IPv4 address to its IPv6-mapped (or compatible) representation.
     
- If the input is already a valid IPv6 address, it is returned as-is.
- If the input is a valid IPv4 address, it will be mapped to IPv6 as `::ffff:a.b.c.d`.
- Returns false for invalid input.

```php
public static toIpv6(?string $ip = null, bool $compatible = false): string|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ipv4` | **string** | The IPv4 address to convert, uses client IP if `null`. |
| `$compatible` | **bool** | Whether to return an IPv4-compatible (::a.b.c.d) instead of mapped (`::ffff:a.b.c.d`). |

**Return Value:**

`string|false` - Returns the IPv6 string (or binary if $binary = true), or false if invalid.

**Examples:**
```php
IP::toIpv6('127.0.0.1')          // "::ffff:127.0.0.1"
IP::toIpv6('192.168.0.1')        // "::ffff:192.168.0.1"
IP::toIpv6('::1')                // "::1"
IP::toIpv6('127.0.0.1', true) // "::127.0.0.1"
```

***

### toVersion

Convert an IP address to a specific IP version or automatically flip to the other version.

Converts the given IP address to either IPv4 or IPv6 format, depending on the specified version. If an IP is provided and no version was provided, it flips the original IP to other version.

```php
public static toVersion(?string $ip = null, ?int $version = null): string|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ipv4` | **string** | The IP address to convert. If `null`, uses client's IP. |
| `$compatible` | **bool** | The target IP version: 4 for IPv4, 6 for IPv6. If `null`, flip `IPv(6<>4)`. |

**Return Value:**

`string|false` - Returns the converted IP address on success, or false if conversion fails.

**Examples:**
```php
IP::toVersion('2001:db8::1', 4); // Returns '::ffff:192.0.2.33' (if mappable)
IP::toVersion('192.0.2.33', 6);  // Returns '::ffff:192.0.2.33'
IP::toVersion('192.0.2.33');     // Returns '::ffff:192.0.2.33' (v6)
```

> **Note:**
> This method will always return a mapped IPV4, when converting IPV4 to IPV6.
> To get IPv4-compatible use `IP::toIpv6()` instead.