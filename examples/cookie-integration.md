#  Client-Side Cookie Management Examples

***

## Overview

Usages example for Luminova's client-side cookies. 

***

## Introduction

Client-side cookie storage is a convenient way to store small pieces of data on the user's browser. This data persists even after the user closes the browser window, making it useful for various purposes such as session management, user preferences, and tracking. In this documentation we will show you examples on how you can use client-side cookie storage.

***
### Examples

```php
<?php 
use Luminova\Cookies\Cookie;
$cookie = new Cookie('foo', 'bar');
```

Get protected properties of cookie object.

```php
<?php 
echo $cookie->name;
echo $cookie->value;
echo $cookie->prefix;
echo $cookie->expires;
echo $cookie->path;
echo $cookie->domain;
echo $cookie->secure;
echo $cookie->httpOnly;
echo $cookie->sameSite;
echo $cookie->raw;
print_r($cookie->options);
print_r($cookie->default);
```

Check if cookie exists.

```php
<?php
$cookie->has('foo') // True
$cookie->has('bar') // False
```

Check if cookie exists using the current object cookie name.
```php
<?php
$cookie->has() // True
```

***

Accessing array cookie value by key index.

```php
<?php 
use Luminova\Cookies\Cookie;

$cookie = new Cookie('bar', ['key1' => 'Hello World', 'key2' => 'Luminova']);
```

Get value by key name.

```php
<?php
echo $cookie->get('key2'); // Luminova
```

Get all the array values of the cookie.

```php
<?php
print_r($cookie->get());
// Array ( [key1] => Hello World [key2] => Luminova )
```

Set cookie from cookie string.

```php 
<?php 
$str = 'theme=dark; Expires=Thu, 18 Dec 2025 12:00:00 UTC; Path=/; Domain=.localhost; secure; httponly; samesite=Lax';
$cookie = (new Cookie('bar'))->newFromString($str);
```