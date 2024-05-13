# Base Config

***

## Overview

Base configuration allows you to extend the Luminova configuration class and define your custom variables or methods for your application configuration.

***

## Introduction

Base configuration serves as and underlying of your application where you can define your important variables for your application. To use this class you need to extend it.

* Class namespace: `\Luminova\Base\BaseConfig`
* This class is an **Abstract class**

***

###  Usages.

```php
<?php 
namespace App\Controllers\Utils;

use \luminova\Base\BaseConfig;
class Config extends BaseConfig 
{
	public const MY_VAR = 'var';
}
```

> Now you can import your configuration class anywhere you would like to use it.

***

## Methods

### getEnv

And alternative to global helper class `env()`, to get environment configuration variables with a specific return type.

```php
public static getEnv(string $key, mixed $default = null, string|null $return = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to retrieve. |
| `$default` | **mixed** | The default value to return if the key is not found. |
| `$return` | **string&#124;null** | Optional return types<br />- [bool, int, float, double, nullable, string] |