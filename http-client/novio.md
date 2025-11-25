# Novio HTTP Request Client

***

## Overview

Luminova's HTTP clients for network requests. You can use the lightweight Novio (cURL-based) client or switch to the advanced Guzzle client. Both share the same interface for consistent communication.

***

## Introduction

**Novio** is Luminova’s lightweight HTTP client designed for fast and reliable communication over the web. Built on PHP’s native **cURL** extension, it requires no extra dependencies and is ideal for projects that value performance and simplicity.

**Network Optimized Versatile I/O Operations**

* **N – Network**: Purpose-built for HTTP communication, leveraging native cURL.
* **O – Optimized**: Fast and efficient, minimizing overhead for high-performance apps.
* **V – Versatile**: Handles everything from single requests to concurrent multi-requests.
* **I – I/O (Input/Output)**: Manages all HTTP input/output operations with ease.
* **O – Operations**: Provides reliable request and response handling every time.

It works for everything from quick requests that return small responses to streaming large files using [File Input Stream](/files/data-stream.md), and can handle single requests or run multiple requests at the same time using PHP’s **CurlMultiHandle**, making it efficient for high-performance applications.

> Luminova also supports the **Guzzle** HTTP client, giving you a consistent way to work with HTTP in PHP while following PHP-FIG standards (PSR-7). 
> You can choose between Novio and Guzzle, and both use the same interface so you can switch without changing your code.
> For a consistent implementation when switching clients, see the [Network Request Helper](/http-client/network.md) documentation.

---

## Usages

See full documentation request [Client Options](/http-client/options.md) here.

### Make a Single Request

```php
use Luminova\Http\Client\Novio;

// Create a Novio client and set a base URL
$novio = new Novio(['base_uri' => 'https://example.com/']);

// Send a simple GET request
$response = $novio->request('GET', 'foo', [
    'Content-Type' => 'text/html',
]);

// Print the response body
echo $response->getBody()->getContents();
```

---

### Send a Request in the Background

You can send requests without waiting for them to finish immediately. Novio uses promises, so you can define what happens when the request succeeds or fails.

```php
use Luminova\Http\Client\Novio;

$novio = new Novio(['base_uri' => 'https://example.com/']);

$novio->requestAsync('GET', 'foo', [
    'Content-Type' => 'text/html',
])
->then(function(ResponseInterface $response) {
    // This runs if the request succeeds
    echo $response->getBody()->getContents();
})
->catch(function(Exception $e) {
    // This runs if something goes wrong
    echo $e->getMessage();
})
->error(function(Exception $e) {
    // You can also handle specific errors here
    echo $e->getMessage();
});
```

---

### Run Several Requests at Once

When you need to call multiple URLs quickly, Novio can send them all at the same time using PHP’s CurlMultiHandle.

```php
use Luminova\Http\Client\Novio;

// Queue up several GET requests
$multi = Novio::multi()
    ->add('GET', 'https://example.com')
    ->add('GET', 'https://example.org')
    ->add('GET', 'https://example.net');
```

**Run them and collect all results:**

```php
$multi->run();

$responses = $multi->getResponses();

// Inspect them all at once
var_export($responses);

// Access a specific response
// echo $responses[0]->getBody()->getContents();
```

**Or handle each response as soon as it finishes:**

```php
foreach ($multi->iterator() as $result) {
    echo $result->getBody()->getContents();
}
```

---

### Guzzle Request Client

A wrapper class for **Guzzle** `Luminova\Http\Client\Guzzle`

The Guzzle client gives you more power and flexibility for handling HTTP requests. It’s built on the popular Guzzle library and works seamlessly with the same API as the Novio client, so you can switch between them without rewriting your code.

**Basic Request Example**

```php
use Luminova\Http\Client\Guzzle;

// Create a Guzzle client with a base URL
$client = new Guzzle([
    'base_uri' => 'https://example.com/'
]);
```

> **Why use the Guzzle client?**
>
> * **More features out of the box** – Handles async requests, retries, request pools, and more advanced scenarios.
> * **Easy to extend** – Works with middleware and plugins for logging, caching, authentication, and other extras.
> * **Same interface as Novio** – Swap out Novio for Guzzle (or vice versa) without changing your code structure.