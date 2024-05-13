## Base Functions

***

## Overview

Base functions are a collection of fundamental utility methods that serve as a helper for application development. These functions typically encapsulate common tasks and operations for frequent use.

***

The Base Functions class serves as a repository for various utility functions that provides a reusable methods to various solutions to tackle different problems. As an abstract class, it serves as a foundation for common functionalities that can be extended and customized to suit specific use cases within your application. With a diverse collection of methods, each designed to address specific needs.

***
### Normalizer Class

By default base function inherited methods from `Normalizer` class which also has some useful methods to smooth your application development.

***

*  Class namespace: `\Luminova\Base\BaseFunction`
* This class is an **Abstract class**
* Parent class namespace: `\Luminova\Functions\Normalizer`

***

To utilize the functionality provided by the Base Functions class, you can either use the global helper function `func()` or import the namespace and call the methods statically.

You can find your functions class which is located in `/app/Controllers/Utils/Functions.php` .

### Helper Function

To call method using global helper function.

```php 
<?php 
func()->methodName();
```

***

### Namespace

Using use keyword to import the namespace in your context.

```php 
<?php
use App\Controllers\Utils\Functions;

Functions::methodName()
```

***

## Methods

### ip

Initialize or return a shared an instance of the IP address class.

```php
public static ip(): \Luminova\Functions\IPAddress
```

**Return Value:**

`IPAddress` - Returns a ip address class instance.

**See Also**

[IP Address Class](/function/ip-address) - See the ip address class for available useful methods.

***

### files

Initialize or return a shared an instance of the Files class.

```php
public static files(): \Luminova\Functions\Files
```

**Return Value:**

`Files` - Returns a file class instance

***

### tor

Initialize or return a shared an instance of the tor detector class.

```php
public static tor(): \Luminova\Functions\TorDetector
```

**Return Value:**

`TorDetector` - Returns a tor detector class instance.

**Example**

Check if user ip address is a not exit node ip.

```php
<?php
use App\Controllers\Utils\Functions;

if(Functions::tor()->isTor(ip_address())){
	echo 'Your ip address is detected in tor exit not, we have ban your account.'
}
```

> Corresponding helper function `is_tor()` to check if ip address is tor ip or not;

***

### math

Initialize or return a shared an instance of the math class.

```php
public static math(): \Luminova\Functions\Maths
```

**Return Value:**

`Maths` - Returns a math class instance.

**See Also**

[Math Class](/function/math) - See the math class for available useful methods.

***

### escape

Escapes a string or array of strings based on the specified context.

```php
public static escape(string|array $input, string $context = 'html', string|null $encoding = null): array|string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string&#124;array** | The string or array of strings to be escaped.<br /> `string` index array&lt;string, string&gt;<br/> - Will use the key as the context.<br />`int` index array&lt;int, string&gt; <br/> - Will use the default context fall all values. |
| `$context` | **string** | The context in which the escaping should be performed.<br />Possible values: 'html', 'js', 'css', 'url', 'attr', 'raw'. |
| `$encoding` | **string&#124;null** | The character encoding to use. Defaults to null. |

**Return Value:**

`array|string` - The escaped string or array of escaped strings.

**Throws:**

- [InvalidArgumentException](/exceptions/invalid-argument) - When an invalid escape context is provided.
- [BadMethodCallException](/exceptions/bad-method) - When the called method does not exist.
- [RuntimeException](/exceptions/runtime) - When the string is not valid UTF-8 or cannot be converted.
- [Exception](#) - When encountered uncaught error.

> To use `Laminas\Escaper` you need to install the library first by running this composer command `$ composer require laminas/laminas-escaper` in your cli.
> 
> If `Laminas\Escaper` is not installed the framework will use the default escaper class instead.

> The global function helper of this method is `escape`.

***

### normalize

Format text before display by matching links, email, phone, hashtags and mentions with a link representation and replace multiple new lines.

```php
public static normalize(string $text, string $target = '_self', string $blocked = null, bool $no_html = true): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Text to be formatted |
| `$target` | **string** | Link target attribute in HTML anchor name.<br />-@example [_blank, _self, _top, _window, _parent or frame name] |
| `$blocked` | **string** | Replace blocked word with |
| `$no_html` | **bool** | Determines whether to remove all HTML tags or only allow certain tags like &lt;p&gt; by default, it's set to true. |

**Return Value:**

`string` - $text

***

### random

Generate a random characters like `password`, `numbers` or `alphabets`.

```php
public static random(int $length = 10, string $type = 'int', bool $uppercase = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$length` | **int** | The length of the random value. |
| `$type` | **string** | The type of random value (e.g., character, alphabet, int, password). |
| `$uppercase` | **bool** |  |

**Return Value:**

`string` - The generated random value.

***

### bigInteger

Create a random integer based on minimum and maximum

```php
public static bigInteger(int $min, int $max): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$min` | **int** | Minimum number. |
| `$max` | **int** | Maximin number. |

**Return Value:**

`string` - String representation of big integer.

***

### ean

Generate product EAN13 id

```php
public static ean(int $country = 615, int $length = 13): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$country` | **int** | start prefix country code |
| `$length` | **int** | maximum length |

**Return Value:**

`string` - Product ean code.

***

### upc

Generate a product UPC ID.

```php
public static upc(int $prefix, int $length = 12): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prefix` | **int** | Start prefix number. |
| `$length` | **int** | Maximum length. |

**Return Value:**

`string` - The generated UPC ID.

***

### uuid

Generates uuid string version 4

```php
public static uuid(): string
```

**Return Value:**

`string` - uuid

***

### isUuid

Checks a valid uuid version 4

```php
public static isUuid(string $uuid): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$uuid` | **string** |  |

**Return Value:**

`bool` - true or false

***

### isEmail

Checks if string is a valid email address

```php
public static isEmail(string $email): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$email` | **string** | email address to validate |

**Return Value:**

`bool` - true or false

***

### isPhone

Checks if string is a valid phone number

```php
public static isPhone(string|int $phone, int $min = 10): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$phone` | **string&#124;int** | phone address to validate |
| `$min` | **int** | Minimum allowed length. |

**Return Value:**

`bool` - true or false

***

### formatPhone

Formats a phone number as (xxx) xxx-xxxx or xxx-xxxx depending on the length.

```php
public static formatPhone(mixed $phone): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$phone` | **mixed** | phone address to format |

***

### strength

Determine if password strength matches the basic strength recommendation.

```php
public static strength(string $password, int $complexity = 4, int $min = 6, int $max = 50): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | password to check. |
| `$complexity` | **int** | maximum complexity pass count max(4) means password must contain (numbers, uppercase, lowercase and pecial characters). |
| `$min` | **int** | minimum allowed password length (default: 6). |
| `$max` | **int** | maximum allowed password length (default: 50). |

**Return Value:**

`bool` - return trues if passed otherwise false

***

### strictInput

Sanitize user input to protect against cross-site scripting attacks.

```php
public static strictInput(string $string, string $type = 'name', string $replacement = ''): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The input string to be sanitized. |
| `$type` | **string** | The expected data type (e.g., 'int', 'email'). |
| `$replacement` | **string** | The symbol to replace disallowed characters with (optional). |

**Return Value:**

`string` - The sanitized string.

> The global function helper of this method is `strict`.

***

### mainDomain

Remove sub-domains from a URL and return the main domain name.

```php
public static mainDomain(string $url): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The input URL from which subdomains should be removed. |

**Return Value:**

`string` - The main domain extracted from the URL.

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

`string` - The extracted domain or an empty string if no domain is found.

 > Note: `www` is considered as none subdomain.
 > 
 > And only the first level of subdomain will be returned if the URL contains multiple levels of subdomain.

***

### truncate

Truncate an input with the specified length and adds an ellipsis at the end if the text is longer than the specified length.

```php
public static truncate(string $text, int $length = 10, int $encoding = 'UTF-8'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The string to truncate. |
| `$length` | **int** | The length to display before truncating. |
| `$encoding` | **int** | Text encoding type |

**Return Value:**

`string` - The truncated string.

***

### base64_url_encode

Base64 encode string for URL passing.

```php
public static base64_url_encode(string $input): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | String to encode |

**Return Value:**

`string` - Base64 encoded string

***

### base64_url_decode

Base64 decode URL base64 encoded string.

```php
public static base64_url_decode(string $input): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | Encoded string to decode |

**Return Value:**

`string` - Base64 decoded string.

***

### maskEmail

Mask email address.

```php
public static maskEmail(string $email, string $masker = '*'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$email` | **string** | Email address to mask. |
| `$masker` | **string** | Mask character (default is &quot;*&quot;). |

**Return Value:**

`string` - Masked email address or null.

***

### mask

Mask a string by position.

```php
public static mask(string $string, string $masker = '*', string $position = 'center'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | String to mask. |
| `$masker` | **string** | Mask character (default is &quot;*&quot;). |
| `$position` | **string** | The position of the string to mask (&quot;center&quot;, &quot;left&quot;, or &quot;right&quot;). |

**Return Value:**

`string` - Return masked string.