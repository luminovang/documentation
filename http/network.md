## Network Request

***

## Overview

The Network class is a valuable tool for working with external APIs or services. By providing a simple interface for sending HTTP requests and handling responses, with support for both cURL and Guzzle

***

The `Network` class is a utility designed to simplify making HTTP requests to external resources such as APIs, web services, or websites. It provides an easy-to-use interface for sending HTTP requests and handling responses, including accessing response headers, status codes, and response bodies.

By default, it uses `cURL` for making HTTP requests, but it also supports the `Guzzle` client for those who prefer it.

***

* Class namespace: `\Luminova\Http\Network`
* This class implements:
[\Luminova\Interface\NetworkInterface](/interface/classes#NetworkInterface)

## Methods

Before you dive into the network implementation, also take a look at the response interface [Network Response Object](/http/response).

### constructor

Constructor for NetworkInterface.

```php
public __construct(\Luminova\Interface\HttpClientInterface|null $client = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$client` | **\Luminova\Interface\HttpClientInterface&#124;null** | The HTTP client instance. |

***

### getClient

Get the HTTP client instance.

```php
public getClient(): \Luminova\Interface\HttpClientInterface
```

**Return Value:**

`\Luminova\Interface\HttpClientInterface` - The HTTP client instance.

***

### send

Send a request.

```php
public send(string $method, string $url, array $data = [], array $headers = []): \Luminova\Http\Message\Response
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The HTTP method (GET, POST, etc.). |
| `$url` | **string** | The URL to send the request to. |
| `$data` | **array** | The request body data. |
| `$headers` | **array** | The request headers. |

**Return Value:**

`\Luminova\Http\Message\Response` - The response from the request.

**Throws:**

- [RequestException](/exceptions/classes#RequestException) - If there is an error making the request.
- [ConnectException](/exceptions/classes#ConnectException) - If there is an error connecting to the server.
- [ClientException](/exceptions/classes#ClientException) - If there is an error on the client side.
- [ServerException](/exceptions/classes#ServerException) - If there is an error on the server side.

***

### get

Perform a GET request.

```php
public get(string $url, array $data = [], array $headers = []): \Luminova\Http\Message\Response
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The URL to send the request to. |
| `$data` | **array** | The request body data. |
| `$headers` | **array** | The request headers. |

**Return Value:**

`\Luminova\Http\Message\Response` - The response from the request.

**Throws:**

- [RequestException](/exceptions/classes#RequestException) - If there is an error making the request.
- [ConnectException](/exceptions/classes#ConnectException) - If there is an error connecting to the server.
- [ClientException](/exceptions/classes#ClientException) - If there is an error on the client side.
- [ServerException](/exceptions/classes#ServerException) - If there is an error on the server side.

***

### fetch

Fetch data using a GET request.

```php
public fetch(string $url, array $headers = []): \Luminova\Http\Message\Response
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The URL to send the request to. |
| `$headers` | **array** | The request headers. |

**Return Value:**

`\Luminova\Http\Message\Response` - The response from the request.

**Throws:**

- [RequestException](/exceptions/classes#RequestException) - If there is an error making the request.
- [ConnectException](/exceptions/classes#ConnectException) - If there is an error connecting to the server.
- [ClientException](/exceptions/classes#ClientException) - If there is an error on the client side.
- [ServerException](/exceptions/classes#ServerException) - If there is an error on the server side.

***

### post

Perform a POST request.

```php
public post(string $url, array $data = [], array $headers = []): \Luminova\Http\Message\Response
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The URL to send the request to. |
| `$data` | **array** | The request body data. |
| `$headers` | **array** | The request headers. |

**Return Value:**

`\Luminova\Http\Message\Response` - The response from the request.

**Throws:**

- [RequestException](/exceptions/classes#RequestException) - If there is an error making the request.
- [ConnectException](/exceptions/classes#ConnectException) - If there is an error connecting to the server.
- [ClientException](/exceptions/classes#ClientException) - If there is an error on the client side.
- [ServerException](/exceptions/classes#ServerException) - If there is an error on the server side.

***

### request

Perform a request.

```php
public request(string $method, string $url, array $data = [], array $headers = []): \Luminova\Http\Message\Response
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The HTTP method (GET, POST, etc.). |
| `$url` | **string** | The URL to send the request to. |
| `$data` | **array** | The request body data. |
| `$headers` | **array** | The request headers. |

**Return Value:**

`\Luminova\Http\Message\Response` - The response from the request.

**Throws:**

- [RequestException](/exceptions/classes#RequestException) - If there is an error making the request.
- [ConnectException](/exceptions/classes#ConnectException) - If there is an error connecting to the server.
- [ClientException](/exceptions/classes#ClientException) - If there is an error on the client side.
- [ServerException](/exceptions/classes#ServerException) - If there is an error on the server side.

***

### sendAsync

Send a request asynchronously.

```php
public sendAsync(\GuzzleHttp\Psr7\Request $request): \GuzzleHttp\Promise\PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$request` | **\GuzzleHttp\Psr7\Request** | The request object to send. |

**Return Value:**

`\GuzzleHttp\Promise\PromiseInterface` - A promise that resolves with the response.

### Examples

Send network request using default `Curl` client.

```php
<?php 
use \Luminova\Http\Network;
use \Luminova\Exceptions\AppException;
use \Exception;

$network = new Network();

try{
	$response = $network->get('https://example.com/api/foo');
	$statusCode = $response->getStatusCode();
	$content = $response->getContents();
} catch (AppException | Exception $e) {
	echo $e->getMessage();
}
```

Send a network request using `Guzzle`.

```php
<?php 
use \Luminova\Http\Network;
use \Luminova\Http\Client\Guzzle;
use \Luminova\Exceptions\AppException;
use \Exception;

$network = new Network(new Guzzle());

try{
	$response = $network->get('https://example.com/api/foo');
	$statusCode = $response->getStatusCode();
	$content = $response->getContents();
} catch (AppException | Exception $e) {
	echo $e->getMessage();
}
```