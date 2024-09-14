# Core Utility Functions Class

***

## Overview

Base functions are a collection of fundamental utility methods that serve as a helper for application development. These functions typically encapsulate common tasks and operations for frequent use.

***

## Introduction

The Core Functions class serves as a repository for various utility functions that provides a reusable methods to various solutions to tackle different problems. As an abstract class, it serves as a foundation for common functionalities that can be extended and customized to suit specific use cases within your application. With a diverse collection of methods, each designed to address specific needs.

***
### Func Class

By default base function inherited methods from `Func` class which also has some useful methods to smooth your application development. To learn more [Read Documentation](/function/ip-address.md).

***

*  Class namespace: `\Luminova\Base\CoreFunction`
* This class is an **Abstract class**
* Parent class namespace: `\Luminova\Functions\Func`

***

### Extending Class

To utilize the functionality provided by the Core Functions class, you can either use the global helper function `func()` or extend the `CoreFunction`. 

You can find your functions class which is located in `/app/Utils/Functions.php` .

```php 
<?php
use App\Utils\Functions;

Functions::methodName()
```

> **Note:** Your extended function class methods cannot be called using global helper function `func`, only the built-in methods are allowed.

***

## Methods

### ip

Initialize or return a shared an instance of the IP address class.

```php
public static ip(): \Luminova\Functions\IP
```

**Return Value:**

`Luminova\Functions\IP` - Returns static instance of ip address class.

**See Also**

[IP Address Class](/function/ip.md) - See the `IP` class documentation for available useful methods.

***

### files

Initialize or return a shared an instance of the Files class.

```php
public static files(): \Luminova\Storages\FileManager
```

**Return Value:**

`\Luminova\Storages\FileManager` - Returns static instance of file manager class.

**See Also**

[File Manager Class](/files/manager.md) - See the `FileManager` class documentation for available useful methods.

***

### tor

Initialize or return a shared an instance of the tor detector class.

```php
public static tor(): \Luminova\Functions\Tor
```

**Return Value:**

`\Luminova\Functions\Tor` - Returns static instance of tor class.

**Example**

Check if user ip address is a not exit node ip.

```php
<?php
use App\Utils\Functions;

if(Functions::tor()->isTor(ip_address())){
	echo 'Your ip address is detected in tor exit not, we have ban your account.'
}
```

> Corresponding helper function `is_tor()` to check if ip address is tor ip or not;

***

### math

Initialize or return a shared an instance of the math class.

```php
public static math(): \Luminova\Functions\Maths
```

**Return Value:**

`Maths` - Returns static instance of math class.

**See Also**

[Math Class](/function/math.md) - See the `Maths` class documentation for available useful methods.