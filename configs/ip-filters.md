# IP Address and Proxy Filtering Configuration

***

## Overview

Control how your application detects and verifies client IP addresses, even behind proxies, to ensure accurate security checks.

***

## Introduction

The `IP Configuration` class allows developers to configure settings related to IP address lookup and validation in their applications. It provides properties to specify the IP address API provider for `IP info` lookup, API key, trusted proxies, and other options as required.

***

* Class namespace: `\App\Config\IPConfig`
* File path: `/app/Config/IPConfig.php`
* This class is marked as **final** and can't be subclassed

## Properties

### apiProvider

Specify the IP address API provide to use in looking up user ip address information.
Available Providers: `ipapi` and `iphub`

```php
public string $apiProvider = 'iphub';
```

***

### ipHubVersion

If you are using `iphub`, then you need to specify the API version.

```php
public string $ipHubVersion = 'v2';
```

***

### apiKey

Specify the IP address provide's API key.

```php
public string $apiKey = '';
```

***

### trustedProxies

List of trusted proxy IP addresses or subnets.

```php
public array<int,string> $trustedProxies = [];
```

> This will be used in conjunction with `Request` class to determine if incoming request is from a truest proxy or ip address. If none is provided then no additional authentication will be done in incoming request.