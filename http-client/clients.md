#  HTTP Clients for Sending Network Requests

***

## Overview

Luminova's HTTP clients for network requests. You can use the lightweight Novio (cURL-based) client or switch to the advanced Guzzle client. Both share the same interface for consistent communication.

***

## Introduction

The Luminova HTTP **Client Interface** defines a standard contract for building HTTP request clients. It ensures every client you use handles network requests in a consistent way, following the PHP-FIG PSR-7 standard for HTTP messages.

---

### Built-in HTTP Clients

Luminova includes two ready-to-use HTTP clients:

* **Novio Client** – A lightweight client using PHP’s native cURL extension (no extra libraries needed).
* **Guzzle Client** – A powerful client with advanced features powered by the Guzzle library.

Both clients implement `Luminova\Interface\ClientInterface` and support [PSR-7 Request/Response](https://www.php-fig.org/psr/psr-7/#32-psrhttpmessagerequestinterface). You can switch between them without rewriting your application code.

---

#### Key Features

1. **Consistent Interface**
   Novio and Guzzle share the same request syntax, so replacing one with the other is seamless.

2. **Base URI Support**
   Define a base URL for all requests, making endpoint management cleaner.

3. **Customizable Requests**
   Easily configure:

   * HTTP headers
   * Query parameters
   * Request body data
   * Timeout settings and [more options](/http-client/options.md)

4. **Lightweight vs. Full-Featured**

   * **Novio** works with just PHP’s cURL extension.
   * **Guzzle** offers more features but requires installing the Guzzle library.

5. **Standards Compliance**
   Both clients implement:

   * **`Luminova\Interface\ClientInterface`** – Defines how all Luminova HTTP clients work.
   * **`Psr\Http\Client\ClientInterface`** – Ensures they meet PSR-7 standards for PHP HTTP clients.

> **Recommendation:**
> If you just want a simple way to send HTTP requests without worrying about the details, use `Luminova\Http\Network`.
> It automatically works with either **Novio** or **Guzzle** and keeps your application code clean.
> For details, see [Network Request Helper](/http-client/network.md).

---

Here’s a streamlined, beginner-friendly rewrite with consistent tone and a bit more clarity:

---

## Examples

### Novio Client

The **Novio** client is perfect if you want a fast, lightweight solution without adding extra libraries. It supports both single requests and concurrent requests using PHP’s `CurlMultiHandle`, making it efficient for high-performance applications.

**Sending a Request:**

```php
use Luminova\Http\Client\Novio;

// Create a Novio client and set a base URL
$client = new Novio([
    'base_uri' => 'https://example.com/'
]);

// Send a simple GET request
$response = $client->request('GET', 'foo', [
    'Content-Type' => 'text/html',
]);

// Print the response body
echo $response->getBody()->getContents();
```

> For more details, see [Novio Request Client](/http-client/novio.md).

---

### Guzzle Client

The **Guzzle** client is more powerful and flexible, built on the popular Guzzle library. It uses the exact same API as Novio, so you can switch between them with no code changes.

**Basic Request Example:**

```php
use Luminova\Http\Client\Guzzle;

// Create a Guzzle client with a base URL
$client = new Guzzle([
    'base_uri' => 'https://example.com/'
]);

// Send a simple GET request
$response = $client->request('GET', 'foo', [
    'Content-Type' => 'text/html',
]);

// Print the response body
echo $response->getBody()->getContents();
```

> **Why choose Guzzle?**
>
> * Comes with advanced features like async requests, retries, and request pools.
> * Easily extended with middleware for logging, caching, or authentication.
> * Uses the same interface as Novio, so swapping clients is effortless.

***

## Class Definition

* Novio Class Namespace: `Luminova\Http\Client\Novio`
* Guzzle Class Namespace: `Luminova\Http\Client\Guzzle`
* Class implements: [Luminova\Interface\ClientInterface](/interface/classes.md#clientinterface), [Psr\Http\Client\ClientInterface](https://www.php-fig.org/psr/psr-18/#client)

---

### Comparison

| Feature               | Luminova Novio Client          | Luminova Guzzle Client            |
|-----------------------|--------------------------------|-----------------------------------|
| **Dependencies**      | None (uses native cURL)        | Requires Guzzle library           |
| **Custom Options**    | Limited to cURL options        | Supports Guzzle request options   |
| **Async Requests**    | Supported                      | Supported                         |
| **Cookie Requests**   | Supported                      | Supported                         |
| **Proxy Requests**    | Supported                      | Supported                         |
| **Multipart**         | Limited to `CurlFile`          | Supports Guzzle Multipart File    |
| **Middleware**        | Not Supported                  | Supported                         |
| **Synchronous**       | Limited to cURL `timeout`      | Supports Guzzle Synchronous       |
| **Header Callback**   | Yes                            | Yes                               |
| **Progress**          | No                             | Yes                               |
| **IDN Conversion**    | Yes                             | Yes                              |
| **PSR-7 Compliant**   | Yes                            | Yes                               |
| **Ease of Use**       | Lightweight and fast           | Feature-rich and flexible         |

***

## Shared Methods

### constructor

Initialize the HTTP request client constructor.

While making request, Use an absolute path to override the `base_uri` of the client, or a  relative path to append to the `base_uri` of the client. The URL can contain the query string as well. Use an option-array to provide a URL template and additional variables to use in the URL template expansion.

```php
public __construct(array<string,mixed> $config = []): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **array<string,mixed>** | Option default client request configuration. |

> When configuration is passed to the constructor, request methods cannot override it value.

***

### getClient

Retrieve the original HTTP request client object.

```php
public getClient(): Psr\Http\Client\ClientInterface
```

**Return Value:**

`Psr\Http\Client\ClientInterface<\T>` - Return instance of request client object such as `Curl` or `Guzzle`.

***

### getConfig

Retrieve configuration option from client object.

```php
public getConfig(?string $option = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$option` | **string&#124;null** | The option name to return (default: null). |

**Return Value:**

`mixed` - Return configuration option based on option name, or return all if option is null, otherwise null.

***

### setOption

Set a custom cURL option and it's value.

```php
public setOption(int $option, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$option` | **int** | The curl option identifier (e.g, `CURLOPT_USERAGENT`). |
| `$value` | **mixed** | The curl client option value. |

**Return Value:**

`self` - Return instance of request client class.

> **Note:** The options will be ignored if used with `Guzzle` client.

***

### sendRequest

Sends a request and returns response.

```php
public sendRequest(Psr\Http\Message\RequestInterface $request): Psr\Http\Message\ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$request` | **RequestInterface** | Instance of HTTP request object. |

**Return Value:**

`Psr\Http\Message\ResponseInterface<\T>` - Return instance of HTTP response object.

**Throws:**

- [Luminova\Exceptions\ClientException](/error-handlers/exceptions.md#clientexception) - If an error happens while processing the request.

***

### sendAsync

Sends an HTTP request asynchronously to the specified URI.

Builds the request using the provided HTTP request object and optional configurations.

```php
public sendAsync(
    Psr\Http\Message\RequestInterface $request, 
    array<string,mixed> $options = []
): Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$request` | **RequestInterface** | The HTTP request object containing request details. |
| `$options` | **array<string,mixed>** | Optional parameters to customize request behavior (e.g., headers, timeout). |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Return guzzle promise that resolves to the request response or cURL request response object.

**Throws:**

- [Luminova\Exceptions\RequestException](/error-handlers/exceptions.md#requestexception) - If the request format is invalid or fails.
- [Luminova\Exceptions\ConnectException](/error-handlers/exceptions.md#connectexception) - If the server cannot be reached.
- [Luminova\Exceptions\ClientException](/error-handlers/exceptions.md#clientexception) - For 4xx client-side HTTP errors.
- [Luminova\Exceptions\ServerException](/error-handlers/exceptions.md#serverexception) - For 5xx server-side HTTP errors.

***

### requestAsync

Initiates an HTTP request asynchronously with a specified method and URI.

```php
public requestAsync(
    string $method, 
    Psr\Http\Message\UriInterface|string $uri = '', 
    array<string,mixed> $options = []
): Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The HTTP method to use (e.g., `GET`, `POST`). |
| `$uri` | **UriInterface&#124;string** | The target URI or URL for the request (absolute or relative). |
| `$options` | **array<string,mixed>** | Optional configurations to adjust the request behavior (e.g., headers, data, timeout). |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Return guzzle promise that resolves to the request response or cURL request response object.

**Throws:**

- [Luminova\Exceptions\RequestException](/error-handlers/exceptions.md#requestexception) - If the request is improperly formatted or fails.
- [Luminova\Exceptions\ConnectException](/error-handlers/exceptions.md#connectexception) - If the server is unreachable.
- [Luminova\Exceptions\ClientException](/error-handlers/exceptions.md#clientexception) - For client-side HTTP errors (4xx responses).
- [Luminova\Exceptions\ServerException](/error-handlers/exceptions.md#serverexception) - For server-side HTTP errors (5xx responses).

***

### send

Sends an HTTP request synchronously to the specified URI.

Uses the provided HTTP request object and optional settings to perform the request.

```php
public send(
    Psr\Http\Message\RequestInterface $request, 
    array<string,mixed> $options = []
): Psr\Http\Message\ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$request` | **RequestInterface** | The HTTP request object containing request details. |
| `$options` | **array<string,mixed>** | Optional parameters to customize the request (e.g., headers, timeout). |

**Return Value:**

`Psr\Http\Message\ResponseInterface` - Return guzzle promise that resolves to the request response or cURL request response object.

**Throws:**

- [Luminova\Exceptions\RequestException](/error-handlers/exceptions.md#requestexception) - If the request format is invalid or fails.
- [Luminova\Exceptions\ConnectException](/error-handlers/exceptions.md#connectexception) - If a connection to the server cannot be established.
- [Luminova\Exceptions\ClientException](/error-handlers/exceptions.md#clientexception) - For 4xx client-side HTTP errors.
- [Luminova\Exceptions\ServerException](/error-handlers/exceptions.md#serverexception) - For 5xx server-side HTTP errors.

***

### request

Performs a synchronous HTTP request to a specified URI.

Executes the request using the specified method and URI, with optional configurations to modify its behavior.

```php
public request(string $method, UriInterface|string $uri = '', array<string,mixed> $options = []): Psr\Http\Message\ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The HTTP method to use (e.g., `GET`, `POST`). |
| `$uri` | **UriInterface\|string** | The target URI or URL for the request (absolute or relative). |
| `$options` | **array<string,mixed>** | Optional configurations to adjust the request behavior (e.g., headers, data, timeout). |

**Return Value:**

\Psr\Http\Message\ResponseInterface` - Return the psr response interface object or cURL response object depending on client.

**Throws:**

- [Luminova\Exceptions\ConnectException](/error-handlers/exceptions.md#connectexception) - If a connection to the server cannot be established.
- [Luminova\Exceptions\RequestException](/error-handlers/exceptions.md#requestexception) - For request formatting, timeout, or protocol issues.
- [Luminova\Exceptions\ClientException](/error-handlers/exceptions.md#clientexception) - For client-side HTTP errors (4xx responses).
- [Luminova\Exceptions\ServerException](/error-handlers/exceptions.md#serverexception) - For server-side HTTP errors (5xx responses).