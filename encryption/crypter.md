# Crypter

***

## Overview

Luminova's Crypter class provides methods for encrypting and decrypting data using encryption algorithms in OpenSSL or Sodium.

***

## Introduction

Luminova Crypter class is straightforward means to implement encryption and decryption functionalities in anywhere in your applications. This helper class seamlessly integrates both Sodium and OpenSSL encryption libraries, providing a unified and simplified interface for encrypting and decrypting data without the need for manual initialization of handlers.

* Class namespace: `\Luminova\Security\Crypter`
* Encryption class interface: [\Luminova\Interface\EncryptionInterface](/interface/classes.md#encryptioninterface)

***

## Methods

Supported AES encryption methods with their corresponding key sizes.

```php
public static array $ciphers = []
```

Each cipher entry specifies the size of the encryption key in bytes.

- `AES-128-CBC` - 128-bit key (16 bytes)
- `AES-192-CBC` - 192-bit key (24 bytes)
- `AES-256-CBC` - 256-bit key (32 bytes)
- `AES-128-CBC-HMAC-SHA1` - 128-bit key (16 bytes)
- `AES-256-CBC-HMAC-SHA1` - 256-bit key (32 bytes)
- `AES-128-CBC-HMAC-SHA256` - 128-bit key (16 bytes)
- `AES-256-CBC-HMAC-SHA256` - 256-bit key (32 bytes)
- `AES-128-CFB` - 128-bit key (16 bytes)
- `AES-192-CFB` - 192-bit key (24 bytes)
- `AES-256-CFB` - 256-bit key (32 bytes)
- `AES-128-CFB1` - 128-bit key (16 bytes)
- `AES-192-CFB1` - 192-bit key (24 bytes)
- `AES-256-CFB1` - 256-bit key (32 bytes)
- `AES-128-CFB8` - 128-bit key (16 bytes)
- `AES-192-CFB8` - 192-bit key (24 bytes)
- `AES-256-CFB8` - 256-bit key (32 bytes)
- `AES-128-CTR` - 128-bit key (16 bytes)
- `AES-192-CTR` - 192-bit key (24 bytes)
- `AES-256-CTR` - 256-bit key (32 bytes)
- `AES-128-ECB` - 128-bit key (16 bytes)
- `AES-192-ECB` - 192-bit key (24 bytes)
- `AES-256-ECB` - 256-bit key (32 bytes)
- `AES-128-OFB` - 128-bit key (16 bytes)
- `AES-192-OFB` - 192-bit key (24 bytes)
- `AES-256-OFB` - 256-bit key (32 bytes)
- `AES-128-XTS` - 128-bit key (16 bytes)
- `AES-256-XTS` - 256-bit key (32 bytes)

***

### getInstance

Get an instance of OpenSSL or Sodium encryption depending on your configurations in encryption .class located in your controller configuration directory in `/app/Config/Encryption.php`.

```php
public static getInstance(): \Luminova\Interface\EncryptionInterface
```

**Return Value:** 

`EncryptionInterface` -  An instance of the encryption class.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/exceptions/classes.md#encryptionexception) - Throws when an empty encryption key is passed.
- [\Luminova\Exceptions\EncryptionException](/exceptions/classes.md#encryptionexception) - Throws when an invalid handler is specified or handler extension is not loaded.

***

### encrypt

Encrypt the given data using OpenSSL or Sodium encryption.

```php
public static encrypt(string $data): string|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **string** | The data to encrypt. |

**Return Value:**

`string|bool` -  The encrypted data, or false if encryption fails.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/exceptions/classes.md#encryptionexception) - Throws when invalid encryption data is passed.

***

### decrypt

Decrypt the given data using OpenSSL or Sodium encryption.

```php
public static decrypt(string $data): string|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **string** | The data to decrypt. |

**Return Value:** 

`string|null` -  The decrypted data, or null if decryption fails.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/exceptions/classes.md#encryptionexception) - Throws when invalid encryption data is passed.

***

### password

Generate a hash representation of user login password string.

```php
public static password(string $password, array|null $options = null): string|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | password string |
| `$options` | **array&#124;null** | Optional hashing options  |

**Return Value:**

`string|bool` - Return hashed password otherwise false on empty password.

**Options**

```php
$options array<string, mixed> = [
	'cost' => 12,
	'salt' => 'custom_salt', // You can optionally specify a custom salt
	'algorithm' => PASSWORD_BCRYPT, // Optionally specify the algorithm
];
```

***

### verify

Verify a user login password against it stored hash and determine if it match.

```php
public static verify(string $password, string $hash): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | User submitted password string |
| `$hash` | **string** | Store password hash |

**Return Value:**

`bool` - Return true if the password match, otherwise false.

***

### supported

Determine if the given key and cipher method are valid.

```php
public static supported(string $key, string $method): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | Encryption key |
| `$method` | **string** | Cipher method |

**Return Value:**

`bool` - Return true if encryption method and key are valid false otherwise.

***

### generate_key

Generate a random encryption key string using your default encryption handler.
For private and public key generation it uses openssl RSA.

```php
public static generate_key(string $type = 'random', array $options = []): string|array|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The type of key to generate: 'random', 'private', or 'public'. |
| `$options` | **array** | Additional options for key generation.<br />- For 'random' type: 'length' specifies the length of the random string.<br />- For 'private' type: 'private_key_bits' specifies the number of bits in the private key,<br />  and 'private_key_type' specifies the type of the private key (e.g., OPENSSL_KEYTYPE_RSA).<br />- For 'public' type: 'private_key' is the private key string from which to derive the public key. |

**Return Value:**

`string|array|false` - The generated key(s), an array of private and public key, or false on failure.

***

### Configuration

These `Configuration` properties allows you to configure encryption settings for your application. The location of the configuration class is `/app/Config/Encryption.php`

#### `$handler`

- **Description:** Specify encryption handler class to use (`openssl` or `sodium`).
- **Type:** `string`
- **Default Value:** 'openssl'

#### `$method`

- **Description:** The encryption mode for openssl (e.g., 'AES-128-CBC', 'AES-192-CBC', 'AES-128-CFB', 'AES-128-ECB').
- **Type:** `string`
- **Default Value:** 'AES-128-CBC'

#### `$digest`

- **Description:** Specify hashing digest to use (e.g., SHA512, SHA256).
- **Type:** `string`
- **Default Value:** 'SHA512'

#### `$keyInfo`

- **Description:** Openssl key information.
- **Type:** `string`
- **Default Value:** `blank string`