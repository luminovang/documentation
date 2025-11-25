# Default Template Engine and Rendering Mode Configuration

***

## Overview

Choose your template engine, adjust caching, and control how pages are rendered to fit your project’s needs.

***

## Introduction

The Template Configuration in Luminova allows you choose and customize your template rendering engine like **PHP**, **Twig**, or **Smarty**. You can set rendering modes, define cache paths, control template visibility, and enable extra extensions for Twig or Smarty as needed. For more information see [View Rendering](/templates/views.md) documentation.

***

### Class Definition

* Class namespace: `\App\Config\Template`
* File path: `/app/Config/Template.php`
* This class is marked as **final** and can't be subclassed

***

## Properties

> **Note:** 
> Do not change the visibility of these properties nor the declaration types.

***

### templateEngine

The template engine used to render application views.

```php
public string $templateEngine = 'default';
```

**Available Template Engines**

1. `default` - To use default PHP template, no extra configuration required .
2. `smarty` - To use smarty, Configurations: `/app/Config/Templates/Smarty/`.
3. `twig` - To use twig, configurations: `/app/Config/Templates/Twig/`.

> To use `Smarty` or `Twig` you will need to first install the library and configure the classes, constants or function to use manually.

Composer command:

**Smarty:**

```bash
composer require smarty/smarty
``` 

**Twig:**

```bash
composer require "twig/twig:^3.0
```

***

### templateIsolation

Enables template isolation.

Enabling isolation will render your templates isolated mode, keeping preventing access to use of `$this` keyword when accessing template scope options or public methods in view class (e.g., instead of `$this->optionName` or `$this->_optionName`). Instead, you get a custom `$self` keyword (e.g., `$self->optionName` or `$self->_optionName`).

```php
public bool $templateIsolation = false;
```

***

### variablePrefixing

Controls whether view option variables are prefixed with an underscore `_`. 

This property controls how to access the template scope options:

- `true`  → Variables use the `_` prefix (e.g., `$this->_optionName` or `$self->_optionName`).
- `false` → No prefix added (e.g., `$this->optionName` or `$self->optionName`).
- `null`  → Variables are stored as raw arrays (`$options`)  (e.g, `$options['optionName']`).

```php
public ?bool $variablePrefixing = true;
```

> **Note:**
> This must be set **before** building your application. 
> Changing it later will break template option recognition. 

***

### cacheFolder

Directory for cached template files.

This property controls the cache template file directory, where template cache is stored.

```php
public string $cacheFolder = 'writeable/caches/';
```

***

### compileFolder

Directory by template engines for when compiling templates.

This property controls the template compile metadata directory. Use for template engines like **Smarty** or **Twig**.

```php
public string $compileFolder = 'writeable/compile/';
```

***

### configFolder

Directory for template engine configuration files.

This property controls the destination to store template configurations when using **Smarty** or **Twig** template engine.

```php
public string $configFolder = 'writeable/config/';
```