# HTTP Request

***

## Overview

The Request class plays a crucial role in web applications by providing a structured and simple way to handle incoming HTTP requests and responses, providing methods for safe access and manipulation.

***

## Introduction

The `Request` class represents an incoming HTTP request received by a server. It encapsulates various aspects of the request, such as headers, query parameters, form data, uploaded files, and more. This class provides a convenient and structured way to access and manipulate incoming HTTP requests, with built-in authentication mechanisms to validate requests based on factors such as origin, proxies, or allowed domains.

***

## Properties

### server

Http server instance.

```php
public null|\Luminova\Http\Server $server
```

***

### header

Http request header instance.

```php
public null|\Luminova\Http\Header $header
```

***

### agent

Browser request user-agent information.

```php
public null|\Luminova\Http\UserAgent $agent
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

Get a value from the request method context array.

```php
public getArray(string $method, string $key, array $default = []): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | HTTP request method context. |
| `$key` | **string** | Request body key. |
| `$default` | **array** | Default value. |

**Return Value:**

`array` - Return array of HTTP request method key values.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#InvalidArgumentException) - Throws if unsupported HTTP method was passed.

***

### getBody

Get the request body as an array or json object.

```php
public getBody(bool $object = false): array|object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$object` | **bool** | Whether to return an array or a json object (default: false). |

**Return Value:**

`array|object` - Return the request body as an array or json object.

***

### getFile

Get the uploaded file information by its input name.

```php
public getFile(string $name): \Luminova\Http\File|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | File input name. |

**Return Value:**

`\Luminova\Http\File|false` - Uploaded file information or false if file not found.

**See Also:**

[File Upload Object](/http/file.md)

***

### getFiles

Get the uploaded files information irritable array.

```php
public getFiles(): false|array&lt;int,\Luminova\Http\File&gt;
```

**Return Value:**

`false|array&lt;int,\Luminova\Http\File&gt;` - Uploaded files information or false if no files found.

**See Also:**

[File Upload Object](/http/file.md)

***

### getMethod

Get the request method.

```php
public getMethod(): string
```

**Return Value:**

`string` - Return the request method in lowercased.

***

### isGet

Check if the request method is GET.

```php
public isGet(): bool
```

**Return Value:**

`bool` - Returns true if the request method is GET, false otherwise.

***

### isPost

Check if the request method is POST.

```php
public isPost(): bool
```

**Return Value:**

`bool` - Returns true if the request method is POST, false otherwise.

***

### isMethod

Check if the request method is the provided method.

```php
public isMethod(string $method): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The method to check against. |

**Return Value:**

`bool` - Returns true if the request method matches the provided method, false otherwise.

***

### getContentType

Get the request content type.

```php
public getContentType(): string
```

**Return Value:**

`string` - The request content type.

***

### getAuth

Get HTTP request header authorization [HTTP_AUTHORIZATION, Authorization].

```php
public getAuth(): string|null
```

**Return Value:**

`string|null` - Return the authorization header value or null if no authorization header was sent.

***

### isCommand

Check to see if a request was made from the command line.

```php
public isCommand(): bool
```

**Return Value:**

`bool` - Return true if the request was made from the command line.

***

### isSecure

Check if the current connection is secure

```php
public isSecure(): bool
```

**Return Value:**

`bool` - Return true if the connection is secure false otherwise.

***

### isAJAX

Check if request is ajax request, see if a request contains the HTTP_X_REQUESTED_WITH header.

```php
public isAJAX(): bool
```

**Return Value:**

`bool` - Return true if request is ajax request, false otherwise

***

### isApi

Check if the request URL indicates an API endpoint.

```php
public isApi(string|null $url = null): bool
```

This method checks if the URL path starts with '/api' or 'public/api'.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string&#124;null** | The request URL to check. |

**Return Value:**

`bool` - Returns true if the URL indicates an API endpoint, false otherwise.

***

### getQuery

Get the request url query string.

```php
public getQuery(): string
```

**Return Value:**

`string` - Return url query string.

***

### getQueries

Get current url query parameters as an array.

```php
public getQueries(): array&lt;string,mixed&gt;
```

**Return Value:**

`array&lt;string,mixed&gt;` - Url query parameters.

***

### getUri

Get current request url

```php
public getUri(): string
```

**Return Value:**

`string` - Return request url or null if not set.

***

### getPaths

Get current request url path information

```php
public getPaths(): string
```

**Return Value:**

`string` - Return request url paths.

***

### getRequestUri

Returns the requested URI (path and query string).

```php
public getRequestUri(): string
```

**Return Value:**

`string` - The raw URI (i.e. not URI decoded)

***

### getHost

Get current hostname without port, if allowed host is set it will check if host is in allowed list or patterns.

```php
public getHost(bool $extension = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$extension` | **bool** | Should throw an exception if invalid host or not allowed host (default: false). |

**Return Value:**

`string` - Return hostname.

**Throws:**

- [\Luminova\Exceptions\SecurityException](/exceptions/classes.md#SecurityException) - If host is invalid or not allowed.

***

### getHostname

Get current hostname with port if port is available.
If allowed host is set it will check if host is in allowed list or patterns.

```php
public getHostname(bool $extension = false, bool $port = true): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$extension` | **bool** | Should throw an exception if invalid host or not allowed host (default: false). |
| `$port` | **bool** | Should return hostname with port (default: true). |

**Return Value:**

`string` - Return hostname.

**Throws:**

- [\Luminova\Exceptions\SecurityException](/exceptions/classes.md#SecurityException) - If host is invalid or not allowed.

***

### getOrigin

Get the origin domain if list of trusted origin domains are specified.

```php
public getOrigin(): string|null
```

It will check if the origin is a trusted origin domain.

**Return Value:**

`string|null` - Origin domain if found and trusted, otherwise null.

***

### getPort

Get the request origin port.

```php
public getPort(): int|string|null
```

**Return Value:**

`int|string|null` - Can be a string if fetched from the server bag

> Check if X-Forwarded-Port header exists and use if available.
> 
> If not available check for server-port header if also not available return NULL as default.

***

### getScheme

Gets the request's scheme.

```php
public getScheme(): string
```

***

### getProtocol

Gets the request server protocol (e.g: HTTP/1.1).

```php
public getProtocol(): string
```

***

### getBrowser

Get user browser information.

```php
public getBrowser(): string
```

**Return Value:**

`string` - Return browser name and platform.

***

### getUserAgent

Get browser user-agent information.

```php
public getUserAgent(?string $useragent = null): UserAgent
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$useragent` | **string&#124;null** | The User Agent string. If not provided, it defaults to $_SERVER['HTTP_USER_AGENT']. |

**Return Value:**

`UserAgent` - Return user agent instance.

***

### isSameOrigin

Check if the request's origin matches the current host.

```php
public isSameOrigin(bool $subdomains = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$subdomains` | **bool** | Whether to consider subdomains or not. Default is true. |

**Return Value:**

`bool` - Returns true if the request's origin matches the current host as define in env `app.hostname`, false otherwise.

***

### isTrusted

Check if the given (hostnames, origins, proxy ip or subnet) matches any of the trusted patterns.

```php
public static isTrusted(string $input, string $context = 'hostname'): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | The domain, origin or ip address to check. |
| `$context` | **string** | The context to check (hostname, origin or proxy). |

**Return Value:**

`bool` - Return true if the input is trusted, false otherwise.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#InvalidArgumentException) - If invalid context is provided.

***

### isTrustedProxy

Check whether this request origin ip address is from a trusted proxy.

```php
public isTrustedProxy(): bool
```

**Return Value:**

`bool` - Return true if the request origin ip address is trusted false otherwise.

***

### isTrustedOrigin

Check whether this request origin is from a trusted origins.

```php
public isTrustedOrigin(): bool
```

**Return Value:**

`bool` - Return true if the request origin is trusted false otherwise.