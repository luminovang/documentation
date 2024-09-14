# Incoming HTTP Request Management Class

***

## Overview

The Request class plays a crucial role in web applications by providing a structured and simple way to handle incoming HTTP requests and responses, providing methods for safe access and manipulation.

***

## Introduction

The `Request` class represents an incoming HTTP request to a server, encapsulating various components such as headers, query parameters, form data, and uploaded files. This class offers a structured approach to access and manipulate incoming HTTP requests, including built-in authentication mechanisms to validate requests based on origin, proxies, or allowed domains.

## Accessing the Request Object

The request object can be accessed in multiple ways, depending on the context. You can use the global helper function, the `Factory` class, or the `Controller` class.

### Access Methods

#### 1. Using Global Helper Function

```php
$request = request();
```

#### 2. Accessing Through the Factory Class

```php
use Luminova\Application\Factory;

$request = Factory::request();
```

#### 3. Initializing the Request Instance Directly

```php
use Luminova\Http\Request;

$request = new Request();
```

#### 4. Accessing in Controller Classes

If your controller extends `BaseController`:

```php
$this->request->foo();
```

If your controller extends `BaseViewController`, you can access it in two ways:

**Direct Access:**

```php
$this->request()->foo();
```

**Initializing in the `onCreate` Method:**

You can initialize the request instance once, then access it later:

```php
$this->request(); // Initialize

// Then access it.
$this->request->foo();
```

***

## Properties

### server

Http server instance.

```php
public ?\Luminova\Http\Server $server = null;
```

***

### header

Http request header instance.

```php
public ?\Luminova\Http\Header $header = null;
```

***

### agent

Browser request user-agent information.

```php
public ?\Luminova\Http\UserAgent $agent = null;
```

***

## Methods

### getGet

Get a value from the GET request.

```php
public getGet(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP GET request.

***

### getPost

Get a value from the POST request.

```php
public getPost(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP POST request.

***

### getPut

Get a value from the PUT request.

```php
public getPut(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP PUT request.

***

### getOptions

Get a value from the OPTIONS request.

```php
public getOptions(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP OPTIONS request.

***

### getPatch

Get a value from the PATCH request.

```php
public getPatch(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP PATCH request.

***

### getHead

Get a value from the HEAD request.

```php
public getHead(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP HEAD request.

***

### getConnect

Get a value from the CONNECT request.

```php
public getConnect(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP CONNECT request.

***

### getTrace

Get a value from the TRACE request.

```php
public getTrace(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP TRACE request.

***

### getPropfind

Get a value from the PROPFIND request.

```php
public getPropfind(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP PROPFIND request.

***

### getMkcol

Get a value from the MKCOL request.

```php
public getMkcol(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP MKCOL request.

***

### getCopy

Get a value from the COPY request.

```php
public getCopy(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP COPY request.

***

### getMove

Get a value from the MOVE request.

```php
public getMove(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP MOVE request.

***

### getLock

Get a value from the LOCK request.

```php
public getLock(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP LOCK request.

***

### getUnlock

Get a value from the UNLOCK request.

```php
public getUnlock(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the value to retrieve. |
| `$default` | **mixed** | (optional) The default value to return if the key is not found. |

**Return Value:**

`mixed` - Response from HTTP UNLOCK request.

***

### getArray

Get a value from the request body as an array.

```php
public getArray(string $method, string $key, array $default = []): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The HTTP request method (e.g, `GET`, `POST`, etc..). |
| `$key` | **string** | The request body name to return. |
| `$default` | **array** | Optional default value to return (default: `[]`). |

**Return Value:**

`array` - Return array of HTTP request method key values.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if unsupported HTTP method was passed.

***

### getBody

Get the entire request body as an `Array` or `JSON` object.

```php
public getBody(bool $object = false): array|object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$object` | **bool** | Whether to return an array or a json object (default: false). |

**Return Value:**

`array|object` - Return the request body as an `array` or `json` object.

***

### getFile

Get an uploaded file object by its input name.

```php
public getFile(string $name): ?\Luminova\Http\File
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The input file name. |

**Return Value:**

`\Luminova\Http\File|null` - Return uploaded file instance or null if file input name not found.

**See Also:**

To learn more about [File Upload Object](/http/file-object.md), refer to the documentation.

***

### getFiles

Get irritable array of uploaded files information.

```php
public getFiles(): \Luminova\Http\File[]|false
```

**Return Value:**

`\Luminova\Http\File[]|false` - Return an array containing uploaded files information or false if no files found.

***

### getMethod

Get the current request method.

```php
public getMethod(): string
```

**Return Value:**

`string` - Return the HTTP request method.

***

### isGet

Check if the request method is `GET`.

```php
public isGet(): bool
```

**Return Value:**

`bool` - Returns true if the request method is `GET`, false otherwise.

***

### isPost

Check if the request method is `POST`.

```php
public isPost(): bool
```

**Return Value:**

`bool` - Returns true if the request method is `POST`, false otherwise.

***

### isMethod

Check if the request method is the provided method.

```php
public isMethod(string $method): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The method to check against (e.g, `POST`, `GET`). |

**Return Value:**

`bool` - Returns true if the request method matches the provided method, false otherwise.

***

### getContentType

Get the request content type.

```php
public getContentType(): string
```

**Return Value:**

`string` - Return the request content type or blank string if not available.

***

### getAuth

Get HTTP request header authorization from  (e.g, `HTTP_AUTHORIZATION`, `Authorization` or `REDIRECT_HTTP_AUTHORIZATION`).

```php
public getAuth(): ?string
```

**Return Value:**

`string|null` - Return the authorization header value or null if no authorization header was sent.

***

**Return Value:**

`bool` - Return true if the request was made from the command line.

***

### isSecure

Check if the current request connection is secure.

```php
public isSecure(): bool
```

**Return Value:**

`bool` - Return true if the connection is secure false otherwise.

***

### isAJAX

Check if request is `ajax` request, see if a request contains the `HTTP_X_REQUESTED_WITH` header.

```php
public isAJAX(): bool
```

**Return Value:**

`bool` - Return true if request is `ajax` request, false otherwise

***

### isApi

Check if the request URL indicates an `API` request endpoint.
This method checks if the URL path prefix matched any of  `/api` or `public/api` or your custom defined API URL prefix.

```php
public isApi(): bool
```

**Return Value:**

`bool` - Returns true if the URL indicates an `API` endpoint, false otherwise.

***

### getQuery

Get the request URL query string.

```php
public getQuery(): string
```

**Return Value:**

`string` - Return the request URL query parameters as string.

***

### getQueries

Get current URL query parameters as an associative array using the parameter name as key.

```php
public getQueries(): array&lt;string,mixed&gt;
```

**Return Value:**

`array<string,mixed>` - Return the request URL query parameters as an array.

***

### getUri

Get current request URL including the scheme, host and query parameters.

```php
public getUri(): string
```

**Return Value:**

`string` - Return the request full URL.

***

### getPaths

Get current request URL path information.

```php
public getPaths(): string
```

**Return Value:**

`string` - Return the request URL paths.

***

### getRequestUri

Returns un-decoded request URI, path and query string.

```php
public getRequestUri(): string
```

**Return Value:**

`string` - Return the raw request URI (i.e. URI not decoded).

***

### getHost

Get current request `hostname` without port, if allowed host is set it will check if host is in allowed list or patterns.

```php
public getHost(bool $extension = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$extension` | **bool** | Weather to throw an exception if invalid host or not allowed host (default: false). |

**Return Value:**

`string` - Throw if host is invalid or not allowed.

**Throws:**

- [\Luminova\Exceptions\SecurityException](/running/exceptions.md#securityexception) - Throw if host is invalid or not allowed.

***

### getHostname

Get current request `hostname` with port if port is available.
If allowed host is set it will check if host is in allowed list or patterns.

```php
public getHostname(bool $extension = false, bool $port = true): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$extension` | **bool** | Weather to throw an exception if invalid host or not allowed host (default: false). |
| `$port` | **bool** | Weather to return hostname with port (default: true). |

**Return Value:**

`string` - Return request hostname and port.

**Throws:**

- [\Luminova\Exceptions\SecurityException](/running/exceptions.md#securityexception) - If host is invalid or not allowed.

***

### getOrigin

Get the request origin domain, if the list of trusted origin domains are specified, it will check if the origin is a trusted origin domain.

```php
public getOrigin(): ?string
```

**Return Value:**

`string|null` - Return the request origin domain if found and trusted, otherwise null.

***

### getPort

Get the request origin port from `X_FORWARDED_PORT` or `SERVER_PORT` if available.

```php
public getPort(): int|string|null
```

**Return Value:**

`int|string|null` - Return either a string if fetched from the server available, or integer, otherwise null.

> Check if X-Forwarded-Port header exists and use if available.
> If not available check for server-port header if also not available return NULL as default.

***

### getScheme

Gets the request scheme name.

```php
public getScheme(): string
```

**Return Value:**

`string` - Return request scheme, if secured return `https` otherwise `http`.

***

### getProtocol

Gets the request server protocol name and version (e.g: `HTTP/1.1`).

```php
public getProtocol(string $default = 'HTTP/1.1'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$default` | **string** | The default server protocol to return if no available (default: `HTTP/1.1`). |

**Return Value:**

`string` - Return Request protocol name and version, if available, otherwise default is return `HTTP/1.1`.

***

### getBrowser

Get the request browser name and platform from user-agent information.

```php
public getBrowser(): string
```

**Return Value:**

`string` - Return browser name and platform.

***

### getUserAgent

Get request browser user-agent information.

```php
public getUserAgent(?string $useragent = null): UserAgent
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$useragent` | **string&#124;null** | The User Agent string, if not provided, it defaults to (`HTTP_USER_AGENT`). |

**Return Value:**

`UserAgent` - Return instance user-agent class containing browser information.

***

### isSameOrigin

Check if the request origin matches the current application host.

```php
public isSameOrigin(bool $subdomains = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$subdomains` | **bool** | Whether to consider sub-domains or not (default: false). |

**Return Value:**

`bool` - Returns true if the request origin matches the current host, false otherwise.

***

### isTrusted

Validates if the given (`hostname`, `origin`, `proxy ip` or `subnet`) matches any of the trusted patterns.

```php
public static isTrusted(string $input, string $context = 'hostname'): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | The domain, origin or IP address to check. |
| `$context` | **string** | The context to check input for (e.g, `hostname`). |

**Return Value:**

`bool` - Return true if the input is trusted, false otherwise.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If invalid context is provided.

**Supported Context:**

- `hostname` - Validates a host name.
- `origin` - Validates an origin hostname.
- `proxy` Validates an IP address or proxy.

> **Note:** This will consider the defined configuration in `App\Config\Security` during validation.

***

### isTrustedProxy

Check whether this request origin IP address is from a trusted proxy.

```php
public isTrustedProxy(): bool
```

**Return Value:**

`bool` - Return true if the request origin IP address is trusted false otherwise.

***

### isTrustedOrigin

Check whether this request origin is from a trusted origins.

```php
public isTrustedOrigin(): bool
```

**Return Value:**

`bool` - Return true if the request origin is trusted false otherwise.