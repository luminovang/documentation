# Helper Class to Handle Private and Plublic Keys

***

## Overview

A Comprehensive Guide to the luminova's Encryption Interface Class for PHP Framework Integration with OpenSSL and Sodium Libraries.

***

## Introduction

Luminova **Encryption Key**

***

## Class Definition

* Full namespace: `\Luminova\Security\Encryption\Key`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

---

## Constants

| Constant | Type | Value | Description |
|:---------|:-----|:------|:------------|
| `TYPE_PUBLIC` |   | 'public' | Flag to generate a public key. |
| `TYPE_PRIVATE` |   | 'private' | Flag to generate a private key. |
| `TYPE_PAIR` |   | 'pair' | Flag to generate a public and private key-pair. |
| `TYPE_RANDOM` |   | 'random' | Flag to generate a random key. |
| `OPENSSL` |   | 'openssl' | Flag for openssl key handler. |
| `SODIUM` |   | 'sodium' | Flag for sodium key handler. |

---

## Properties

### ciphers

Supported AES encryption methods with their corresponding key sizes.

```php
public static array<string,array> $ciphers = [
  'AES-128-CBC' => ['size' => 16],
  'AES-192-CBC' => ['size' => 24],
  // ...
]
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

## Methods

### constructor

Key generator for public, private, or random keys.

The constructor creates a key based on the given type:
- `Key::TYPE_PUBLIC`: Generates a public key from an existing private key, or creates a new key pair.
- `Key::TYPE_PRIVATE`: Generates a new private key.
- `Key::TYPE_PAIR`: Generates a key-pair (private and public key).
- `Key::TYPE_RANDOM`: Generates a random key string.

```php
public __construct(
	string $type = Key::TYPE_PUBLIC,
	?string $handler = null,
	?string $passphrase = null,
	array<string,mixed> $options = []
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | Type of key to generate (e.g, `pubKey::TYPE_*`). |
| `$handler` | **string\|null** | Crypto handler (`Key::OPENSSL` or `Key::SODIUM`).<br/>If null, defaults to the configured handler.  |
| `$passphrase` | **string\|null** | Optional passphrase for OpenSSL private keys, or for generating new ones. |
| `$options` | **array<string,mixed>** | Options for deriving or generating keys:<br>- `private_key` string: existing private/secret key.<br/>- Other key options. |

**Key Type Options:**

- For `random` type:
  - `length`: Key length in bytes (default: cipher size).
  - `handler`: The key handler lowercased (`openssl` or `sodium`).

- For `public` type:
  - `private_key`: Use this private key to derive the public key or provide optional private key options to generate new private key.

- For `private` type:
  - `private_key_bits`: Bit length for new private key (default: 2048).
  - `private_key_type`: Key type (default: **OPENSSL_KEYTYPE_RSA**).

See reference for more information [https://php.net/manual/en/function.openssl-pkey-new.php](https://php.net/manual/en/function.openssl-pkey-new.php)

**Examples:**

Generate a random key string:

```php
$key = new Key(Key::TYPE_RANDOM, [
  'length' => 32, 
  'handler' => 'openssl'
]);

echo $key->getRandom();
// Or $key->getResult()
```

Generate a private key:

```php
$key = new Key(Key::TYPE_PRIVATE, [
  'private_key_bits' => 2048,
  'private_key_type' => OPENSSL_KEYTYPE_RSA
]);

echo $key->getPrivate();
// Or $key->getResult()
```

Generate a public key from an existing private key:
```php
// Get private key from file.
$private = \Luminova\Funcs\get_content('/path/to/private.pem');

$key = new Key(Key::TYPE_PUBLIC, ['private_key' => $private]);

echo $key->getPrivate();
echo $key->getPublic();

// Or $key->getResult()
```

***

### getResult

Get the generated key value.

```php
public getResult(): string|array|null
```

**Return Value:**

`string|array|null` - Return key detail, or null if not available.

***

### getPublic

Get the generated public key.

```php
public getPublic(): ?string
```

**Return Value:**

`string|null` - Public key string, or null if not available.

***

### getPrivate

Get the generated private key.

```php
public getPrivate(): ?string
```

**Return Value:**

`string|null` - Private key string (PEM format), or null if not available.

***

### getRandom

Get the generated random key.

```php
public getRandom(bool $toHex = true): ?string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$toHex` | **bool** | Whether to return the key in hex format (default: true). |

**Return Value:**

`string|null` - Random key string in hex or raw bytes, or null if not available.

***

### size

Retrieve the key size based on cipher method.

```php
public static size(?string $method = null): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string&#124;null** | Cipher method name (default: `App\Config\Encryption->method`). |

**Return Value:**

`int` - Return the key size in bytes.

***

### cipher

Retrieve cipher information by method.

```php
public static cipher(?string $method = null): ?array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string&#124;null** | Cipher method name (default: `App\Config\Encryption->method`). |

**Return Value:**

`array{size: int}|null` - Cipher properties or null if unsupported.

***

### method

Retrieve encryption method from default configuration.

```php
public static method(string $default = 'AES-128-CBC'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$default` | **string** | The default cipher method if not found. |

**Return Value:**

`string` - Return the cipher method or default.

***

### handler

Determine the application encryption handler from configuration.

Checks if the required PHP extension is loaded and optionally asserts that the handler is valid, throwing exceptions if not.

```php
public static handler(bool $assert = false): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$assert` | **bool** | If true, throws an exception on invalid handler. |

**Return Value:**

`string|false` - Return handler name (`Key::OPENSSL` or `Key::SODIUM`) or false if unavailable.

**Throws:**
- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If $assert is true and the handler is invalid or unsupported.

***

### isSupported

Verify that the given key length matches the expected size for the cipher method.

If no cipher method is provided, the default from `App\Config\Encryption->method`
will be used.

```php
public static isSupported(string $key, ?string $method = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The encryption key to validate. |
| `$method` | **string&#124;null** | Optional cipher method (default: `App\Config\Encryption->method`). |

**Return Value:**

`bool` - Return true if the key length is valid for the cipher, false otherwise.

***

### isPrivate

Validate if a given key is private.

```php
public static isPrivate(
	\OpenSSLAsymmetricKey|\OpenSSLCertificate|array|string $key, 
	?string $passphrase = null, 
	?string $handler = null
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **OpenSSLAsymmetricKey&#124;OpenSSLCertificate&#124;array&#124;string** | Key in PEM format (OpenSSL) or Base64-encoded string (Sodium). |
| `$passphrase` | **string&#124;null** | Passphrase for OpenSSL private keys. |
| `$handler` | **string&#124;null** | Encryption handler (e.g, `Key::OPENSSL` or `Key::SODIUM`) (default: auto-detects). |

**Return Value:**

`bool` - Return true if valid private key, false otherwise.

***

### isPublic

Validate if a given key is public.

```php
public static isPublic(
	\OpenSSLAsymmetricKey|\OpenSSLCertificate|array|string $key, 
	?string $handler = null
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **OpenSSLAsymmetricKey&#124;OpenSSLCertificate&#124;array&#124;string** | Key in PEM format (OpenSSL) or Base64-encoded string (Sodium). |
| `$handler` | **string&#124;null** | Encryption handler (e.g, `Key::OPENSSL` or `Key::SODIUM`) (default: auto-detects). |

**Return Value:**

`bool` - Return true if valid public key, false otherwise.

***

### isMatch

Validate whether a private and public key pair match.

```php
public static isMatch(
	\OpenSSLAsymmetricKey|\OpenSSLCertificate|array|string $privateKey,
	\OpenSSLAsymmetricKey|\OpenSSLCertificate|array|string $publicKey,
	int $algo = OPENSSL_ALGO_SHA256,
	?string $data = null,
	?string $passphrase = null,
	?string $handler = null
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$privateKey` | **\OpenSSLAsymmetricKey&#124;\OpenSSLCertificate&#124;array&#124;string** | Private key (PEM for OpenSSL or Base64 for Sodium). |
| `$publicKey` | **\OpenSSLAsymmetricKey&#124;\OpenSSLCertificate&#124;array&#124;string** | Public key (PEM for OpenSSL or Base64 for Sodium). |
| `$algo` | **int** | OpenSSL algorithm (default: OPENSSL_ALGO_SHA256). |
| `$data` | **string&#124;null** | Optional data to sign (default: 'test_message'). |
| `$passphrase` | **string&#124;null** | Passphrase for OpenSSL private keys. |
| `$handler` | **string&#124;null** | Encryption handler (e.g., `Key::OPENSSL` or `Key::SODIUM`) (default: auto-detects). |

**Return Value:**

`bool` - Return true if keys match, false otherwise.

***

### newPublic

Generate a public key from an existing private key, or create a new key pair.

If a private key is provided, the public key will be derived from it.
If not, a new private/public key pair will be generated using **Openssl**.

Derive or generate a public key using the chosen crypto handler.

- **For OpenSSL:**
  If a private key is provided, the public key is derived from it.
  If not, a new private key is created and its public key is extracted.

- **For Sodium:**
  If a secret (private) key is provided, the corresponding public key is derived.
  If not, a new key-pair is generated and the public key is returned.

```php
public static newPublic(
	?string $handler = null, 
	?string $passphrase = null, 
	array<string,mixed> $options = []
): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **string&#124;null** | Crypto handler (`Key::OPENSSL` or `Key::SODIUM`).<br />If null, defaults to the configured handler. |
| `$passphrase` | **string&#124;null** | Optional passphrase for OpenSSL private keys,<br />or for generating new ones. |
| `$options` | **array** | Options for deriving or generating keys:<br />- `private_key` string: existing private/secret key. |

**Return Value:**

`string|false` - Return the generated/derived public key **(PEM for OpenSSL, base64 for Sodium)**,
or false on failure.

***

### newPrivate

Generate a new private key.

Creates a private key using OpenSSL asymmetric encryption. If no options are given, a default 2048-bit RSA key is generated.

Creates a private key using the given handler.
- **Openssl:** generates a PEM-encoded RSA key (default 2048-bit).
- **Sodium:** generates an Ed25519 private key (Base64-encoded).

```php
public static newPrivate(
	?string $handler = null, 
	?string $passphrase = null, 
	?array<string,mixed> $options = []
): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **string&#124;null** | Encryption handler (`Key::OPENSSL` or `Key::SODIUM`). |
| `$passphrase` | **string&#124;null** | Optional key passphrase (OpenSSL only). |
| `$options` | **array<string,mixed>&#124;null** | Options for key generation (OpenSSL only). |

**Return Value:**

`string|false` - Return a private key string **(PEM or Base64)** or false on failure.

> **Note:**
>
> * If an empty options array is passed, the options default to:
>
>   * `private_key_bits`: Number of bits (default: 2048).
>   * `private_key_type`: Key type (default: `OPENSSL_KEYTYPE_RSA`).
> * Pass `null` to fall back to the Openssl default configuration.

***

### newKeyPair

Generate a new private/public key pair using the chosen crypto handler.

- **For OpenSSL:**
  A PEM-encoded private key is generated (optionally protected with a passphrase),
  and the corresponding public key is extracted.

- **For Sodium:**
  A new key-pair is created, and both keys are returned as base64 strings.

```php
public static newKeyPair(
	?string $handler = null, 
	?string $passphrase = null, 
	array<string,mixed> $options = []
): array|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **string&#124;null** | Crypto handler (`Key::OPENSSL` or `Key::SODIUM`).<br />If null, defaults to the configured handler. |
| `$passphrase` | **string&#124;null** | Optional passphrase for OpenSSL private key generation. |
| `$options` | **array<string,mixed>** | Options for generating the key pair:<br />- For OpenSSL: key size, type, etc. |

**Return Value:**

`array{private: string, public: string}|false` - Return an array with both private and public keys (PEM for OpenSSL, base64 for Sodium),
or false on failure.

***

### newRandom

Generate a random encryption key.

This method creates a random key using the selected encryption handler. If no handler or key length is provided, it falls back to the application’s default handler and cipher size.

Uses the specified handler:
- Sodium: generates a secretbox key (32 bytes).
- OpenSSL: generates pseudo-random bytes (default: cipher size).

```php
public static newRandom(?string $handler = null, ?int $length = null, bool $toHex = true): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$handler` | **string&#124;null** | Encryption handler (`Key::OPENSSL` or `Key::SODIUM`). |
| `$length` | **int&#124;null** | Key length in bytes (OpenSSL only; defaults to cipher size). |
| `$toHex` | **bool** | If true, return the key as a hex-encoded string. If false, return raw bytes (default: `true`). |

**Return Value:**

`string` - Return random key string (hex or raw).
