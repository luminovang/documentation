# JWT-Based Authentication Helper Class

***

## Overview

JWT helper class provides methods for handling authentication. With support for signing, decoding, and validating tokens, to ensure secure token-based authentication and makes it easy to manage.

***

## Introduction

The `JWTAuth` class is designed to handle the creation, decoding, and validation of JSON Web Tokens (JWT) for user authentication and authorization within the Luminova framework. It provides an easy way to manage JWTs using various signing algorithms and ensure secure token-based authentication for your applications.

***

### Default and Custom Claims in JWT Payload

When generating a JWT payload, the following default and custom claims are automatically included:

- `uid` **(string|int)**: The original user identifier. This can be a string or integer representing the user.
- `sub` **(string)**: The subject identifier, typically the user identifier in string format.
- `iat` **(int)**: The issued-at timestamp, representing when the token was created (current timestamp).
- `iss` **(string)**: The issuer of the token. Defaults to a predefined value if not provided.
- `aud` **(string)**: The intended audience for the token. Defaults to a predefined value if not provided.
- `exp` **(int)**: The expiration time of the token, expressed as a UNIX timestamp. Defaults to 1 hour from the current time if not provided.
- `fmv` **(string)**: The framework version used. Typically set to the current version of the framework.
- `app` **(string)**: The name of the application. Defaults to the application's name.
- `version` **(string)**: The application version code. Represents the version of the app generating the token.

***

### Usages Examples

The `JWTAuth` class can be instantiated either using the constructor or through the `getInstance()` method, which provides a singleton instance of the class.

#### Using the Constructor

```php
use \Luminova\Security\JWTAuth;

$jwt = new JWTAuth(
    'HS256',                   // Signing algorithm
    'my-password-salt',        // Password salt
    root('/writeable/jwt-keys/'), // Path to JWT keys
    'https://example.com',     // Issuer
    'https://api.example.com'  // Audience
);
```

#### Using the Singleton Method

```php
$jwt = JWTAuth::getInstance(...);
```

#### Basic JWT API Middleware Authentication

This middleware example demonstrates how to use JWT for API authentication, ensuring that only valid requests with proper JWT tokens are allowed access to the protected route.

```php
namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use App\Models\User;

class NewsController extends BaseController 
{
    #[Route('/api/news/(:root)', middleware: 'before', methods: ['ANY'])]
    public function newsMiddleware(): int 
    {
        $isValid = $jwt->validate(
            $this->request->getAuth(), // $_SERVER['HTTP_AUTHORIZATION']
            $this->request->header->get('X-Api-Client-Id'), // $_SERVER['HTTP_X_API_CLIENT_ID']
            function(bool $passed, stdClass $payload): bool 
            {
                if(!$passed){
                    return false;
                }

                return (new User)->isPaidClientAndNotExpired(
                    $payload->uid, 
                    $payload->customKey
                );
            }
        );

        if($isValid){
            return STATUS_SUCCESS;
        }

        response(401)->json([
            'status' => 401,
            'message' => 'Invalid news subscription, access denied.'
        ]);

        return STATUS_ERROR;
    }
}
```

***

### Validation Error Object

In some cases errors may occur while validating JWT, to retrieve information about the error see below:

**Object Key Descriptions:**

- `code` **(int)** - The error code.
- `message` **(string)** - The error message.
- `uid` **(string|int)** - The request client identifier associated with the error.
- `token` **(string|null)** - The request token associated with the error.

**Handing Error:**

```php 
if($jwt->validate($token, $client_id)){
    return STATUS_SUCCESS;
}

$err = $jwt->getError();

echo $err->code;
echo $err->message;
echo $err->uid;
echo $err->token;
```
Or within the payload object.

```php 
$jwt->validate($token, $client_id, function(bool $passed, stdClass $payload): bool {
    if(!$passed || $payload->err !== null){
        echo $payload->err->code;
        echo $payload->err->message;
        echo $payload->err->uid;
        echo $payload->err->token;
        return false;
    }

    return true;
});
```

***

## Class Definition

* Class namespace: `\Luminova\Security\JWTAuth`
* This class implements: [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface)

***

## Properties

### algo

The algorithm used for JWT encoding and decoding (default: `HS256`).

```php
protected ?string $algo = null;
```

***

### salt

Optional salt value used as a prefix for keys (default: null).

```php
protected ?string $salt = null;
```

***

### path

The directory path for storing generated keys (default: `/writeable/auth/`).

```php
protected ?string $path = null;
```

***

### iss

The issuer claim for the JWT, typically the application URL (default:` APP_URL` as `https://your-domain.com`).

```php
protected ?string $iss = null;
```

***

### aud

The audience claim for the JWT, typically the API endpoint (default: `APP_URL . '/api'` as `https://your-domain.com/api`).

```php
protected ?string $aud = null;
```

***

## Methods

### constructor

Initialize the JWTAuth class constructor with configurable JWT and key settings.

```php
public __construct(
    ?string $algo = null, 
    ?string $salt = null, 
    ?string $path = null, 
    ?string $iss = null, 
    ?string $aud = null
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$algo` | **string&#124;null** | The algorithm used for JWT encoding and decoding (default: `HS256`). |
| `$salt` | **string&#124;null** | Optional salt value used as a prefix for keys (default: `null`). |
| `$path` | **string&#124;null** | The directory path for storing generated keys (default: `/writeable/auth/`). |
| `$iss` | **string&#124;null** | The issuer claim for the JWT, typically the application URL (default: `APP_URL`). |
| `$aud` | **string&#124;null** | The audience claim for the JWT, typically the API endpoint (default: `APP_URL . '/api'`). |

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - If the provided $path is not readable or writable.

***

### getInstance

Get or create a singleton instance of the JWTAuth class.

This method implements the Singleton pattern, ensuring that only one instance
of the JWTAuth class exists throughout the application's lifecycle.

```php
public static getInstance(
    ?string $algo = null, 
    ?string $salt = null, 
    ?string $path = null, 
    ?string $iss = null, 
    ?string $aud = null
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$algo` | **string&#124;null** | The algorithm used for JWT encoding and decoding (default: `HS256`). |
| `$salt` | **string&#124;null** | Optional salt value used as a prefix for keys (default: `null`). |
| `$path` | **string&#124;null** | The directory path for storing generated keys (default: `/writeable/auth/`). |
| `$iss` | **string&#124;null** | The issuer claim for the JWT, typically the application URL (default: `APP_URL`). |
| `$aud` | **string&#124;null** | The audience claim for the JWT, typically the API endpoint (default: `APP_URL . '/api'`). |

**Return Value:**

`self` -  Returns the singleton instance of the JWTAuth class.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - If the provided $path is not readable or writable.

***

### getError

Retrieves the validation error object.

This method returns the error object set during the last validation operation. If no error occurred or no validation has been performed, it returns null.

```php
public static getError(): ?object
```

**Return Value:**

`object` -  Return the error object containing details about the last validation error, or null if no error occurred or no validation has been performed.

***

### encode

Generates a JWT token using the `HS256` algorithm.

This method creates a JWT token with a provided payload, user identifier, and custom claims (such as role or token ID).
It also supports specifying the expiration time of the token and optional custom `iss` (issuer) and `aud` (audience) claims. 

```php
public encode(
    array $payload, 
    string|int $user_id, 
    int $expiry = 3600, 
    ?string $token_id = null, 
    string|int|null $role = null
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$payload` | **array** | The payload for the JWT, containing any custom claims to be included in the token. |
| `$user_id` | **string&#124;int** | The identifier for the user. This is typically a unique ID associated with the user. |
| `$expiry` | **int** | The expiration time in seconds (default: 3600). |
| `$token_id` | **string&#124;null** | JWT ID: a unique identifier for this token (optional). |
| `$role` | **string&#124;int&#124;null** | Custom claim: the role of the user (optional). |

**Return Value:**

`string` -  Return the generated JWT token.

**Example:** 

Encodes a payload into a JWT token.

```php
$payload = ['name' => 'John Doe'];
$user_id = 123;
$expiry = 7200; // 2 hours
$token = $jwt->encode($payload, $user_id, $expiry, null, 'admin');
echo $token; // Outputs the generated JWT token
```

***

### decode

Decode and validate a JWT token.

This method decodes a JWT token using the specified algorithm and the user-specific key.
It validates the token by checking its signature and expiration time. If the token is valid, it returns the decoded payload as an object, otherwise, it returns `false`.

```php
public decode(string $token, string|int $user_id): \stdClass|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$token` | **string** | The JWT token to decode. |
| `$user_id` | **string&#124;int** | The user identifier, used to generate the key for decoding. |

**Return Value:**

`\stdClass|false` -  Returns the decoded JWT payload as an object if valid, false otherwise.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - Throws on development mode if an error is encountered while decoding.

**Example:** 

Decodes and validates a JWT token.

```php
$token = 'eyJhbGciOi...';
$user_id = 123;
$decoded = $jwt->decode($token, $user_id);

if ($decoded !== false) {
    // Token is valid, use $decoded->sub to access the user ID or other claims
} else {
    // Token is invalid or expired
}
```

***

### validate

Validate token and user ID, with an optional callback for additional validation on the payload.

This method checks the validity of a token and its associated user ID. Optionally, you can provide a callback to perform additional validation on the token's payload (e.g., checking user quota). If the token and user ID are valid, it returns `true`; otherwise, it returns `false`. The token format can include non or any scheme (e.g., "Bearer", "Token", etc.).

```php
public validate(?string $token, string|int $user_id, \Closure|null $callback = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$token` | **string&#124;null** | The authentication token, typically in the format `scheme token` (e.g., `Bearer my-token`). |
| `$user_id` | **string&#124;int** | The user identifier, which should match the `user_id` claim in the token. |
| `$callback` | **\Closure&#124;null** | An optional callback function that is invoked with the validation result and the decoded payload.<br />The callback can be used to perform additional checks, such as user-specific data validation. |

**Return Value:**

`bool` -  Returns `true` if the authentication is valid, otherwise `false`.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - Throws on development mode if an error is encountered while decoding.

**Example:**

Validates a token and user ID, without callback:

```php
$isValid = $jwt->validate('Bearer my-token', 'user-id');
if ($isValid) {
    // Authentication is valid
} else {
    // Authentication failed
}
```

Validates a token and user ID, with a callback:

```php
$isValid = $jwt->validate('Bearer my-token', 'user-id', function(bool $passed, \stdClass $payload): bool {
    if (!$passed) {
        return false; // Reject the token if initial validation fails
    }
    
    // Additional validation based on payload
    if ($payload->maxQuota > 0) {
        $quota = (new User())->find($payload->uid, ['usage_quota']);
        return $quota ? $payload->maxQuota < $quota->usage_quota : false;
    }
    return true; // Accept the token if the custom validation passes
});
if ($isValid) {
    // Authentication is valid
} else {
    // Authentication failed
}
```

***

### validateFromFile

Validate the authentication key from a file stored in a specified path.

This method checks if a valid authentication token exists in a file corresponding to the provided user identifier (`user_id`). If the file exists and contains a token, it proceeds to validate the token. An optional callback can be provided to perform additional validation on the decoded payload. If the validation is successful, it returns `true`; otherwise, it returns `false`.

```php
public validateFromFile(string|int $user_id, \Closure|null $callback = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$user_id` | **string&#124;int** | The user identifier whose authentication token is stored in a file. |
| `$callback` | **\Closure&#124;null** | An optional callback function that can be used to perform additional validation on the payload.<br />The callback will receive the validation result and the decoded payload. |

**Return Value:**

`bool` -  Returns `true` if the authentication is valid and the token was successfully validated, otherwise `false`.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - Throws on development mode if an error is encountered while decoding.

**Example:** 

Without callback:

```php
$isValid = $jwt->validateFromFile('user-id');

if ($isValid) {
    // Authentication is valid
} else {
    // Authentication failed
}
```
With callback:

```php
$isValid = $jwt->validateFromFile('user-id', function(bool $valid, \stdClass $payload): bool {
    if (!$valid) {
        return false; // Reject the token if validation fails
    }
    
    // Perform additional checks on the payload
    return isset($payload->role) && $payload->role === 'admin';
});

if ($isValid) {
    // Authentication is valid
} else {
    // Authentication failed
}
```

***

### filename

Generate a hashed or plain filename for storing a user's private key on the server.

This method creates a filename based on the provided `user_id`. By default, the filename is hashed using the MD5 algorithm for security purposes, but this behavior can be controlled by the `$md5_hash` parameter. The filename will always have a `.key` extension, ensuring it is suitable for storing private keys.

```php
public static filename(string|int $user_id, bool $md5_hash = true): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$user_id` | **string&#124;int** | The user identifier used to generate the filename. |
| `$md5_hash` | **bool** | Whether to hash the filename using the MD5 algorithm (default: `true`). |

**Return Value:**

`string` -  Return the generated filename, either hashed or plain, with a `.key` extension.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - If the `$user_id` is empty or invalid.

**Example:**

With hashed filename:

```php
$filename = JWTAuth::filename('user-id');
// Example output: 'c4ca4238a0b923820dcc509a6f75849b.key'
```
With plain filename:

```php
$filename = JWTAuth::filename('user-id', false);
// Example output: 'user-id-private-jwt-key-file.key'
```

***

### key

Generate a securely hashed encryption key using the user identifier and the specified hashing algorithm.

This method creates an encryption key based on the user identifier (`user_id`), using a specified hashing algorithm (e.g., `sha256`, `md5`, etc.). It also includes the application key (`app.key`) from the environment configuration as a salt for added security.

```php
public key(string|int $user_id, string $algo = 'sha256'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$user_id` | **string&#124;int** | The user identifier to generate the encryption key. |
| `$algo` | **string** | The hashing algorithm to use for the encryption key (default: `sha256`). |

**Return Value:**

`string` -  Return the securely hashed encryption key.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - If the user identifier is empty or the application key is missing.

**Example:**

With default algorithm (sha256):

```php
$key = $jwt->key('user-id');
// Example output: 'b3d14f6e5576c4d8c825489f3b8b21076c85a1695d9ecf264228db24919fc699'
```
With a custom hashing algorithm (e.g., md5):

```php
$key = $jwt->key('user-id', 'md5');
// Example output: 'f623b2b3d1e9c139aadad62f0c5d4a4327b20e63'
```

***

### sign

Generate a JWT token based on user ID, sign it, and store the private key on the server.

This method generates a JWT token with the specified payload and user ID, signs it, and stores the private key
on the server in a file. It also computes a hash of the key file, which can be used for file identification.
The token is stored with an expiration time (default of 30 days), and the key file hash is returned for further use.
If the token cannot be signed or stored, the method returns `false`.

```php
public sign(array $payload, string|int $user_id, int $expiry = 2592000, ?string &$file_hash = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$payload` | **array** | The additional data to include in the JWT payload. |
| `$user_id` | **string&#124;int** | The unique identifier for the user. |
| `$expiry` | **int** | The expiration time in seconds (default is 30 days or 2592000 seconds). |
| `&$file_hash` | **string&#124;null** | A reference to a variable that will hold the key file hash, used to identify the stored key file. |

**Return Value:**

`bool` -  Returns `true` if the token was successfully signed and stored, otherwise `false`.

**Example:**

Signing a JWT token for a user:

```php
$payload = ['role' => 'admin', 'maxQuota' => 100];
$user_id = 'user123';
$file_hash = null;
$result = $jwt->sign($payload, $user_id, 3600, $file_hash);

if ($result) {
    echo "Token signed and stored. File hash: $file_hash";
} else {
    echo "Failed to sign or store the token.";
}
```

***

### store

Store the user private key on the server.

This method stores the provided authentication key (JWT token) in a file on the server.
It ensures that the necessary directory exists and deletes any existing file with the same name
before saving the new key. If the storage process fails at any point, the method will return `false`.

```php
public store(string $filename, string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The name of the file to store the key in. |
| `$key` | **string** | The authentication key value (e.g., JWT token) to store. |

**Return Value:**

`bool` -  Returns `true` if the key was successfully saved, `false` otherwise.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) Throws on development mode if an error occurs while saving the key.

**Example:**

Encoding a JWT token and storing it in a file:

```php
$payload = ['role' => 'admin', 'maxQuota' => 100];
$user_id = 'user123';
$expiry = 3600; // 1 hour

// Generate JWT token using encode method
$token = $jwt->encode($payload, $user_id, $expiry);

// Store the token using store method
$filename = JWTAuth::filename($user_id);
$isStored = $jwt->store($filename, $token);

if ($isStored) {
    echo "Token stored successfully in file: $filename";
} else {
    echo "Failed to store the token.";
}
```

