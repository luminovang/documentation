# Configurations for Incoming API Requests

***

## Overview

In other to secure your API endpoint, set up the allowed origins and header to prevent unwanted access.

***

## Introduction

These properties serve as essential configuration and security measures for your application's APIs. They allow you to define the origins permitted to communicate with your API endpoints and specify the allowed headers.

***

* Class namespace: `\App\Config\Apis`
* File path: `/app/Config/Apis.php`
* This class is marked as **final** and can't be subclassed

***

## Properties

### forbidEmptyOrigin

Indicates whether to forbid requests with empty Origin headers.
If true, requests with empty Origin headers are forbidden.

```php
public bool $forbidEmptyOrigin = true
```

***

### allowCredentials

Set whether to allow credentials in API requests.
If true, the Access-Control-Allow-Credentials header is included.

```php
public bool $allowCredentials = true
```

***

### allowOrigins

Set the allowed origins for the Access-Control-Allow-Origin header in API requests.
An array of allowed origins or a wildcard `*` to allows all origins.

```php
public array<int,string>|string $allowOrigins = [
    'https://localhost',
    'http://localhost',
    '127.0.0.1'
]
```
> Note passing string null will result in allow all origins.

***

### allowHeaders

Set the allowed headers for the Access-Control-Allow-Headers header in API requests.
An array of allowed header names.

```php
public array<int,string> $allowHeaders = [
    'Content-Type',
    'Access-Control-Allow-Headers',
    'Authorization',
    'X-Requested-With'
]
```