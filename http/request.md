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

If your controller extends `Luminova\Base\BaseController` or ``Luminova\Base\BaseViewController`:

```php
$this->request->fooMethod();
```

***

## Class Definition

* Class namespace: `Luminova\Http\Request`
* This class implements:  [\Luminova\Interface\HttpRequestInterface](/interface/classes.md#httprequestinterface), [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface), [\Stringable](https://www.php.net/manual/en/class.stringable.php)

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

### constructor

Initializes a new Request object, representing an HTTP request.

This constructor optionally accept request properties like method, URI, body, files, raw body, server variables, and headers. allowing you to create a custom HTTP request.

```php
public function __construct(
    private ?string $method = null,
    private ?string $uri = null,
    private array $body = [],
    private array $files = [],
	private array $cookies = [],
    private ?string $raw = null,
    ?array $server = null,
    ?array $headers = null
)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string\|null** | Optional HTTP method used for the request (e.g., 'GET', 'POST').. |
| `$uri` | **string\|null** | Optional request URI, typically the path and query string. |
| `$body` | **array<string,mixed>** | Optional request body, provided as an associative array (e.g., `['field' => 'value']`).<br/>This may include form data, JSON, etc.. |
| `$files` | **array<string,mixed>** | Optional request files, provided as an associative array (e.g., `['field' => array|string]`). |
| `$cookies` | **array<string,mixed>** | Optional. An associative array of Cookies (e.g, $_COOKIE). |
| `$raw` | **string\|null** | Optional request raw-body string. |
| `$server` | **array<string,mixed>\|null** | Optional server variables (e.g., `$_SERVER`).<br/>If not provided, defaults to global `$_SERVER`.. |
| `$headers` | **array<string,mixed>\|null** | Optional associative array of HTTP headers.<br/>If not provided, headers request headers will be extracted from `apache_request_headers` or `$_SERVER`. |

**Example**

Create a new request-response object by passing appropriate values.

```php
$request = new Luminova\Http\Request(
    method: 'POST',
    uri: 'https://example.com/api/v1/users',
    body: ['name' => 'John Doe', 'email' => 'john@example.com'], 
    files: [
        'image' => [
            'name' => 'profile.jpg',
            'type' => 'image/jpeg',
            'tmp_name' => '/tmp/phpYzdqkD',
            'error' => 0,
            'size' => 123456
        ]
    ],
    server: [
        'REQUEST_METHOD' => 'POST',
        'REMOTE_ADDR' => '192.168.1.10',
        'HTTP_USER_AGENT' => 'Mozilla/5.0',
        'SERVER_PROTOCOL' => 'HTTP/1.1',
        'SERVER_NAME' => 'example.com',
        'REQUEST_URI' => '/api/v1/users',
    ],
    headers: [
        'Content-Type' => 'application/json',
        'Authorization' => 'Bearer some-token',
        'Accept' => 'application/json',
        'X-Custom-Header' => 'CustomValue'
    ] 
);

echo $request->getPost('name') //John Doe
```

***

### toString

Converts the request body to a raw string format based on the content type.

```php
public toString(): string
```

**Return Value:**

`string` - Return the raw string representation of the request body.

 **Supported Content Types:**

- `application/x-www-form-urlencoded`: Converts the body to a URL-encoded query string.
- `application/json`: Converts the body to a JSON string.
- `multipart/form-data`: Converts the body to a multipart/form-data format.

***

### toMultipart

Converts the request body to a `multipart/form-data` string format.

```php
public toMultipart(): string
```

**Return Value:**

`string` - Return the multipart form-data representation of the request body.

***

### setField

Set a specific field in the request body for the given HTTP method.

```php
public setField(string $field, mixed $value, ?string $method = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The name of the field to set. |
| `$value` | **mixed** | The value to assign to the field. |
| `$method` | **string\|null** | Optional HTTP method, if null the current request method will be used (e.g, `GET`, `POST`). |

**Return Value:**

`self` - Returns the instance request class.

***

### getGet

Get a field value from HTTP `GET` request method or entire fields if `$field` parameter is `null`.

```php
public getGet(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getPost

Get a field value from HTTP `POST` request method or entire fields if `$field` parameter is `null`.

```php
public getPost(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getPut

Get a value from the `PUT` request or entire fields if `$field` parameter is `null`.

```php
public getPut(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getOptions

Get a value from the `OPTIONS` request or entire fields if `$field` parameter is `null`.

```php
public getOptions(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getPatch

Get a value from the `PATCH` request method or entire fields if `$field` parameter is `null`.

```php
public getPatch(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getHead

Get a value from the `HEAD` request method or entire fields if `$field` parameter is `null`.

```php
public getHead(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getConnect

Get a value from the `CONNECT` request method or entire fields if `$field` parameter is `null`.

```php
public getConnect(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getTrace

Get a value from the `TRACE` request method or entire fields if `$field` parameter is `null`.

```php
public getTrace(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getPropfind

Get a value from the `PROPFIND` request method or entire fields if `$field` parameter is `null`.

```php
public getPropfind(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getMkcol

Get a value from the `MKCOL` request method or entire fields if `$field` parameter is `null`.

```php
public getMkcol(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getCopy

Get a value from the `COPY` request method or entire fields if `$field` parameter is `null`.

```php
public getCopy(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getMove

Get a value from the `MOVE` request method or entire fields if `$field` parameter is `null`.

```php
public getMove(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getLock

Get a value from the `LOCK` request method or entire fields if `$field` parameter is `null`.

```php
public getLock(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getUnlock

Get a value from the `UNLOCK` request method or entire fields if `$field` parameter is `null`.

```php
public getUnlock(string|null $field, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string\|null** | The field key to retrieve the value value from. |
| `$default` | **mixed** | An optional default value to return if the field is not found (default: null). |

**Return Value:**

`mixed` - Return the value from HTTP request method body based on field name.

***

### getArray

Get a value from the request body as an array.

```php
public getArray(string $field, array $default = [], ?string $method = null): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string** | The request body field name to return. |
| `$default` | **array** | Optional default value to return (default: `[]`). |
| `$method` | **string\|null** | Optional HTTP request method, if null current request method will be used (e.g, `GET`, `POST`, etc..). |

**Return Value:**

`array` - Return array of HTTP request method key values.

***

### getCookie

Retrieves the instance of cookie jar containing the cookies from the request headers.

This method parses the `Cookie` header, identifies session cookies, and initializes a cookie jar. It supports session-specific configurations and handles both session and non-session cookies.

```php
public getCookie(?string $name = null): Luminova\Interface\CookieJarInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string\|null** | An optional cookie name to pre-initialize. |

**Return Value:**

`Luminova\Interface\CookieJarInterface` - Return the cookie jar instance populated with parsed cookies.

**Line**

* See also [HTTP Cookie File Jar Class](/cookies/cookie-file-jar.md).

**Example:**

```php
print_r($request->getCookie()->getCookieNames())
```

***

### getBody

Get the entire request body as an `Array` or `JSON` object.

```php
public getBody(bool $object = false): array|object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$object` | **bool** | Whether to return an array or a JSON object (default: false). |

**Return Value:**

`array<string,mixed>|object` - Return the request body as an `array` or `json` object.

***

### getFile

Get an uploaded file instance or any generator yielding file instances for multiple files.

```php
public getFile(string $name, ?int $index = null): Generator<int, File>|File|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The file input field name. |
| `$index` | **int\|null** |  Optional file index for multiple files (default: null). |

**Return Value:**

`Generator<int, \Luminova\Http\File>|\Luminova\Http\File|null` - Return uploaded `File` instance, a generator yielding `File` instances. or null if input name was not found.

**See Also:**

To learn more about [File Upload Object](/http/files.md), refer to the documentation.

**Process File Object:**

Handle file upload based on multiple files or single file.

```php
<?php
use Luminova\Storages\Uploader;
use Luminova\Http\File;
use \Generator;

$images = $request->getFiles('images');
$config = [
    'upload_path' => root('writeable/storages/foo/'),
    'if_existed' => File::IF_EXIST_OVERWRITE
];

if ($images instanceof Generator) {
    foreach ($images as $index => $image) {
        if($image->valid()){
            $image->setConfig($config);
            $status = Uploader::upload($image);
        }
    }
}else if ($images instanceof File) {
    if($images->valid()){
        $images->setConfig($config);
        $status = Uploader::upload($images);
    }
}
```

***

### getFiles

Get raw array of original uploaded file information without any modification.

```php
public getFiles(): array
```

**Return Value:**

`array<string,array>` -  Return an array containing uploaded files information.

**Array Structure for Multiple File Uploads**

```php
[
    'images' => [
        'name'      => ['file1.jpg', 'file2.png', 'file3.gif'],
        'type'      => ['image/jpeg', 'image/png', 'image/gif'],
        'tmp_name'  => ['/tmp/phpYzdqkD', '/tmp/phpeEwEWq', '/tmp/php7sdfXy'],
        'error'     => [0, 0, 0],
        'size'      => [123456, 234567, 345678]
    ]
];
```

**Also Support Flat File Structure:**

```php
[
    'images' => [
        [
            'name' => 'file1.jpg',
            'type' => 'image/jpeg',
            'tmp_name' => '/tmp/phpYzdqkD',
            'error' => 0,
            'size' => 123456
        ],
        [
            'name' => 'file2.png',
            'type' => 'image/png',
            'tmp_name' => '/tmp/phpeEwEWq',
            'error' => 0,
            'size' => 234567
        ],
        [
            'name' => 'file3.gif',
            'type' => 'image/gif',
            'tmp_name' => '/tmp/php7sdfXy',
            'error' => 0,
            'size' => 345678
        ]
    ]
];
```

> **Note:** This structure may be developer-friendly, but it isn't the standard way PHP handles uploaded files.

***

### getMethod

Get the current request method.

```php
public getMethod(): string
```

**Return Value:**

`string` - Return the HTTP request method.

***

### getBoundary

Extract the boundary from the Content-Type header.

```php
public getBoundary(): ?string
```

**Return Value:**

`string|null` - Returns the boundary string or null if not found.

***

### getFromMultipart

Parses a multipart/form-data string into an associative array with form fields and file data.

```php
public getFromMultipart(string $data, string $boundary): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **string** | The raw multipart form data content. |
| `$boundary` | **string** | The boundary string used to separate form data parts. |

**Return Value:**

`array{param:array,files:array}` - Return an array containing two keys `params` and `files`.

- `params` - Associative array of form field names and values
- `files` - Associative array of files with metadata and binary content

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

### getUrl

Get the full URL of the current request, including the scheme, host and query parameters. (e.g, `https://example.com/foo/bar?query=123`)

```php
public getUrl(): string
```

**Return Value:**

`string` - Return the full URL of the request.

***

### getUri

Get the URI (path and query string) of the current request (e.g, `/foo/bar?query=123`).

```php
public getUri(): string
```

**Return Value:**

`string` - Return the URI of the request (path and query string).

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
public getPort(): int
```

**Return Value:**

`int` - Return the port number, otherwise default to `443` secured or `80` for insecure.

> Check if X-Forwarded-Port header exists and use if available.
> If not available check for server-port header if also not available return default port.

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

### hasField

Check if a specific field exists in the request body for the given HTTP method.

```php
public hasField(string $field, ?string $method = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string** | The name of the field to check. |
| `$method` | **string&#124;null** | Optional HTTP method, if null the current request method will be used (e.g, `GET`, `POST`). |

**Return Value:**

`bool` - Returns true if the field exists for the given method; otherwise, false.

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
public isMethod(string $method = 'GET'): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The method to check against (e.g, `POST`, `GET`). |

**Return Value:**

`bool` - Returns true if the request method matches the provided method, false otherwise.

***

### isAuth

Check if the Authorization header matches the specified type.

```php
public isAuth(string $type = 'Bearer'): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The expected type of authorization (e.g., 'Bearer', 'Basic'). |

**Return Value:**

`bool` - Returns true if the Authorization header matches the specified type, otherwise false.

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

### isProxy

Checks if the request is routed through a proxy server by inspecting various proxy-related headers.

```php
public isProxy(): bool
```

**Return Value:**

`bool` - Returns true if the request is likely from proxy, false otherwise.

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
| `$input` | **string** | The domain, origin or IP address to check (e.g, `example.com`, `192.168.0.1`). |
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