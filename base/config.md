# Base Config

***

## Overview

Base configuration serves as an underlying of your application configuration classes, which may be required by the framework depending on the component used in your application. 

***

## Introduction

The base configuration service as the foundation of your application's configuration classes, which are essential for the framework depending on the components utilized in your application. To create a new configuration class, you need to extend and define necessary properties and method as you need.

***

* Class namespace: `\Luminova\Base\BaseConfig`
* This class is an **Abstract class**

***

###  Usages

Bellow is an example how you can extend the `BaseConfig` class.

```php
<?php 
namespace App\Utils;

use \luminova\Base\BaseConfig;
class Config extends BaseConfig 
{
	public const MY_VAR = 'var';
}
```

> Now you can import your configuration class anywhere you would like to use it or register in service to make it available anywhere.

***

## Methods

### getEnv

An alternative to the global helper function `env`, the `getEnv` method allows you to retrieve an environment variable with a specific return type.

```php
public static final getEnv(string $key, mixed $default = null, string|null $return = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to retrieve. |
| `$default` | **mixed** | The default value to return if the key is not found. |
| `$return` | **string&#124;null** | Optional return types<br />- [bool, int, float, double, nullable, string] |