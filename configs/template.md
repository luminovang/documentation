# Configurations for Application Template Rendering and Handling Mode 

***

## Overview

Customize your template rendering engine,  cache path, and rendering mode or enable other useful features based on your coding and application requirements.

***

## Introduction

Template Configuration in Luminova allows you to set up PHP, Twig, and Smarty template rendering engines for application views. You can configure modes, cache path, visibilities, as well as Smarty and Twig extensions.

***

* Class namespace: `\App\Config\Template`
* File path: `/app/Config/Template.php`
* This class is marked as **final** and can't be subclassed

***
## Properties

> *IMPORTANT:* Do not change the visibility of these properties nor the declaration types.

***

### templateEngine

Specify the template engine to use for rendering your application views.

```php
public string $templateEngine = 'default';
```

**Available Template Engines**

1. `default` - To use default PHP template, no additional configuration is required..
2. `smarty` - To use smarty, Configurations: `/app/Config/Templates/Smarty/`.
3. `twig` - To use twig, configurations: `/app/Config/Templates/Twig/`.

> To use `Smarty` or `Twig` you will need to first install the library by running command `composer require smarty/smarty` for smarty or `composer require "twig/twig:^3.0` for Twig.
> 
> Also you need to configure the classes, constants or function to use manually.

***

### templateIsolation

Enabling this isolation will render your view in isolation mode keeping away access to your application controller class and template class object while enforcing template view options to be accessed as variables (e.g., `$verName` or `$_verName` instead of `$this->verName` or `$this->_verName`).

Furthermore, access to `protected` and `public` properties registered in your `App\Application` class won't be possible using the `$this` keyword (e.g., `$this->myClassName->doMethod()`). 

Instead, you can use a custom `$self` keyword (e.g., `$self->myClassName->doMethod()`) and classes must be exported first using template dependency injection method `$this->export()`,  in application controller.

```php
public bool $templateIsolation = false;
```

**Example**

```php
// /app/Application.php
<?php
namespace App;
use \Luminova\Core\CoreApplication;
class Application extends CoreApplication 
{
	public function __construct()
	{
	  	parent::__construct();
		$this->export(new FooClass(), 'foo');
	}
}
```
Or using `onCreate` method.

```php
// /app/Application.php
<?php
namespace App;
use \Luminova\Core\CoreApplication;
class Application extends CoreApplication 
{
	protected function onCreate()
	{
		$this->export(new FooClass(), 'foo');
	}
}
```

***

### variablePrefixing

This property controls the use of variable key prefixing with `_` for view options, when set to `false`, accessing view options with underscore prefixing will be disabled.

Instead of using `$this->_vername` or  in isolation `$_vername` , you will use `$this->vername` or `$vername` in isolation.

```php
public bool|null $variablePrefixing = true;
```

> *Note:*
> 
> You must specify your variable type before building, your application you can't change this option after building.
> 
> Changing it later will cause view options not to be discovered except if you will have to trace and replace all files where you have used previous implementation.
> 
> If `NULL` is passed, the view options will be left as raw arrays and can be accessed by `$options`.

***

### cacheFolder

Set your application template cache file directory path.

```php
public string $cacheFolder = 'writeable/caches/';
```

***

### compileFolder

Set your application template compile folder, this will be used by template engines like `Smarty` or `Twig`.

```php
public string $compileFolder = 'writeable/compile/';
```

***

### configFolder

Set your application template configuration folder for `Smarty` or `Twig` template engine.

```php
public string $configFolder = 'writeable/config/';
```