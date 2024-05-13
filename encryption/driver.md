# Encryption Driver

***

## Overview

The Encryption Driver allows you to secure sensitive data in your applications. By implementing either OpenSSL or Sodium encryption methods, with flexibility in choosing your encryption approach.

***

## Introduction

The Encryption Driver provides a flexible solution for data encryption within the Luminova Framework. It serves as an abstraction layer that supports multiple encryption algorithms, including OpenSSL and Sodium, ensuring robust security measures while offering developers the flexibility to choose the encryption method that best fits their needs.

***

* Class interface: `\Luminova\Interface\EncryptionInterface`

***


### constructor

Constructor, optional pass a blank key string and `setKey()` later before encrypt/decrypt.

 `OpenSSL` Class namespace: `\Luminova\Security\Encryption\OpenSSL`
 
```php
new OpenSSL(?string $key = null, ?string $method = null, int $size = 16);
```

`Sodium` Class namespace: `\Luminova\Security\Encryption\Sodium`

```php
new Sodium(?string $key = null, ?string $method = null, int $size = 16);
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | The encryption key. |
| `$method` | **string&#124;null** | Optional encryption cypher method.. |
| `$size` | **int** | Optional key size for encryption.. |


**Throws:**

- [\Luminova\Exceptions\InvalidException](/exceptions/classes.md#InvalidException) - If the method or block size is invalid while using openssl.


***

## Methods

***

### setData

Set the data to encrypt/decrypt.

```php
public setData(string $data): void
```


**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **string** | The data to encrypt/decrypt. |

***

### setKey

Set the encryption key.

```php
public setKey(string $key): void
```


**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The encryption key. |


***

### setNonce

Set nonce for encryption and decryption, if null random nonce will be generated.

```php
public setNonce(string|null $nonce = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$nonce` | **string&#124;null** | The nonce for encryption. |


***

### setMethod

Set the encryption method and block size for openssl, this method will be ignored on `Sodium`.

```php
public setMethod(string $method, int $size = 16): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The encryption cypher method. |
| `$mode` | **int** | Optional Key size for encryption.. |

**Throws:**

- [\Luminova\Exceptions\InvalidException](/exceptions/classes.md#InvalidException) - If the method or block size is invalid.


***

### nonce

Generate a random nonce, or return from a string.

```php
public nonce(string|null $string = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string&#124;null** | The string to extract the nonce from. |


**Return Value:**

The nonce string.

***

### encrypt

Encrypt data.

```php
public encrypt(): string|bool
```

**Return Value:**

The encrypted data, or false if encryption fails.



**Throws:**

- [\Luminova\Exceptions\InvalidException](/exceptions/classes.md#InvalidException) - If encryption fails due to invalid parameters.


***

### decrypt

Decrypt data.

```php
public decrypt(): string|bool
```


**Return Value:**

The decrypted data, or false if decryption fails.



**Throws:**

- [\Luminova\Exceptions\InvalidException](/exceptions/classes.md#InvalidException) - If decryption fails due to invalid parameters.


***

### free

Free up resources.

```php
public free(): void
```