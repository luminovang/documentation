## Encryption Interface Class

***

## Overview

A Comprehensive Guide to the luminova&#039;s Encryption Interface Class for PHP Framework Integration with OpenSSL and Sodium Libraries.

***

The Luminova Crypter class serves as a valuable asset for developers utilizing the Luminova PHP framework, offering a convenient and secure solution for implementing encryption and decryption functionalities. By abstracting away the complexities of cryptographic libraries and providing a unified interface, the Crypter class facilitates the integration of encryption features into PHP applications, enhancing overall security and reliability.


* Full name: `\Luminova\Interface\EncryptionInterface`


## Methods


### __construct

Constructor, optional pass a blank key string and `setKey()` later before encrypt/decrypt.

```php
public __construct(string $key = &#039;&#039;): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The encryption key. |


**Throws:**

- [\Luminova\Exceptions\`InvalidException`](/exceptions/classes#`InvalidException`) - If the method or block size is invalid while using openssl.


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


### setCypherAlgo

Set the encryption method and block size for openssl.

```php
public setCypherAlgo(int|null $blockSize, string $mode): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$blockSize` | **int&#124;null** | The block size for encryption. |
| `$mode` | **string** | The encryption mode. |

**Throws:**

- [\Luminova\Exceptions\`InvalidException`](/exceptions/classes#`InvalidException`) - If the method or block size is invalid.



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

`string` -  The nonce string.



***


### encrypt

Encrypt data.

```php
public encrypt(): string|bool
```

**Return Value:** 

`string|bool` -  The encrypted data, or false if encryption fails.


**Throws:**

- [\Luminova\Exceptions\`InvalidException`](/exceptions/classes#`InvalidException`) - If encryption fails due to invalid parameters.



***


### decrypt

Decrypt data.

```php
public decrypt(): string|bool
```

**Return Value:** 

`string|bool` -  The decrypted data, or false if decryption fails.



**Throws:**

- [\Luminova\Exceptions\`InvalidException`](/exceptions/classes#`InvalidException`) - If decryption fails due to invalid parameters.



***



### free

Free up resources.

```php
public free(): void
```