# Base Class for Application Configuration Classes

***

## Overview

Base configuration serves as an underlying of your application configuration classes, which may be required by the framework depending on the component used in your application. 

***

## Introduction

The base configuration service as the foundation of your application's configuration classes, which are essential for the framework depending on the components utilized in your application. To create a new configuration class, you need to extend and define necessary properties and method as you need.

***

## Class Declaration

* Class namespace: `\Luminova\Base\BaseConfig`
* This class is an **Abstract class**
* This class implements: [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface)

***

###  Usages

Bellow is an example how you can extend the `BaseConfig` class.

```php
// app/Config/Config.php
<?php 
namespace App\Config;

use \luminova\Base\BaseConfig;

class Config extends BaseConfig 
{
	public const MY_VAR = 'var';
}
```

> Now you can import your configuration class anywhere you would like to use it or register in service to make it available anywhere.

***

## Properties

File extensions based on MIME types. Where the key is the MIME type and the value is the extension.

```php 
protected static array $extensions = [
	'image/jpeg' => 'jpg',
	'image/png' => 'png',
	// ...
];
```
> **Note:** Only define this property in `App\Config\Files` class.

***

## Methods

### getEnv

An alternative to the global helper function `env`, the `getEnv` method allows you to retrieve an environment variable with a specific return type.

```php
final public static getEnv(string $key, mixed $default = null, string|null $return = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The environment variable key to retrieve. |
| `$default` | **mixed** | The default value to return if the key is not found. |
| `$return` | **string&#124;null** | The expected return type. Can be one of: |

**Return Value:**

`mixed` - Returns the environment variable cast to the specified type, or default if not found.

**Supported Return Types:**

- `bool`.
- `int`.
- `float`.
- `double`.
- `nullable`.
- `string`.

***

### getNonce

Generate or retrieve a nonce with an optional prefix.

```php
final public static getNonce(int $length = 16, string $prefix = ''): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$length` | **int** | The length of the random bytes to generate (default: 16). |
| `$prefix` | **string** | An optional prefix for the nonce (default: ''). |

**Return Value:**

`string` Return a cached generated nonce value.

***

### removeCsp

Remove a directive from the Content-Security-Policy (CSP).

```php
public removeCsp(string $directive): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$directive` | **string** | The CSP directive to remove. |

**Return Value:**

`self` - Returns the instance of configuration class.

***

### clearCsp

Clear all directives from the Content-Security-Policy (CSP).

```php
public clearCsp(): self
```

**Return Value:**

`self` - Returns the instance of configuration class.

***

### getCsp

Build and return the Content-Security-Policy (CSP) as a string.

```php
public getCsp(): string
```

**Return Value:**

`string` - Returns the CSP policy string in the correct format.

***

### getCspMetaTag

Generate the `<meta>` tag for embedding the CSP in HTML documents.

```php
public getCspMetaTag(string $id = ''): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **string** | The CSP element identifier (default: none). |

**Return Value:**

`string` - Returns the `<meta>` tag with the CSP as the content.

***

### sendCspHeader

Send the Content-Security-Policy (CSP) as an HTTP header.

```php
public sendCspHeader(): void
```

### getExtension

Get the file extension based on the MIME type.

This function returns the appropriate file extension for a given MIME type. It first checks if the MIME type exists in the developer defined static $extensions array, and if not, it uses a match expression to determine the extension.

```php
public static getExtension(string $mimeType): string 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mimeType` | **string** | The MIME type of the file. |

**Return Value:**

`string` - Return the corresponding file extension (without the dot), or 'bin' if the MIME type is not recognized.

**Example:**

```php
<?php
use App\Config\Files;

$file = 'path/to/file.png';

Files::getExtension(get_mime($file));
```
