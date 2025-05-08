# Encryption and Decryption Handling with Crypter

***

## Overview

Luminova's Crypter class provides methods for encrypting and decrypting data using encryption algorithms in OpenSSL or Sodium.

***

## Introduction

Luminova Crypter class is straightforward means to implement encryption and decryption functionalities in anywhere in your applications. This helper class seamlessly integrates both Sodium and OpenSSL encryption libraries, providing a unified and simplified interface for encrypting and decrypting data without the need for manual initialization of handlers.

***

## Class Definition

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

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - Throws when an empty encryption key is passed.
- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - Throws when an invalid handler is specified or handler extension is not loaded.

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

`string|bool` -  Return the encrypted data, or false if encryption fails.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - Throws when invalid encryption data is passed.

**Encryption Example**

```php
<?php
$encrypted = Crypter::encrypt('Hello world');
```

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

`string|null` - Return the decrypted data, or null if decryption fails.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - Throws when invalid encryption data is passed.

**Decryption Example**

```php
<?php
echo Crypter::decrypt($encrypted);
```

***

### password

Generate a hash representation of user login password string.

```php
public static password(string $password, ?array $options = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | The actual password to hash. |
| `$options` | **array&#124;null** |Optional password hash options.  |

**Return Value:**

`string|false` - Return hashed password otherwise false on empty password.

**Default Options:**

```php
[
	'cost' => 12,
	'salt' => 'custom_salt', // You can optionally specify a custom salt
	'algorithm' => PASSWORD_BCRYPT, // Optionally specify the algorithm
];
```

**Hashing Password Example**

```php
<?php
$hash = Crypter::password('12345@123');
```

***

### isPassword

Verify user password against it stored hash value to determine if if they match.

```php
public static isPassword(string $password, string $hash): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | The password string to verify. |
| `$hash` | **string** | The password stored hash value. |

**Return Value:**

`bool` - Return true if password matches with the hash, otherwise false.

**Verifying Password Hash Value Example**

```php
<?php
$hash = 'X763....';
$ok = Crypter::isPassword('12345@123', $hash);
```

***

### supported

Determine if the given key and cipher method are valid.

```php
public static supported(string $key, string $method): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The encryption key. |
| `$method` | **string** | The encryption cipher method. |

**Return Value:**

`bool` - Return true if encryption method and key are valid false otherwise.

***

### generate_key

Generates a random encryption key or a pair of private and public keys using the default encryption handler. 
For `RSA` key generation, `OpenSSL` is utilized.

```php
public static generate_key(string $type = 'random', array $options = []): string|array|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The type of key to generate: (e.g, 'random', 'private', or 'public'). |
| `$options` | **array** | Associative array of options for key generation. |

**Return Value:**

`string|array|false` - Returns the generated key(s), an array containing private and public keys, or `false` on failure.

**Options Keys:**

- For `random` type:
  - `length`: Specifies the length of the random string to generate.

- For `private` type:
  - `private_key_bits`: (default: 2048) Specifies the number of bits in the private key.
  - `private_key_type`: (default: `OPENSSL_KEYTYPE_RSA`) Specifies the type of the private key (e.g., `OPENSSL_KEYTYPE_RSA`).

- For `public` type:
  - `private_key`: Specifies the private key string from which to derive the public key. If not provided, a new private key will be generated.

**Example Usage:**

Generate a random key:

```php
$key = Crypter::generate_key('random', ['length' => 32]);
```

Generate a private key:
```php
$privateKey = Crypter::generate_key('private', [
    'private_key_bits' => 2048,
    'private_key_type' => OPENSSL_KEYTYPE_RSA
]);
```

Generate a public key:
```php
$keys = Crypter::generate_key('public', ['private_key' => $privateKey]);
```

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