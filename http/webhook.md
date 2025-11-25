# Webhook Request Helper

***

## Overview

The Webhook class is a simple and secure webhook helper for sending or receiving HTTP webhook requests.

***

## Introduction

The **Webhook** class in Luminova provides a simple and secure way to send and receive webhook requests over HTTP. It is designed to handle both **outgoing** and **incoming** webhook communications in your application, making it easier to integrate with third-party services or build internal automation features.

### What It Does

The `Webhook` class helps you:

* Send HTTP requests with custom headers and data (called "payloads").
* Automatically generate and attach a signature to the request using **HMAC** (hash-based message authentication).
* Receive incoming webhook requests and **verify** the signature to make sure the request is genuine and not tampered with.
* Process and access the request data securely.

> **You can use this class to build both:**
>
> * **Webhook clients** (sending data to other services).
> * **Webhook endpoints** (receiving and handling incoming webhooks).

---

## Usage

**Typical Use Cases:**

1. **Outbound Webhook**: Send a secure webhook from your app to another service when something happens (e.g. user signup, order completed).
2. **Inbound Webhook**: Accept webhooks from another system (e.g. payment gateway, GitHub, or Slack) and verify the data before processing it.

### Outgoing Webhook Request

This example sends a signed webhook request to an external URL using a shared secret for `HMAC` verification.

```php
use Luminova\Http\Webhook;

$webhook = (new Webhook('https://webhook.site/<WEBHOOK-TOKEN>'))
    ->setMethod('POST')
    ->setHeaders([
        'Accept' => 'application/json',
        'X-App-ID' => 'your-app-id',
    ])
    ->setPayload([
        'status' => 'OK',
        'user' => [
            'id' => 42,
            'email' => 'user@example.com',
        ],
    ])
    ->setSecret('your-shared-secret')     // Set secret key (optional if passed in sign())
    ->setAlgo('sha256')                   // Optional algorithm (default is sha256)
    ->setSignatureHeader('X-Signature')   // Set custom header name for signature
    ->setEvent('user.registered')         // Optional event name
    ->sign();                             // Generate and attach the signature

$response = $webhook->send();

if ($response->getStatusCode() === 200) {
    echo "Webhook sent successfully with signature: {$webhook->getSignature()}.";
} else {
    echo "Failed to send webhook. Status: " . $response->getStatusCode();
}
```

---

### Incoming Webhook Request

This example demonstrates how to listen for and validate incoming webhook requests using a shared secret and signature header.

```php
use Luminova\Http\Webhook;

// Create listener instance from incoming signature header
$webhook = Webhook::listen(request()->header->get('X-Signature'), 'user.registered')
    ->setAlgo('sha256')
    ->setSecret('your-shared-secret'); // Set secret to verify the signature

// Option 1: Get raw payload after verification
$data = $webhook->unpack();

// Option 2: Use callback to process payload
$data = $webhook->unpack(function(mixed $payload): mixed {
    // Do something with the payload
    return $payload;
});

// Check signature validity
if ($webhook->isSignature()) {
    // Payload verified
}
```

---

### Incoming Webhook Routing

This example shows how to define a dedicated route to handle webhook events inside a controller using the Luminova routing system.

**Controller Example:**

```php
namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Http\Webhook;
use Luminova\Attributes\Prefix;
use Luminova\Attributes\Route;
use App\Errors\Controllers\ErrorController;

#[Prefix(pattern: '/webhooks(:root)', onError: [ErrorController::class, 'onApiError'])]
class WebhooksController extends Controller 
{
    #[Route('/webhooks/users/', methods: ['POST'])]
    public function onUsersWebhook(): int 
    {
        $webhook = Webhook::listen($this->request->header->get('X-Signature'))
            ->setEvent('user.registered')
            ->setAlgo('sha256')
            ->setSecret('your-shared-secret');

        try {
            return $webhook->unpack(function(mixed $payload): int {
                // Handle the payload and return JSON response

                return response(200)->json([
                    'status' => 'OK',
                    'message' => 'Accepted',
                ]);
            });
        } catch (Throwable $e) {
            logger('error', $e->getMessage());
        }

        return response(500)->json([
            'status' => 'ERROR',
            'message' => 'Server error',
        ]);
    }
}
```

**Route Access:**

Any `POST` request sent to `https://example.com/webhooks/users` with a valid `X-Signature` header will be processed by this controller method, verifying the payload before accepting it.

---

## Class Definition

* Class namespace: `Luminova\Http\Webhook`
* This class implements: [Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)

---

**Things to Know:**

* **Payload**: The data you want to send or receive.
* **Signature**: A hash value used to verify that the payload was not changed during transfer.
* **Secret**: A key known only to your app and the other system to sign/verify requests.
* **Header Name**: The HTTP header used to send the signature (default: `X-Signature`, but customizable).

---

## Constants

### Protected Constants

| Constant    | Type   | Description                                                             |
| ----------- | ------ | ----------------------------------------------------------------------- |
| `EVENT_KEY` | string | Configuration key used to define the webhook event name in the payload. |
| `SIGN_KEY`  | string | Flag used to indicate if the payload should be signed before sending.   |

---

## Properties

### headers

Custom headers to include in the outgoing webhook request.

```php
protected array<string,mixed> $headers = []
```

***

### algo

The hashing algorithm used for `HMAC` signing (default: `sha256`).

```php
protected string $algo = 'sha256'
```

***

### xSignature

The name of the HTTP header used to transmit the payload signature.

```php
protected string $xSignature = 'X-Signature'
```

***

### salt

Salt key used in generating random secret for payloads sign signature.

```php
protected string $salt = 'app.webhook.salt';
```

> **Note:** The salt is used only when not secret is provided of payload signing.

***

### method

The HTTP method to use for the request (default: `POST`).

```php
protected string $method = 'POST';
```

***

### timeout

The request timeout in seconds (default: `0` for no timeout).

```php
protected int $timeout = 0;
```

***

## Methods

### constructor

Create a new webhook instance.

```php
public __construct(string $url, string $method = 'POST', int $timeout = 0): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The URL to which the webhook request will be sent. |
| `$method` | **string** | The HTTP method to use for the request (default: `POST`). |
| `$timeout` | **int** | The request timeout in seconds (default: `0` for no timeout). |

***

### getPayload

Retrieve the webhook payload.

This method returns either the full payload (including internal config)
or just the actual data section depending on the given flag.

```php
public getPayload(bool $withConfig = false): array|string|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$withConfig` | **bool** | Set to true to return the full payload including config metadata (default: `false`). |

**Return Value:**

`array|string|null` - Return the payload array, raw data string, or null if empty.

***

### getSignature

Get the HMAC signature generated for the payload.

```php
public getSignature(): ?string
```

**Return Value:**

`string|null` - Returns the signature string, or null if not generated.

***

### getSecret

Get the secret key used for signing or verifying the signature.

```php
public getSecret(): ?string
```

**Return Value:**

`string|null` - Returns the secret key, or null if not set.

***

### setMethod

Set the HTTP method to use for sending the request.

```php
public setMethod(string $method): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The HTTP method (e.g., GET, POST, PUT, DELETE). |

**Return Value:**

`Luminova\Http\Webhook` - Return instance of webhook class.

***

### setHeaders

Set custom headers to send with the request.

```php
public setHeaders(array<string,string> $headers): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$headers` | **array<string,string>** | Associative array of headers. |

**Return Value:**

`Luminova\Http\Webhook` - Return instance of webhook class.

**Example:**

```php
$webhook->setHeaders([
    'X-App-Token' => 'abc123',
    'Accept' => 'application/json'
]);
```

***

### setPayload

Set the data to be sent as the request payload
or verify as incoming payload.

```php
public setPayload(array|string $payload): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$payload` | **array&#124;string** | Payload can be an array or a raw JSON string. |

**Return Value:**

`Luminova\Http\Webhook` - Return instance of webhook class.

**Example:** 

```php
$webhook->setPayload(['order' => 'order.id']);

// Or String
$webhook->setPayload('order.id');

// Or String Json
$webhook->setPayload('{"order": "order.id"}');
```

***

### addPayload

Adds a key-value pair to the webhook payload.

```php
public addPayload(string $name, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The key to add to the payload. |
| `$value` | **mixed** | The value to assign to the given key. |

**Return Value:**

`Luminova\Http\Webhook` - Return instance of webhook class.

**Example:**

```php
$webhook->addPayload('event', 'user.created');
```

***

### setSecret

Set the secret key used for signing or verifying the payload.

```php
public setSecret(string $secret): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$secret` | **string** | The shared secret key. |

**Return Value:**

`Luminova\Http\Webhook` - Return instance of webhook class.

***

### setEvent

Set the event name inside the webhook payload configuration.

This stores the event name under the `_config` key of the payload, using the predefined `EVENT_KEY` constant.

```php
public setEvent(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The event name to assign (e.g., 'user.created', 'payment.failed'). |

**Return Value:**

`Luminova\Http\Webhook` - Return instance of webhook class.

***

### setTimeout

Set the request timeout duration in seconds.

```php
public setTimeout(int $seconds): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$seconds` | **int** | Number of seconds before the request times out. |

**Return Value:**

`Luminova\Http\Webhook` - Return instance of webhook class.

**Example:**

```php
$webhook->setTimeout(30); // 30 second timeout
```

***

### setAlgo

Set the HMAC hashing algorithm used for signing the webhook payload.

This defines the algorithm used in signature generation and verification (e.g., `sha256`, `sha512`).

```php
public setAlgo(string $algo): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$algo` | **string** | The name of the hashing algorithm (default is 'sha256'). |

**Return Value:**

`Luminova\Http\Webhook` - Return instance of webhook class.

***

### setSignatureHeader

Define the header name where the `HMAC` signature will be attached.

This sets the HTTP header used to transmit the payload signature (e.g., `X-Signature`, `X-MyApp-Signature`).

```php
public setSignatureHeader(string $xSignature): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$xSignature` | **string** | The custom header name for the signature (default: `X-Signature`). |

**Return Value:**

`Luminova\Http\Webhook` - Return instance of webhook class.

***

### sign

Enable **HMAC** signing for the webhook payload.

This method marks the payload as signable. When the webhook is sent, an `HMAC` signature
will be generated and included in the request headers under the defined signature header.

```php
public sign(?string $secret = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$secret` | **string&#124;null** | Optional secret key for signing.<br />If omitted, the value from `setSecret()` is used if available. |

**Return Value:**

`Luminova\Http\Webhook` - Return instance of webhook class.

**Example:** 

```php
$webhook->setPayload(['event' => 'update'])
    ->sign()
    ->setAlgo('sha256')
    ->setSignatureHeader('X-MyApp-Signature'); 
    
// Get the signature from request header 
// $request->header->get('X-Myapp-Signature')
```

> **Note:** If no secret is provided and none was set previously, a random secret will be generated.
> You can retrieve the key used by calling `getSecret()` after sending.

***

### listen

Listen and prepare a webhook for incoming verification.

This method initializes a new webhook instance with the provided signature and optional event name.
It should be called at the start of the webhook lifecycle to register the signature and context.

```php
public static listen(string $signature, ?string $event = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$signature` | **string** | The signature from the incoming request (e.g., `X-Signature` header). |
| `$event` | **string&#124;null** | Optional event name expected in the payload. |

**Return Value:**

`\T<Luminova\Http\Webhook>` - Return a new webhook instance configured for signature verification.

**Example:** 

```php
use Luminova\Http\Webhook;

$webhook = Webhook::listen(
    $request->header->get('X-Signature'), /* Or using $_SERVER['HTTP_X_SIGNATURE'] */
    'event-name' /* Optional event name to accept */
)
->setAlgo('sha256')
->setSecret('your-secret-key');
```

**See Also:**

* `Luminova\Http\Webhook::isSignature()` - Verifying incoming request signature.
* `Luminova\Http\Webhook::unpack()` - Unpack incoming request payload.

***

### unpack

Unpack and validate the incoming webhook data.

This method verifies the signature of the payload and optionally runs a callback
with the decoded data if validation succeeds.

```php
public unpack(?Closure $onResponse = null, ?array $data = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$onResponse` | **\Closure&#124;null** | Optional callback to handle the decoded payload. |
| `$data` | **array&#124;null** | Optional raw data to verify (defaults to request body). |

**Return Value:**

`mixed` - Return the decoded payload, or the result of the callback if provided.

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If the signature check fails or the webhook is misconfigured.
- [Luminova\Exceptions\LogicException](/error-handlers/exceptions.md#logicexception) - If called before initializing the webhook via `listen()`.

**Example:** 

```php
use Luminova\Http\Webhook;

$webhook = Webhook::listen(...);

$webhook->unpack(function(mixed $payload): mixed {
    // Handle verified data
    return $payload;
});

// Or get the payload directly
$data = $webhook->unpack();
```

> You can also pass raw input using `(new Request)->getBody()`.

***

### isSignature

Verify the webhook payload against its **HMAC** signature.

This method validates the incoming request using the provided or incoming detected payload
and compares the HMAC digest to the signature. If an event name is provided in the context, it is also validated against the payload.

```php
public isSignature(?array $data = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **array&#124;null** | Optional payload to validate (defaults to request body). |

**Return Value:**

`bool` - Return true if the payload and signature match; false otherwise.

***

### send

Send the configured webhook request to the target URL.

Automatically signs the payload if signing is enabled.
The payload is attached using the specified field type based on the request method.

**Available Fields:**

- `query` → For GET requests (appends data to the URL).
- `body` → For raw `POST/PUT/PATCH/DELETE` payloads (e.g. `JSON`).
- `form_params` → For `application/x-www-form-urlencoded`.
- `multipart` → For `multipart/form-data`.

```php
public send(?string $field = null): Luminova\Interface\ResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string&#124;null** | Optional request field to attach the payload.<br/>If null, defaults to `query` for GET and `body` for other methods. |

**Return Value:**

`Luminova\Interface\ResponseInterface<\Psr\Http\Message\ResponseInterface>` 
    - Return the HTTP response returned from the request.

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If the payload is invalid or missing during the signing process.
- [Luminova\Exceptions\RequestException](/error-handlers/exceptions.md#requestexception) - If error is encountered while sending webhook request.

***

### signPayload

Signs the encoded payload and sets the signature header.

This method is responsible for preparing and signing the payload using HMAC hashing.

It performs the following:
- Encodes the payload to string using `encode()`.
- Generates a secret if not already defined, using the app key and salt.
- Computes a signature using the chosen hashing algorithm and secret.
- Sets the signature in the request headers under the configured signature key.

```php
protected signPayload(): void
```

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If the encoded payload is empty or invalid.

**Overriding:**

If you override this method:
- Ensure `$this->rawPayload` is set with a valid encoded string.
- Validate the payload before signing to prevent empty or invalid input.
- Manually set `$this->signature` and `$this->headers[$this->xSignature]`.

```php
protected function signPayload(): void
{
    // Encode payload to string 
    $this->rawPayload = $this->encode($this->payload);

    // Validate 
    if (!$this->rawPayload) {
        throw new EncryptionException('Cannot sign: payload is empty or invalid.');
    }

    // Set or generate your secrete
    $this->secret = 'secrete';

    // Generate payload signature;
    $this->signature = 'secrete+hashing';

    // Set payload request X-Signature Header
    $this->headers[$this->xSignature] = $this->signature;
}
```