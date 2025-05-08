# Sending Outgoing HTTP Network Requests

***

## Overview

Network class an ideal choice for developers looking to implement reliable and efficient HTTP communication in their applications with or without additional dependency required.

***

## Introduction

The `Network` class is a utility designed to simplify HTTP request client operations, it allows you to specify the request client to use ensuring compatibility when the need to change request client arise.

- **Simplified Request Client**: The `Curl` client operates independently, using `cURL` for making network requests, which allows for basic functionality without additional dependencies. For more details refer to the documentation [Curl Request Clients Implementation](/http/request-client.md).
- **Complex Requests**: For more advanced use cases, the class integrates the [Guzzle HTTP Client](https://docs.guzzlephp.org/en/stable/overview.html) library, enabling developers to utilize advanced features and a wide range of customization options. For more details about Luminova Guzzle client extension, refer to the documentation [Request Clients Implementation](/http/request-client.md).

***

## Usage Examples

You can initialize the Network class with either the **cURL** or **Guzzle** client. The client initialization options are immutable and cannot be overridden after initialization.

### Initialization cURL Client

```php
<?php
use Luminova\Http\Client\Curl;

$client = new Curl([
    'base_uri' => 'https://example.com/'
]);
```

---

### Initialization Guzzle Client

**Installation**

To use the Network class with Guzzle client, ensure you have the required packages installed. 
Install it via Composer:

```bash
composer require guzzlehttp/guzzle
```

---

### Guzzle Client Options

When using `Guzzle` client all guzzle HTTP methods will be made available in the Network class, for more detailed about Guzzle client and available options, refer to the [Guzzle documentation](https://docs.guzzlephp.org/en/stable/request-options.html).

```php
<?php
use Luminova\Http\Client\Guzzle;

$client = new Guzzle([
    'base_uri' => 'https://example.com/'
]);
```

***

### Making Requests

You can make HTTP requests using the `get`, `post`, and other methods provided by the **cURL** or **Guzzle** client class. 

> **Note:** If you did not specify a base URI, you can pass the full URL directly in the request method.

```php
use Luminova\Https\Network;

$network = new Network($client);

// Full URL request
$response = $network->get('https://example.com/foo');

// Base URI request
$response = $network->get('foo');
```

***

#### Sending a GET Request

In this example, a GET request is sent using the default `cURL` client provided by the `Network` class. 
The response is then processed to retrieve the status code and content. Any exceptions are caught and handled appropriately.

> **Note:** If you didn't specify  `stream` option set to `true`, you can directly get the contents by simply calling `getContents` from the response object instead of `$response->getBody()->getContents()` . This is only available using `cURL` client 

```php
<?php
$response = $network->get('foo');

if ($response->getStatusCode() === 200) {
    echo $response->getContents();
}
```
Optionally without using stream, you can still open a new stream and read and write the content to stream.

```php
<?php
$response = $network->get('foo');

if ($response->getStatusCode() === 200) {
    echo $response->getBody()->getContents();
}
```

***

#### Using Stream To Read Content In Parts

```php
<?php 
$response = $network->get('foo', [
    'stream' => true
]);

$body = $response->getBody();

while (!$body->eof()) {
    echo $body->read(1024);
}
```

***

### Sending a POST Request

```php
$response = $network->post('foo', [
    'form_params' => [
        'foo' => 'bar'
    ]
]);

if ($response->getStatusCode() === 201) {
    echo 'Data submitted successfully!';
}
```
***

### On Header Received Callback

You can handle large responses with callbacks:

```php
$response = $network->get('large-file', [
    'on_headers' => function (Response $response, array $header) {
        if ($response->getHeaderLine('Content-Length') > 1024) {
            throw new \Exception('The file is too big!');
        }
    }
]);
```

***

## Class Definition

* Class namespace: `\Luminova\Http\Network`
* This class implements: [\Luminova\Interface\NetworkInterface](/interface/classes.md#networkinterface), [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface)

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

Initializes the Network class with an optional client `ClientInterface` object.
If client is `NULL`, it uses `cURL` client for making HTTP requests.

```php
public __construct(?Psr\Http\Client\ClientInterface $client = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$client` | **ClientInterface<\T>&#124;null** | The HTTP client to used in making requests (default: `Curl`). |

***

### getClient

Retrieves the current HTTP client instance.
If using `cURL`, it will return cURL which implements `ClientInterface` interface otherwise it will return PSR `ClientInterface`.

```php
public getClient(): \Psr\Http\Client\ClientInterface
```

**Return Value:**

`\Psr\Http\Client\ClientInterface<\T>` - Return the instance of the HTTP client used for requests.

***

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

- `\Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `\Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `\Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `\Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

***

### get

Executes a GET request to the specified URL with optional data and headers.

```php
public get(string $uri = '', array<string,mixed> $options = []): ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uri` | **string** | The target URL or URI for the request (default: ''). |
| `$options` | **array** | Additional configuration options to use for the request (default: `[]`). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface` - Return the server's response to the GET request.

**Throws:**

- `\Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `\Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `\Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `\Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

***

### fetch

Sends an HTTP request and returns a promise that resolves with the response.

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
| `$uri` | **UriInterface\|string** | The target URL or URI for the HTTP request. |
| `$method` | **string** |  The HTTP method to use (e.g., `GET`, `POST`, `PUT`, `DELETE`). |
| `$options` | **array** | Additional configuration options to use for the request (default: `[]`). |

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
public post(string $uri = '', array<string,mixed> $options = []): ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uri` | **string** | The target URL or URI for the request (default: ''). |
| `$options` | **array** | Additional configuration options to use for the request (default: `[]`). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface` - Return the server's response to the POST request.

**Throws:**

- `\Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `\Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `\Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `\Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

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
| `$method` | **string** | The HTTP method to use (e.g., `Network::GET`, `Network::POST`,` Network::PUT`, `Network::DELETE`). |
| `$uri` | **UriInterface\|string** |  The request URI object or string (default: ''). |
| `$options` | **array** | Additional configuration options to use for the request (default: `[]`). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface` - Return the response returned by the server.

**Throws:**

- `\Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `\Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `\Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `\Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

***

### sendAsync

Sends an HTTP request asynchronously using a request object.

```php
public sendAsync(
    \Psr\Http\Message\RequestInterface $request, 
    array<string,mixed> $options = []
): PromiseInterface
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
| `$method` | **string** | The HTTP method to use (e.g, `Network::GET`, `Network::POST`, etc..). |
| `$uri` | **UriInterface\|string** | The request URI object or string (default: ''). |
| `$options` | **array<string,mixed>** | Request options to apply to the given request and to the transfer (default: `[]`). |

**Return Value:**

- `Luminova\Interface\PromiseInterface` - Return Luminova promise that resolves or reject to the request response.