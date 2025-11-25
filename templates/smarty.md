# Smarty Templete Engine Implementation

***

## Overview

Using the Smarty template engine to render templates, Luminova custom methods simplify your experience and minimize class initialization, ensuring optimal performance.

***

## Introduction

Welcome to the Luminova framework documentation for integrating and optimizing the `Smarty` template engine. This guide introduces built-in methods `call_static`, `call_new`, `call_func`, and `get_const`, designed to enhance efficiency and maintainability when using `Smarty`. These methods minimize unnecessary class initialization and reduce maintenance efforts, ensuring optimal performance. Our aim is to simplify your experience with Smarty in the Luminova framework, allowing you to focus on creating your applications.

***

### Smarty

**Version:** `v5.1` - [Learn more](https://smarty-php.github.io/smarty)

To get started with Smarty, install it via Composer:

```bash
composer require "smarty/smarty"
```

***

### Call Static Methods

To call a static method in `Smarty`, you first need to register your class using the Smarty API method `registerClass`.

Using Luminova, you can register your class to be accessible in Smarty templates by adding class name in `/app/Config/Templates/Smarty/Classes.php`. under the `registerClasses` method, define your class alias as the array key and the string class name as the array value.

However, a more recommended approach is to use the built-in method `call_static` to access any class object without manually registering each class you need.

```php
function call_static(string $className, string $method, ?string $class = null, mixed ...$arguments): mixed
```

> The above is a pseudo class example illustrating how `call_static` handles method loading in a plain `PHP` design.

#### Usage

To call a method previously defined with `private static array $classes = [];` in `/app/Config/Templates/Smarty/Classes.php`:

```tpl
{call_static->foo method='myMethod'}
```

To directly call a static method from the class namespace:

```tpl
{call_static->instance class='\\Foo\\Bar\\ExampleClass' method='myExampleMethod'}
```

> *Note:*
> In other to call a method from a class namespace you must specify `instance` as the `call_static` method name.

***

### Call New

In pure Smarty, to initialize a class and access its properties, you first need to register your class object using Smarty's `registerObject` API method. You can then access your object using `{foo->methodName p1='foo' p2=$bar}`.

Alternatively, with Luminova, you can use the `call_new` class method to initialize a class object, directly access the object, or assign it to a variable.

```php
function call_new(string $className, ?string $class = null, mixed ...$arguments): object
```

#### Usage

To initialize a class instance using the `alias` previously defined with `private static array $classes = []`:

```tpl
{call_new->fooClass param='param' assign='foo'}
```

To directly initialize a class instance from the class namespace:

```tpl
{call_new->instance class='\\Foo\\Bar\\Example' assign='foo'}
```

To access methods and properties of the instance assigned above in `$foo`:

```tpl
{$foo->methodName()}
```

```tpl
{$foo->propertyName}
```

***

### Call Function

In pure Smarty, calling a global function requires extending `Smarty\Extension\Base` and overriding the `getModifierCallback` method, allowing you to use `{myFunction()}`. With Luminova, this extension is already handled, so you can call any global function directly using `{myFunction()}`.

Alternatively, you can use the `call_func` class method to call any global function.

```php
function call_func(string $functionName, mixed ...$arguments): mixed
```

#### Usage

Here is an example of calling a function:

```tpl
{call_func->myFunction param='param'}
```

***

### Get Constants

To get a constant variable in Smarty, you can use the default Smarty constant implementation `{$smarty.const.APP_NAME}`.

Optionally, you can use the `get_const` class method to retrieve any globally defined constant variable. This approach is recommended for better future updates and maintainability.

```php
function get_const(string $constant): mixed
```

#### Usage

The example below will output your application name:

```tpl
{get_const->APP_NAME}
```

***

### Reserved

The following are reserved keywords for method parameters and default method name for initializing class from namespace.

- `class` (argument) - Specify the class name you want to call.
- `method` (argument) - Specify the method name you want to call.
- `instance` (method) - Specify that you are initializing a new class object.

***

### Exported Class

Accessing your exported classes in smarty template files.

Here's an example of how to export the required classes within your application class.

```php 
<?php 
namespace App;

use Luminova\Foundation\Core\Application as CoreApplication;
use \App\Utils\FooClass;
use \App\Utils\BarStaticClass;

class Application extends CoreApplication 
{
	public function __construct()
	{
		$this->export(FooClass::class); 
		$this->export(BarStaticClass::class, null, false); 
	}
}
```
***
### Accessing Methods

#### Non-Static Methods

For non-static class methods, which are initialized during or before export.

```html
<p>My non-static class { FooClass->getSomeMethod() }</p>
```

#### Static Methods

For static class methods, that doesn't initialize during nor after export.

```html
<p>My static class { BarStaticClass::getSomeMethod() }</p>
```