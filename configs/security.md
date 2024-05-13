## Security Configuration

***

## Overview

Security configuration provides flexibility and control over security settings, ensuring that only trusted origins and hostnames can interact with the application.

***

The `Security Configuration` class provides properties to configure security-related settings in your application, such as trusted origins and hostnames, which will be used in the `Request` class for authenticating incoming request origins and hostname. 

***

* Class namespace: `\App\Controllers\Config\Security`
* File path: `/app/Controllers/Config/Security.php`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

***
## Properties

### trustedOrigins

Set the list of trusted origin domains or patterns.

```php
public static array<int,string> $trustedOrigins = [];
```

> This will be use in `Request` class for authenticating incoming requests origin.
>
> If none is specified then not additional check will be done when you call method `request()->getOrigin()` or `request()->isTrustedOrigin()`.

***

### trustedHostname

Set the list of trusted hostname or patterns.

```php
public static array<int,string> $trustedHostname = [];
```

> This will be use in `Request` class for authenticating your hostname.
>
> If none is specified then not additional check will be done when you call method `request()->getHostname()`.