# Modules Autoloading and Class Aliases Configuration

***

## Overview

Automatically load application modules and set class aliases to simplify imports and improve project organization.

***

## Introduction

Autoload modules in your application.

***
* File path: `/app/Config/Modules.php`

***

```php
return [
    'alias' => [
        //'Foo' => 'SomeClass\ExampleFoo',
    ],
    'psr-4' => [
       //'Example\MyNamespace'    => '/example/MyClass/',
    ]
];
```