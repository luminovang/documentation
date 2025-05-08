# Handling Responses from Outgoing Network Requests

***

## Overview

The HTTP response object allows easy capture handling of network request responses sent through the cURL client. It provides useful methods to access the response information.

***

## Introduction

The `Response` object in Luminova is designed to capture and manage HTTP responses from network requests made using the `cURL` client. It offers a comprehensive set of methods to access essential information such as the status code, headers, body, and other relevant details related to the received response.

***

## Class Definition

* Class namespace: `\Luminova\Http\Message\Response`
* This class implements:  [\Stringable](https://www.php.net/manual/en/class.stringable.php)

***

## Methods

### constructor

Initializes a new network request response object.

```php
public __construct(
    private int $statusCode = 200, 
    private array<string,array<int,mixed>> $headers = [], 
    private string $contents = '', 
    private array<string,mixed> $info = [], 
    private string $reasonPhrase = 'OK', 
    private string $protocolVersion = '1.1', 
    private ?Luminova\Storages\Stream $stream = null,
    public ?Luminova\Interface\CookieJarInterface $cookie = null
): mixed
```

**Parameters:**

| Parameter         | Type                              | Description                                           |
|-------------------|-----------------------------------|-------------------------------------------------------|
| `$statusCode`     | **int**                           | The HTTP status code of the response (default: `200`). |
| `$headers`        | **array<string,array<int,mixed>>**                        | An array of response headers.                        |
| `$contents`       | **string**                       | The extracted content from the response.             |
| `$info`           | **array<string,mixed>**                        | Additional response information from cURL (optional).|
| `$reasonPhrase`   | **string**                       | Reason phrase associated with the status code (default: `OK`). |
| `$protocolVersion` | **string**                       | The HTTP protocol version (default: '1.1').         |
| `$stream`         | **Luminova\Storages\Stream\|null** | Optional stream object as response.                               |
| `$cookie`         | **Luminova\Interface\CookieJarInterface\|null** | Optionally HTTP cookie jar object.                               |

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

`string` - The reason phrase.

---

### getStatusCode

Retrieves the HTTP status code of the response.

```php
public getStatusCode(): int
```

**Return Value:** 

`int` - The HTTP status code.

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

`array` - An associative array of response headers.

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

`array` - An array of header values.

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

`string` - A string of concatenated values for the given header.

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
public getBody(): \Luminova\Storages\Stream
```

**Return Value:** 

`\Luminova\Storages\Stream` - The response body as a stream object.

**Throws:**  
- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the temporary stream cannot be opened.

**See Also:**  

For more information on stream management, refer to the [Stream Documentation here](/files/stream.md).

---

### getContents

Retrieves the extracted response contents as a string. Use this only for non-stream request responses.

```php
public getContents(): string
```

**Return Value:** 

`string` - The processed response contents.

---

### getInfo

Retrieves additional information about the response.

```php
public getInfo(): array<string, mixed>
```

**Return Value:** 

`array` - An associative array of response metadata.

---

### withHeader

Returns a new instance with the specified header value replacing the existing one.

```php
public withHeader(string $name, string|string[] $value): \Luminova\Http\Message\Response
```

**Parameters:**

| Parameter | Type                           | Description                        |
|-----------|--------------------------------|------------------------------------|
| `$name`   | **string**                    | Case-insensitive header field name. |
| `$value`  | **string\|string[]**           | Header value(s).                  |

**Return Value:** 

`Response` - A new response object with the updated header.

**Throws:**  

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If the header name or value is invalid.

---

### withoutHeader

Returns a new instance without the specified header.

```php
public withoutHeader(string $name): \Luminova\Http\Message\Response
```

**Parameters:**

| Parameter | Type   | Description                          |
|-----------|--------|--------------------------------------|
| `$name`   | **string** | Case-insensitive header field name to remove. |

**Return Value:** 

`Response` - A new response object without the specified header.

---

### withStatus

Returns a new instance with the specified status code and optional reason phrase.

```php
public withStatus(int $code, string $reasonPhrase = ''): \Luminova\Http\Message\Response
```

**Parameters:**

| Parameter      | Type   | Description                          |
|----------------|--------|--------------------------------------|
| `$code`        | **int** | The 3-digit HTTP status code (e.g., `1xx` to `5xx`). |
| `$reasonPhrase`| **string** | Optional reason phrase.            |

**Return Value:** 

`Response` - A new response object with the specified status.

**Throws:**  

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If the status code is invalid.

---

### withProtocolVersion

Returns a new instance with the specified HTTP protocol version.

```php
public withProtocolVersion(string $version): \Luminova\Http\Message\Response
```

**Parameters:**

| Parameter | Type   | Description                          |
|-----------|--------|--------------------------------------|
| `$version` | **string** | HTTP protocol version (e.g., `1.1`, `1.0`). |

**Return Value:** 

`Response` - A new response object with the specified protocol version.