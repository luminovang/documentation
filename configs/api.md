# Incoming API Request Configuration

***

## Overview

Secure your API endpoints by controlling allowed origins, headers, and access rules to prevent unauthorized or malicious requests.

***

## Introduction

These properties define essential security settings for your application's APIs. Use them to control which origins are allowed to access your API endpoints and which HTTP headers can be sent with requests.

***

## Class Definition

* Class namespace: `\App\Config\Apis`
* File path: `/app/Config/Apis.php`
* This class is marked as **final** and can't be subclassed

***

## Properties

### forbidEmptyOrigin

Whether to reject API requests with an empty Origin header.

This property controls whether to forbid requests with an **empty Origin** header.
If `true`, requests without an Origin header are denied.

```php
public bool $forbidEmptyOrigin = true
```

***

### allowCredentials

Whether to include credentials in API responses.

This property controls whether to allow credentials header in **API** requests.
If true, the `Access-Control-Allow-Credentials` header is sent.

```php
public bool $allowCredentials = true
```

***

### allowOrigins

Allowed origins for incoming API requests.

This property controls which origins should be allowed to access API endpoints.

- Use `'*'` to allow any origin.  
- Use the string `'null'` to also allow all origins explicitly.  
- Provide an array to restrict to specific origins.  

```php
public string|array<int,string> $allowOrigins = '*'
```

**Example:**

```php
public string|array $allowOrigins = [
    'https://example.com',
    'https://luminova.ng',
    '127.0.0.1'
]
```

***

### allowHeaders

Allowed request headers for API requests.

This property controls which headers should be allowed in request headers when accessing API endpoints.

```php
public array<int,string> $allowHeaders = []
```

> An empty array allows all headers by default.

**Example:**
```php
public array $allowHeaders = [
    'Content-Type',
    'Authorization',
    'X-Requested-With',
    'Host',
    'Accept', 
    'User-Agent'
];
```

> **Note:**
> When you define a custom headers to only allow, if request is sent with none matching headers, it will fail.