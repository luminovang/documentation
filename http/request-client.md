# HTTP Request Clients for Outgoing Network Requests

***

## Overview

HTTP clients offer flexibility when managing requests. With support for a lightweight cURL-based client and a feature-rich Guzzle implementation, which provides a consistent API for HTTP communication

***

## Introduction

The HTTP request client is designed to simplify HTTP requests to external resources, such as APIs, web services, and websites. It simplifies the process of sending requests and efficiently handling responses, allowing you to read entire content or use the [File Input Stream](/files/stream.md) to read large contents in parts.

The Luminova HTTP clients provide an abstraction layer for handling HTTP requests. Follows PHP-FIG's standards for HTTP clients. These clients are designed with a consistent interface and support two implementations:  
1. **Luminova cURL Client**  
2. **Luminova Guzzle Client**  

Both clients adhere to the `Luminova\Interface\ClientInterface` contract and the [PSR-7 ClientInterface](https://www.php-fig.org/psr/psr-7/#32-psrhttpmessagerequestinterface).

> **Luminova\Http\Network**: Simplify HTTP request management using the `Network` class. It enables you to specify a client object (e.g., `Curl` or `Guzzle`) for handling HTTP requests seamlessly. [Learn more](https://luminova.ng/docs/0.0.0/http/network).

***

### Luminova cURL Client Implementations

- **Namespace**: `\Luminova\Http\Client\Curl`  
- **Description**: A lightweight HTTP client based on PHP's native `cURL` extension. Ideal for environments with minimal dependencies.  

**Usage Example**  

```php
use Luminova\Http\Client\Curl;

// Instantiate the cURL client
$curl = new Curl([
    'base_uri' => 'https://example.com/'
]);

// Send a GET request
$response = $curl->request('GET', 'foo', [
    'Content-Type' => 'text/html',
]);

echo $response->getBody()->getContents();
```

**Features**:  
- **Simple and Fast**: Utilizes PHP's native cURL functions with support for (async requests, and more)  
- **Lightweight**: No external dependencies.  

---

### Luminova Guzzle Client Implementations

- **Namespace**: `\Luminova\Http\Client\Guzzle`  
- **Description**: An extended HTTP client powered by the Guzzle library. Provides advanced features and robust request handling.  

---

### **Usage Example**  

```php
use Luminova\Http\Client\Guzzle;

// Instantiate the Guzzle client
$guzzle = new Guzzle([
    'base_uri' => 'https://example.com/'
]);

// Send a GET request
$response = $guzzle->request('GET', 'foo', [
    'Content-Type' => 'text/html',
]);

echo $response->getBody()->getContents();
```

**Features**:  
- **Advanced Functionality**: Leverages Guzzle's full feature set (async requests, middleware, and more).  
- **Extensible**: Supports third-party plugins and middleware for enhanced capabilities. 

---

### Shared Interface

Both clients implement the following interfaces:  

- **`Luminova\Interface\ClientInterface`**: A custom interface defining the HTTP client contract.  
- **`Psr\Http\Client\ClientInterface`**: Ensures compliance with PSR-7 standards for HTTP clients.  

---

### Features of Luminova HTTP Clients

1. **Consistent Interface**  
   Both `Curl` and `Guzzle` clients follow the same request syntax, making it easy to switch between implementations.  

2. **Base URI Support**  
   Allows setting a base URL for all subsequent requests, simplifying endpoint management.  

3. **Customizable Requests**  
   Supports:  
   - HTTP headers  
   - Query string parameters  
   - Request body content  
   - Timeout settings and [more options](/http/request-client-options.md)

4. **Compatibility**  
   - The `Curl` client requires only PHP's native cURL extension.  
   - The `Guzzle` client requires the Guzzle library.

---

### Comparison Table

| Feature               | Luminova cURL Client           | Luminova Guzzle Client            |
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

## Class Definition

* cURL Class Namespace: `\Luminova\Http\Client\Curl`
* Guzzle Class Namespace: `\Luminova\Http\Client\Guzzle`
* Class implements: [\Luminova\Interface\ClientInterface](/interface/classes.md#clientinterface), [\Psr\Http\Client\ClientInterface](https://www.php-fig.org/psr/psr-18/#client)

***

## Methods

### constructor

Initialize the HTTP request client constructor.

While making request, Use an absolute path to override the `base_uri` of the client, or a  relative path to append to the `base_uri` of the client. The URL can contain the query string as well. Use an option-array to provide a URL template and additional variables to use in the URL template expansion.

```php
public __construct(array<string,mixed> $config = []): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **array<string,mixed>** | Option default client request configuration.<br /><br />> When configuration is passed to the constructor, request methods cannot override it value. |

***

### getClient

Retrieve the original HTTP request client object.

```php
public getClient(): \Psr\Http\Client\ClientInterface
```

**Return Value:**

`\Psr\Http\Client\ClientInterface<\T>` - Return instance of request client object such as `Curl` or `Guzzle`.

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
public sendRequest(\Psr\Http\Message\RequestInterface $request): \Psr\Http\Message\ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$request` | **\Psr\Http\Message\RequestInterface** | Instance of HTTP request object. |

**Return Value:**

`\Psr\Http\Message\ResponseInterface<\T>` - Return instance of HTTP response object.

**Throws:**

- [\Luminova\Exceptions\ClientException](/running/exceptions.md#clientexception) - If an error happens while processing the request.

***

### sendAsync

Sends an HTTP request asynchronously to the specified URI.

Builds the request using the provided HTTP request object and optional configurations.

```php
public sendAsync(
    \Psr\Http\Message\RequestInterface $request, 
    array<string,mixed> $options = []
): \Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$request` | **\Psr\Http\Message\RequestInterface** | The HTTP request object containing request details. |
| `$options` | **array<string,mixed>** | Optional parameters to customize request behavior (e.g., headers, timeout). |

**Return Value:**

`\Luminova\Interface\PromiseInterface` - Return guzzle promise that resolves to the request response or cURL request response object.

**Throws:**

- [\Luminova\Exceptions\RequestException](/running/exceptions.md#requestexception) - If the request format is invalid or fails.
- [\Luminova\Exceptions\ConnectException](/running/exceptions.md#connectexception) - If the server cannot be reached.
- [\Luminova\Exceptions\ClientException](/running/exceptions.md#clientexception) - For 4xx client-side HTTP errors.
- [\Luminova\Exceptions\ServerException](/running/exceptions.md#serverexception) - For 5xx server-side HTTP errors.

***

### requestAsync

Initiates an HTTP request asynchronously with a specified method and URI.

```php
public requestAsync(
    string $method, 
    \Psr\Http\Message\UriInterface|string $uri = '', 
    array<string,mixed> $options = []
): \Luminova\Interface\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The HTTP method to use (e.g., `GET`, `POST`). |
| `$uri` | **\Psr\Http\Message\UriInterface&#124;string** | The target URI or URL for the request (absolute or relative). |
| `$options` | **array<string,mixed>** | Optional configurations to adjust the request behavior (e.g., headers, data, timeout). |

**Return Value:**

`\Luminova\Interface\PromiseInterface` - Return guzzle promise that resolves to the request response or cURL request response object.

**Throws:**

- [\Luminova\Exceptions\RequestException](/running/exceptions.md#requestexception) - If the request is improperly formatted or fails.
- [\Luminova\Exceptions\ConnectException](/running/exceptions.md#connectexception) - If the server is unreachable.
- [\Luminova\Exceptions\ClientException](/running/exceptions.md#clientexception) - For client-side HTTP errors (4xx responses).
- [\Luminova\Exceptions\ServerException](/running/exceptions.md#serverexception) - For server-side HTTP errors (5xx responses).

***

### send

Sends an HTTP request synchronously to the specified URI.

Uses the provided HTTP request object and optional settings to perform the request.

```php
public send(
    \Psr\Http\Message\RequestInterface $request, 
    array<string,mixed> $options = []
): \Psr\Http\Message\ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$request` | **\Psr\Http\Message\RequestInterface** | The HTTP request object containing request details. |
| `$options` | **array<string,mixed>** | Optional parameters to customize the request (e.g., headers, timeout). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface` - Return guzzle promise that resolves to the request response or cURL request response object.

**Throws:**

- [\Luminova\Exceptions\RequestException](/running/exceptions.md#requestexception) - If the request format is invalid or fails.
- [\Luminova\Exceptions\ConnectException](/running/exceptions.md#connectexception) - If a connection to the server cannot be established.
- [\Luminova\Exceptions\ClientException](/running/exceptions.md#clientexception) - For 4xx client-side HTTP errors.
- [\Luminova\Exceptions\ServerException](/running/exceptions.md#serverexception) - For 5xx server-side HTTP errors.

***

### request

Performs a synchronous HTTP request to a specified URI.

Executes the request using the specified method and URI, with optional configurations to modify its behavior.

```php
public request(string $method, string $uri = '', array<string,mixed> $options = []): \Psr\Http\Message\ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The HTTP method to use (e.g., `GET`, `POST`). |
| `$uri` | **string** | The target URI or URL for the request (absolute or relative). |
| `$options` | **array<string,mixed>** | Optional configurations to adjust the request behavior (e.g., headers, data, timeout). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface` - Return the psr response interface object or cURL response object depending on client.

**Throws:**

- [\Luminova\Exceptions\ConnectException](/running/exceptions.md#connectexception) - If a connection to the server cannot be established.
- [\Luminova\Exceptions\RequestException](/running/exceptions.md#requestexception) - For request formatting, timeout, or protocol issues.
- [\Luminova\Exceptions\ClientException](/running/exceptions.md#clientexception) - For client-side HTTP errors (4xx responses).
- [\Luminova\Exceptions\ServerException](/running/exceptions.md#serverexception) - For server-side HTTP errors (5xx responses).