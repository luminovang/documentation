# Content Security Policy (CSP) Configuration

***

## Overview

The Content Security Policy configurations, provide methods to add, modify, or remove directives as needed. This ensures both convenience and a high level of security for web applications.

***

## Introduction

The `ContentSecurityPolicy` class extends the `BaseConfig` class to provide a configuration of Content-Security-Policy (CSP) customized for web applications. It allows you to sets up default directives for security and easily modify or add more directives as needed.

The class allows several `CSP` directives, such as `default-src`, `script-src`, `img-src`, `style-src`, and `object-src`, to control the security policies around resources like scripts, images, and stylesheets.

To extend or modify this CSP configuration can be done by adding or removing directives through the base class's methods, such as `addCsp()`, `removeCsp()`, and `clearCsp()`.

***

### Features:

- Automatically configures a base set of Content-Security-Policy (CSP) directives.
- Supports adding or removing directives dynamically.
- Outputs the CSP as either a header or a `<meta>` tag for use in HTML pages.
- Ensures security through strict CSP policies, with flexibility for customization.

***

### Usage Instructions:

1. **Instantiation**:
   You can instantiate the class and use its methods to customize or retrieve the CSP.

   ```php
   $csp = new ContentSecurityPolicy();
   ```

2. **Add/Remove Directives**:
   You can add or remove specific directives using the methods provided by `BaseConfig`.

    Add a new directive:
   ```php
   $csp->addCsp('font-src', ['https://fonts.example.com']);
   ```
   Remove an existing directive:
	 ```php
   $csp->removeCsp('img-src');
   ```

4. **Generate CSP Meta Tag**:
   To include the CSP in an HTML document as a `<meta>` tag, you can use:

   ```php
   echo $csp->getCspMetaTag();
   ```

   This would output something like:
   ```html
   <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self' https://scripts.example.com; img-src *; style-src 'self' 'unsafe-inline'; object-src 'none'">
   ```

5. **Send CSP Header**:
   To send the CSP as an HTTP header, use:

   ```php
   $csp->getCspHeader();
   ```

   This sends the following HTTP header:
   ```
   Content-Security-Policy: default-src 'self'; script-src 'self' https://scripts.example.com; img-src *; style-src 'self' 'unsafe-inline'; object-src 'none'
   ```

***

### Class Example:

```php
// /app/Config/ContentSecurityPolicy.php
<?php
namespace App\Config;

use Luminova\Base\BaseConfig;

final class ContentSecurityPolicy extends BaseConfig
{
    /**
     * Initializes the Content-Security-Policy with a set of default directives.
     * 
     * - default-src: Allows resources from the same origin.
     * - script-src: Allows scripts from the same origin and 'https://scripts.example.com'.
     * - img-src: Allows images from any source.
     * - style-src: Allows inline styles and styles from the same origin.
     * - object-src: Blocks all embedded objects.
     */
    protected function onCreate(): void
    {
        $this->addCsp('default-src', ["'self'"])
             ->addCsp('script-src', ["'self'", 'https://scripts.example.com'])
             ->addCsp('img-src', ['*'])
             ->addCsp('style-src', ["'self'", "'unsafe-inline'"])
             ->addCsp('object-src', ["'none'"]);
    }
}
```

***

### Extending the Class:

You can extend `ContentSecurityPolicy` or `BaseConfig` to further customize the CSP for specific use cases. For instance:

```php
// /app/Config/CustomCsp.php

<?php
namespace App\Config;

use App\Config\ContentSecurityPolicy;

final class CustomCsp extends ContentSecurityPolicy
{
    protected function onCreate(): void
    {
        parent::onCreate();
        $this->addCsp('font-src', ["https://fonts.example.com"]);
    }
}
```

In this example, the `CustomCsp` class extends the `ContentSecurityPolicy` class, adds a new `font-src` directive to allow fonts from a specific domain.