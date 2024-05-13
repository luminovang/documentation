## HTTP Status Codes

***

## Overview

The HttpCode class serves as a helper to manage HTTP status codes and their corresponding messages.

***

The `HttpCode` class is a helper class that provides a list of HTTP status codes and their corresponding messages. You can access this list by using the public property `$codes`, or by calling the `get()` method with `null` to retrieve all status codes and messages, or with a specific status code to retrieve its message.

Additionally, you can use magic getter methods like `HttpCode::status200()` to directly retrieve the message of a specific status code.

***

* Class namespace: `\Luminova\Http\HttpCode`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

***

## Properties

### codes

Http status code.

```php
public static array<int,string>$codes = [
    200 => 'OK',
    //...
]
```

***

## Methods

### get

Return a status code title, if `$code` is null return array of all status codes and it title.

```php
public static get(int|null $code = null): string|null|array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$code` | **int&#124;null** | Status code. |

**Return Value:**

`string|null|array` - Status code title or array of status codes.

***

### statusxxx

Get a status code title using a fancy magic method call. The `xxx` represent the status code you want to return it title

```php
public static status[xxx](): string|null
```

**Return Value:**

`string|null` - Status code title.

> *Note:* When using the magic call method, prefix `status` with the code you want to retrieve.
> 
> e.g. you need to type `status` following by the status code int `HttpCode::status404()`.

***

### Examples

array with all status codes and messages

```php
<?php
var_dump(HttpCode::get());
// OR
var_dump((new HttpCode())->codes);
```

Returning status name for HTTP `200` status code.

```php 
<?php 
    echo HttpCode::status200(); // OK
```

Returning status name for HTTP `503` status code.

```php 
<?php 
    echo HttpCode::status503(); // Service Unavailable
```

```php
<?php
echo HttpCode::get(400); // Bad Request
```