# Browser and Client Agent Detection Configuration

***

## Overview

Configure how your application identifies browsers, devices, and user agents to deliver custom responses or apply specific rules.

***

## Introduction

The `Browser` configuration, allows you to defines patterns and keywords used to identify browsers, devices, and bots from user agent strings. It allows developers to adjust how their application detects and responds to different clients.

### Purpose

The `Browser` class centralizes all user-agent detection settings used by the `Request` class. By specifying known patterns for web crawlers, mobile devices, and major browsers, developers can maintain consistent detection logic and update these settings easily as new clients emerge.

***

## Class Definition

* Class namespace: `\App\Config\Browser`
* File path: `/app/Config/Browser.php`
* This class is marked as **final** and can't be subclassed

***

## Properties

### robotPatterns

Define list of know robot patterns and name.

An associative array of strings representing common keywords or patterns found in robots user agent string. Where array key is the robot pattern or keyword and the value is the name.

```php
public array<string,string> $robotPatterns = [
    'Googlebot' => 'Googlebot',
    //...
]
```

***

### mobileKeywords

Define list of know mobile device patterns and name.

An associative array of strings representing common keywords found in mobile device user agents. Where array key is the mobile keyword and the value is the name.

```php
public array<string,string> $mobileKeywords = [
    'android'  => 'Android',
    //...
]
```

> **Note:**
> The order is important. 
> Always put the known mobile first before `Generic Mobile`.

***

### browsers

Define list of know browsers keyword or patterns.

Define a know list of identifier representing common keywords or patterns found in browser user agents.

```php
public string[] $browsers = [
   'Chrome',
    //...
]
```

> **Use Case:**
> This will be taken in consideration when you call method `Luminova\Http\UserAgent::isTrusted()`.
>
> Its also a good authentication when you are developing a hybrid mobile app, to ensure only your webview can access.