# HTTP Socket Server Examples

***

## Overview

In this example, we demonstrate how to create a simple HTTP Server to handle incoming HTTP, WebSocket, and SocketIO requests, routing each request accordingly.

***

## Introduction

This example demonstrates how to build a simple HTTP server capable of handling real-time communication using `HTTP`, `WebSocket`, and `Socket.IO` protocols. It also incorporates graceful shutdown and commands to start and stop the server.

---

### Getting Started

#### Prerequisites:
- **Luminova Framework**: Make sure you've installed the framework and its dependencies.
- **PHP CLI**: The server runs through PHP's CLI (Command Line Interface).

---

### Setting Up the Server

1. **Autoload Dependencies**: Before running the server, you'll need to load the composer dependencies. This is handled automatically by Luminova's `Boot.php`.

```php
require __DIR__ . '/system/Boot.php';
Boot::cli();  // Initialize CLI environment
```

2. **Create the Server**: Instantiate the `HttpServer` class by specifying the `IP` address and `port`. For example, we’re binding it to `127.0.0.1` on port `8081`.

```php
$server = new HttpServer('127.0.0.1', 8081, false);
```

> **Note:** Here we passed the third parameter `$autostart` to `false`, so we can manually handle start and shutdown with commands.

3. **Server Control Commands**: In this example the script supports options to start, stop, or kill the server via command line flags.

```bash
# Start the server
php server --start

# Stop the server
php server --stop

# Force stop the server and kill the port process
php server --stop --kill
```

---

### Full Sample Code

```php
// /server
#!/usr/bin/env php
<?php
use Luminova\Http\HttpServer;
use Luminova\Interface\HttpRequestInterface;
use Luminova\Http\Message\Response;
use Luminova\Boot;

/**
 * Autoload composer dependencies
 */
require __DIR__ . '/system/Boot.php';

// Initialize the CLI environment
Boot::cli();

// Create a new instance of the HTTP server
$server = new HttpServer('127.0.0.1', 8081, false);
$options = getopt("", [
    "start::",
    "stop::",
    "kill::"
]);

// Enable debug mode
$server->debug();

// Enable WebSocket support
$server->websocket();

// Set up connection handler
$server->connect(function(array $client) {
    logger('debug', "Connected: ", $client);
});

// Set up disconnection handler
$server->disconnect(function(array $client) {
    logger('debug', "Disconnected: ", $client);
});

// Set up error handler
$server->error(function(HttpRequestInterface $request, string $type) {
    return new Response(
        200,
        ['Content-Type' => 'text/plain'],
        "Form submitted!"
    );
});

// Set up GET route for the root URL
$server->get('/', function(HttpRequestInterface $request) {
    return new Response(
        200,
        ['Content-Type' => 'text/html'],
        "<h1>Hello, World!</h1>"
    );
});

// Set up GET route for user endpoint
$server->get('/user', function(HttpRequestInterface $request) {
    $name = $request->getGet('name', 'Guest');
    return new Response(
        200,
        ['Content-Type' => 'text/plain'],
        "Hello, {$name}"
    );
});

// Set up POST route for form submission
$server->post('/submit', function(HttpRequestInterface $request) {
    return new Response(
        200,
        ['Content-Type' => 'text/plain'],
        "Form submitted!"
    );
});

 $server->get('/foo', function(HttpRequestInterface $request) {
    return "HTTP/1.1 200 OK\r\nContent-Type: text/plain\r\n\r\Response string from foo!";
});

// Set up WebSocket message acceptance handler
$server->accept('/ws(?:/.*)?', function(string $message, array $payload) {
	logger('debug', "Websocket Message: {$message}", $payload);
});

// Set up socket.io message acceptance handler
$server->accept('/socket.io(?:/.*)?', function(string $message, array $payload) {
	logger('debug', "SocketIO Message: {$message}", $payload);
});

// Start the server
if(isset($options['start'])){
    $server->start()->run();
}

if(isset($options['stop'])){
    $server->shutdown(isset($options['kill']));
}

// Exit the script
exit(0);
```