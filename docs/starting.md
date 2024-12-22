# v3.3.0 Full Changelog

***

## Overview

Provides a detailed record of all changes in Luminova's latest version, updates, and improvements made to the Luminova Framework over time.

***

## Introduction

## General Overview

Luminova **3.4^** introduces major updates and performance improvements. Key features of this release include:

- **New Utility Classes & Features**: Enhanced functionality with the introduction of classes features for:
  - HMVC Architecture Support
  - Event Handling
  - XHtml Document Building
  - Input Field Building
  - Process Execution
  - Cookie File Jar
  - JWT Authentication
  - Fiber Async & Await Class
  - HTTP Server Implementations (Support for WebSocket)
  - CLI Image Art Module 
  - CLI Layout Builder Module
  - Email & Network Logger Helper
  - Lazy Object Initialization (PHP 8.4 and backwards compatibility)
  - Application Performance Profiling
  - HTTP Proxied Request
  - Local Network Application Debugging (With NovaKit Development Only)
  
---

## New Features

#### Since 3.4.0

- **[HTTP Cookie File Jar](/cookies/cookie-file-jar.md):**  
  Introduced the `Luminova\Cookies\CookieFileJar` class, for managing HTTP request cookies in a file-based jar.

- **[HTTP Proxy Network Request](/http/network#lmv-docs-configuration-options):**  
  Added support for HTTP proxy requests when using the `Luminova\Http\Client\Curl` client class, enhancing network request capabilities.

- **[HTTP Incoming Request Response](/http/request#lmv-docs-getcookie):**  
  Added the `getCookie` method with read-only support for managing cookies sent via the `Cookie` header in incoming HTTP requests.

---

## Depreciated

#### Since 3.4.0

- **Base View Controller Class:** The class name `Luminova\Base\BaseViewController` is **deprecated** as of version **3.4.0**. Use `Luminova\Base\BaseController` instead. The `BaseViewController` no longer serves its intended purpose and is scheduled for removal in a future release. Developers are encouraged to update their code to use the `BaseController` for continued support and functionality.

---

## To-Do List

### **Since 3.4.0**

#### 1. Controller Classes Update  

Controller classes that currently extend `BaseViewController` must be updated to extend `BaseController` instead, as part of the transition introduced in version **3.4.0**.  

#### Affected Files and Paths:
- `/app/Controllers/Http/*.php` - MVC Controllers.
- `/app/Controllers/Errors/*.php` - MVC Error Handling Controllers.
- `/app/Modules/Controllers/Http/*.php` - HMVC Root Controllers.
- `/app/Modules/Controllers/Errors/*.php` - HMVC Root Error Handling Controllers.
- `/app/Modules/<Module>/Controllers/Http/*.php` - HMVC Specific Module Controllers.
- `/app/Modules/<Module>/Controllers/Errors/*.php` - HMVC Specific Module Error Handling Controllers.

#### Before Update:
```php
<?php
namespace App\Your\Controller\NameSpace;

use \Luminova\Base\BaseViewController;

class MyController extends BaseViewController 
{
  // Controller logic
}
```

#### After Update:
```php
<?php
namespace App\Your\Controller\NameSpace;

use \Luminova\Base\BaseController;

class MyController extends BaseController 
{
    // Controller logic
}
```  

> **Note:** This change ensures compatibility with future releases and provides access to the enhanced functionality and optimized architecture of `BaseController`.