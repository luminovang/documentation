# Rendering Custom Output in Controller Methods

***

## Overview

Response class allows you to render or download content without processing, which gives you full control over the output and processing of the content.

***

## Introduction

The `View Response` class simplifies the rendering of content within your application's controller methods or routing closures. Unlike the [Template View](/templates/response.md) class, this class allows you to render content directly without additional processing, giving you complete control over the output.

This is suitable to use in responding `APIs` content without an addition overhead in processing the contents.

**Key Features:**

- **Direct Rendering:** Easily render content within your application's controllers or routes without reprocessing.
- **Global Access:** Use the global procedural function to access the class instance without explicit instantiation.

**Limitations:**

- **No View Caching:** This method does not support view caching. Implement custom caching if needed.
- **No Template Support:** This class does not support the use of `Smarty` or `Twig` templates.

***

* Class namespace: `\Luminova\Template\Response`

***

### Usages

**Helper Function Initialization**

The global helper of Response class will return a shared instance of response class.

```php
$response = response(200);
```

Initialize class directly.

```php
use Luminova\Template\Response;

$response = new Response(200);
```

To output json content.

```php 
$response->json([
    'message' => 'Foo bar'
]);
```

To output html content.

```php 
$response->html('<p>Foo bar</p>');
```

To output file content in browser.

```php
$response->stream('/path/to/file/', 'large-pdf.pdf', [
    'Content-Type' => 'application/pdf'
], true, 3600);
```

To download file from browser.

```php
$response->download('/path/to/file/document.pdf', 'large-pdf.pdf', [
    'Content-Type' => 'application/pdf'
]);
```
To download content from browser.

```php
$response->download('Hello world!', 'hello.txt', [
    'Content-Type' => 'text/plain'
]);
```

***

## Methods

### constructor

Initialize response class constructor with HTTP status code and optional headers.

```php
function __construct(
	private int $status = 200, 
	private array $headers = [],
	private bool $encode = false,
	private bool $minifyCodeblocks = false,
	private bool $codeblockButton = false
)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | HTTP status code (default: `200 OK`). |
| `$headers` | **array<string,mixed>** | HTTP headers as key-value pairs. |
| `$encode` | **bool** | Whether to enable content encoding like `gzip`. |
| `$minifyCodeblocks` | **bool** | Indicates if code blocks should be minified. |
| `$codeblockButton` | **bool** |  Indicates if code blocks should include a copy button. |
		
***

### getStatusCode

Get the current HTTP status code.

```php
public getStatusCode(): int
```

**Return Value:**

`int` - Return the current HTTP status code.

***

### getHeaders

Retrieve all HTTP headers.

```php
public getHeaders(): array<string,mixed>
```

**Return Value:**

`array<string,mixed>` - Return list of all HTTP headers.

***

### getHeader

Retrieve a specific HTTP header.

```php
public getHeader(string $name): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the header. |

**Return Value:**

`mixed` - Return the header value, or null if it doesn't exist.

***

### getProtocolVersion

Retrieves the HTTP protocol version (e.g, `1.0`, `1.1`).

```php
public getProtocolVersion(): float
```

**Return Value:**

`float` - Return the HTTP protocol version.

***

### clearHeaders

Clear all previous HTTP headers.

```php
public clearHeaders(): void
```

***

### clearRedirects

Clear previous set HTTP header redirects.

```php
public clearRedirects(): bool
```

**Return Value:**

`bool` - Return true if any redirect was cleared, false otherwise.

***

### hasRedirects

Determine if the response headers has any redirects.

```php
public hasRedirects(): bool
```

**Return Value:**

`bool` - Return true if headers contain any redirect, otherwise false.

***

### setStatus

Set the HTTP status code using setStatus method.

```php
public setStatus(int $status = 200): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | HTTP status code (default: 200 OK). |

**Return Value:**

`Response` - Return response class instance.

***

### encode

Set enable or disable content encoding, otherwise it will use default flag in env file `enable.encoding`

```php
public encode(bool $encode): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$encode` | **bool** | Enable content encoding like gzip, deflate. |

**Return Value:**

`Response` - Return response class instance.

***

### minify

Set enable or disable content minification, otherwise it will use default flag in env file `page.minification`.

```php
public minify(bool $minify): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$minify` | **bool** | Enable content minification. |

**Return Value:**

`Response` - Return response class instance.

***

### codeblock

Set if `HTML` codeblock tags should be ignore during content minification.
For this to work correctly `minification` of content must be enabled either by calling method `minify` or enabling minification in `env` file.

```php
public codeblock(bool $minify, bool $button = false): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$minify` | **bool** | Indicate if codeblocks should be minified. |
| `$button` | **bool** | Indicate if codeblock tags should include a copy button (default: false). |

**Return Value:**

`Response` - Return response class instance.

***

### header

Set response header to use.

```php
public header(string $key, mixed $value): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The header key. |
| `$value` | **mixed** | The header value for key. |

**Return Value:**

`Response` - Return response class instance.

***

### headers

Set response headers to use.

```php
public headers(array<string,mixed> $headers): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$headers` | **array<string,mixed>** | The headers key-pair. |

**Return Value:**

`Response` - Return response class instance.

***

### send

Send HTTP response headers to the client.
This method sends the HTTP status code (if set) and all accumulated headers to the client.

```php
public send(): void 
```

***

### render

Render any content format anywhere with your own customizations.
Using this method allows you to pass `encode` or `minify` without using the default from `env`.

```php
public render(mixed $content, int $status = 200, array $headers = [],  bool $encode = false, bool $minify = false): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **mixed** | The content to render. |
| `$status` | **int** | The HTTP status code (default: 200 OK). |
| `$headers` | **array** | Additional output headers. |
| `$encode` | **bool** | Weather to enable content encoding like gzip (default: false). |
| `$minify` | **bool** | Weather to minify content (default: false). |

**Return Value:**

`int` - Return status code success as STATUS_SUCCESS, otherwise failure as STATUS_ERROR.

> **Note:** The default content type is `application/json` if non was provided in `$headers`.

***

### json

Send a JSON response.

```php
public json(array|object $content): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **array&#124;object** | The array or json object to be rendered as JSON. |

**Return Value:**

`int` - Return status code success, otherwise failure.

***

### text

Send a plain text response.

```php
public text(string $content): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The plain text content to render. |

**Return Value:**

`int` - Return status code success, otherwise failure.

***

### html

Send an HTML response.

```php
public html(string $content): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The HTML content to render. |

**Return Value:**

`int` - Return status code success, otherwise failure.

***

### xml

Send an XML response.

```php
public xml(string $content): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The XML content to render. |

**Return Value:**

`int` - Return status code success, otherwise failure.

***

### download

Download file from server or download response string content.

```php
public download(string $fileOrContent, ?string $name = null, array $headers = []): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | Path to the file or content to be downloaded. |
| `$name` | **string&#124;null** | Optional name to be used for the downloaded file. |
| `$headers` | **array** | Optional download headers. |

**Return Value:**

`bool` - Return true if the download was successful, false otherwise.

***

### stream

Streaming large files.
This allows you to display any file on browser.

```php
public stream(string $path, string $basename, array $headers = [], bool $eTag = true, int $expiry = 0): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The path to file storage (e.g: `/writeable/storages/images/`). |
| `$name` | **string** | The file name (e.g: image.png). |
| `$headers` | **array** | Optional stream output headers. |
| `$eTag` | **bool** | Whether to generate ETag headers (default: true). |
| `$expiry` | **int** | Expiry time in seconds for cache control (default: 0), indicating no cache. |

**Return Value:**

`bool` - Return true if file streaming was successful, false otherwise.

***

### redirect

Redirect the client to a different URL location with the appropriate status code (`302`, `303`, or `307`) .
This method handles HTTP redirection by sending an appropriate `Location` or `Refresh` header and optionally specifying a status code.  It auto-detects `IIS` environments and uses the `refresh` method for compatibility. 

```php
public redirect(string $uri, ?string $method = null, ?int $code = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uri` | **string** | The target URI for the redirection. |
| `$method` | **string\|null** | Optional. The redirection method (`refresh` or `null` for standard). |
| `$code` | **int\|null** | Optional HTTP status code (e.g., `302`, `303`, `307`).<br/>If set to null, it will try detecting status code based on request method and protocol version. |