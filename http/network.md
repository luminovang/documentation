# Network Class for Managing HTTP Outgoing Requests

***

## Overview

Network class an ideal choice for developers looking to implement reliable and efficient HTTP communication in their applications with or without additional dependency required.

***

## Introduction

The `Network` class is a utility designed to simplify HTTP requests to external resources, such as APIs, web services, and websites. It simplifies the process of sending requests and efficiently handling responses, allowing you to read entire content or use the [File Input Stream](/files/stream.md) to read large contents in parts.

## Key Features

- **Independence**: The `Network` class operates independently, using `cURL` for making network requests, which allows for basic functionality without additional dependencies.
- **Complex Requests**: For more advanced use cases, the class integrates the [Guzzle HTTP Client](https://docs.guzzlephp.org/en/stable/overview.html) library, enabling developers to utilize advanced features and a wide range of customization options.

Whether performing simple `GET` and `POST` requests or engaging in more complex interactions, the `Network` class offers robust capabilities to meet your needs.

***

* Class namespace: `\Luminova\Http\Network`
* This class implements:
[\Luminova\Interface\NetworkInterface](/interface/classes.md#networkinterface)

***

## Usage Examples

**cURL Client Options:** Find the detailed [description below](/http/network#lmv-docs-configuration-options).

```php
[
    'verify'            => false,
    'max'               => 5,
    'connect_timeout'   => 0,
    'allow_redirects'   => true,
    'stream'            => false, 
    'sink'              => 'php://temp', // with read and write mode
    'file_time'         => false,
    'version'           => CURL_HTTP_VERSION_NONE,
    'decode_content'    => '',
    'referer'           => false,
    'output_headers'    => false,
    'headers'           => [ ... ],
    'on_headers'        => function (Luminova\Http\Message\Response $response, array $header) { ... },

    // The bellow options cannot be used together
    'query'             => [ ... ],
    'body'              => [ ... ],
    'form_params'       => [ ... ],
    'multipart'         => [ ... ],
];
```

### Class Initialization

You can initialize the Network class with either the **cURL** or **Guzzle** client. The client initialization options are immutable and cannot be overridden after initialization.

### Using cURL Client

```php
<?php
use Luminova\Http\Client\Curl;

$client = new Curl([
    'base_uri' => 'https://example.com/'
]);
```

### Using Guzzle Client

**Installation**

To use the Network class with Guzzle client, ensure you have the required packages installed. 
Install it via Composer:

```bash
composer require guzzlehttp/guzzle
```

#### Guzzle Client Options

When using `Guzzle` client all guzzle HTTP methods will be made available throw the Network class, for more detailed about Guzzle client and available options, refer to the [Guzzle documentation](https://docs.guzzlephp.org/en/stable/request-options.html).

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

## Methods

### constructor

Initializes the Network class with an optional client `NetworkClientInterface` object.
If client is `NULL`, it uses `cURL` client for making HTTP requests.

```php
public __construct(\Luminova\Interface\NetworkClientInterface|null $client = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$client` | **\Luminova\Interface\NetworkClientInterface<\T>&#124;null** | The HTTP client to used in making requests. |

***

### getClient

Retrieves the current HTTP client instance.
If using `cURL`, it will return cURL which implements `NetworkClientInterface` interface otherwise it will return PSR `ClientInterface`.

```php
public getClient(): NetworkClientInterface<\T>|ClientInterface<\T>
```

**Return Value:**

`\Luminova\Interface\NetworkClientInterface<\T>|\Psr\Http\Client\ClientInterface<\T>` - Return the instance of the HTTP client used for requests.

***

### send

Sends an HTTP request with the specified method, URL, data, and headers.

```php
public send(string $method, string $url, array $options = []): ResponseInterface|Response
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The HTTP method to use (e.g., GET, POST, PUT, DELETE). |
| `$url` | **string** | The target URL or URI for the request. |
| `$options` | **array** | Additional configuration options to use for the request (default: `[]`). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface|\Luminova\Http\Message\Response` - Return the response returned by the server.

**Throws:**

- `\Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `\Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `\Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `\Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

***

### get

Executes a GET request to the specified URL with optional data and headers.

```php
public get(string $url, array $options = []): ResponseInterface|Response
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The target URL or URI for the request. |
| `$options` | **array** | Additional configuration options to use for the request (default: `[]`). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface|\Luminova\Http\Message\Response` - Return the server's response to the GET request.

**Throws:**

- `\Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `\Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `\Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `\Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

***

### fetch

Fetches data from the specified URL using a GET request.

```php
public fetch(string $url, array $options = []): ResponseInterface|Response
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The target URL or URI for the request. |
| `$options` | **array** | Additional configuration options to use for the request (default: `[]`). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface|\Luminova\Http\Message\Response` - Return the server's response containing the fetched data.

**Throws:**

- `\Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `\Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `\Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `\Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

***

### post

Executes a POST request to the specified URL with the provided data and headers.

```php
public post(string $url, array $options = []): ResponseInterface|Response
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The target URL or URI for the request. |
| `$options` | **array** | Additional configuration options to use for the request (default: `[]`). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface|\Luminova\Http\Message\Response` - Return the server's response to the POST request.

**Throws:**

- `\Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `\Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `\Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `\Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

***

### request

Performs an HTTP request with the specified method, URL, data, and headers.

```php
public request(string $method, string $url, array $options = []): ResponseInterface|Response
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The HTTP method to use (e.g., GET, POST, PUT, DELETE). |
| `$url` | **string** | The target URL or URI for the request. |
| `$options` | **array** | Additional configuration options to use for the request (default: `[]`). |

**Return Value:**

`\Psr\Http\Message\ResponseInterface|\Luminova\Http\Message\Response` - Return the response returned by the server.

**Throws:**

- `\Luminova\Exceptions\Http\RequestException` - Throws if an error occurs while making the request.
- `\Luminova\Exceptions\Http\ConnectException` - Throws if a connection to the server cannot be established.
- `\Luminova\Exceptions\Http\ClientException` - Throws if the client encounters an error (`4xx` HTTP status codes).
- `\Luminova\Exceptions\Http\ServerException` - Throws if the server encounters an error (`5xx` HTTP status codes).

***

### Configuration Options

The following options are available for the **Luminova cURL client**, most of the option name are same in `Guzzle` client as well, to see full guzzle option read the documentation by following the link in this documentation.

** `verify` (string|bool)**

- **Description**: Determines whether to verify the peer's SSL certificate.
- **Default**: `false`
- **When to Use**: Set this to `true` to ensure secure connections, `false` if you're working with self-signed certificates or `string` to specify the certificate path `/path/to/cert.pem`.

** `max` (int)**

- **Description**: Specifies the maximum number of redirects to follow.
- **Default**: `5`
- **When to Use**: Adjust this value if you anticipate needing to follow more redirects. For example, set a higher value for APIs that might redirect multiple times.

** `output_headers` (bool)**

- **Description**: Specify weather to output header with the content body same as setting `CURLOPT_HEADER`.
- **Default**: `false`
- **When to Use**: Use this when you want to output or write both the header and body.

** `connect_timeout` (int)**

- **Description**: Sets the maximum time in seconds to wait for a connection to be established.
- **Default**: `0` (no timeout)
- **When to Use**: Use this to prevent long hangs when attempting to connect to a server. Set a reasonable timeout value (e.g., 10 seconds) to handle connection issues gracefully.

** `allow_redirects` (bool)**

- **Description**: Indicates whether to automatically follow HTTP redirects.
- **Default**: `true`
- **When to Use**: Keep this as `true` for most scenarios, especially when working with URLs that may redirect. Set to `false` if you want to handle redirects manually.

** `stream` (bool)**

- **Description**: Specifies whether to return the response as a stream instead of a string.
- **Default**: `false`
- **When to Use**: Set this to `true` if you expect large responses and want to process them as they arrive, rather than loading the entire response into memory at once.

** `sink` (path-string|resource)**

- **Description**: The path or resource to write the response body to.
- **Default**: `php://temp` (temporary memory storage)
- **When to Use**: Specify a file path if you want to save the response directly to a file, particularly useful for downloading large files.

** `file_time` (bool)**

- **Description**: Determines whether to retrieve the file modification time.
- **Default**: `false`
- **When to Use**: Set this to `true` if you need to check the last modified time of the file being downloaded.

**`version` (string)**

- **Description**: Specifies the HTTP version to use for the request.
- **Default**: `CURL_HTTP_VERSION_NONE`
- **When to Use**: Use this option if you need to enforce a specific HTTP version (e.g., `CURL_HTTP_VERSION_2_0`) for compatibility with server requirements.

**`decode_content` (string)**

- **Description**: Controls how to decode the response body.
- **Default**: Blank string (no specific decoding)
- **When to Use**: Specify encoding types (e.g., `gzip`, `deflate`) if you expect the server to return compressed content.

**`referer` (bool)**

- **Description**: Indicates whether to send the Referer header with the request.
- **Default**: `false`
- **When to Use**: Set this to `true` if you need to pass along the referring URL for analytics or tracking purposes.

**`query` (array)**

- **Description**: Query parameters for GET requests.
- **When to Use**: Use this to append parameters to the URL for GET requests, which can be useful for filtering or searching resources.

```php
'query' => [                    
    'Foo' => 'Bar'
],
```

** `body` (array)**

- **Description**: The request body for POST, PUT, and PATCH requests.
- **When to Use**: Use this when you need to send data in the body of a request, such as JSON payloads.

```php
'body' => [            
    'foo' => 'bar'
],
```

** `form_params` (array)**

- **Description**: Parameters for form submissions in POST, PUT, and PATCH requests.
- **When to Use**: Use this for sending `application/x-www-form-urlencoded` data, typical for form submissions.

```php
'form_params' => [              
    'foo' => 'bar'
],
```

** `multipart` (array)**

- **Description**: Contains multipart form data for uploading files and data.
- **When to Use**: Use this option when you need to upload files or large data as `multipart/form-data`.

```php
'multipart' => [             
    [
        'name'     => 'foo',
        'contents' => 'some data..',
    ],
    [
        'name'     => 'bar',
        'contents' => '/path/to/file/image.png' // If using path it must be readable
    ],
    [
        'name'     => 'baz',
        'contents' => new \CurlFile('/path/to/file/image.png'),
        'filename' => 'avatar.png'
    ]
],
```

** `headers` (array)**

- **Description**: Sets custom headers for the request.
- **When to Use**: Use this to add or override headers, such as authentication tokens or content types.

```php
'headers' => [                  
    'Foo' => 'Bar'
],
```

** `on_headers` (callable)**

- **Description**: A callback function that is called when headers are received.
- **When to Use**: Use this to perform custom logic based on response headers, such as checking content length or handling specific status codes.

```php
'on_headers' => function (Luminova\Http\Message\Response $response, array $header) { 
    // Custom logic when header is received
},
```