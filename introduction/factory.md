# Factories

***

## Overview

Factory is Luminova's utility class that provides class instance management without the need for class registration or configuration. However, you must initialize classes using predefined names.

***

## Introduction

Factory is a utility class in the Luminova framework designed to simplify the management of class instances across the Luminova codebase. It serves as a centralized hub, allowing the management of shared instances of classes throughout the framework.

Unlike the `Services` utility, Factory does not require configuration or registering classes. Class registration is defined within the Factory class and ready to be initialized when needed. Additionally, Factory does not support initializing classes by fully qualified class name (e.g., `SomeClass::class`), you must initialize using the predefine names.

***

#### Usages

To dynamically create an instance of a factory class, you can use the `Factory::methodName()`.

```php
<?php
use \Luminova\Application\Factory;
$instance = Factory::cookie('foo', 'foo-value', [], true);
```

**Global Helper Function**

Additionally, there is a global helper function `factory()` available, which provides a convenient way to access factory instances.

```php
<?php
$instance = factory('cookie', 'foo', 'foo-value', [], true);
```

***

* Class namespace: `\Luminova\Application\Factory`

***

## Methods

### has

Check if class is available in factories

```php
public static has(string $factory): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$factory` | **string** | The factory name. |

**Return Value:**

`bool` - Return true if class is available in factories, false otherwise.

***

### delete

Delete a shared instance of factory class.

```php
public static delete(string $factory): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$factory` | **string** | The factory name. |

**Return Value:**
`bool` - Return true if shared instance was deleted, false otherwise.

***

### clear

Clear all shared instance of factory.

```php
public static clear(): void
```

***

## Class Instance Methods

### service

Return shared service class instance.

```php
public static service(): \Luminova\Application\Services
```

**Return Value:**

`\Luminova\Application\Services` - Return instance of service class

***

### functions

Utility function helper class.

```php
public static functions(bool $shared = true): \Luminova\Application\Functions
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`Functions` - Instance of the utility function helper class.

***

### session

Server-side user session class.

```php
public static session(?\Luminova\Interface\SessionManagerInterface $manager = null, bool $shared = true): \Luminova\Sessions\Session
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$params` | **\Luminova\Interface\SessionManagerInterface&#124;null** | Session storage interface. |
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`Session` - Instance of the server-side user session class.

***

### cookie

Client-side cookie class.

```php
public static cookie(string $name, mixed $value = '', array $options = [], bool $shared = true): \Luminova\Cookies\Cookie
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | Cookie name. |
| `$value` | **string** | Cookie value. |
| `$options` | **array** | Optional cookie options. |
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`Cookie` - Instance of the client-side cookie class.

***

### task

Time task utility class.

```php
public static task(bool $shared = true): \Luminova\Time\Task
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`Task` - Instance of the time task utility class.

***

### modules

PSR-4 Module autoloader and file importer class.

```php
public static modules(bool $shared = true): \Luminova\Library\Modules
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`Modules` - Instance of the PSR-4 Module autoloader and file importer class.

***

### language

Application translation class.

```php
public static language(?string $locale = null, bool $shared = true): \Luminova\Languages\Translator
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$locale` | **string&#124;null** | Locale for translation. |
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`Translator` - Instance of the application translation class.

***

### logger

PSR logger class.

```php
public static logger(bool $shared = true): \Psr\Log\LoggerInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`LoggerInterface` - Instance of the PSR logger class.

***

### fileManager

File manager class.

```php
public static fileManager(bool $shared = true): \Luminova\Storages\FileManager
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`FileManager` - Instance of the file manager class.

***

### validate

Input validation class.

```php
public static validate(bool $shared = true): \Luminova\Security\Validation
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`Validation` - Instance of the input validation class.

***

### response

Render response class.

```php
public static response(int $status = 200, bool $shared = true): \Luminova\Template\Response
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | HTTP response status code. Default is `200`. |
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`Response` - Instance of the render response class.

***

### request

HTTP Request class.

```php
public static request(bool $shared = true): \Luminova\Http\Request
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`Request` - Instance of the HTTP Request class.

***

### network

HTTP Network request class.

```php
public static network(?Luminova\Interface\HttpClientInterface $client = null, bool $shared = true): Luminova\Interface\NetworkInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$client` | **HttpClientInterface&#124;null** | HTTP client instance. |
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`NetworkInterface` - Instance of the HTTP Network request class.

***

### caller

This method can be used to call all methods withing a giving class class or list all extenders.

```php
public static caller(bool $shared = true): \Luminova\Application\Caller
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$shared` | **bool** | Whether to return a shared instance. Default is `true`. |

**Return Value** 

`Caller` - Instance of the Caller class.