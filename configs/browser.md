# Browser Configuration

***

## Overview

This class provides configuration options related to browsers and user agents.

***

## Introduction

The `Browser` configuration, located at `\app\Config\Browser.php`, provides essential configuration options related to browsers and user agents. This class is designed to help define and manage common patterns and keywords found in user agent strings, enabling developers to customize the behavior of their applications based on browser or device information.

### Purpose

The purpose of the `Browser` class is to centralize configuration settings related to browser and user agent identification in `Request` class. By defining known patterns for common robots, mobile user agents, and browser user agents, developers can easily manage and update these configurations as needed.

***

* Class namespace: `\App\Config\Browser`
* File path: `/app/Config/Browser.php`
* This class is marked as **final** and can't be subclassed

***
## Properties

### robotPatterns

An associative array of strings representing common keywords or patterns found in robots user agent string.
The array key is the robot pattern or keyword and the value is the name.

```php
public array<string,string> $robotPatterns = [
    'Googlebot' => 'Googlebot',
    //...
]
```

***

### mobileKeywords

An associative array of strings representing common keywords found in mobile device user agents.
The array key is the mobile keyword and the value is the name.

```php
public array<string,string> $mobileKeywords = [
    'android'  => 'Android',
    //...
]
```

> *Note:* 
>
> The arrangement order is important. Always put the known mobile first before `Generic Mobile`.

***

### browsers

An array of strings representing common keywords or patterns found in browser user agents.

```php
public string[] $browsers = [
   'Chrome',
    //...
]
```
> This array is considered when calling the `$userAgent->isTrusted()` method.
>
> It's also a useful authentication method for hybrid mobile app development. 
>
> By defining a custom user-agent string for your webview app, you can ensure that only your app can access the website