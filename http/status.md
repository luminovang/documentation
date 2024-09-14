# Management of HTTP Status Codes

***

## Overview

The HttpCode class serves as a helper to manage HTTP status codes and their corresponding messages.

***

## Introduction

The `HttpCode` class is a helper class that provides a list of HTTP status codes and their corresponding messages. You can access this list by using the public property `$codes`, or by calling the `get()` method with `null` to retrieve all status codes and messages, or with a specific status code to retrieve its message.

Additionally, you can use magic getter methods like `HttpCode::status200()` to directly retrieve the message of a specific status code.

> **Note:** This class cannot be initialized, to use it you need to call the methods or property statically.

***

* Class namespace: `\Luminova\Http\HttpCode`
* This class is marked as **final** and can't be subclassed

***

## Properties

### codes

Array of all HTTP status codes and messages.

```php
public static array<int,string>$codes = [
    200 => 'OK',
    //...
]
```

***

## Methods

### get

Return an HTTP status code message or the entire status codes if null is passed.

```php
public static get(?int $code = null): array|string|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$code` | **int&#124;null** | The HTTP status code (e.g, 200, 404 etc) (default: null). |

**Return Value:**

`array<int,string>string|null` - Return the status code message, null if code not found or array of status codes if null is passed.

***

### statusxxx

Get a status code message using PHP's magic method call. 
Your method call must follow this pattern: `status followed by the http status code`. Example, `xxx` represent the status code you want to return it message the prefix `status` must come before the HTTP code `HttpCode::status404()`.

```php
public static status[xxx](): ?string
```

**Return Value:**

`string|null` - Return the HTTP status code message, otherwise null.

***

### Examples

Retrieve an array with all status codes and messages.

```php
<?php
var_dump(HttpCode::get());
// OR
var_dump(HttpCode::$codes);
```

Returning status message for HTTP `200` status code.

```php 
<?php 
echo HttpCode::status200(); // OK
```

Returning status message for HTTP `503` status code.

```php 
<?php 
echo HttpCode::status503(); // Service Unavailable
```

```php
<?php
echo HttpCode::get(400); // Bad Request
```