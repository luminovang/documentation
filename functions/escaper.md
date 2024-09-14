# Client Input Sanitization and Escaping with the Escape Class

***

## Overview

Escaper provides essential methods for escaping and sanitizing data to enhance security and prevent vulnerabilities, this ensures that user-generated input is safely handled across different contexts.

***

## Introduction

The `Escape` class is designed to provide a useful set of methods for escaping and sanitizing data. This ensures that input data is safe to use in HTML, JavaScript, URLs, and other contexts where untrusted input could potentially lead to security vulnerabilities, such as Cross-Site Scripting (XSS) attacks.

## Features

- **HTML Escaping:** Prevents HTML injection attacks by escaping characters that could be interpreted as HTML tags or attributes.
- **JavaScript Escaping:** Escapes characters that might be interpreted as JavaScript code, ensuring that data is safely embedded in scripts.
- **URL Escaping:** Encodes special characters in URLs to ensure they are correctly transmitted and interpreted.
- **Attribute Escaping:** Escapes characters in HTML attributes to prevent malicious data injection.
- **Custom Escaping:** Allows for custom escaping rules and contexts as needed.

***

### Third-Party Libraries

The `Escape` class also supports integration with third-party libraries such as `\Laminas\Escaper\Escaper`. To utilize the methods provided by this library, you first need to install it. You can do so by running the following command:

```bash
composer require laminas/laminas-escaper
```

Once installed, whenever you call escape methods, it will use the `\Laminas\Escaper\Escaper` instead of the built-in `Escape` class methods for enhanced escaping functionality.

***

* Class namespace: `\Luminova\Functions\Escape`

## Properties

### encoding

The Escaper encoding (default: `utf-8`).

```php
protected string $encoding = 'utf-8';
```

***

### encodingFlags

The Escaper encoding flags for special characters to HTML entities (default: `ENT_QUOTES|ENT_SUBSTITUTE`).

```php
protected int $encodingFlags = ENT_QUOTES | ENT_SUBSTITUTE;
```

***

### supportedEncodings

The list of supported encodings for escaper.
Full supported list can be found [below](/functions/escaper.md) in this documentation.

```php
protected string[] $supportedEncodings = [
    'utf-8',
    //...
]
```

***

## Methods

More easy approach is to use the global helper function. to escape input which also supports escaping array.

```php
<?php 
echo escape('<script>alert("XSS")</script>');
```
Or escaping an array.

```php
<?php 
echo escape([
    'html' => '<script>alert("XSS")</script>',
    'js' => 'alert("XSS")'
]);
```

**Initialization**

Luminova provide different ways initialize the and use escape class.

```php
<?php 
$escaper = factory()>escaper();
```

```php
<?php 
$escaper = factory('escaper');
```

Use the Factory class loader.

```php
<?php 
use Luminova\Application\Factory;

$escaper = Factory::escaper();
```

Initializing directory.

```php
<?php
use \Luminova\Functions\Escape;

$escaper = new Escape();
```

Escape HTML special characters.

```php
<?php
echo $escaper->escapeHtml('<script>alert("XSS")</script>');

// Output: &lt;script&gt;alert(&quot;XSS&quot;)&lt;/script&gt;
```

Escapes JavaScript characters.

```php
<?php
echo $escaper->escapeJs('alert("XSS")');

// Output: alert(&quot;XSS&quot;);
```

Encodes special characters in a URL.

```php
<?php
echo $escaper->escapeUrl('https://example.com/?search=foo&bar=baz');

// Output: https%3A%2F%2Fexample.com%2F%3Fsearch%3Dfoo%26bar%3Dbaz
```

Escape HTML attribute values.

```php
<?php
echo $escaper->escapeHtmlAttr('" onmouseover="alert(\'XSS\')"');

// Output: &quot; onmouseover=&quot;alert('XSS')&quot;
```

Escape with custom patterns.

```php
<?php
echo $escaper->escapeWith('<div>Example</div>', [
    '/</' => '&lt;',
    '/>/' => '&gt;',
]);

// Output: &lt;div&gt;Example&lt;/div&gt;
```

### constructor

Initialize the escaper constructor.

```php
public __construct(string|null $encoding = 'utf-8'): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$encoding` | **string&#124;null** | The character encoding to use (default: 'utf-8'). |

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if unsupported encoding or empty string is provided.

***

### __call

The magic call method allows you to call methods specifically for third-party PHP escaper class using `\Laminas\Escaper\Escaper`.

```php
public __call(string $name, array $arguments): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the method being called. |
| `$arguments` | **array** | The arguments passed to the method. |

**Return Value:**

`mixed` - Return the result of the method call.

**Throws:**

- [\Luminova\Exceptions\BadMethodCallException](/running/exceptions.md#badmethodcallexception) - When the called method does not exist.

***

### setEncoding

Set escaper encoding type.
If set encoding is called when using `Laminas Escaper` library, new instance of Laminas Escaper will be created.

```php
public setEncoding(string $encoding): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$encoding` | **string** | The character encoding to use (e.g: 'utf-8'). |

**Return Value**

`Luminova\Functions\Escape` Return instance of escape class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if unsupported encoding or empty string is provided.

***

### getEncoding

Get the character encoding used by the escaper.

```php
protected getEncoding(): string
```

**Return Value:**

`string` - Return the character encoding.

***

### escapeHtml

Escapes HTML characters in a string to prevent HTML injection attacks. 
Converts special characters to their HTML entities.

```php
protected escapeHtml(string $string): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The string to be escaped. |

**Return Value:**

`string` - Return the escaped string.

***

### escapeHtmlAttr

Escapes characters in HTML attributes to prevent injection attacks within HTML attributes.

```php
protected escapeHtmlAttr(string $string): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The string to be escaped. |

**Return Value:**

`string` - Return the escaped string.

***

### escapeJs

Escapes characters in a string that might be interpreted as JavaScript code. 
Ensures that data used in JavaScript contexts is safe.

```php
protected escapeJs(array|string $string): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **array&#124;string** | The string or array of strings to be escaped. |

**Return Value:**

`string` - The escaped string or array of strings.

***

### escapeCss

Escape CSS special characters.

```php
protected escapeCss(string $string): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The string to be escaped. |

**Return Value:**

`string` - Return the escaped string.

***

### escapeUrl

Encodes special characters in a URL to ensure it is correctly interpreted by browsers and servers.

```php
protected escapeUrl(string $string): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** |The URL to be escaped. |

**Return Value:**

`string` - Return the escaped URL.

***

### escapeWith

Applies custom escaping rules to the input string. 
Allows for flexibility in handling various contexts.

```php
protected escapeWith(string $string, array<string,string> $rules): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The URL to be escaped. |
| `$rules` | **array<string,string>** | An associative array where keys are patterns and values are replacements. |

**Return Value:**

`string` - Return the escaped string.

***

### toUtf8

Convert a string to UTF-8 encoding.

```php
protected toUtf8(string $string): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The string to be converted. |

**Return Value:**

`string` - Return the converted string.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - When the string is not valid UTF-8 or cannot be converted.

***

### fromUtf8

Convert a string from UTF-8 encoding.

```php
protected fromUtf8(string $string): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The string to be converted. |

**Return Value:**

`string` - Return the converted string.

***

### convertEncoding

Convert a string to a different character encoding.

```php
protected convertEncoding(array|string $string, string $to, array|string|null $from = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **array&#124;string** | The string or array of strings to be converted. |
| `$to` | **string** | The target character encoding. |
| `$from` | **array&#124;string&#124;null** | The source character encoding. Defaults to null (auto-detection). |

**Return Value:**

`string` - Return the converted string.

***

## Example Usage

Here’s a practical example demonstrating how to use the `Escaper` class to safely output user-generated content in different contexts:

```php
<?php
$userInput = '<script>alert("XSS")</script>';

// Safe HTML output
echo $escaper->escapeHtml($userInput);

// Safe JavaScript output
echo $escaper->escapeJs($userInput);

// Safe URL output
echo 'https://example.com/?search=' . $escaper->escapeUrl($userInput);

// Safe HTML attribute output
echo '<input type="text" value="' . $escaper->escapeHtmlAttr($userInput) . '">';
```

***

## Supported Encodings

The `$supportedEncodings` array lists the character encodings supported by the system. Each entry represents a specific encoding type that the system can handle.

| Type              | Description                   |
|-------------------|-----------------------------------|
| `iso-8859-1`      | ISO 8859-1 Latin-1 (Western European) |
| `iso8859-1`       | Alternative notation for ISO 8859-1 |
| `iso-8859-5`      | ISO 8859-5 Latin/Cyrillic          |
| `iso8859-5`       | Alternative notation for ISO 8859-5 |
| `iso-8859-15`     | ISO 8859-15 Latin-9 (Western European with Euro) |
| `iso8859-15`      | Alternative notation for ISO 8859-15 |
| `utf-8`           | Unicode Transformation Format - 8 bits |
| `cp866`           | Code Page 866 (Cyrillic)           |
| `ibm866`          | IBM Code Page 866 (Cyrillic)       |
| `866`             | Alternative notation for Code Page 866 |
| `cp1251`          | Code Page 1251 (Cyrillic)          |
| `windows-1251`    | Windows Code Page 1251 (Cyrillic)  |
| `win-1251`        | Alternative notation for Windows Code Page 1251 |
| `1251`            | Alternative notation for Code Page 1251 |
| `cp1252`          | Code Page 1252 (Western European) |
| `windows-1252`    | Windows Code Page 1252 (Western European) |
| `1252`            | Alternative notation for Code Page 1252 |
| `koi8-r`          | KOI8-R (Cyrillic)                  |
| `koi8-ru`         | KOI8-RU (Cyrillic)                 |
| `koi8r`           | Alternative notation for KOI8-R    |
| `big5`            | Big5 (Traditional Chinese)         |
| `950`             | Alternative notation for Big5      |
| `gb2312`          | GB 2312 (Simplified Chinese)       |
| `936`             | Alternative notation for GB 2312   |
| `big5-hkscs`      | Big5-HKSCS (Traditional Chinese with Hong Kong Supplementary Character Set) |
| `shift_jis`       | Shift JIS (Japanese)               |
| `sjis`            | Alternative notation for Shift JIS |
| `sjis-win`        | Shift JIS (Japanese) for Windows   |
| `cp932`           | Code Page 932 (Japanese)           |
| `932`             | Alternative notation for Code Page 932 |
| `euc-jp`          | EUC-JP (Japanese)                  |
| `eucjp`           | Alternative notation for EUC-JP    |
| `eucjp-win`       | EUC-JP (Japanese) for Windows      |
| `macroman`        | MacRoman (Western European)        |
