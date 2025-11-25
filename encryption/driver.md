# Encryption Driver for OpenSSL and Sodium

***

## Overview

Secure and flexible encryption system supporting OpenSSL and Sodium drivers in Luminova. Easily encrypt and decrypt data with modern cryptography standards.

***

## Introduction

The **Encryption Driver** is the foundation of Luminova’s encryption system.
It provides a clean and consistent API that abstracts away the complexity of working with different encryption libraries.

You can use either the **OpenSSL** or **Sodium** driver to handle encryption and decryption operations with ease. Both drivers support setting custom keys, methods, and key sizes, while following modern cryptographic best practices.

This makes it ideal for developers who want strong encryption without manually managing ciphers, padding, or key handling details.

For most applications, using the [Crypter Class](/encryption/crypter.md) is recommended it automatically selects and configures the best available encryption driver and method.

***

## Class Definition

* Openssl namespace: `\Luminova\Security\Encryption\Driver\Openssl`
* Sodium namespace: `\Luminova\Security\Encryption\Driver\Sodium`
* Implements:
[Luminova\Interface\EncryptionInterface](/interface/classes.md#encryptioninterface)

***

## Methods

### constructor

Creates a new encryption driver instance.

You can optionally pass a key during initialization or call `setKey()` later before performing encryption or decryption.

```php
public __construct(?string $key = null, ?string $method = null, int $size = 16): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | Optional encryption key or set later (via `setKey()`). |
| `$method` | **string&#124;null** | Optional encryption cypher method or set later (via `setMethod()`). |
| `$size` | **int** | Optional encryption key size to use if failed to determine size from method. |

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If the cipher method or block size is invalid when using OpenSSL.

**Examples:**

Using Openssl encryption handling.
 
```php
use Luminova\Security\Encryption\Driver\Openssl;

$openssl = new Openssl(
    key: 'mykey',
    method: 'AES-128-CBC',
    size: 16
);
```

Using Sodium encryption handling.

```php
use Luminova\Security\Encryption\Driver\Sodium;

$sodium = new Sodium(
    key: 'mykey',
    method: 'AES-128-CBC',
    size: 16
);
```

**Programmatic Example:**

Initialize based on application encryption configuration.

```php
use Luminova\Security\Encryption\Driver\{Openssl, Sodium};
use Luminova\Security\Encryption\Key;

$handler = Key::handler(true);
$method  = Key::method();
$key     = 'mykey';

$class = match ($handler) {
    'openssl' => Openssl::class,
    'sodium'  => Sodium::class,
    default   => throw new Error('Encryption handler not supported.'),
};

$crypto = new $class(
    $key,
    $method,
    Key::size($method)
);

// Encrypt
$crypto->setNonce($nonce); // Optional
$crypto->setData('Plain text');
$hash = $crypto->encrypt();

// Decrypt
echo $crypto->setData($hash)->decrypt();
```

> **Recommendation**
> 
> Use the [Crypter Class](/encryption/crypter.md) for automatic driver selection **(OpenSSL or Sodium)**, encryption method resolution, and key configuration.
> It provides a unified and secure interface without requiring manual setup.

***

### setData

Set the data to encrypt/decrypt.

This method allows you to specify encoded message hash to decrypt or a plain text to encrypt.

```php
public setData(string $data): EncryptionInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **string** | The data to encrypt or hash to decrypt. |

**Return Value:**

`Luminova\Interface\EncryptionInterface` - Return instance of encryption driver class.

***

### setKey

Set the encryption or decryption key.

```php
public setKey(string $key): EncryptionInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cryptography key. |

**Return Value:**

`Luminova\Interface\EncryptionInterface` - Return instance of encryption driver class.

***

### setNonce

Set nonce for encryption and decryption, if null random nonce will be generated.

```php
public setNonce(?string $nonce = null): EncryptionInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$nonce` | **string&#124;null** | Optional cryptography nonce for encryption. |

**Return Value:**

`Luminova\Interface\EncryptionInterface` - Return instance of encryption driver class.

> **Note:**
> This method is only useful with OpenSSl driver, if set in Sodium, the value will be ignored.

***

### setMethod

Set the encryption method and block size for openssl, this method will be ignored on `Sodium`.

```php
public setMethod(string $method, int $size = 16): EncryptionInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The encryption cypher method. |
| `$mode` | **int** | Optional encryption key size to use if failed to determine size from method. |

**Return Value:**

`Luminova\Interface\EncryptionInterface` - Return instance of encryption driver class.

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If the method or block size is invalid.

***

### nonce

Generate a random nonce, or return from a string.

```php
public nonce(?string $string = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string&#124;null** | The string to extract the nonce from. |

**Return Value:**

`string` - Return the generated encryption nonce string.

***

### encrypt

Encrypt data into an encoded message.

This method performs cryptography to generate an encryption hash from a plan text.

```php
public encrypt(): string|false
```

**Return Value:**

`string|false` Returns the encrypted hash value, or false if encryption fails.

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If encryption fails due to invalid parameters.

***

### decrypt

Decrypt an encoded message.

This method performs cryptography to decipher an encrypted hash into a readable plan text.

```php
public decrypt(): string|false
```

**Return Value:**

`string|false` - Returns the decrypted data, or false if decryption fails.

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If decryption fails due to invalid parameters.

***

### free

Free up encryption resources.

```php
public free(): void
```