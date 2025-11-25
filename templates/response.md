# Handling Custom Response Rendering

***

## Overview

The Response class allows you to send raw output, JSON, or files directly to the browser, client, or as a controller method response for routing. Perfect for APIs, JS frameworks, or dynamic endpoints.

***

## Introduction

The **Response** class is a simple way to send content from your application straight to the browser or API client. You can use it inside controller methods, routing callbacks, or anywhere you need to deliver output directly without any extra layers in between.

It’s tightly integrated with Luminova’s routing system. When you return a `Response` object from a controller method, it carries all the information (status code, headers, content) that the router needs to handle the response automatically.

This is different from the [Template View Rendering Class](/templates/views.md), which is built for rendering templates stored in `/resources/Views/` or in module paths like `/app/Modules/<Module>/Views/`. The `Response` class skips that overhead, making it perfect for APIs or any scenario where you need raw, fast output. It also works well with modern frontend frameworks like **React**, **Vue**, **Next.js**, or **Nuxt**, letting you build hybrid backends without being tied to a template engine.

#### Key Features

* **Direct Output:** Send raw data or processed content straight from controllers or routes.
* **Global Helper Access:** Call the `Response` instance using a global helper — no need to `new` it manually.

#### Limitations

* **No Built-in Caching:** You’ll need to handle caching yourself if required.
* **No Template Engine Support:** This class doesn’t use template engines like `Smarty` or `Twig`.

> **When to Use**
> Use `Response` when you’re building APIs, returning JSON, or streaming content where view templates would just get in the way.

***

## Usages

### Creating a Response Instance

You can create a `Response` object in two ways:

**Using the global helper (recommended):**
This gives you a shared instance without manually importing the class.

```php
$response = response(200); // 200 = HTTP OK
```

**Using direct instantiation:**

If you prefer working explicitly with the class:

```php
use Luminova\Template\Response;

$response = new Response(200);
```

---

### Sending Content to the Client

The `Response` class makes it easy to send different kinds of content.

**Render custom content with headers:**

```php
$response->render('<content body>', [
    'Content-Type' => 'text/custom'
]);
```

**Send a JSON response (great for APIs):**

```php
$response->json([
	'status' => 'OK',
    'message' => 'API response body'
]);
```

**Send raw HTML content:**

```php
$response->html('<p>Hello World!</p>');
```

---

### Returning a Response from Controllers

This example shows how to return data from a controller method as an HTTP response:

```php
// /app/Controllers/Http/RestController.php

use Luminova\Template\Response;
use Luminova\Attributes\Route;

#[Route('/api/info', methods: ['GET'])]
public function apiInfo(): Response
{
    // Create a JSON response with HTTP 200 (OK)
    return new Response(200, content: [
        'status' => 'OK',
        'info' => ['PHP', 'Luminova']
    ]);
}
```

---

### Returning a Response in Middleware

You can also return a response directly inside middleware logic.
If authentication fails, you can send an error response with `failed()` to indicate the middleware did not pass.

```php
// /app/Controllers/Http/RestController.php

use Luminova\Template\Response;
use Luminova\Attributes\Route;

#[Route('/api/(:root)', methods: ['GET'], middleware: Route::HTTP_BEFORE_MIDDLEWARE)]
public function middleware(): int|Response
{
    // Allow request if user is online
    if ($this->app->session->isOnline()) {
        return STATUS_SUCCESS; // Continue routing
    }

    // Otherwise, return a failed response (HTTP 401)
    return (new Response(401, content: ['error' => 'Access Denied']))->failed();
}
```

***

## Class Definition

* Class namespace: `Luminova\Template\Response`
* This class implements [Luminova\Interface\ViewResponseInterface](/interface/classes.md#viewresponseinterface)

***

## Methods

### constructor

Initialize a response object with optional content, headers, and processing settings.

- Sets the HTTP status code.
- Optionally applies content compression and HTML minification.
- Can automatically add copy buttons to code blocks after minification.
- If `$content` is provided, it will be processed immediately. Output via `output()` or get result `getResult()`.

```php
function __construct(
	private int $status = 200, 
	private array $headers = [],
	private bool $compress = false,
	private bool $minifyCodeblocks = false,
	private bool $codeblockButton = false,
	string|array|object|null $content = null
)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | HTTP status code (default: `200 OK`). |
| `$headers` | **array<string,mixed>** | Optional response headers as key-value pairs. |
| `$compress` | **bool** | Whether to apply content compression (e.g., `gzip`, `deflate`),  (default: `false`). |
| `$minifyCodeblocks` | **bool** | Whether to exclude HTML code blocks from minification (default: `false`). |
| `$codeblockButton` | **bool** | Whether to add a copy button to matched HTML code blocks (default: `false`). |
| `$content` | **string\|array\|object\|null** | Optional content to process immediately (default: `false`). |

**Throws:**

[Luminova\Exceptions\ResponseException](/error-handlers/exceptions.md#responseexception) - If content is not `null` and error was encountered while processing.

> **Note:** 
> If the `minify` method is not explicitly invoked, the environment variable `page.minification` determines HTML minification.

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

Retrieve all set HTTP headers.

```php
public getHeaders(): array<string,mixed>
```

**Return Value:**

`array<string,mixed>` - Return an array of all headers.

***

### getHeader

Retrieve the value of a specific HTTP header.

```php
public getHeader(string $name): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the header. |

**Return Value:**

`mixed` - Return the header value, or null if not found.

***

### getProtocolVersion

Get the HTTP protocol version being used (e.g., `1.0`, `1.1`).

```php
public getProtocolVersion(): float
```

**Return Value:**

`float` - Return the HTTP protocol version.

***

### getResult

Retrieve response metadata built by `content()`.

**Returned Array Keys:**
- **exit** (`int`): Response exit status code (e.g., `STATUS_*`).
- **status** (`int`): HTTP status code to send.
- **headers** (`array<string,mixed>`): Validated response headers.
- **contents** (`string`): Processed response body.

```php
public getResult(): ?array
```

**Return Value:**

`array<string,mixed>|null` - Return the response data previously built using `content()` or null.

**Example:**

```php
$response = new Response();
$response->content('<p>Hello world!</p>');

$result = $response->getResult();
echo $result['contents'];
```

***

### clearHeaders

Clear all previously set HTTP headers.

```php
public clearHeaders(): void
```

***

### clearRedirects

Clear any redirects set in the response headers.

```php
public clearRedirects(): bool
```

**Return Value:**

`bool` - Return true if any redirects were cleared, false otherwise.

***

### hasRedirects

Check if the response headers contain any redirects.

```php
public hasRedirects(): bool
```

**Return Value:**

`bool` - Return true if redirects are set, false otherwise.

***

### setStatus

Set the HTTP status code for the response.

```php
public setStatus(int $status): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | The HTTP status code to be set (e.g, `200`). |

**Return Value:**

`Response` - Return instance of the Response class.

***

### compress

Enable or disable content compression using encoding (e.g., `gzip`, `deflate`).

```php
public compress(bool $compress = true): self;
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$encode` | **bool** | Whether to compress content (default: true). |

**Return Value:**

`Response` -  Return instance of the Response class.

***

### minify

Enable or disable HTML content minification.

Set enable or disable HTML content minification, otherwise it will use default flag in env file `page.minification`.

```php
public minify(bool $minify = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$minify` | **bool** | Whether to minify the HTML content (default: true). |

**Return Value:**

`Response` - Return instance of the Response class.

> This overrides the environment variable `page.minification`.

***

### codeblock

Configure minification behavior for HTML code blocks and optional copy button.

> Set if `HTML` codeblock tags should be ignore during content minification. 
> For this to work correctly `minification` of content must be enabled either by calling method `minify` or enabling minification in `env` file.

```php
public codeblock(bool $minify, bool $button = false): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$minify` | **bool** | Whether to exclude HTML code blocks from minification (default: false). |
| `$button` | **bool** | Whether to include a copy button in code blocks (default: false). |

**Return Value:**

`Response` - Return instance of the Response class.

***

### header

Set an individual HTTP header.

```php
public header(string $key, mixed $value): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** |  The header name. |
| `$value` | **mixed** | The header value for name. |

**Return Value:**

`Response` - Return instance of the Response class.

***

### headers

Set multiple HTTP headers at once.

```php
public headers(array<string,mixed> $headers): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$headers` | **array<string,mixed>** | An associative array of headers key-pair. |

**Return Value:**

`Response` - Return instance of the Response class.

***

### sendStatus

Send the HTTP status code header with the corresponding status message.

> This method also sends the `Status` header for compatibility with older clients.

```php
public sendStatus(): bool 
```

**Return Value:**

`bool` - Returns true if the status header is valid and successfully sent, otherwise false.

***

### send

Send HTTP response headers to the client.

Send all response headers and the status code to the client without content body. Optionally validate the against REST Api headers based on `App\Config\Apis` if `$validate` is set to true.

```php
public send(bool $validate = false): void 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$validate` | **bool** | Whether to apply APIs headers validations (default: false). |

***

### failed

Mark the response as failed.

When a response is marked failed:
- Methods like `output()`, `render()`, `json()`, `html()`, etc., will return `STATUS_ERROR` instead of `STATUS_SUCCESS` or `STATUS_SILENCE`, even if content is empty or HTTP status is `204`.
- Essential for marking a failed middleware authentication, so routing system can terminate execution.

```php
public failed(bool $failed = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$failed` | **bool** | IWhether to mark the response as failed (default: `true`). |

**Return Value:**

`self` - Return instance of response class.

**Example:**

```php
$response = (new Response())
    ->failed()
    ->content(['message']);
 
$response->output(); // Returns STATUS_ERROR
```

> **Note:** 
> Only use this method when handling middleware responses or when you want to explicitly return `STATUS_ERROR`.

***

### render

Render and send response content along with optional headers.

- Processes headers and content (minification, compression).
- Sends headers and outputs the response body.

Using this method allows you to output response content along with additional optional headers. You can use `encode` or `minify` without relying on default configuration in (`env`) file.

```php
public render(string $content, array $headers = []): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The response content to render. |
| `$headers` | **array<string,mixed>** | Additional headers to send with the content.. |

**Return Value:**

`int` - Return the status code: `STATUS_SUCCESS` if successful, otherwise `STATUS_ERROR`.

**Throws:**

[Luminova\Exceptions\ResponseException](/error-handlers/exceptions.md#responseexception) - If any error occur while rendering response.

***

### content

Build and process response content without sending it.

- Converts non-string content to JSON automatically.
- Processes headers, minification, compression, and content length.
- Stores the result internally for later retrieval via `getResult()`.

```php
public content(string|array|object $content, array $headers = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string\|array\|object** | Response body or data to convert to `JSON`. |
| `$headers` | **array<string,mixed>** | Optional headers to send with the content. |

**Return Value:**

`self` - Return the instance of response class.

**Throws:**

[Luminova\Exceptions\ResponseException](/error-handlers/exceptions.md#responseexception) - If any error occur while processing response.

**Example:**

```php
$response = new Response();
$response->content('<p>Hello world!</p>');

$response->output();

var_dump($response->getResult());
```

***

### output

Send the processed response to the client.

- Outputs HTTP status, headers, and body generated by `content()`.
- Clears output buffers before sending content.
- Optionally skips sending headers if they have already been sent.

```php
public output(bool $ifHeaderNotSent = false): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ifHeaderNotSent` | **bool** | If `true`, headers are only sent if they **have not** already been sent (default: `false`). |

**Return Value:**

`int` - Response exit status code: `STATUS_SUCCESS`, `STATUS_SILENCE`, or `STATUS_ERROR`.

***

### json

Send a JSON response.

```php
public json(array|object $content): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **array&#124;object** | An array or JSON object data to be encoded as JSON string. |

**Return Value:**

`int` - Return status code: `STATUS_SUCCESS` if successful, otherwise `STATUS_ERROR`.

**Throws:**

[Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - Throws if a JSON encoding error occurs.

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

`int` - Return status code: `STATUS_SUCCESS` if successful, otherwise `STATUS_ERROR`.

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

`int` - Return status code: `STATUS_SUCCESS` if successful, otherwise `STATUS_ERROR`.

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

`int` - Return status code: `STATUS_SUCCESS` if successful, otherwise `STATUS_ERROR`.

***

### download

Send a file or content as a browser download.

```php
public download(
	string $fileOrContent, 
	?string $name = null, 
	array $headers = [],
	int $chunkSize = 8192,
    int $delay = 0
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | Path to the file or content to be downloaded. |
| `$name` | **string&#124;null** | Optional name to be used for the downloaded file. |
| `$headers` | **array** | Optional download headers. |
| `$chunkSize` | **int** | The size of each chunk in bytes for large content (default: 8192, 8KB). |
| `$delay` | **int** | The delay between each chunk in microseconds (default: 0). |

**Return Value:**

`bool` - Return true if the download was successful, false otherwise.

**Examples:**

Download a file.

```php
$response->download('/path/to/file/document.pdf', 'large-pdf.pdf', [
    'Content-Type' => 'application/pdf'
]);
```

Download raw content as a file.

```php
$response->download('Hello world!', 'hello.txt', [
    'Content-Type' => 'text/plain'
]);
```

***

### stream

Stream output any file or large files to the client.

```php
public stream(
	string $path, 
	string $basename, 
	array $headers = [], 
	bool $eTag = true, 
	bool $weakEtag = false,
	int $expiry = 0,
	int $length = (1 << 21),
    int $delay = 0
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The path to file storage (e.g: `/writeable/storages/images/`). |
| `$name` | **string** | The file name (e.g., `image.png`). |
| `$headers` | **array** | Optional stream output headers. |
| `$eTag` | **bool** | Whether to generate ETag headers (default: `true`). |
| `$weakEtag` | **bool** | Whether to use a weak ETag header or string (default: `false`). |
| `$expiry` | **int** | Expiry time in seconds for cache control (default: 0), indicating no cache. |
| `$length` | **int** | Optional size of each chunk to be read (default: 2MB). |
| `$delay` | **int** | Optional delay in microseconds between chunk length (default: 0). |

**Return Value:**

`bool` - Return true if file streaming was successful, false otherwise.

**Example:**

Stream a file to the browser:

```php
$response->stream('/path/to/file/', 'large-pdf.pdf', [
    'Content-Type' => 'application/pdf'
], true, 3600);
```

**See:**

[Private File Delivery Manager](/files/file-delivery.md) - For more information and advanced usage.

***

### redirect

Redirect the client to a new URL.

Redirect the client to a different URI location with the appropriate status code (`302`, `303`, or `307`) .
This method handles HTTP redirection by sending an appropriate `Location` or `Refresh` header and optionally specifying a status code.  It auto-detects `IIS` environments and uses the `refresh` method for compatibility. 

```php
public redirect(string $uri, ?string $method = null, ?int $code = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uri` | **string** | The target URI for the redirection. |
| `$method` | **string\|null** | Optional. The redirection method (`refresh` or `null` for standard). |
| `$code` | **int\|null** | Optional HTTP status code (e.g., `302`, `303`, `307`). |

> **Note:** If `$code` is set to `null`, it will try detecting status code based on request method and protocol version.