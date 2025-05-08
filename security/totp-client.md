# Time-Based One-Time Password: Google Authenticator Client

***

## Overview

Installation guides for the PHP Luminova framework highlight the benefits of using Composer for easy maintenance and ensuring you're always up to date with the latest framework and dependency.

***

## Introduction

The Luminova Authenticator client interface simplify generating and validating Time-Based One-Time Passwords (TOTP). By implementing the `Luminova\Interface\AuthenticatorInterface`, ensuring compatibility with the [Luminova\Security\TOTP](/security/totp.md) class. This design allows flexibility in implementing custom TOTP solutions while maintaining compliance with the Luminova `TOTP` class. For apps using alternatives to Google Authenticator, implementing the `AuthenticatorInterface` ensures compatibility with Luminova's MFA system.

### Google Authenticator Client

The Google Authenticator client allow the generation of secure authentication secrets, QR code creation, and TOTP validation. It offers flexibility through customizable account details, issuer names, time zones, and caching mechanisms. By adhering to PSR standards, it integrates with various cache implementations, ensuring one-time code usage for enhanced security.

Key features include:

- Generating Base32-encoded secrets for authentication.
- Creating QR codes for easy configuration with TOTP apps.
- Verifying user-provided codes against the stored secret.
- Flexible support for time zones and caching to prevent code reuse.
- Compliance with PSR caching interfaces and the Luminova base cache.

***

### Usage Examples

**Initializing the Authenticator**

You can create a `Google` authenticator instance by providing account details, issuer name, and optional time zone or caching instance.

```php
use Luminova\Security\Authenticator\Google;
use Luminova\Cache\FileCache;

$authenticator = new Google(
    'user@example.com',       // Account name (email or username)
    'MyAppName',              // Issuer (your application name)
    new DateTimeZone('UTC'),  // Optional timezone
    new FileCache('totp')     // Optional PSR-compliant cache instance
);
```

---

**Generating a Secret**

Generate a secure Base32-encoded secret key for a new user. The secret is stored and can be used to generate or validate TOTP codes.

```php
$secret = Google::generateSecret(24);
echo "User Secret: {$secret}";
```

> **Note:** Generate a Base32 secret key of a specific length (must be divisible by 8):

---

**Creating a QR Code**

Generate a QR code URL for easy configuration in Google Authenticator or similar apps. The QR code encodes the secret and account details.

```php
$secret = Google::generateSecret();
$authenticator->setSecret($secret); // Generate new secret for user
$qrCodeUrl = $authenticator->getQRCodeUrl();

echo "<img src='{$qrCodeUrl}' alt='Scan this QR Code to configure your authenticator'>";
```

---

**Validating a Code**

Verify a TOTP code provided by the user. Specify optional parameters like time-step duration and allowed discrepancies for added flexibility.

```php
$userProvidedCode = '123456';
$authenticator->setSecret('USER_OLD_SECRET123'); // Set user own secret
$isValid = $authenticator->verify($userProvidedCode);

if ($isValid) {
    echo "Authentication successful!";
} else {
    echo "Invalid code. Please try again.";
}
```

---

**Updating Secret, Timezone, or Cache**

The authenticator instance can be updated dynamically to set a new secret, time zone, or caching instance:

```php
$authenticator->setSecret('NEWSECRET123');
$authenticator->setTimezone(new DateTimeZone('America/New_York'));
$authenticator->setCache(new FileCache('totp-storage'));
```

***

## Class Definition

* Class namespace: `\Luminova\Security\Authenticator\Google`
* This class implements:
[\Luminova\Interface\AuthenticatorInterface](/interface/classes.md#authenticatorinterface)

***

## Methods

### constructor

Initialize a Google Authenticator instance with the necessary parameters.

```php
public __construct(
    string $accountName, 
    string $issuer, 
    DateTimeZone|string|null $timezone = null, 
    Psr\Cache\CacheItemPoolInterface|Psr\SimpleCache\CacheInterface|Luminova\Base\BaseCache|null $cache = null
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$accountName` | **string** | The account name (e.g., user email or username). |
| `$issuer` | **string** | The issuer's name (e.g., your app or website name). |
| `$timezone` | **DateTimeZone&#124;string&#124;null** | The timezone for time-based calculations (optional). |
| `$cache` | **CacheItemPoolInterface&#124;CacheInterface&#124;BaseCache&#124;null** | The instance of PSR cache or Luminova base-cache for preventing code reuse (optional). |

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - If the issuer or account name contains invalid characters (colon `:`).

***

### getLabel

Get the label used in the TOTP configuration, optionally URL-encoded.

```php
public getLabel(bool $encode = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$encode` | **bool** | Whether to URL-encode the label. |

**Return Value:**

`string` - Return the label in the format 'Issuer:AccountName'.

***

### getAccount

Get the account name associated with this instance.

```php
public getAccount(): string
```

**Return Value:**

`string` - Return the account name.

***

### getSecret

Get the shared secret key.

```php
public getSecret(): string
```

**Return Value:**

`string` - Return the shared secret key.

***

### getQRCodeUrl

Generate a QR code URL for configuring Google Authenticator.

```php
public getQRCodeUrl(): string
```

**Return Value:**

`string` - Return the URL for the QR code.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - If called without a valid authentication secret.

***

### setSecret

Initialize a Google Authenticator instance with the necessary parameters.

```php
public setSecret(string $secret): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$secret` | **string** | The shared secret key for TOTP generation. |

**Return Value:**

`\Luminova\Interface\AuthenticatorInterface` - Return the current instance of authenticator client.

***

### setTimezone

Set the timezone for time-based calculations.

```php
public setTimezone(\DateTimeZone|string|null $timezone): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | The new timezone. |

**Return Value:**

`\Luminova\Interface\AuthenticatorInterface` - Return the current instance of authenticator client.

***

### setCache

Set the cache instance for code reuse prevention.

```php
public setCache(Psr\Cache\CacheItemPoolInterface|Psr\SimpleCache\CacheInterface|Luminova\Base\BaseCache $cache): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$cache` | **CacheItemPoolInterface&#124;CacheInterface&#124;BaseCache** | The instance of PSR cache or Luminova base-cache. |

**Return Value:**

`\Luminova\Interface\AuthenticatorInterface` - Return the current instance of authenticator client.

***

### generateSecret

Generate a new Base32-encoded secret key.

```php
public static generateSecret(int $length = 16): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$length` | **int** | The desired length of the secret (default: 16). |

**Return Value:**

`string` - Return the generated Base32 secret key.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If the length is not positive or not divisible by 8.

***

### verify

Verify a given TOTP code against the shared secret.

```php
public verify(string $code, int $discrepancy = 1, int $timeStep = 30): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$code` | **string** | The user-provided TOTP code. |
| `$discrepancy` | **int** | The allowed time-step discrepancy (default: 1). |
| `$timeStep` | **int** | The time step duration in seconds (default: 30). |

**Return Value:**

`bool` - Return true if the code is valid, false otherwise.

**Throws:**

- [\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - If called without a valid authentication secret or an invalid base32 character is found in secret.

