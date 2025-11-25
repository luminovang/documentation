# Crypter Encryption and Decryption  Helper Class

***

## Overview

Luminova Crypter makes encryption simple. Encrypt and decrypt data easily with Sodium or OpenSSL using one unified, beginner-friendly static method.

***

## Introduction

The **Crypter** class is a simple helper for encryption and decryption, it makes encryption in Luminova easier, safer, and more consistent.

It automatically uses your application’s encryption settings, so you don’t have to manually set up or switch handlers. Whether you choose **Sodium** or **OpenSSL**, Crypter provides one easy interface for both.

This means you can:

* Encrypt and decrypt data with just a single call.
* Switch between handlers without changing your code.
* Avoid dealing with the low-level details of each handler.

***

## Class Definition

* Class namespace: `Luminova\Security\Encryption\Crypter`
* This class is marked as **final** and can't be subclassed

***

## Methods

### getInstance

Create and return the configured encryption handler instance.

Initializes the selected handler with the encryption key, cipher method, and key size.

> You may pass an explicit key via the $key argument, fall back to the application key (env `app.key`).
> Configuration class is located in your controller configuration directory in `/app/Config/Encryption.php`.

```php
public static getInstance(?string $key = null): Luminova\Interface\EncryptionInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string\|null** | Optional encryption key (default: `env('app.key')`). |

**Return Value:** 

`EncryptionInterface` -  Return instance of application encryption handler (e.g, `Openssl` or `Sodium`).

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) 
  - If the application key is missing.
  - If the handler is invalid or the required extension is not loaded.
  - If Sodium < 1.0.14 is installed.

***

### encrypt

Encrypt plaintext with the default encryption configuration.

Generates a nonce if required and uses the configured handler **(OpenSSL or Sodium)** to perform encryption.

```php
public static encrypt(string $data, ?string $key = null, ?string $nonce = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **string** | The plaintext to encrypt. |
| `$key` | **string\|null** | Optional encryption key (default: `env('app.key')`). |
| `$nonce` | **string\|null** | Optional nonce (default: `auto-generate`). |

**Return Value:**

`string|false` -  Return encrypted data in binary or base64, or false on failure.

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If the input is invalid or encryption fails.

**Example**

```php
use Luminova\Security\Encryption\Crypter;

$encrypted = Crypter::encrypt('Hello world');
```

***

### decrypt

Decrypt a **ciphertext** with the default application encryption configuration or custom key.

Restores plaintext from the encrypted input. Validates data and propagates errors from the underlying handler.

```php
public static decrypt(string $data, ?string $key = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **string** | The encrypted data to decrypt. |
| `$key` | **string\|null** | Optional encryption key (default: `env('app.key')`). |

**Return Value:** 

`string|false` - Return decrypted plaintext or false on failure.

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If the input is invalid or decryption fails.

**Example**

```php
use Luminova\Security\Encryption\Crypter;

$encrypted = Crypter::encrypt('Hello world');

echo Crypter::decrypt($encrypted);
```