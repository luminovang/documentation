# PSR-7 HTTP URI Class with IDN Support

***

## Overview

The HTTP URI class provides methods for creating and managing URIs, including support for Internationalized Domain Names (IDNs), while adhering to the PSR-7 HTTP Message Interface standards.

***

## Introduction

The Luminova `Uri` class provides an implementation of the `Psr\Http\Message\UriInterface` for constructing, parsing, and manipulating Uniform Resource Identifiers (URIs). This class is designed to comply with the PSR-7 HTTP Message Interface standards and is capable of handling complex URI operations with including IDN (Internationalized Domain Name) support.

***

### Usage Examples

Constructs a new `Uri` instance with optional components.

```php
$uri = new Luminova\Http\Uri(
    'https',
    'username:password',
    'example.com',
    443,
    '/api/resource',
    'key=value',
    'section1'
);

echo $uri; 
// Output: https://username:password@example.com:443/api/resource?key=value#section1
```

---

### Create From Array

Create with an array of URI components.

```php
use Luminova\Http\Uri;

$uriComponents = [
    'scheme' => 'https',
    'host' => 'example.com',
    'path' => '/api/resource',
    'query' => 'key=value',
    'fragment' => 'section1'
];

$uri = Luminova\Http\Uri::fromArray($uriComponents);

// Output: https://example.com/api/resource?key=value#section1
echo $uri;
```

---

### Modifying Components

Each method returns a new instance with the modified component, maintaining immutability.

```php
$uri = new Luminova\Http\Uri('https', '', 'example.com', 443, '/path', '', '');

$newUri = $uri
    ->withScheme('http')
    ->withHost('test.com')
    ->withPath('/new-path')
    ->withQuery('id=123')
    ->withFragment('section');

echo $newUri;
// Output: http://test.com/new-path?id=123#section
```

---

### Adding User Info

```php
$uri = new Luminova\Http\Uri('https', '', 'example.com', 443);

$updatedUri = $uri->withUserInfo('user', 'password');

echo $updatedUri;
// Output: https://user:password@example.com:443
```

---

### IDN Support

Create a URI with an internationalized domain name

```php
use Luminova\Http\Uri;

$uri = new Uri('https', '', '例子.测试', 443, '/path', 'query=value', 'fragment');

// Convert the host to its ASCII representation
try {
    $asciiUri = $uri->toAsciiIdn();
    echo $asciiUri;
    // Output: https://xn--fsq.xn--0zwm56d:443/path?query=value#fragment
} catch (Luminova\Exceptions\ErrorException $e) {
    echo "Error converting to ASCII: " . $e->getMessage();
}
```

---

### With Options for IDN Conversion

You can customize the conversion process by passing specific options to the method. For example, using `IDNA_NONTRANSITIONAL_TO_ASCII`:

```php
use Luminova\Http\Uri;

$uri = new Uri('https', '', 'bücher.de', null, '/store', '', '');

// Convert the host using a non-transitional option
try {
    $asciiUri = $uri->toAsciiIdn(IDNA_NONTRANSITIONAL_TO_ASCII);
    echo $asciiUri;
    // Output: https://xn--bcher-kva.de/store
} catch (ErrorException $e) {
    echo "Error converting to ASCII: " . $e->getMessage();
}
```

***

## Class Definition

* Class Namespace: `\Luminova\Http\Uri`
* Class implements:
[\Luminova\Exceptions\LazyInterface](/running/exceptions.md#lazyinterface), [\Stringable](https://www.php.net/manual/en/class.stringable.php)

***

## Methods

### constructor

Constructs a new `Uri` instance with optional components. This initializer allows you to define a URI with individual parts, providing flexibility for creating URIs programmatically.

```php
public __construct(
    string $scheme = '', 
    string $userInfo = '', 
    string $host = '', 
    ?int $port = null, 
    string $path = '', 
    string $query = '', 
    string $fragment = ''
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$scheme` | **string** | Optional URI scheme (e.g., &quot;http&quot;, &quot;https&quot;). |
| `$userInfo` | **string** | Optional user information (e.g., &quot;username:password&quot;). |
| `$host` | **string** | Optional host (e.g., &quot;example.com&quot;). |
| `$port` | **int&#124;null** | Optional port (e.g., 80, 443, or null for default ports). |
| `$path` | **string** | Optional path (e.g., &quot;/api/resource&quot;). |
| `$query` | **string** | Optional query string (e.g., &quot;key=value&quot;). |
| `$fragment` | **string** | Optional fragment (e.g., &quot;section1&quot;). |

***

### fromArray

Creates a new Uri instance from an array of URI components.

The `fromArray` method creates a new `Uri` instance from an associative array of URI components. This static constructor is particularly useful when working with the results of functions like `parse_url`.

```php
public static fromArray(array<string,mixed> $parts): UriInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$parts` | **array<string,mixed>** | An associative array of URI components. |

**Return Value:**

`\Psr\Http\Message\UriInterface` - Return a new Uri instance populated with the provided array values.

**Supported keys in the array include:**

- **scheme**: `(string)` - The URI scheme (e.g., "http", "https").
- **user**: `(string)` - The username for authentication.
- **pass**: `(string)` - The password for authentication (appended to the user).
- **host**: `(string)` - The host portion of the URI (e.g., "example.com").
- **port**: `(int|null)` - The port number (e.g., 80, 443).
- **path**: `(string)` - The URI path (e.g., "/foo/bar").
- **query**: `(string)` - The query string (e.g., "key=value").
- **fragment**: `(string)` - The fragment identifier (e.g., "#section").

***

### getScheme

Retrieves the URI scheme component.

```php
public getScheme(): string
```

**Return Value:**

`string` - Return the URI scheme (e.g., "http", "https").

***

### getAuthority

Retrieves the URI authority component.

```php
public getAuthority(): string
```

**Return Value:**

`string` - Return the URI authority, including user info, host, and port.

***

### getUserInfo

Retrieves the user information component.

```php
public getUserInfo(): string
```

**Return Value:**

`string` - Return the user information (e.g., "username:password").

***

### getHost

Retrieves the host component.

```php
public getHost(): string
```

**Return Value:**

`string` - Return the host (e.g., "example.com").

***

### getPort

Retrieves the port component.

```php
public getPort(): ?int
```

**Return Value:**

`string` - Return the port number or null if no port is specified.

***

### getPath

Retrieves the path component.

```php
public getPath(): string
```

**Return Value:**

`string` - Return the path (e.g., "/api/resource").

***

### getQuery

Retrieves the query string component.

```php
public getQuery(): string
```

**Return Value:**

`string` - Return the query string (e.g., "key=value").

***

### getFragment

Retrieves the fragment component.

```php
public getFragment(): string
```

**Return Value:**

`string` - Return the fragment (e.g., "section1").

***

### toString

Converts the URI object to a string representation.

```php
public toString(): string
```

**Return Value:**

`string` - Return the full URI as a string.

***

### toAsciiIdn

Converts the URI's host to its ASCII representation using IDN (Internationalized Domain Names) conversion.

This method converts the host component of the URI to its ASCII representation, which is necessary for proper handling of internationalized domain names. It uses the `idn_to_ascii` function with the specified options for the conversion.

```php
public toAsciiIdn(int $option = IDNA_DEFAULT): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$option` | **int** | The option flag for IDN conversion. Defaults to IDNA_DEFAULT.<br />See the PHP manual for possible values (e.g., IDNA_NONTRANSITIONAL_TO_ASCII). |

**Return Value:**

`string` - Return the full URI string with the host converted to its ASCII representation.

**Throws:**

- [\Luminova\Exceptions\ErrorException](/running/exceptions.md#errorexception) - If IDN support is unavailable or if the conversion fails.

***

### withScheme

Returns a new instance with the specified scheme.

```php
public withScheme(string $scheme): \Psr\Http\Message\UriInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$scheme` | **string** | The new scheme (e.g., &quot;http&quot;, &quot;https&quot;). |

**Return Value:**

Return a new instance with the updated scheme.

***

### withUserInfo

Returns a new instance with the specified user info.

```php
public withUserInfo(string $user, ?string $password = null): \Psr\Http\Message\UriInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$user` | **string** | The username. |
| `$password` | **string&#124;null** | The password (optional). |

**Return Value:**

Return a new instance with the updated user info.

***

### withHost

Returns a new instance with the specified host.

```php
public withHost(string $host): \Psr\Http\Message\UriInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$host` | **string** | The new host (e.g., &quot;example.com&quot;). |

**Return Value:**

`\Psr\Http\Message\UriInterface` - Return a new instance with the updated host.

***

### withPort

Returns a new instance with the specified port.

```php
public withPort(int|null $port): \Psr\Http\Message\UriInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$port` | **int&#124;null** | The new port or null for no port. |

**Return Value:**

`\Psr\Http\Message\UriInterface` - Return a new instance with the updated port.

***

### withPath

Returns a new instance with the specified path.

```php
public withPath(string $path): \Psr\Http\Message\UriInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The new path (e.g., &quot;/api/resource&quot;). |

**Return Value:**

`\Psr\Http\Message\UriInterface` - Return a new instance with the updated path.

***

### withQuery

Returns a new instance with the specified query string.

```php
public withQuery(string $query): \Psr\Http\Message\UriInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The new query string (e.g., &quot;key=value&quot;). |

**Return Value:**

`\Psr\Http\Message\UriInterface` - Return a new instance with the updated query string.

***

### withFragment

Returns a new instance with the specified fragment.

```php
public withFragment(string $fragment): \Psr\Http\Message\UriInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fragment` | **string** | The new fragment (e.g., &quot;section1&quot;). |

**Return Value:**

`\Psr\Http\Message\UriInterface` - Return a new instance with the updated fragment.
