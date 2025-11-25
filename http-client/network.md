# Network - HTTP Client Request Helper

***

## Overview

Network class an ideal choice for developers looking to implement reliable and efficient HTTP communication in their applications with or without additional dependency required.

***

## Introduction

The Luminova **Network** class is a helper for sending HTTP requests with less boilerplate. Instead of creating a new client object every time, you can send requests directly using static methods that match common HTTP verbs (like `get`, `post`, `put`, etc.).

It also supports a `config` method to set default request options once at runtime — for example, in your application’s `__construct`, `onCreate`, or controller methods. This keeps your code consistent and avoids repeating setup.

Under the hood, the static methods use the **Novio** HTTP client. If you prefer Guzzle or another custom client, you can create a `Network` instance and pass in your own client object.

For full details, see the [Request Client documentation](/http-client/clients.md).

***

## Usages

### Dynamic Methods

The network class supports a static dynamic method resolution for HTTP verbs such as GET, POST, PUT, etc.
The `config()` method can be used to set default configuration (e.g., `base_uri`).

**Examples:**

```php
Network::get('https://example.com/');
Network::post('https://example.com/', [
    'form_params' => [
        'foo' => 'bar'
    ]
]);

// Async requests 
Network::getAsync('https://example.com/')->then(function(ResponseInterface $response){
    // Handle
});
Network::postAsync('https://example.com/')->then(function(ResponseInterface $response){
    // Handle
});
```

---

### Custom Client

You can initialize the Network class with either the **Novio**, **Guzzle** or other HTTP client. The client initialization options are immutable and cannot be overridden after initialization.

**Initialization with Novio Client**

```php
use Luminova\Http\Client\Novio;

$client = new Novio(['base_uri' => 'https://example.com/']);
```

#### Initialization with Guzzle Client

To use the Network class with Guzzle client, ensure you have the required packages installed. 
Install it via Composer:

**Command:**

```bash
composer require guzzlehttp/guzzle
```

**Guzzle Client Options:**

When using `Guzzle` client all guzzle HTTP methods will be made available in the Network class, for more detailed about Guzzle client and available options, refer to the [Guzzle documentation](https://docs.guzzlephp.org/en/stable/request-options.html).

```php
use Luminova\Http\Client\Guzzle;

$client = new Guzzle(['base_uri' => 'https://example.com/']);
```

***

### Sending Requests

You can make HTTP requests using the `get`, `post`, and other methods provided by the **Novio** or **Guzzle** client class. 

```php
use Luminova\Https\Network;

$network = new Network($client);

// Full URL request
$response = $network->get('https://example.com/foo');

// Base URI request
$response = $network->get('foo');

// Post request
$response = $network->post('foo', [
    'form_params' => [
        'foo' => 'bar'
    ]
]);
```

> **Note:** 
> If you did not specify a base URI, you can pass the full URL directly in the request method.
> But can't use both features together.

***

#### Handling Responses

In this example, a GET request is sent using the default Novio client. 

The response is then processed to retrieve the status code and content. Any exceptions are caught and handled appropriately.

```php
if ($response->getStatusCode() === 200) {
    var_dump($response->getBody()->getContents());
}
```

**Novio Specific Feature:**

If you didn't specify  `stream` option set to `true`, you can directly get the contents by simply calling `getContents` from the response object instead of `$response->getBody()->getContents()`.

```php
if ($response->getStatusCode() === 200) {
    var_dump($response->getContents());
}
```

> **Note:** This is only available using the **Novio** client.

***

#### Response with Stream

Read response content in parts:

```php
$response = $network->get('foo', [
    'stream' => true
]);

$body = $response->getBody();

while (!$body->eof()) {
    echo $body->read(1024);
}
```

***

### On Header Received Callback

You can handle large responses with callbacks:

```php
$response = $network->get('large-file', [
    'on_headers' => function (Luminova\Http\Message\Response $response, array $header) {
        if ($response->getHeaderLine('Content-Length') > 1024) {
            throw new \Exception('The file is too big!');
        }
    }
]);
```

***

## Class Definition

* Class namespace: `Luminova\Http\Network`
* This class implements: [Luminova\Interface\NetworkInterface](/interface/classes.md#networkinterface), [Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)

***

### Constants

Constants in the network request class.

| **Constant**     | **Type**  | **Value**  | **Description**                                   |
|-------------------|-----------|------------|---------------------------------------------------|
| `SKIP_HEADER`     | `int`     | `5319`     | Custom header flag-value to skip headers in the request.       |
| `GET`             | `string`  | `GET`      | Represents the HTTP GET method.                  |
| `POST`            | `string`  | `POST`     | Represents the HTTP POST method.                 |
| `PUT`             | `string`  | `PUT`      | Represents the HTTP PUT method.                  |
| `PATCH`           | `string`  | `PATCH`    | Represents the HTTP PATCH method.                |
| `DELETE`          | `string`  | `DELETE`   | Represents the HTTP DELETE method.               |
| `OPTIONS`         | `string`  | `OPTIONS`  | Represents the HTTP OPTIONS method.              |

***

## Methods

### constructor

Initializes the Network class with an optional HTTP client instance.

The constructor accepts an optional request client object that implements `Psr\Http\Client\ClientInterface`. If client is `NULL`, it default to `Novio` client for making HTTP requests.

**Available Clients:**

- `Luminova\Http\Client\Novio` - Luminova HTTP client.
- `Luminova\Http\Client\Guzzle` - Guzzle HTTP client.

```php
public __construct(?Psr\Http\Client\ClientInterface $client = null)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$client` | **ClientInterface<\T>&#124;null** | The HTTP client to use for making requests. (default: `Novio`). |

***

### getClient

Retrieves the current HTTP client instance.

If using `Novio` request client, it will return **Novio** which implements `ClientInterface` interface otherwise it will return instance of request client that implements `ClientInterface`.

```php
public getClient(): ?Psr\Http\Client\ClientInterface
```

**Return Value:**

`\Psr\Http\Client\ClientInterface<\T>|null` - Returns the instance of the HTTP client used for requests, such as `Guzzle`, `Novio`, or any other HTTP client that implements the `PSR` ClientInterface, otherwise null.

***

### config

Define immutable global configurations for HTTP requests.

This method sets global options such as `base_uri`, headers, or timeouts. It must be called **before** making any network request (especially when using static methods), as the configuration becomes locked after the first usage.

```php
public static config(array $config): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **array** | Key-value pairs of request configuration options. |

**Example:**

Setting a base URL for all static requests:

```php
Network::config([
    'base_uri' => 'https://example.com/'
]);

// Then, you can make relative requests:
Network::get('user/100/info');
```

> **Note:** 
> This method is best suited for static usage, where a default base URL or common settings (e.g., headers) are required across all requests.
> Once the client is created, the config cannot be changed.

---

### send

Sends an HTTP request with the specified method, URL, data, and headers.

```php
public function send(\Psr\Http\Message\RequestInterface $request, array<string,mixed> $options = []): ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$request` | **RequestInterface;** | The request object that contains all necessary details for the request. |
| `$options` | **array** | Additional configuration options to use for the request (default: `[]`). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface` - Return the response returned by the server.

**Throws:**

- `Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

***

### get

Executes a GET request to the specified URL with optional data and headers.

```php
public get(Psr\Http\Message\UriInterface||string $uri = '', array<string,mixed> $options = []): ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uri` | **UriInterface\|string** | The request URI object or string (default: ''). |
| `$options` | **array** | Optional request options, such as headers or query parameters (e.g., `['headers' => [...]]`).. |

**Return Value:**

`\Psr\Http\Message\ResponseInterface` - Return the server's response to the GET request.

**Throws:**

- `Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

***

### fetch

Sends an HTTP request and returns a promise that resolves with the response.

This method supports both asynchronous and synchronous requests. To disable asynchronous behavior, include the option `['async' => false]` in the `$options` array.

```php
public fetch(
    Psr\Http\Message\UriInterface|string $uri = '', 
    string $method = self::GET, 
    array<string,mixed> $options = []
): PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uri` | **UriInterface\|string** | The request URI object or string (default: `''`). |
| `$method` | **string** | The HTTP method to use (e.g., `GET`, `Luminova\Http\Method::POST`). |
| `$options` | **array** | Optional request options, such as headers or query parameters (e.g., `['headers' => [...]]`). |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Return a promise that resolves with the HTTP response.

**Example:**

```php
(new Network())
    ->fetch('https://example.com', 'GET', ['headers' => ['Accept' => 'application/json']])
    ->then(function (Psr\Http\Message\ResponseInterface $response) {
        echo $response->getBody()->getContents();
    })
    ->catch(function (Throwable $exception) {
        echo 'Error: ' . $exception->getMessage();
    });
```

***

### post

Executes a POST request to the specified URL with the provided data and headers.

```php
public post(UriInterface|string $uri = '', array<string,mixed> $options = []): ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uri` | **UriInterface\|string** | The request URI object or string (default: `''`). |
| `$options` | **array** | Optional request options, such as headers or query parameters (e.g., `['headers' => [...]]`). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface` - Return the server's response to the POST request.

**Throws:**

- `Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

***

### request

Performs an HTTP request with the specified method, URL, data, and headers.

```php
public request(
    string $method, 
    \Psr\Http\Message\UriInterface|string $uri = '', 
    array<string,mixed> $options = []
): ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The HTTP method to use (e.g., `Method::GET`, `Method::POST`,` Method::PUT`, `Network::DELETE`). |
| `$uri` | **UriInterface\|string** | The request URI object or string (default: `''`). |
| `$options` | **array** | Optional request options, such as headers or query parameters (e.g., `['headers' => [...]]`). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface` - Return the response returned by the server.

**Throws:**

- `Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

***

### sendAsync

Sends an HTTP request asynchronously using a request object.

```php
public sendAsync(\Psr\Http\Message\RequestInterface $request, array<string,mixed> $options = []): PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$request` | **RequestInterface<\T>** | The request object that contains all necessary details for the request. |
| `$options` | **array<string,mixed>** | Request options to apply to the given request and to the transfer (default: `[]`). |

**Return Value:**

- `Luminova\Interface\PromiseInterface` - Return Luminova promise that resolves or reject to the request response.

***

### requestAsync

Sends an HTTP request asynchronously using a UriInterface object or string.

```php
public requestAsync(
    string $method, 
    \Psr\Http\Message\UriInterface|string $uri = '',
    array<string,mixed> $options = []
): PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The HTTP method to use (e.g, `Method::GET`, `Method::POST`, etc..). |
| `$uri` | **UriInterface\|string** | The request URI object or string (default: ''). |
| `$options` | **array<string,mixed>** | Request options to apply to the given request and to the transfer (default: `[]`). |

**Return Value:**

- `Luminova\Interface\PromiseInterface` - Return Luminova promise that resolves or reject to the request response.