# PHP Common Helper Functions

***

## Overview

The common helpers provide a set of static utility methods for formatting, validating, and normalizing data. They include features such as string and email masking, phone number formatting, and more

***

## Introduction

The common **Helpers** class offers a collection of static utility methods that simplify and standardize data handling across your application. It enhances development efficiency by providing ready-to-use solutions for common tasks.

Key features include:

- **Input Formatting:** Normalize various input types for consistency and reliability.  
- **Email & Phone Validation:** Ensure emails and phone numbers meet standard formats and regional rules.  
- **Random String Generation:** Generate secure random strings for tokens, identifiers, and more.  
- **EAN/UPC Generation:** Create valid product codes for inventory and retail use.  
- **UUID Generation:** Generate universally unique identifiers for reliably identifying records.  
- **Email & String Masking:** Obscure sensitive data to enhance privacy and security.

***

## Class Definition

* Class namespace: `Luminova\Common\Helpers`

---

## Methods

The methods can be accessed statically.

```php
use Luminova\Common\Helpers;

echo Helpers::random(50, 'string');
```

***

### normalize

Format text before display by matching links, email, phone, hashtags and mentions with a link representation and replace multiple line breaks.

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

Generate a random string or value.

```php
public static random(int $length = 10, string $type = 'int', bool $uppercase = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$length` | **int** | The length of the random value to generate. |
| `$type` | **string** | The type of random value to generate (e.g., character, alphabet, int, password, bytes, hex). |
| `$uppercase` | **bool** | Whether to convert non-numeric values to uppercase (default: false). |

**Return Value:**

`string` - Return the generated randomized value.

**Supported Types:**

- `character` - Includes special characters like `%#*^,?+$;"{}][|\/:=)(@!.-`.
- `alphabet` - Contains only alphabetical characters (both uppercase and lowercase).
- `password` - Combines letters, numbers, and an expanded set of special characters (`%#^_-@!$&*+=|~?<>[]{}()`).
- `bytes` - Returns a raw binary string of the specified length.
- `hex` - Returns a hexadecimal representation of random bytes.
- `int|integer` - Contains only numeric characters (0-9).

**Examples:**

Generates a secure password of 16 characters.

```php 
Helpers::random(16, 'password');
```

Generates an 8-character string in uppercase letters.
```php 
Helpers::random(8, 'alphabet', true);
```

Generates a 32-character hexadecimal string.

```php 
Helpers::random(32, 'hex');
```

***

### bigInteger

Generate a random BIGINT within a specified range.

> UNSIGNED BIGINT: 0 to 18,446,744,073,709,551,615 (20 digits):
> `$min` = 0
> `$max` = 18446744073709551615
>
> SIGNED BIGINT: -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 (19 digits):
> `$min` = -9223372036854775808
> `$max` = 9223372036854775807

```php
public static bigInteger(int $min, int $max): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$min` | **int** | The minimum value (default: `0`). |
| `$max` | **int** | The maximum value (default: `18446744073709551615`). |

**Return Value:**

`string` - Return a string representation of the generated `BIGINT`.

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

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the namespace or name is not provided for versions 3 or 5.

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

Checks if string is a valid email address, with optional support for internationalized domains.

```php
public static isEmail(string $email, bool $allow_idn = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$email` | **string** | The email address to validate. |
| `$allow_idn` | **bool** | Set to true to allow internationalized domains (default: false). |

**Return Value:**

`bool` - Returns true if valid email address, false otherwise.

***

### isPhone

Validates if the input is a valid phone number.

```php
public static isPhone(string|int $phone, int $min = 10, int $max = 15): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$phone` | **string&#124;int** | The phone address to validate |
| `$min` | **int** | The minimum allowed length allowed (default: 10). |
| `$max` | **int** | The maximum allowed length (default: 15). |

**Return Value:**

`bool` - Returns true if valid phone number, false otherwise.

***

### isUrl

Checks if the string is a valid URL, with optional support for internationalized domains.

```php
public static isUrl(string $url, bool $allow_idn = false, bool $http_only = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The URL to validate. |
| `$allow_idn` | **bool** | Set to true to allow internationalized domains (default: false). |
| `$http_only` | **bool** | Whether to support urls with `http` and `https` scheme (default: false). |

**Return Value:**

`bool` - Returns true if valid URL, false otherwise.

***

### isBase64Encoded

Determines whether the given string is Base64-encoded (standard, URL-safe, or MIME style).

Supports both standard URL-safe, and MIME-safe Base64 strings (with newlines).

```php
public static isBase64Encoded(string $data, bool $strict = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **string** | The input string to validate. |
| `$strict` | **bool** | If true, base64_decode() will return false on invalid characters. |

**Return Value:**

`bool` - Returns true if the string appears to be valid Base64; false otherwise.

***

### isBinary

Determines if the content string is likely a binary based on the presence of non-printable characters.

```php
public static isBinary(string $data): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$data` | **string** | The string to check for binary. |

**Return Value:**

`bool` - Return true if it's a binary, false otherwise.

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

### toStrictInput

Strictly sanitizes user input to protect against invalid characters and ensure it conforms to the expected type.

This method validates and sanitizes a given string based on predefined patterns for various data types.
If the string contains HTML tags, those tags and their content will be removed or replaced according to the provided replacement. If `NULL` is provided for `$replacement` and the string does not match the expected type, an exception will be thrown.

```php
public static toStrictInput(string $value, string $type = 'name', string|null $replacement = ''): ?string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **string** | The input string value to be sanitized. |
| `$type` | **string** | The expected data type (e.g., 'int', 'email', 'username'). |
| `$replacement` | **string\|null** | The symbol to replace disallowed characters or null to throw and exception (default: ''). |

**Return Value:**

`string|null` - Return the sanitized string or null if input doesn't match nor support replacing like `email` `url` `username` or `password`.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the input contains invalid characters, or HTML tags, and no replacement is provided.

**Available Types:**

- `int`       : Only numeric characters (0-9) are allowed.
- `numeric`     : Numeric characters, including negative numbers and decimals.
- `key`       : Alphanumeric characters, underscores, and hyphens.
- `password`  : Alphanumeric characters, and special characters (@, *, !, _, -).
- `username`  : Alphanumeric characters, hyphen, underscore, and dot.
- `email`     : Alphanumeric characters and characters allowed in email addresses.
- `url`       : Valid URL characters (alphanumeric, ?, #, &, +, =, . , : , /, -).
- `money`     : Numeric characters, including decimal and negative values.
- `double`    : Floating point numbers (numeric and decimal points).
- `alphabet`  : Only alphabetic characters (a-z, A-Z).
- `phone`     : Numeric characters, plus sign, and hyphen (e.g., phone numbers).
- `name`      : Unicode characters, spaces, and common name symbols (e.g., apostrophe).
- `timezone`  : Alphanumeric characters, hyphen, slash, and colon (e.g., timezone names).
- `time`      : Alphanumeric characters and colon (e.g., time format).
- `date`      : Alphanumeric characters, hyphen, slash, comma, and space (e.g., date format).
- `uuid`      : A valid UUID format (e.g., 8-4-4-4-12 hexadecimal characters).
- `default`   : Removes HTML tags.

**Example:**

```php
use Luminova\Common\Helpers;

$input = '1235hJndhb@<script>alert("hello");</script>';
echo Helpers::toStrictInput($input, 'int'); // Returns 1235
```

> **Note:** 
> HTML tags (including their content) are completely removed for the 'any' type.
> This method ensures secure handling of input to prevent invalid characters or unsafe content.
> Additionally this method can easily be used from it global helper function `strict(...)`.

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

### base64UrlEncode

Convert a string to base64 encode in other to pass it as a URL parameter.

```php
public static base64UrlEncode(string $input): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | The string to encode. |

**Return Value:**

`string` - Return base64 encoded string url.

***

### base64UrlDecode

Decode a URL Base64 decoded string back to it's original content.

```php
public static base64UrlDecode(string $input): string
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
| `$masker` | **string** | The mask character (default: `*`). |

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
| `$masker` | **string** | The mask character (default: `*`). |
| `$position` | **string** | The position of the string to mask (`center`, `left`, or `right`). |

**Return Value:**

`string` - Return masked string.

***

### hexToBinary

Converts a hexadecimal string into its binary representation.

```php
public static hexToBinary(string $hexStr, ?string $destination = null): string|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$hexStr` | **string** | The input string containing hexadecimal data. |
| `$destination` | **string\|null** | Optional. If specified, saves the binary data to a file.<br/>- If it's a `file path`, the binary data is saved directly.<br/>- If it's a `directory`, a unique filename is generated. |

**Return Value:**

`string|bool` - Return the binary string if no destination is provided. If a file is written, returns `true` on success, `false` on failure.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an invalid hex is encountered.