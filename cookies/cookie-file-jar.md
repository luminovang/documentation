# Managing Cookies with a Cookie FileJar for HTTP Requests

***

## Overview

The cookie file jar provides an easy way to manage cookies when making HTTP requests with the Luminova cURL client or retrieving an incoming HTTP request cookie header.

***

## Introduction

Cookie file jar provides an easy way to manage cookies when making HTTP requests with Luminova cURL client or retrieving incoming HTTP request cookie header.

The Luminova framework simplifies cookie management during HTTP requests using the `Luminova\Http\Client\Curl` client. The `CookieFileJar` class provides a interface and methods for handling cookies by storing them in a file and reusing them across multiple requests. This makes it ideal for maintaining session states or persisting authentication cookies.

Additionally, the `CookieFileJar` class processes incoming HTTP requests that include a `Cookie` header. It integrates with the [Incoming HTTP Request Class](/http/request.md), enabling easy access to all cookie information associated with the current request object.

### Key Features

- **Persistent Storage**:  
  Cookies are saved to the specified file only if the `persistCookie` option is set to `true` in the configuration array.  
- **Flexible Domain and Path Management**:  
  By default, the cookie domain and path are automatically derived from the request URL. However, you can override this behavior by specifying a custom domain and path in the configuration during initialization.

***

### Cookie Array Format

When initializing or working with an array of cookies (e.g., in methods like `newCookie` or `toNetscapeCookies`), the array must follow this specific structure:

1. **Lowercase Option Keys**:  
   All cookie option keys should be in lowercase.

2. **Cookie Name as Key**:  
   The cookie name is used as the array key.

3. **Options as Nested Array**:  
   Cookie options are stored as a child array under the `options` key, while the `value` key holds the cookie's value.

#### Example

```php
$cookies = [
    "CookieName" => [
        "value" => "64ke1i31a93f",
        "options" => [
            "prefix"   => "optional-prefix",
            "raw"      => false, 
            "expires"  => "Thu, 31 Oct 2080 16:12:50 GMT",
            "path"     => "/",
            "domain"   => ".example.com",
            "secure"   => true,
            "httponly" => true,
            "samesite" => "Lax"
        ]
    ],
    //...
];
```

This structure ensures consistency and compatibility across cookie-related operations.

***

#### Implementing Cookie Jar in an HTTP Request

Below is an example that demonstrates how to use the `CookieFileJar` with Luminova cURL Network client class:

```php
use Luminova\Http\Network;
use Luminova\Http\Client\Curl;
use Luminova\Cookies\CookieFileJar;

// Create a CookieFileJar to manage cookies
$cookies = new CookieFileJar(root('/writeable/temp/') . 'cookies.txt', [
    'netscapeFormat' => true    // Store cookies in Netscape format
]);

// Optionally set new cookie before request
$cookies->set('Foo', 'Bar', [
    'expires' => 3600
]);

// Initialize the HTTP client with custom headers
$network = new Network(new Curl([
    'headers' => [
        'Content-Type' => 'application/json'
    ],
    'cookies' => $cookies, // Pass the CookieFileJar instance
]));

// Make a GET request with cookies
$response = $network->get('http://example.com');

// Output the response content
echo $response->getContents();

// Retrieve a specific cookie value
(!$response->cookie->isEmpty()){
    $foo = $response->cookie->getCookie('cookie-foo');
    echo $foo->getValue();
    echo $foo->getOption('httponly');
}

var_dump($response->cookie->getCookieNames());
```

***

## Class Definition

* Class namespace: `\Luminova\Cookies\CookieFileJar`
* This class implements:
[\Luminova\Interface\CookieJarInterface](/interface/classes.md#cookiejarinterface), [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface), [\Stringable](https://www.php.net/manual/en/class.stringable.php), [\Countable](https://www.php.net/manual/en/class.countable.php)

***

## Methods

### Initialization Configuration

This property defines customizable initialization options for handling cookies, such as their name, domain, path, and storage preferences. These settings influence how cookies are managed and stored during a session or across requests.

**Configuration Keys:**
- **name** `(string|null)`: Sets the default cookie name to manage. The cookie's name. Can also be set after initialization. Default is `null`.
- **domain** `(string|null)`: Specifies the domain for the cookie. If set, it overrides domain extraction from the request URL. Default is `null`.
- **path** `(string|null)`: Specifies the path for the cookie. If set, it overrides path extraction from the request URL. Default is `null`.
- **newSession** `(bool)`: Indicates whether the cookie starts a new session (uses PHP cURL's `CURLOPT_COOKIESESSION`). Default is `false`.
- **netscapeFormat** `(bool)`: Determines whether cookies are stored in Netscape format instead of a custom JSON format. Default is `false`.
- **readOnly** `(bool)`: If set to `true`, the cookie jar operates in read-only mode, only used during the current session. Cookies will not be written to the file, and persistent storage is disabled. Defaults to `false`.
- **emulateBrowser** `(bool)`: Determines whether cookies should be handled in a way that mimics browser behavior. When set to `true`, cookies are sent using the `Cookie` header as part of the request headers (via `CURLOPT_HTTPHEADER`). If `false`, cookies are sent using the `CURLOPT_COOKIE` option in cURL. This can be useful for scenarios where specific header-based cookie handling is required to emulate browser-like interactions. Default is `false`.

**Example usage of cookie configuration:**

```php
$cookieJar = new CookieFileJar('path/to/cookie.txt', [
    'name' => 'SessionID',
    'domain' => '.example.com',
    'path' => '/',
    'newSession' => false,
    'netscapeFormat' => false,
    'readOnly' => false,
    'emulateBrowser' => false,
]);
```

***

### constructor

Constructor to initialize cookies from a file path or an array.

```php
public __construct(string|array<string,array<string,mixed> $from, array<string,mixed> $config = []): mixed
```

If a string is provided, it is treated as a file path, and cookies are loaded from the file.
If an array is provided, it is directly used as the cookies array.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$from` | **string&#124;array<string,array<string,mixed>>** | The source of cookies, either a file path (string) or an array of cookies. |
| `$config` | **array<string,mixed>** | Optional settings or configurations for cookies. |

***

### newCookie

Create a new cookie instance.

```php
public static newCookie(array<string,array<string,mixed> $cookies, array<string,mixed> $config = []): \Luminova\Interface\CookieJarInterface
```

This method creates a new cookie instance with the specified array cookies.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$cookies` | **array<string,array<string,mixed>>** | An associative array of cookies where<br />the key is the cookie name and the value contains the cookie data and options. |
| `$config` | **array<string,mixed>** | Optional configurations or settings to apply to the<br />new or updated cookie. |

**Return Value:**

`\Luminova\Interface\CookieJarInterface` - Returns the created or updated cookie jar instance.

**Example:**

Define cookies:
 
```php
$cookies = [
    'SessionID' => [
        'value' => 'abc123',
        'options' => [
            'path' => '/',
            'domain' => '.example.com',
            'secure' => true,
            'httponly' => true,
            'samesite' => 'Lax',
        ],
    ]
];

$cookieJar = CookieHandler::newCookie($cookies, [
'netscapeFormat' => true
]);

// Retrieve a specific cookie
$cookie = $cookieJar->getCookie('SessionID');
echo "Name: SessionID, Value: {$cookie->getValue()}";
```

***

### set

Set a cookie value by name with additional options.

```php
public set(mixed $name, mixed $value, array $options = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **mixed** | The name of the cookie. |
| `$value` | **mixed** | The value of the cookie. |
| `$options` | **array** | An array of cookie options. |

**Return Value:**

`self` - Return instance of Cookie Jar.

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

`self` - Return instance of Cookie Jar.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### setOptions

Set cookie options.

```php
public setOptions(\App\Config\Cookie|array<string,mixed> $options): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **\App\Config\Cookie&#124;array<string,mixed>** | An array of cookie options or cookie config class object. |

**Return Value:**

`self` - Return instance of Cookie Jar.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### setCookiePath

Set a custom path for retrieving request cookies.

This path will be used when filtering cookies instead of extracting the path from a request URL.

```php
public setCookiePath(string $path): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The custom path to set. |

**Return Value:**

`self` - Returns the current instance for method chaining.

> **Note:** If set a custom cookie request path, you must also set domain `setCookieDomain`.

***

### setCookieDomain

Set a custom domain for retrieving request cookies.

```php
public setCookieDomain(string $domain): self
```

This domain will be used when filtering cookies instead of extracting the domain from a request URL.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$domain` | **string** | The custom domain to set. |

**Return Value:**

`self` - Return instance of Cookie Jar.

> **Note:** If set a custom cookie request domain, you must also set path `setCookiePath`.

***

### setCookies

Set cookie from an array.

```php
public setCookies(array<string,array<string,mixed>> $cookies): self;
```
**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$cookies` | **array<string,array<string,mixed>>** | The array of cookies to set. |

**Return Value:**

`self` - Return instance of Cookie Jar.

***

### get

Retrieve cookie information by it's name.

```php
public get(?string $name): array<string,mixed>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string&#124;null** | The cookie name to retrieve. |

**Return Value:**

`array<string,mixed>` - Return the value of the cookie name or entire cookies if null is passed.

***

### getCookie

Set a specify cookie name to make ready for access or manage.

```php
public getCookie(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of cookie to access. |

**Return Value:**

`self` - Return instance of cookie class.

**Example:**

Manage Cookie By Name:

```php
$cookie = $cookieJar->getCookie('my-cookie');

echo $cookie->getPrefix();
echo $cookie->getDomain();
echo $cookie->toString();
```

***

### getCookies

Retrieves the entire cookies from the source.

```php
public getCookies(): array<string,array<string,mixed>
```

If cookies have already been loaded, they are returned directly.
If a file path is provided, the cookies are loaded from the file.
If no file path is provided, an empty array is returned.

**Return Value:**

`array<string,array<string,mixed>` - Return an associative array of cookies where keys are the cookie names.

***

### getCookieNames

Retrieves an array of all cookie names in cookie jar.

```php
public getCookieNames(): array<int,string>
```

**Return Value:**

`array<int,string>` - Return all loaded cookie names.

***

### getFromHeader

Parse and retrieve cookies from `Set-Cookie` header values.

```php
public static getFromHeader(array<int,string> $headers, bool $raw = false, array<string,mixed> $default = []): array<string,array<string,mixed>
```

This method processes an array of `Set-Cookie` header strings to extract cookie names, values, and options. And returns an array containing the extracted cookies.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$headers` | **array<int,string>** | An array of `Set-Cookie` header strings to parse. |
| `$raw` | **bool** | Whether to handle cookies as raw values (default: false). |
| `$default` | **array<string,mixed>** | Optional default options to apply to cookies. |

**Return Value:**

`array<string,array<string,mixed>` - Return an associative array of extracted cookies, where the key is the cookie name and the value contains the cookie data and options.

**Example:**

Set cookies from header
 
 ```php
$headers = [
    "SessionID=abc123; Path=/; HttpOnly",
    "UserID=42; Secure; Max-Age=3600",
];

$defaultOptions = [
    'domain' => 'example.com',
];

// Parse cookies from headers
$cookies = $cookieJar->getFromHeader($headers, false, $defaultOptions);

$newCookieJar = new CookieJar($cookies, [
    'readOnly' => true
]);

echo $newCookieJar->getCookie('SessionID')->getValue();
```

***

### getFromGlobal

Parse and retrieve cookies from global variable `$_COOKIE` values.

```php
public static getFromGlobal(array<string,mixed> $cookies, bool $raw = false, array<string,mixed> $default = []): array<string,array<string,mixed>
```

This method processes an array of `Cookies` key-pir value to extract cookie names, values and returns an array containing the extracted cookies.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$cookies` | **array<string,mixed>** | An array of `$_COOKIE` to parse. |
| `$raw` | **bool** | Whether to handle cookies as raw values (default: false). |
| `$default` | **array<string,mixed>** | Optional default options to apply to cookies. |

**Return Value:**

`array<string,array<string,mixed>` - Return an associative array of extracted cookies, where the key is the cookie name and the value contains the cookie data and options.

**Example:**

Set cookies from header
 
 ```php
$cookies = [
    "SessionID" => "abc123",
    "UserID" => 42,
];

$defaultOptions = [
    'domain' => 'example.com',
];

// Parse cookies from global
$cookies = $cookieJar->getFromGlobal($_COOKIE, false, $defaultOptions);

// Or parse cookies from array
//$cookies = $cookieJar->getFromGlobal($cookies, false, $defaultOptions);

$newCookieJar = new CookieJar($cookies, [
    'readOnly' => true
]);

echo $newCookieJar->getCookie('SessionID')->getValue();
```

***

### getCookieFile

Retrieves the cookie storage jar filename.

```php
public getCookieFile(): ?string
```

**Return Value:**

`string|null` - Return the cookie filepath and filename or null.

***

### getConfig

Retrieves the cookie configuration options.

```php
public getConfig(): array<string,mixed>
```

**Return Value:**

`array<string,mixed>` - Return an array of cookie global configuration.

***

### getCookieByDomain

Retrieve all cookies for a specific domain and path. Only unexpired cookies are returned.

```php
public getCookieByDomain(string $domain, string $path = '/'): array<string,array>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$domain` | **string** | The domain to filter cookies. |
| `$path` | **string** | The path to filter cookies. Defaults to '/'. |

**Return Value:**

`array<string,array>` - Return an array of cookies for the specified domain and path.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - If the domain is empty.

***

### getCookieStringByDomain

Retrieve cookies for a specific domain and path as a formatted string.

```php
public getCookieStringByDomain(?string $url = null, bool $metadata = false): ?string
```

Only unexpired cookies are included.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string&#124;null** | The request URL to extract domain and path.<br />If null, defaults to set domain (`setCookieDomain`) and path (`setCookiePath`). |
| `$metadata` | **bool** | Weather to include metadata (e.g., `Expires`, `Max-Age` etc...).<br/>This can be used for testing purposes or to emulate a browser's behavior.. |

**Return Value:**

`string|null` - Return a semicolon-separated cookie string, or null if no cookies match.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - If no valid domain is found or set.

***

### getName

Get the cookie name if specified.

```php
public getName(): ?string
```

**Return Value:**

`string|null` - Return the name of the cookie or null string if not specified.

***

### getOptions

Get the options associated with the cookie name if name is specified.

```php
public getOptions(): array<string,mixed>
```

**Return Value:**

`array<string,mixed>` - Return the options associated with the cookie.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### getOption

Get an option value associated with the cookie name

```php
public getOption(string $key): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The option key name to retrieve. |

**Return Value:**

`mixed` - Return the option value associated with the cookie-option key.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### getValue

Get the cookie value if name is specified.

```php
public getValue(bool $as_array = false): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$as_array` | **bool** | Weather to return value as an array if its a valid json string (default: false). |

**Return Value:**

`mixed` - Return the value of the cookie.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### getDomain

Get the domain associated with the cookie, if name is specified.

```php
public getDomain(): string
```

**Return Value:**

`string` - Return the domain associated with the cookie.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### getPrefix

Get the prefix associated with the cookie.

```php
public getPrefix(): string
```

**Return Value:**

`string` Return the prefix associated with the cookie.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### getMaxAge

Get the maximum age of the cookie.

```php
public getMaxAge(): int
```

**Return Value:**

`int` Return the maximum age of the cookie.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### getPath

Get the path associated with the cookie.

```php
public getPath(): string
```

**Return Value:**

`string` - The path associated with the cookie.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### getSameSite

Get the SameSite attribute of the cookie.

```php
public getSameSite(): string
```

**Return Value:**

`string` - Return the SameSite attribute of the cookie.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### getPrefixedName

Get the prefixed name of the cookie.

```php
public getPrefixedName(): string
```

**Return Value:**

`string` - Return the prepended prefix name of the cookie.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### getExpiry

Get the cookie expiration time.

```php
public getExpiry(bool $return_string = false): string|int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$return_string` | **bool** | Weather to retrieve unix-timestamp or formate cookie datetime string |

**Return Value:**

`string|int` - Return cookie expiration timestamp or formatted string presentation.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### count

Count the number of cookies in storage.

```php
public count(): int
```

**Return Value:**

`int` - Return the number of cookies in storage.

***

### size

Calculates the total size of all cookies in bytes.

```php
public size(): int
```

This method iterates through all cookies in the jar, calculates their size in bytes, and caches the result to avoid redundant recalculations. The size includes the name-value pairs for each cookie.

**Return Value:**

`int` - Return total size of the cookies in bytes.

***

### has

Check if a cookie with name exists.

```php
public has(string $name): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The key of the cookie to check. |

**Return Value:**

`bool` - Return true if the cookie name exists, false otherwise.

***

### hasPrefix

Check if a cookie name has a prefix.

```php
public hasPrefix(?string $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string&#124;null** | An optional cookie name to check for prefix,<br />if null will use name from `getCookie()` method or from config name key. |

**Return Value:**

`bool` - Return true if the cookie name has a prefix, false otherwise.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### clear

Clear the entire cookie stored in file jar.

```php
public clear(): bool
```

**Return Value:**

`bool` - Return true if the cookie was cleared, false otherwise.

***

### delete

Remove a cookie by name.

```php
public delete(?string $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string&#124;null** | An optional cookie name to remove,<br />if null will use name from `getCookie()` method or from config name key. |

**Return Value:**

`bool` - Return true if the cookie was removed, false otherwise.

***

### forceExpire

Force mark a cookie as expired.

```php
public forceExpire(?string $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string\|null** | An optional cookie name to expire, if null will use name from `getCookie()` method or from config name key. |

**Return Value:**

`bool` - Return true if cookie has expired, otherwise false.

***

### isSecure

Check if the cookie is secure.

```php
public isSecure(): bool
```

**Return Value:**

`bool` - Return true if the cookie is secure, false otherwise.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### isHttpOnly

Check if the cookie is HTTP-only.

```php
public isHttpOnly(): bool
```

**Return Value:**

`bool` - Return true if the cookie is HTTP-only, false otherwise.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### isRaw

Check if the cookie value is raw.

```php
public isRaw(): bool
```

**Return Value:**

`bool` - Return true if the cookie value is raw, false otherwise.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### isExpired

Check if the cookie has expired.

```php
public isExpired(): bool
```

**Return Value:**

`bool` - Return true if the cookie has expired otherwise false.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### isSubdomains

Check weather the cookie include subdomains.

```php
public isSubdomains(): bool
```

**Return Value:**

`bool` - Return true if subdomain is included, false otherwise.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.
- 
***

### isEmpty

Checks if the cookie jar is empty.

```php
public isEmpty(): bool
```

This method determines whether the cookie jar contains any cookies.
It returns true if there are no cookies stored, and false otherwise.

**Return Value:**

`bool` - Returns true if the cookie jar is empty, false otherwise.

***

### isReadOnly

Checks if the cookie jar configuration is set to read-only mode.

```php
public isReadOnly(): bool
```

Determines whether the cookie jar is in read-only mode, preventing any modifications or persistent storage of cookies. This behavior is controlled by the `readOnly` configuration key during initialization.

**Return Value:**

`bool` - Returns true if the cookie jar is read-only, otherwise false.

***

### isNewSession

Check weather cookie configuration is marked as a new session.

```php
public isNewSession(): bool
```

**Return Value:**

`bool` - Return true if marked as a new session, false otherwise.

**See Also:**

* https://www.php.net/manual/en/function.curl-setopt.php 

***

### isNetscapeCookie

Check weather cookie configuration is set to be read and write as netscape cookie format.

```php
public isNetscapeCookie(): bool
```

**Return Value:**

`bool` - Return true if the cookie should be in netscape cookie format, false otherwise.

***

### isEmulateBrowser

Check weather cookie configuration is set to emulate browser behavior during requests, by setting cookies in the request header object `Cookie` instead of `CURLOPT_COOKIE`.

```php
public isEmulateBrowser(): bool
```

**Return Value:**

`bool` - Return true if should emulate browser, false otherwise.

***

### toString

Get the current cookie name object to a string header value format.

```php
public toString(bool $metadata = false): string;
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$metadata` | **bool** | Weather to include metadata (e.g., `Expires`, `Max-Age` etc...).<br/>This can be used for testing purposes or to emulate a browser's behavior.. |

**Return Value:**

`string` - Return the header string representation of the Cookie object.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

**Example:**

```php
echo $cookieJar->toString(true);
```

**Output:**

```txt
PHPSESSID=jal8an935c; Expires=Fri, 01-Nov-2080 16:24:40 GMT; Max-Age=1764361606; Path=/; Domain=.example.com; Secure; HttpOnly; SameSite=Lax
```

***

### toArray

Convert the current cookie name to an array.

```php
public toArray(): array<string,mixed>
```

**Return Value:**

`array<string,mixed>` - Return an array representation of the Cookie object.

**Throws:**

- [\Luminova\Exceptions\CookieException](/running/exceptions.md#cookieexception) - Throws if called without cookie name specified.

***

### toNetscapeCookies

Converts an array of cookies to a Netscape-formatted cookie file string.

```php
public toNetscapeCookies(?array $cookies = null): string
```

This method generates a string representation of cookies in the Netscape cookie file format.
If no cookies are provided or available, it returns null.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$cookies` | **array&#124;null** | An optional array of cookies to convert. If null, uses the instance's cookies.<br />Each cookie should be an associative array with 'value' and 'options' keys. |

**Return Value:**

`string` - Return a string containing the Netscape-formatted cookies, or an empty string if no cookies are available.

**Example:**

Convert all loaded cookies or specified cookies to netscape-formatted cookies.

```php
echo $cookieJar->toNetscapeCookies();
```

**Output:**

```txt
#HttpOnly_.example.com TRUE / TRUE 3497703880 PHPSESSID jal8an935c
```

> **Note:** The specified cookies must adhere to the cookie array format as [described above](cookies/cookie-file-jar#lmv-docs-cookie-array-format)