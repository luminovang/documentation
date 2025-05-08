# Real-Time Communication using HTTP Socket Server

***

## Overview

HTTP Socket Server is a simple module for real-time communication for HTTP and WebSocket requests. It allows you to define routes and set up error and connection handlers all in one place.

***

## Introduction

The `HttpServer` class is designed to provide a flexible and efficient HTTP server for handling incoming requests. This class allows you to create a server that can manage different connection types, handle both HTTP and WebSocket protocols, and apply various configurations such as rate limiting, timeouts, port management, sequential and simultaneous client connections. It is ideal for developers building lightweight web applications, services, or custom servers that need to manage client-server communication efficiently.

### Key Features

- **Address and Port Binding:** Initialize the server with a specific address and port.
- **Automatic Startup:** Optionally start the server automatically upon creation.
- **Flexible Routing:** Route HTTP requests dynamically with support for `GET`, `POST`, and other HTTP methods.
- **WebSocket Support:** Accept WebSocket connections for real-time, bidirectional communication.
- **Rate Limiting and Timeouts:** Set limits on client requests and automatically close idle connections.
- **Debugging Support:** Enable or disable debug mode to output detailed information for troubleshooting.
- **Port Management:** Check if a port is in use and free it if necessary to prevent server failures.
- **Graceful Shutdown:** Cleanly close all connections and free up resources when the server is stopped.
- **Server Management:** Support for sequential and simultaneous Handling of Client Connections.

---

## Sequential vs. Simultaneous Client Connections

- **Sequential (Single Process Mode)**: 
  - When the `$simultaneous` flag is set to `false`, the server handles each connection one by one. This mode is simpler and can be useful for lightweight applications or scenarios where the traffic is low, and the overhead of managing multiple processes is unnecessary.
  - **Benefit**: Lower resource consumption (CPU and memory) due to the absence of process creation and management, suitable for environments with limited resources.

- **Simultaneous (Multi-Process Mode)**:
  - When the `$simultaneous` flag is set to `true`, the server forks a new process for each client connection, allowing multiple requests to be handled concurrently. Each new connection results in a forked child process, managed by the server, which allows handling numerous connections in parallel.
  - **Benefit**: Improved performance under heavy load, as multiple client requests are processed simultaneously. This mode is particularly useful for high-traffic applications where serving multiple clients concurrently is essential to avoid delays.

---

### Forking Processes

In **Multi-Process Mode**, the server employs process forking using the `pcntl_fork()` function. For each incoming client, the server creates a child process that handles the client's request independently of the main server process. This allows:

- **Non-blocking I/O**: Since each client is handled in its own process, other client connections are not blocked by long-running operations from another client.
- **Improved concurrency**: The server can handle multiple clients at the same time without waiting for one to finish before serving the next.
  
Additionally, the server uses mechanisms like **process cleanup** to manage the lifecycle of child processes and avoid process exhaustion by ensuring that the maximum allowed number of concurrent processes is respected.

---

### Key Difference

- **Sequential Mode**:
  - Simpler to manage.
  - Less overhead (no forking or process management).
  - May result in slower response times under high loads since clients are handled one at a time.
  
- **Simultaneous Mode**:
  - More complex due to process management.
  - Higher CPU and memory usage due to forking.
  - Much faster under high loads as multiple clients can be served in parallel.

This dual-mode approach allows you to customize the server's behavior to specific performance needs, ensuring both scalability and resource efficiency based on traffic patterns.

---

## Usage Examples

### Basic Server Setup

To create and start an HTTP server listening on `127.0.0.1` at port `8080`, you can initialize the server and begin accepting requests:

```php
use Luminova\Http\HttpServer;
$server = new HttpServer('127.0.0.1', 8080);
$server->start();
```

---

### Rate Limiting

You can configure the server to limit the number of requests a client can make to prevent abuse:

**Max 100 requests per 60 seconds:**

```php
$server->setRateLimit(100, 60);
```

### Connection Timeouts

You can configure the server's timeouts for idle connections to prevent resource exhaustion:

**Idle connections close after 30 seconds:**
```php
$server->setTimeout(30);
```

---

### Graceful Shutdown

When you need to shut down the server, call the `shutdown()` method to ensure all client connections are properly closed:

```php
$server->shutdown();
```

---

### HTTP Requests Handling

You can define routes for different HTTP methods using `get`,  `post` methods, allowing the server to handle incoming requests dynamically:

**With Response Class Object:**
```php
$server->get('/', function(HttpRequestInterface $request) {
    return new Response(
        200,
        ['Content-Type' => 'text/html'],
        '<h1>Hello, World!</h1>'
    );
});
```

**With String  Response:**

```php
$server->get('/', function(HttpRequestInterface $request) {
    return "HTTP/1.1 200 OK\r\nContent-Type: text/plain\r\n\r\Hello World!";
});
```

You can now test the server by visiting `http://127.0.0.1:8081/` on your browser or run below command for debugging:

```bash
curl -X GET "http://127.0.0.1:8081/
```

---

### WebSocket Protocol Handling

The server also supports WebSocket and SocketIO connections, which can handled by setting up `accept` or `route` method.

#### Enabling WebSocket Support

To accept WebSocket connections, simply enable the WebSocket functionality:

```php
$server->websocket(true);
```

#### WebSocket Handler

```php
$server->accept('/ws(?:/.*)?', function(string $message, array $payload) {
    logger('debug', "SocketIO Message: {$message}", $payload);
});
```

#### Socket.io Handler

```php
$server->accept('/socket.io(?:/.*)?', function(string $message, array $payload) {
    logger('debug', "WebSocket Message: {$message}", $payload);
});
```

You can set up real-time communication through these WebSocket endpoints, making your server more interactive.

---

### Debugging and Logs

If you're encountering issues, enabling debug mode provides more insights into what's going on.

```php
$server->debug();
```

To see which process is using a port, for example port `8081`, use this command:

```bash
lsof -i :8081
# kill -9 <PID> Optional kill the process if need to
```

---

### Server Shutdown

#### Graceful Termination with Signals

You can also handle signals like `SIGINT` (Ctrl+C) or `SIGTERM` for more graceful termination in CLI environments:

```php
declare(ticks = 1);

pcntl_signal(SIGINT, function () use ($server) {
    $server->shutdown();
    exit;
});

pcntl_signal(SIGTERM, function () use ($server) {
    $server->shutdown();
    exit;
});
```

####  Register Shutdown Function

In some cases, your script may exit unexpectedly due to an error or signal. You can use `register_shutdown_function` to ensure the socket is closed whenever the script exits:

```php
register_shutdown_function([$server, 'shutdown']);
```

---

## Class Definition

* Class namespace: `\Luminova\Http\HttpServer`

---

## Properties

### guid

A unique identifier for the WebSocket connection, used in the handshake process to establish communication.

```php
public string $guid = '258EAFA5-E914-47DA-95CA-C5AB0DC85B11';
```

***

## Methods

### constructor

Initializes the HTTP server by binding to the specified address and port.

This constructor sets up the necessary properties for the HTTP server and optionally starts
the server immediately if `$autostart` is set to `true`. The server can bind to an IP address
or hostname, with an optional port. You can also pass stream context options for advanced configurations.

```php
public __construct(
    string $address, 
    ?int $port = null, 
    bool $autostart = true, 
    resource|array|null $options = null
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string** | The connection IP address, hostname or a valid TCP connection string to bind to (e.g., `127.0.0.1`, `example.com`, or `tcp://127.0.0.1:8080`). |
| `$port` | **int&#124;null** | Optional port number between 1024 and 49151 to bind to (default: null). |
| `$autostart` | **bool** | Whether to start the server automatically upon initialization (default: true). |
| `$options` | **resource&#124;array&#124;null** | Optional resource for stream context or an array of options to create `stream_context_create` resource (default: `null`). |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - if the server fails to create a valid socket.

***

### __call

Magic method to route calls to undefined methods.

This method allows routing requests to the corresponding HTTP method handlers based on the method name.

```php
public __call(string $method, array $arguments): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | HTTP method (e.g., 'GET', 'POST'). |
| `$arguments` | **array** | The arguments to pass to the route. |

**Example**

```php
$server->put('/post', function(HttpRequestInterface $request) {
    return new Response(
        200,
        ['Content-Type' => 'text/plain'],
        "Post was created"
    );
});
```

***

### debug

Enables or disables debug mode for the application.

When debug mode is enabled, detailed debug messages will be output.
This can be useful for troubleshooting and understanding application behavior during development or testing.

```php
public debug(bool $debug = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$debug` | **bool** | Indicates whether to enable (true) or disable (false) debug mode. |

**Return Value:**

`self` - Returns instance of HttpServer.

***

### connectionRequired

Specifies whether the client must connect to the server before sending and receiving HTTP requests.

When set to true, the server requires clients to connect before making requests. In this case,
you must implement `connect()` and `disconnect()` routes to handle the connection lifecycle.

```php
public connectionRequired(bool $required = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$required` | **bool** | Whether a connection is required before handling requests (default: true). |

**Return Value:**

`self` - Returns instance of HttpServer.

**For example:**
- Clients should send a GET request to connect: `curl -X GET http://127.0.0.1:8080/connect`.
- Similarly, to disconnect: `curl -X GET http://127.0.0.1:8080/disconnect`.

> If set to false, you can handle the connection and disconnection logic manually without the need
for these specific routes.

***

### websocket

Configures whether the server should accept WebSocket connections.

```php
public websocket(bool $accept = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$accept` | **bool** | Set to true to enable WebSocket connections; false to disable. |

**Return Value:**

`self` - Returns instance of HttpServer.

***

### isConnected

Check if socket server is successfully connected to address and port.

```php
public isConnected(): bool
```

**Return Value:**

`bool` - Return true if is connected or otherwise false.

***

### isPortFree

Checks if the configured port is currently in use.

This method attempts to establish a connection to the provided address
and port. If a connection is successful, the port is considered in use.

```php
public isPortFree(): bool
```

**Return Value:**

`bool` - Return true if the port is in use, false otherwise.

***

### freePortIfStuck

Check if the port is in use and free it if necessary

```php
public freePortIfStuck(int $port = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$port` | **int** | The port number to check |

**Return Value:**

`bool` - Returns true if the port was freed, false otherwise

***

### setTimeout

Automatically close connections that are idle for too long.
This can prevent resource exhaustion if clients stay connected without sending requests.

```php
public setTimeout(int $timeout, int $microseconds = 0): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timeout` | **int** | The server timeout in seconds wait for idle connections. |
| `$microseconds` | **int** | The microseconds part of the timeout to be set (default: 0). |

**Return Value:**

`self` - Returns instance of HttpServer.

***

### setRateLimit

Sets the rate limit for incoming client requests.

This method defines the maximum number of requests a client can make
within a specified time window (in seconds). Helps prevent abuse or
overuse of the server's resources.

```php
public setRateLimit(int $maxRequests, int $timeWindow): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$maxRequests` | **int** | Maximum number of requests allowed in the time window. |
| `$timeWindow` | **int** | Time window in seconds for the request limit. |

**Return Value:**

`self` - Returns instance of HttpServer.

***

### setMaxProcesses

Set the maximum number of client processes to create when the third argument of `start()` is true.

This method allows you to define the limit on the number of simultaneous client processes
that can be spawned by the server. Setting this value helps to control resource usage
and prevents the server from being overwhelmed by too many concurrent connections.
It is particularly useful in environments with limited resources or when handling a
large number of client requests.

```php
public setMaxProcesses(int $max): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$max` | **int** |  |

**Return Value:**

`self` - Returns instance of HttpServer.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throw if `$max` is not a positive integer.

***

### setHeader

Set headers from an associative array of key-value pairs.

```php
public setHeader(string $key, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The header key/name to set. |
| `$value` | **mixed** | The value of the header key. |

**Return Value:**

`self` - Returns instance of HttpServer.

***

### setHeaders

Set headers from an associative array of key-value pairs.

```php
public setHeaders(array&lt;string,mixed&gt; $headers): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$headers` | **array<string,mixed>** | Associative array of headers, where the key is the header name<br />and the value is the header value. |

**Return Value:**

`self` - Returns instance of HttpServer.

***

### getSocket

Get the server socket resource for registration in the event loop.

```php
public getSocket(): ?resource
```

**Return Value:**

`resource|null` - The server socket resource or null.

***

### getId

Get the ID of the server socket.

```php
public getId(): int
```

**Return Value:**

`int` - Return the ID of the server socket.

***

### getAddress

Extracts the address and port from the DNS connection string.

If no address or port is found in the DNS, defaults to localhost
(127.0.0.1) and port 8080.

```php
public getAddress(): array
```

**Return Value:**

`array` - Return an associative array containing 'address' and 'port'.

***

### start

Starts the server by creating a socket and binding to the specified address and port.

This method attempts to create and bind a socket to the server's address and port. If the port
is already in use or any other error occurs, an exception is thrown. Additionally, it enables
non-blocking mode on the server socket.

```php
public start(): self
```

**Return Value:**

`self` - Returns the Http Server instance after the socket is successfully created.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the server cannot bind to the address or port, or if the port is already in use.

***

### run

Runs the server and begins listening for incoming connections.

This method initiates the server to handle incoming client connections and processes
requests based on routes. It can either handle connections sequentially or simultaneously based on
the `$simultaneous` flag. The server will run in a loop until it is stopped.

```php
public run(int|null $timeout = 100000, int $sleep = 10000, bool $simultaneous = false): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timeout` | **int&#124;null** | Optional timeout for client connections, in microseconds (default: 100000 µs). |
| `$sleep` | **int** | Time in microseconds to wait between connection attempts (default: 10000 µs). |
| `$simultaneous` | **bool** | Whether to allow multiple simultaneous connections (default: false). |

***

### shutdown

Gracefully shuts down the server and closes the socket connection.

This method ensures that the server is properly shut down, closing any open socket
connections to prevent resource leaks. It also clears all client connections and
routes. If a `DISCONNECT` callback is registered, it will be executed during shutdown.
Optionally, the method can attempt to free the port if it is stuck after shutdown.

```php
public shutdown(bool $kill = false): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$kill` | **bool** | Whether to attempt freeing the port if it's stuck (default: false). |

***

### on

Marks a client as connected to the server and sets its connection status.

```php
public on(string|null $address = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string&#124;null** | The client's IP address or identifier.<br />If not provided, defaults to the server's own IP address. |

***

### off

Marks a client as disconnected and removes it from the active clients list.

```php
public off(string|null $address = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string&#124;null** | The client's IP address or identifier.<br />If not provided, defaults to the server's own IP address. |

***

### accept

Accept an incoming client connection and route the request.

This method handles an incoming connection, reads the client's request,
and sends an HTTP response based on the defined routes. If a specific
response is provided, it will be used; otherwise, a default response
will be sent.

```php
public accept(string $pattern, \Closure $response): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | URI pattern or null for all (e.g, `/`, `/foo/{segment}`). |
| `$response` | **\Closure** | Optional response object or string. |

***

### get

Define a route for GET requests.

```php
public get(string $pattern, \Closure $response): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | URI pattern (e.g, '/') to match. |
| `$response` | **\Closure** | The response callback. |

***

### post

Define a route for POST requests.

```php
public post(string $pattern, \Closure $response): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | URI pattern (e.g, '/') to match. |
| `$response` | **\Closure** | The response callback. |

***

### ping

Register a simple "ping" route for health checks.

This method registers a `GET /ping` route, responding with a "PONG" message.
It can be used to verify that the server is responsive. The response includes
appropriate HTTP headers and a 200 OK status.

```php
public ping(): void
```

***

### error

Define a route to capture errors and response to with custom error.

```php
public error(\Closure $response): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$response` | **\Closure** | The response callback. |

***

### connect

Define a route for connection responses.

The callback should accept an array of connected clients associated with the request's IP address.

```php
public connect(\Closure|null $response = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$response` | **\Closure&#124;null** | The response callback.<br />Callback signature: function(array $clients): void<br />where $clients is an array of connected client information. |

***

### disconnect

Define a route for disconnection responses.

The optional callback can be used to process disconnection logic, accepting an array of
connected clients associated with the request's IP address.

```php
public disconnect(\Closure|null $response = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$response` | **\Closure&#124;null** | The response callback (optional).<br />Callback signature: function(array $clients): void<br />where $clients is an array of connected client information. |

***

### route

Define a route for specific HTTP methods.

```php
public route(string $methods, string $pattern, \Closure $response): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$methods` | **string** | HTTP methods to accept (e.g, `GET&amp;#124;POST&amp;#124;PUT`). |
| `$pattern` | **string&#124;null** | URI pattern or null for all (e.g, `/`). |
| `$response` | **\Closure** | The response callback. |

***

### onResponse

Sets a callback to intercept and modify the server response before sending it.

The provided closure should accept the response content and headers as parameters
and must return the modified string content.

```php
public onResponse(\Closure $intercept): void
```

**Example usage:**

```php
$server->onResponse(function(string $response, array $headers): string {
    $response .= '<p>Prepend Content</p>';
    return $response;
});
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$intercept` | **\Closure** | Callback to modify the response content. |