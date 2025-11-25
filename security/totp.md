# Time-Based One-Time Password Authenticator

***

## Overview

An interface for Time-Based One-Time Password (TOTP) generation and validation. It uses an AuthenticatorInterface implementation (e.g., Google Authenticator) to manage secrets, generate QR codes and  

***

## Introduction

The **TOTP** class simplifies the integration of Time-Based One-Time Passwords (TOTP) in your application. It provides user-friendly methods to specify an authenticator client, ensuring consistency across your implementation.  

By default, the [Google Authenticator client](/security/totp-client.md) `Luminova\Security\Authenticator\Google` is used. To use a different client, simply implement the `Luminova\Interface\AuthenticatorInterface` interface to create a custom authenticator.  

---

### Usage Examples  

#### Create a TOTP Instance  

```php
use Luminova\Security\TOTP;
use Luminova\Security\Authenticator\Google;
use Luminova\Cache\FileCache;
use DateTimeZone;

// Initialize the TOTP authenticator
$auth = TOTP::create(
    new Google(
        'MySiteAuthenticator',
        'user@example.com',
        new DateTimeZone('Africa/Lagos'), // Optional timezone
        new FileCache('authenticator')   // Optional PSR-6 cache instance
    )
);
```

#### Setup TOTP for a New User  

```php
// Generate a secret and associate it with the user
$secret = $auth->secret(Google::generateSecret());

// Display the QR Code image and secret for the user
echo $auth->image();
echo "\nScan this QR Code\nYour Authenticator Secret: {$secret}";
```

#### Verify User Secret and TOTP Code  

```php
$user_secret = 'EWQAIIFPZPDLFS2K'; // User's secret
$code = '12345'; // TOTP code provided by the user

// Assign the user's secret to the authenticator
$auth->secret($user_secret);

// Validate the provided code
if ($auth->validate($code)) {
    echo 'Token is valid';
} else {
    echo 'Token is invalid';
}
```

***

## Class Definition

* Class namespace: `Luminova\Security\TOTP`
* This class is marked as **final** and can't be subclassed\

***

## Methods

### create

Factory method to create a TOTP instance.

```php
public static create(?Luminova\Interface\AuthenticatorInterface $client = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$client` | **Luminova\Interface\AuthenticatorInterface&#124;null** | Optional custom authenticator client.<br />Defaults to a Google Authenticator client. |

**Return Value:**

`self` - Return a new TOTP instance.

***

### secret

Get or set the secret key used for TOTP (Time-Based One-Time Password).

This method allows you to either retrieve the current secret key or set a new one.
If a secret is provided, it will be set as the new secret key.
If no secret is provided, the current secret key will be returned.

```php
public secret(?string $secret = null): ?string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$secret` | **string&#124;null** | Optional. The secret key to set.<br />If null, the method will retrieve the current secret. |

**Return Value:**

`string|null` - Returns the newly set secret key if a new secret was provided, or the current secret key if no new secret was given. Returns `nul`l if no secret is set and none was provided.

***

### url

Generate the QR code URL for the authenticator.

```php
public url(): string
```

**Return Value:**

`string` - Return the URL for the QR code.

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If called without a valid authentication secret.

***

### image

Generate the QR code image for the TOTP setup.

This method generates a QR code image for Time-Based One-Time Password (TOTP) setup.
It can create the image in different formats and sizes, either using an installed QR code library or a fallback method using an external service.

```php
public image(int $size = 200, string $format = 'svg'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$size` | **int** | The size of the QR code image in pixels. Default is 200. |
| `$format` | **string** | The desired format of the QR code image.<br />Supported formats are 'png', 'svg', and 'base64'. Default is 'svg'. |

**Return Value:**

`string` - Return the QR code image as HTML element, depending on the chosen format and generation method.

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If the 'base64' format is requested but the QR code library is not installed, or if called without a valid authentication secret.

***

### label

Retrieve the label for the TOTP account.

```php
public label(): string
```

**Return Value:**

`string` - Return the label for the account.

***

### account

Retrieve the account identifier used for the TOTP.

```php
public account(): string
```

**Return Value:**

`string` - Return the account identifier.

***

### validate

Validate a TOTP code.

```php
public validate(string $code, int $discrepancy = 1, int $timeStep = 30): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$code` | **string** | The TOTP code to validate. |
| `$discrepancy` | **int** | The allowed time window (in steps) for validation. Default: 1. |
| `$timeStep` | **int** | The time step in seconds for TOTP generation. Default: 30. |

**Return Value:**

`bool` - Return true if the code is valid; otherwise, false.

**Throws:**

- [Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - If called without a valid authentication secret or an invalid base32 character is found in secret.
