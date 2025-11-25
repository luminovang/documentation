# HTTP Response Message Object

***

## Overview

The HTTP response object allows easy capture handling of network request responses sent through the cURL client. It provides useful methods to access the response information.

***

## Introduction

The **HTTP Response** object in Luminova represents the result of a network request made through the **Novio** or **Guzzle** client. It gives you easy access to everything that comes back from the server, the status code, response headers, body content, and other useful details. This makes it simple to inspect, debug, or process any HTTP response your application receives.

***

## Class Definition

* Class namespace: `Luminova\Http\Message\Response`
* This class implements:  [\Psr\Http\Message\ResponseInterface](https://www.php-fig.org/psr/psr-7/#33-psrhttpmessageresponseinterface), [\Stringable](https://www.php.net/manual/en/class.stringable.php)

***

## Methods

### constructor

Creates a new HTTP response object.

```php
public __construct(
   \Psr\Http\Message\StreamInterface|string $body = '',
   int $statusCode = 200, 
   array<string,mixed> $headers = [], 
   array<string,mixed> $info = [],
   string $reasonPhrase = 'OK',
   string $protocolVersion = '1.1',
   ?Luminova\Interface\CookieJarInterface $cookie = null
)
```

**Parameters:**

| Parameter         | Type                              | Description                                           |
|-------------------|-----------------------------------|-------------------------------------------------------|
| `$stream`         | **StreamInterface\|string** | Optional stream object as response.                         |
| `$statusCode`     | **int**                     | The HTTP status code of the response (default: `200`).      |
| `$headers`        | **array<string,mixed>**     | An array of response headers.                               |
| `$info`           | **array<string,mixed>**     | Additional response information from cURL (optional).       |
| `$reasonPhrase`   | **string**               | Reason phrase associated with the status code (default: `OK`). |
| `$protocolVersion` | **string**                 | The HTTP protocol version (default: '1.1').                 |
| `$cookie`         | **CookieJarInterface\|null** | Optionally HTTP cookie jar object.                         |

---

### toString

Convert the HTTP response to a formatted string.

This method generates the complete HTTP response string, including the status line, headers, and body content. It checks for the 'Content-Length' header and adds it if not present.

```php
public toString(): string
```

**Return Value:** 

`string` - Return the complete HTTP response as a string.

---

### getProtocolVersion

Retrieves the HTTP protocol version (e.g., `1.0`, `1.1`). Returns an empty string if not set.

```php
public getProtocolVersion(): string
```

**Return Value:** 

`string` - The HTTP protocol version.

---

### getReasonPhrase

Retrieves the reason phrase associated with the HTTP status code (e.g., `OK`, `Internal Server Error`).

```php
public getReasonPhrase(): string
```

**Return Value:** 

`string` - Returns the HTTP message phrase.

---

### getStatusCode

Retrieves the HTTP status code of the response.

```php
public getStatusCode(): int
```

**Return Value:** 

`int` - Returns the HTTP status code.

---

### getLength

Retrieves the request response contents length.

```php
public getLength(): int
```

**Return Value:** 

`int` - Returns the content length.

---

### getFileTime

Retrieves the file modified time for the request.

```php
public getFileTime(): int
```

**Return Value:** 

`int` - Returns the file modified time if available; otherwise, returns `-1`.

---

### getHeaders

Retrieves all response headers.

```php
public getHeaders(): array<string,array>
```

**Return Value:** 

`array` - Returns an associative array of response headers.

---

### getHeader

Retrieves a specific header value by its key.

```php
public getHeader(string $name): array<int,mixed>
```

**Parameters:**

| Parameter | Type   | Description                          |
|-----------|--------|--------------------------------------|
| `$name`   | **string** | The header key to retrieve.         |

**Return Value:** 

`array` - Returns an array of header values.

---

### getHeadersString

Convert an associative array of headers into a formatted string.
This method converts the response headers to a string representation suitable for HTTP responses, where each header is formatted as `key: value\r\n` and separated by `CRLF` Carriage Return (ASCII 13, `\r` ) Line Feed (ASCII 10, `\n`).

```php
public getHeadersString(): string
```

**Return Value:** 

`string` - Return a formatted string containing all headers, followed by an additional CRLF to signal the end of the headers section.

---

### getHeaderLine

Retrieves a comma-separated string of values for a specific header.

```php
public getHeaderLine(string $header): string
```

**Parameters:**

| Parameter | Type   | Description                          |
|-----------|--------|--------------------------------------|
| `$header` | **string** | The header key name to retrieve.   |

**Return Value:** 

`string` - Returns a string of concatenated values for the given header.

---

### hasHeader

Checks if a specific header exists in the response.

```php
public hasHeader(string $name): bool
```

**Parameters:**

| Parameter | Type   | Description                          |
|-----------|--------|--------------------------------------|
| `$name`   | **string** | The header name to check.           |

**Return Value:** 

`bool` - Returns true if the header exists, false otherwise.

---

### getBody

Retrieves the response body as a stream. This method opens a temporary stream, writes the response contents if available (and if not already a stream), and then rewinds the stream before returning it.

```php
public getBody(): StreamInterface
```

**Return Value:** 

`Luminova\Utility\Storage\Stream<StreamInterface>` - The response body as a stream object.

**Throws:**  
- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the temporary stream cannot be opened.

**See Also:**  

For more information on stream management, refer to the [Stream Documentation here](/files/data-stream.md).

---

### getContents

Retrieves the extracted response contents as a string. Use this only for non-stream request responses.

```php
public getContents(): string
```

**Return Value:** 

`string` - Returns the processed response contents.

---

### getInfo

Retrieves additional information about the response.

```php
public getInfo(): array<string,mixed>
```

**Return Value:** 

`array` - Returns an associative array of response metadata.

---

### withHeader

Returns a new instance with the specified header value replacing the existing one.

```php
public withHeader(string $name, string|string[] $value): ResponseInterface
```

**Parameters:**

| Parameter | Type                           | Description                        |
|-----------|--------------------------------|------------------------------------|
| `$name`   | **string**                    | Case-insensitive header field name. |
| `$value`  | **string\|string[]**           | Header value(s).                  |

**Return Value:** 

`ResponseInterface` - A new response object with the updated header.

**Throws:**  

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the header name or value is invalid.

---

### withoutHeader

Returns a new instance without the specified header.

```php
public withoutHeader(string $name): ResponseInterface
```

**Parameters:**

| Parameter | Type   | Description                          |
|-----------|--------|--------------------------------------|
| `$name`   | **string** | Case-insensitive header field name to remove. |

**Return Value:** 

`ResponseInterface` - Returns a new response object without the specified header.

---

### withStatus

Returns a new instance with the specified status code and optional reason phrase.

```php
public withStatus(int $code, string $reasonPhrase = ''): ResponseInterface
```

**Parameters:**

| Parameter      | Type   | Description                          |
|----------------|--------|--------------------------------------|
| `$code`        | **int** | The 3-digit HTTP status code (e.g., `1xx` to `5xx`). |
| `$reasonPhrase`| **string** | Optional reason phrase.            |

**Return Value:** 

`ResponseInterface` - A new response object with the specified status.

**Throws:**  

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the status code is invalid.

---

### withProtocolVersion

Returns a new instance with the specified HTTP protocol version.

```php
public withProtocolVersion(string $version): ResponseInterface
```

**Parameters:**

| Parameter | Type   | Description                          |
|-----------|--------|--------------------------------------|
| `$version` | **string** | HTTP protocol version (e.g., `1.1`, `1.0`). |

**Return Value:** 

`ResponseInterface` - A new response object with the specified protocol version.