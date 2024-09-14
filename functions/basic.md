# Utility Functions for Basic Operations Using the Func Class

***

## Overview

Useful methods to format and normalize inputs, including validating email and phone numbers or generating randomized strings, EAN, UPC, UUIDs, email masking and more.

***

## Introduction

The `Func` module provides a range of useful methods designed to enhance and standardize data handling in your applications. Key functionalities include:

- **Input Formatting:** Easily format various types of inputs to ensure consistency and correctness.
- **Email Validation:** Validate email addresses to confirm they meet standard email format requirements.
- **Phone Number Validation:** Check phone numbers for proper formatting and validity based on regional rules.
- **Randomized String Generation:** Create unique, random strings for various purposes, such as tokens or identifiers.
- **EAN & UPC Generation:** Generate European Article Number (EAN) and Universal Product Code (UPC) for products and inventory management.
- **UUID Generation:** Generate universally unique identifiers (UUIDs) to ensure the uniqueness of elements or records in your system.
- **Email Masking:** Protect email addresses by masking them, which helps to prevent spam or unauthorized use.
- **String Masking:** Apply masking to other sensitive strings to enhance security and privacy.

These methods are designed to eas common data processing tasks, ensuring data integrity and facilitating a smoother development process.

***

* Class namespace: `\Luminova\Functions\Func`

## Methods

The methods can be accessed through global function  helper, `Factory` instance or call statically.

```php
<?php
echo func()->random(50, 'string');
```

Or using factory instance to initialize function and call math method.

```php
<?php
use Luminova\Application\Factory;

echo Factory::functions()->random(50, 'string');
```

Or continently call it statically.

```php
<?php
use \Luminova\Functions\Func;

echo Func::random(50, 'string');
```

***

### normalize

Format text before display by matching links, email, phone,
hashtags and mentions with a link representation and replace multiple line breaks.

```php
public static normalize(string $text, string $target = '_self', string $blocked = null, bool $noHtml = true): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Text to be formatted |
| `$target` | **string** | Link target attribute in HTML anchor name.<br />-@example [_blank, _self, _top, _window, _parent or frame name] |
| `$blocked` | **string** | Replace blocked word with |
| `$noHtml` | **bool** | Determines whether to remove all HTML tags or only allow certain tags like &lt;p&gt; by default, it's set to true. |

**Return Value:**

`string` - Return the formatted text.

***

### random

Generate a randomize string of integers, characters, alphabets, or password.

```php
public static random(int $length = 10, string $type = 'int', bool $uppercase = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$length` | **int** | The length of the random value. |
| `$type` | **string** | The type of random value (e.g., character, alphabet, int, password). |
| `$uppercase` | **bool** | Whether to make the value uppercase if it's a string (default: false). |

**Return Value:**

`string` - Return the generated random value.

***

### bigInteger

Create a random big integer based on minimum and maximum.

```php
public static bigInteger(int $min, int $max): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$min` | **int** | The minimum number. |
| `$max` | **int** | The maximin number. |

**Return Value:**

`string` - Return the generated big integer.

***

### ean

Generate product `EAN13` id.

```php
public static ean(int $country = 615, int $length = 13): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$country` | **int** | The start prefix country code. |
| `$length` | **int** | The maximum length. |

**Return Value:**

`string` - Return the generated product ean code.

***

### upc

Generate a product `UPC` ID.

```php
public static upc(int $prefix, int $length = 12): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prefix` | **int** | The start prefix number. |
| `$length` | **int** | The maximum length. |

**Return Value:**

`string` - Return the generated UPC ID.

***

### uuid

Generates a `UUID` string of the specified version.

```php
public static uuid(int $version = 4, ?string $namespace = null, ?string $name = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$version` | **int** | The version of the UUID to generate (1, 2, 3, 4, or 5). |
| `$namespace` | **string&#124;null** | The namespace for versions 3 and 5. |
| `$name` | **string&#124;null** | The name for versions 3 and 5. |

**Return Value:**

`string` - Return the generated UUID string.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If the namespace or name is not provided for versions 3 or 5.

***

### isUuid

Validates a `UUID` string against a specific version.

```php
public static isUuid(string $uuid, int $version = 4): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uuid` | **string** | The UUID string to check. |
| `$version` | **int** | The UUID version to check (default: 4). |

**Return Value:**

`bool` - Return true if the UUID is valid, false otherwise.

***

### isEmail

Checks if string is a valid email address

```php
public static isEmail(string $email): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$email` | **string** | The email address to validate. |

**Return Value:**

`bool` - Return true if email is valid, otherwise false.

***

### isPhone

Checks if string is a valid phone number.

```php
public static isPhone(string|int $phone, int $min = 10): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$phone` | **string&#124;int** | The phone address to validate |
| `$min` | **int** | The minimum allowed length allowed (default: 10). |

**Return Value:**

`bool` - Return true if phone number is valid, otherwise false.

***

### formatPhone

Formats a phone number as `(xxx) xxx-xxxx` or `xxx-xxxx` depending on the length.

```php
public static formatPhone(string $phone): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$phone` | **string** | The phone address to format. |

**Return Value:**

`string` - Return the formatted phone number.

***

### strength

Determine if password strength matches the basic strength recommendation.

```php
public static strength(string $password, int $complexity = 4, int $min = 6, int $max = 50): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | The password string to check. |
| `$complexity` | **int** | The maximum complexity pass count `4` means password must contain (numbers, uppercase, lowercase and special characters). |
| `$min` | **int** | The minimum allowed password length (default: 6). |
| `$max` | **int** | The maximum allowed password length (default: 50). |

**Return Value:**

`bool` - Return true if password passed, otherwise false.

***

### strictType

Sanitize user input to protect against cross-site scripting attacks by removing unwanted characters from string and retain only expected types.

```php
public static strictType(string $string, string $type = 'name', string $replacement = ''): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The input string to be sanitized. |
| `$type` | **string** | The expected data type (e.g., 'int', 'email'). |
| `$replacement` | **string** | The symbol to replace disallowed characters with (optional). |

**Return Value:**

`string` - Return the sanitized string.

***

### mainDomain

Remove subdomains from a URL and return the main domain name only.

```php
public static mainDomain(string $url): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The input URL from which subdomains should be removed. |

**Return Value:**

`string` - Return the main domain extracted from the URL.

***

### subdomain

Remove main domain from a URL and return only the first subdomain name.

```php
public static subdomain(string $url): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The input URL from which the domain should be extracted. |

**Return Value:**

`string` - Return the extracted domain or an empty string if no domain is found.

> **Note:** `www` is considered as none subdomain.
> And only the first level of subdomain will be returned if the url contains multiple levels of subdomain.

***

### truncate

Truncate a string in specified length and adds an ellipsis at the end if the text is longer than the specified length.

```php
public static truncate(string $text, int $length = 10, string $encoding = 'UTF-8'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The string to truncate. |
| `$length` | **int** | The length to display before truncating. |
| `$encoding` | **string** | The text encoding type. |

**Return Value:**

`string` - Return the truncated string.

***

### base64_url_encode

Convert a string to base64 encode in other to pass it as a URL parameter.

```php
public static base64_url_encode(string $input): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | The string to encode. |

**Return Value:**

`string` - Return base64 encoded string url.

***

### base64_url_decode

Decode a URL Base64 decoded string back to it's original content.

```php
public static base64_url_decode(string $input): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | The base64 encoded URL string to decode. |

**Return Value:**

`string` - Return base64 decoded string.

***

### maskEmail

Mask an email address to hide part of it (e.g `pe***t@example.com`).

```php
public static maskEmail(string $email, string $masker = '*'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$email` | **string** | The email address to mask. |
| `$masker` | **string** | The mask character (default: &quot;*&quot;). |

**Return Value:**

`string` - Return masked email address.

***

### mask

Mask a string by position to hide part of it (e.g: `mfk****kld`).

```php
public static mask(string $string, string $masker = '*', string $position = 'center'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The string to mask. |
| `$masker` | **string** | The mask character (default: &quot;*&quot;). |
| `$position` | **string** | The position of the string to mask (&quot;center&quot;, &quot;left&quot;, or &quot;right&quot;). |

**Return Value:**

`string` - Return masked string.