# Twig Implementation

***

## Overview

Using the Twig template engine, to efficiently render templates and minimizing class initialization to save you the time and effort of manually registering classes and functions.

***

## Introduction

Welcome to the Luminova framework documentation for integrating and optimizing the `Twig` template engine. This guide introduces built-in methods—`call_static`, `call_new`, `call_func`, and `get_const`—designed to enhance efficiency and maintainability. These methods minimize unnecessary class initialization and save you the time and effort of manually registering classes and functions, ensuring optimal performance. Our goal is to simplify your experience with Twig in the Luminova framework, allowing you to focus on building robust and dynamic applications.

***

### Twig

**Version:** `v3.10.2` - [Learn more](https://twig.symfony.com)

To get started with Twig, install it via Composer:

```bash
composer require "twig/twig:^3.0"
```

***
### Call Static

To call a static method in `Twig`, you first need to extend `Twig's` `AbstractExtension` and register your Twig functions in the overridden `getFunctions` method, and also call the `addExtension` method to add your extensions, before you can then access these functions using `{{ myFunction() }}` in your templates.

In the Luminova framework, registering Twig functions can be done in `/app/Controllers/Config/Templates/Twig/Functions.php`. Within the `registerFunctions` method, define your Twig functions in the array within the method.

However, a more recommended approach is to use the built-in `call_static` method to access any class object and functions without manually registering each class and function you need.

```php
function call_static(string $className, string $method, mixed ...$arguments): mixed
```

> The above is a pseudo-class example illustrating how `call_static` handles method loading in a plain `PHP` design.

#### Usage

Optionally, you can define a list of classes with aliases in `protected static array $classes = []` within the Twig function class at `/app/Controllers/Config/Templates/Twig/Functions.php`.

To call a method previously defined with a static class method:

```tpl
{{ call_static('foo', 'myMethod') }}
```

To directly call a static method from the class namespace:

```tpl
{{ call_static('\\Foo\\Bar\\ExampleClass', 'myMethod', ...arguments) }}
```

### Call New

In pure Twig, to initialize a class instance and access its properties and methods, you first need to register your class in a Twig functions extension. Initializing and accessing the class object can be done as shown below:

```tpl
{{ foo('arg1', 'arg2').methodName() }}
```

Or, you can assign the instance to a variable:

```tpl
{% set instance = foo('arg1', 'arg2') %}
{{ instance.methodName() }}
```

Alternatively, with Luminova, you can use the `call_new` method to initialize a class object, directly access the object, or assign it to a variable. This simplifies the process and avoids the need for manual class registration.

```php
function call_new(string $className, mixed ...$arguments): object
```

#### Usage

To initialize a class instance using the `alias` previously defined with `protected static array $classes = []`:

```tpl
{{call_new('fooClass', 'arg1', 'arg2').myMethod()}}
```
Or assign class object to variable. 
```tpl
{% set instance = call_new('fooClass', 'arg1', 'arg2') %}
```

To directly initialize a class instance from the class namespace:

```tpl
{% set instance = call_new('\\Foo\\Bar\\Example', 'arg1', 'arg2') %}
```

To access methods and properties of the instance assigned above in `instance`:

```tpl
{{instance.methodName()}}
```

```tpl
{{instance.propertyName}}
```

***

### Call Function

To call a global function in Twig, you typically need to extend `AbstractExtension` and override the `getFunctions` method to add a new Twig function. This function can then be accessed by calling the function name, like `{{ myFunction() }}`.

Alternatively, with Luminova, you can use the `call_func` method to call any global function without prior registration.

```php
function call_func(string $functionName, mixed ...$arguments): mixed
```

#### Usage

Here is an example of calling a function using `call_func`:

```tpl
{{ call_func('myFunction', 'arg1', 'arg2') }}
```

***

### Get Constants

To access a constant variable in Twig, you typically need to register your constant variables in the Twig extension `getGlobals` method. This allows you to access global constant variables like `{{ APP_NAME }}`.

Alternatively, you can use the `get_const` method to retrieve any globally defined constant variable without manually defining them again. This approach is recommended for better future updates and maintainability.

```php
function get_const(string $constant): mixed
```

#### Usage

The example below demonstrates how to output your application name using `get_const`:

```tpl
{{ get_const('APP_NAME') }}
```

***

### Exported Class

In Twig templates, unlike `Smarty`, you can't directly access class static methods without first registering them in a Twig extension. For better performance, it's recommended to use the `call_static` method for static class methods. But if you must your static class, ensure you enable the initialization flag the third parameter in `export` method to initialize the class.

Here's an example of how to export the required classes within your application class.

```php
<?php 
namespace App\Controllers;

use \Luminova\Base\BaseApplication;
use \App\Controllers\Utils\FooClass;
use \App\Controllers\Utils\BarStaticClass;

class Application extends BaseApplication 
{
    public function __construct()
    {
        $this->export(FooClass::class); 
        $this->export(BarStaticClass::class); 

        // Static export without initialization for Twig.
        $this->export(BadStaticClass::class, null, false); 
    }
}
```
***

### Accessing Methods

#### Non-Static Methods

For non-static class methods, which are initialized during or before export.

```html
<p>My non-static class method: {{ FooClass.getSomeMethod() }}</p>
```

#### Static Methods

For static class methods:

```html
<p>My static class method: {{ BarStaticClass.getSomeMethod() }}</p>
```

#### Handling Bad Static Exports

If you attempt to access static class methods without the initialization flag, it will result in an error or unintended behavior.

```html
<p>{{ BadStaticClass.getSomeMethod() }}</p>
```
> **Exception**: Throws an exception "Impossible to invoke a method ("getSomeMethod") on a string variable."

Accessing the static class by its reference will output the class namespace:

```html
<p>{{ BadStaticClass }}</p>
```
> **Outputs**: \App\Controllers\Utils\BadStaticClass

***

**Your Thought**

*Can't you do something to allow access to static classes or initialize them within the framework code?*

**Our Answer**

Yes, we can. However, we chose not to do that because we want you to understand what you are doing and why. To follow better coding practices and enhance your application's performance, always use the `call_static` method if the class is only required within a Twig template file and not needed in your controller class. This ensures more efficient and manageable code.