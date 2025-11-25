# Novio HTTP Client Options

***

## Overview

Luminova's Novio HTTP client options let you control request behavior when sending requests, with many settings also supported by the Guzzle client.

***

## Introduction

The HTTP request client options are **customizable properties** used by Luminova's HTTP Novio client to control different aspects of an ongoing request. These options allow you to configure and fine-tune how the request is sent and processed. They include headers, query string parameters, timeout settings, the body of a request, and much more.
  
	
> **Note**
>
> These options are meant for the **Novio** HTTP client, Luminova’s built-in request handler. 
> While some align with **Guzzle’s** behavior, but they are not identical.
> 
> If you need the full set of Guzzle-specific options, check the official [Guzzle Request Options documentation](https://docs.guzzlephp.org/en/stable/request-options.html).

***

### Usages

Prepare the HTTP Novio request with options.

```php
use Luminova\Http\Client\Novio;

// Instantiate the HTTP client with non-overridable options
$client = new Novio([
    'base_uri' => 'http://example.com',
]);
```

---

Prepare the HTTP Guzzle request with options.

```php
use Luminova\Http\Client\Guzzle;

// Instantiate the HTTP client with non-overridable options
$client = new Guzzle([
    'base_uri' => 'http://example.com',
]);
```

Set the HTTP client to network class to make requests.

```php
use Luminova\Http\Network;

// Set the request client object
$network = new Network($client);

// Send a GET request with additional options
$response = $network->get('/foo/bar', [
    'headers' => [
        'Content-Type' => 'application/json',
    ],
]);
```

***

## Request Client Options

Find the detailed [description below](/http-client/options#lmv-docs-configuration-options).

```php
[
   'base_uri'          => null,
   'idn_conversion'    => true,
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
   'proxy'             => [ ... ],
   'cookies'           => Luminova\Interface\CookieJarInterface|string,
   'on_headers'        => callable (Luminova\Interface\ResponseInterface<MessageInterface> $response, array $header): void,
   'onBeforeRequest'   => callable (string $url, array $headers, ?int $jobId): void,
   'onProgress'        => callable (mixed $resource, float $downloadSize, float $downloaded, float $uploadSize, float $uploaded): void,

   // SSL certificate
   'verify'            => false,
   'ssl'               => [
      'cert_info'         => null,
      'ssl_cert_file'     => null,
      'cert_path'         => null,
      'ssl_cert_password' => null,
      'cert_timeout'      => null,
      'verify_host'       => 0
   ],

   // The bellow options cannot be used together
   'query'             => [ ... ],
   'body'              => [ ... ],
   'form_params'       => [ ... ],
   'multipart'         => [ ... ]
];
```

***

## Client Options

The following HTTP client options are available for Luminova's **Novio client**, most of the option are same in **Guzzle client** as well, to see full guzzle option read the documentation by following the link in this documentation.

---

### base_uri

Specifies the base URL for all subsequent requests made using the same client instance.  

- **Type**: `(string|null)`  
- **Default**: `null`  
- **Description**: When `base_uri` is set (e.g., `https://example.com`), you can provide only the endpoint path for requests (e.g., `foo/bar`). The client will automatically combine the base URL with the endpoint path. This simplifies request management, especially for APIs with a consistent base URL.  

**Example**

Create an HTTP client with a base URI:

```php
use Luminova\Http\Client\Novio;

$client = new Novio([
    'base_uri' => 'https://example.com'
]);
```

Every request now builds on that base:

```php
$response = $client->request('GET', 'api/users');
// Final URL: https://example.com/api/users

$response = $client->request('POST', 'api/posts', [
   'body' => [
      'data' => 'Post content'
   ]
]);
// Final URL: https://example.com/api/posts
```

---

### idn_conversion

Enables or disables Internationalized Domain Name (IDN) conversion. IDN conversion translates domain names containing non-ASCII characters (e.g., `exámple.com`) into their ASCII-compatible encoding (Punycode) if the `ext-intl` extension is available.

- **Type**: `(bool|int)`  
- **Default**: `true`  
- **Description**: When enabled, IDN conversion automatically converts domain names with Unicode characters to their ASCII-compatible representation (Punycode), ensuring compatibility with servers that do not support internationalized domain names.  
  - `true`: Enables automatic IDN conversion using `IDNA_DEFAULT` option.  
  - `false`: Disables IDN conversion. Domain names will remain in their original Unicode form.  
  - `int`: Allows you to specify additional flags for IDN conversion using `idn_to_ascii()` (e.g., `IDNA_*`). For more IDN intl constants visit [PHP documentation](https://www.php.net/manual/en/intl.constants.php).

### Example  

```php
use Luminova\Http\Client\Novio;

// Create a client with a base URI containing non-ASCII characters
$client = new Novio(['base_uri' => 'https://exámple.com']);

// IDN conversion enabled (default behavior)
$response = $client->request('GET', 'api/users', ['idn_conversion' => true]); 
// Resolves to: https://xn--exmple-cua.com/api/users

// IDN conversion disabled
$response = $client->request('POST', 'api/posts', [
   'idn_conversion' => false,
   'body' => [
      'data' => 'Post content'
   ]
]); 
// Resolves to: https://exámple.com/api/posts
```

IDN conversion with custom flags.

```php
$response = $client->request('GET', 'api/data', [
   'idn_conversion' => IDNA_NONTRANSITIONAL_TO_UNICODE
]); 
```

---

### verify

Determines whether to verify the peer's SSL certificate.

- **Type**: `(string|bool)`
- **Default**: `false`
- **Description**: Set this to `true` to ensure secure connections and use the default CA bundle provided by operating system. `false` if you're working with self-signed certificates or `string` to specify the certificate path `/path/to/cert.pem`.

**Example** 

```php
[
   'verify' => '/path/to/cert.pem'
]
```

---

### ssl 

On line the `verify` option that allow setting certificate path or using the default `CA`. The ssl option allows for full control over SSL configuration requests.

- **Type**: `(array<string,mixed>)`
- **Description**: Set this to `true` to ensure secure connections and use the default CA bundle provided by operating system. `false` if you're working with self-signed certificates or `string` to specify the certificate path `/path/to/cert.pem`.

**Option Keys**

- **cert_info** `(string|null)`: Path to the certificate authority file for verifying the SSL connection (e.g, `.crt`, `.pem`, `.cer`). Used with `CURLOPT_CAINFO` (default: `null`).  
- **ssl_cert_file** `(string|null)`: Path to the SSL certificate file for client-side authentication (e.g, `.pem`, `.p12`). Used with `CURLOPT_SSLCERT` (default: `null`).  
- **cert_path** `(string|null)`: Path to the directory containing certificate authority files. Used with `CURLOPT_CAPATH` (default: `null`).  
- **ssl_cert_password** `(string|null)`: Password for the SSL certificate file, if protected. Used with `CURLOPT_SSLCERTPASSWD` (default: `null`).  
- **cert_timeout** `(int|null)`: Timeout value (in seconds) for caching certificate authority information. Used with `CURLOPT_CA_CACHE_TIMEOUT` (default: `null`).  
- **verify_host** `(int|null)`: Flag to check the SSL certificate's host.  
   - `0`: Disable hostname verification (default: `0`).
   - `1`: Check if the Common Name exists in the SSL certificate.
   - `2`: Verify that the Common Name or Subject Alternative Name matches the host.

**Example**

```php
[
   'ssl' => [
      'cert_info' => 'path/to/ca_cert.crt', 
      'ssl_cert_file' => '/path/to/client_cert.p12',
      'cert_path' => '/path/to/cert/directory',
      'cert_timeout' => 3600,
      'ssl_cert_password' => 'password123',          // Password for .p12 client certificate
      'verify_host' => 2
   ]
]
```

> **Note:** 
> The `verify` option and `ssl` cannot be used together.

---

### max 

Specifies the maximum number of redirects to follow.

- **Type**: `(int)`
- **Default**: `5`
- **Description**: Adjust this value if you anticipate needing to follow more redirects. For example, set a higher value for APIs that might redirect multiple times.

---

### output_headers

Specify weather to output header with the content body same as setting `CURLOPT_HEADER`.

- **Type**: `(bool)`
- **Default**: `false`
- **Description**: Use this when you want to output or write both the header and body.

---

### connect_timeout

Sets the maximum time in seconds to wait for a connection to be established.

- **Type**: `(int)`
- **Default**: `0` (no timeout)
- **Description**: Use this to prevent long hangs when attempting to connect to a server. Set a reasonable timeout value (e.g., 10 seconds) to handle connection issues gracefully.

---

### allow_redirects

Indicates whether to automatically follow HTTP redirects.

- **Type**: `(bool)`
- **Default**: `true`
- **Description**: Keep this as `true` for most scenarios, especially when working with URLs that may redirect. Set to `false` if you want to handle redirects manually.

--- 

### stream

Specifies whether to return the response as a stream instead of a string.

- **Type**: `(bool)`
- **Default**: `false`
- **Description**: Set this to `true` if you expect large responses and want to process them as they arrive, rather than loading the entire response into memory at once.

---

### sink

The path or resource to write the response body to.

- **Type**: `(path-string|resource)`
- **Default**: `php:\/\/temp` (temporary memory storage)
- **Description**: Specify a file path if you want to save the response directly to a file, particularly useful for downloading large files.

---

### file_time

Determines whether to retrieve the file modification time.

- **Type**: `(bool)`
- **Default**: `false`
- **Description**: Set this to `true` if you need to check the last modified time of the file being downloaded.

---

### version

Specifies the HTTP version to use for the request.

- **Type**: `(string)`
- **Default**: `CURL_HTTP_VERSION_NONE`
- **Description**: Use this option if you need to enforce a specific HTTP version (e.g., `CURL_HTTP_VERSION_2_0`) for compatibility with server requirements.

---

### decode_content

Controls how to decode the response body.

- **Type**: `(string|bool|null)`
- **Default**: Blank string (no specific decoding)
- **Description**: Specify encoding types (e.g., `gzip`, `deflate`) if you expect the server to return compressed content.

**Examples:**

```php
[
   'decode_content' => true // Use default decoding
   'decode_content' => false // Disable decoding explicitly
   'decode_content' => null // Leave default (no decoding)
   'decode_content' => '' // Enable all encodings
   'decode_content' => 'gzip' // Enable specific encoding
]
```
---

### referer

Indicates whether to send the Referrer header with the request.

- **Type**: `(bool)`
- **Default**: `false`
- **Description**: Set this to `true` if you need to pass along the referring URL for analytics or tracking purposes.

--- 

### query

Query parameters for **GET** requests.

- **Type**: `(array<string,mixed>)` 
- **Description**: Use this to append parameters to the URL for **GET** requests, which can be useful for filtering or searching resources.

**Example:**

```php
// GET https://example.com/search?q=luminova&page=2

[
   'query' => [                    
      'q' => 'luminova',
      'page' => 2
   ]
]
```

---

### body

The request body for **POST, PUT, and PATCH** requests.

- **Type**: `(array<string,mixed>)` 
- **Description**: Use this when you need to send data in the body of a request, such as JSON payloads.

**Example:**

```php
[
   'body' => [            
      'name' => 'Peter',
      'email' => 'peter@example.com'
   ]
]
```
---

### form_params

Parameters for form submissions in **POST, PUT, and PATCH** requests. 
The data does not appear in the URL.

- **Type**: `(array<string,mixed>)`
- **Description**: Use this for sending `application/x-www-form-urlencoded` data, typical for form submissions.

**Example:**

```php
// POST email=test@example.com&password=secret
[
   'form_params' => [              
      'email' => 'test@example.com',
      'password' => 'secret'
   ]
]
```

---

### multipart

Contains multipart form data for uploading files and data.

- **Type**: `(array<string,array<string,mixed>>)`
- **Description**: Use this option when you need to upload files or large data as `multipart/form-data`.

**Example:**

```php
[
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
   ]
]
```

---

### headers

Sets custom headers for the request.

- **Type**: `(array<string,string>)`
- **Description**: Use this to add or override headers, such as authentication tokens or content types.

**Example:**

```php
[
   'headers' => [                  
      'X-For' => 'Value',
      'User-Agent' => 'Mozilla/5.0'
   ]
]
```

---

### on_headers

A callback function that is called when headers are received.

- **Type**: `(callable)`
- **Description**: Use this to perform custom logic based on response headers, such as checking content length or handling specific status codes.

**Example:**

```php
[
   'on_headers' => function (Luminova\Http\Message\Response $response, array $header) { 
      // Custom logic when header is received
   }
]
```

---

### onProgress

Track the progress of an HTTP request.

* **Type**: `(callable)`
* **Description**: Allows you to monitor the upload or download progress of a request using the Novio client. Useful for large file transfers or real-time progress feedback.

**Example:**

```php
[
   'onProgress' => function (mixed $resource, float $downloadSize, float $downloaded, float $uploadSize, float $uploaded): void {
      // Handle progress
   }
]
```

---

### onBeforeRequest

Hook into a request just before it's sent.

* **Type**: `(callable)`
* **Description**: Called before each HTTP request is dispatched. You can inspect or modify the request URL, headers, or perform logging and setup.

**Example:**

```php
[
   'onBeforeRequest' => function (string $url, array $headers, ?int $jobId): void 
   {
      // Pre-request handling
   }
]
```

---

### proxy

Set the proxy information to use for HTTP requests.

- **Type**: `(array<string,mixed>|string)`
- **Description**: Configures a proxy for HTTP requests. It can be specified as a string for a single proxy URL or an array for detailed proxy settings. The array format allows separate proxies for `http` and `https`, optional authentication credentials, and domains to bypass the proxy.

**Value Formats**:  

  - **String Format**: Directly specifies a single proxy URL.  
  - **Array Format**: Provides more custom control with support for:  
    - `http`: Proxy for HTTP requests.  
    - `https`: Proxy for HTTPS requests.  
    - `username`: Optional username for proxy authentication.  
    - `password`: Optional password for proxy authentication.  
    - `no`: List of domains to bypass the proxy.

**String Format Example** 

```php
[
   'proxy' => 'http://localhost:8125'
]
```

**Array Format Example**

```php
[
   'proxy' => [
      'http' => '1.1.1.1:8080',
      'https' => '1.1.1.1:8081',
      'username' => 'foo',
      'password' => 'bar',
      'no' => ['.foo.ng', 'example.com']
   ]
]
```

---

### cookies

Configure HTTP request cookie storage for persistent cookie requests.

- **Type**: `(Luminova\Interface\CookieJarInterface|string)`
- **Description**: This allows you integrate with `cUR`L Network request client class when making request that require cookies for session management or tracking.
For more information see the [documentation](/cookies/cookie-file-jar.md).

**String Format Example**  
```php
[
   'cookies' => 'path/to/writeable/tempt/cookies.txt'
]
```

**Cookie File Jar Example**  
```php
[
   'cookies' => new Luminova\Cookies\FileJar('path/to/writeable/tempt/cookies.txt', [...])
]
```