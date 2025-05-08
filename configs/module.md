# Configurations for Custom Modules Autoloading and Name Alias

***

## Overview

Autoload modules in your application

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