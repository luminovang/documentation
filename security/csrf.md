# CSRF Protection

***

## Overview

Integrate CSRF with forms and requests in your application to enhance its security and protect against CSRF attacks, safeguarding user data and maintaining trust in your application's integrity.

***

## Introduction

The CSRF (Cross-Site Request Forgery), Luminova Framework provides functionality to protect against CSRF attacks, a type of malicious exploit where unauthorized commands are transmitted even from a user that the web application trusts. This class helps ensure the integrity and authenticity of requests sent to your application.

The CSRF class generates unique tokens for each user session and verifies these tokens when processing form submissions or other sensitive requests. By including these tokens in forms or requests, the CSRF class can distinguish between legitimate requests initiated by the user and potentially harmful requests sent by attackers.

### Features

1. Automatically generates unique CSRF tokens for each user session.
2. Verifies the authenticity of CSRF tokens submitted with form submissions or requests.
3. Allows for the regeneration of CSRF tokens to prevent token reuse and enhance security.
4. Allows to customize the storage location either session storage or cookie.

***

* Class namespace: `\Luminova\Security\Csrf`

***

## Methods

### getToken

Retrieves a previously generated CSRF token or generates a new token if none was found, then stores it.

```php
public static getToken(): string
```

**Return Value:**

`string` - The CSRF token.

***

### refresh

Generates a new CSRF token and stores it.

```php
public static refresh(): string
```

Use this method when you need to regenerate a token after validation.

**Return Value:**

`string` - The generated CSRF token.

***

### delete

Delete stored CSRF token.

```php
public static delete(): void
```

***

### inputToken

Generates and displays an HTML hidden input field for the CSRF token.

```php
public static inputToken(): void
```

***

### metaToken

Generates and displays an HTML meta tag for the CSRF token.

```php
public static metaToken(): void
```

***

### validate

Validates a submitted CSRF token.

```php
public static validate(string $token, bool $reuse = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$token` | **string** | The token submitted by the user. |
| `$reuse` | **bool** | Weather to retain or delete the token after successful verification (default: true). |

**Return Value:**

`bool` - Return true if the submitted token is valid, false otherwise.

***

### hasToken

Check if a token has already been generated.

```php
public static hasToken(): bool
```

**Return Value:**

`bool` - Returns true if a token has already been created, otherwise false.