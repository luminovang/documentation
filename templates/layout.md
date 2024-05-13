# Layout Inheritance

***

## Overview

Template Layout provides a simple and flexible template manipulation to native PHP templating for managing layout files and extending layout sections within views.

***

## Introduction

Luminova framework enables developers to build applications using template engines such as `TWIG` or `SMARTY`, or default `PHP`. However, `PHP` itself is already a template engine, to enhance its capabilities, we introduce Template Layout in Luminova.

Template Layout offers a straightforward and flexible methods for manipulating templates in the default PHP templating. We prioritize simplicity and cleanliness to ensure optimal performance. Our focus is on addressing the inheritance problem in PHP templating without adding unnecessary complexity since the framework already provides everything you may need to build your application. With Template Layout, you can extend layout sections in your templates easily without sacrificing performance.

***

* Class namespace: `\Luminova\Template\Layout`

***

## Samples
In this example we are going to create two files `scaffolding.php` and `card.php` in `/resources/views/layouts/` directory.
The `scaffolding` will serve as our main layout while the `card` will serve as a sub layout which our `scaffolding` will extend it contents.

#### Scaffolding

The file `/resources/views/layouts/scaffolding.php` demonstrates how to divide your layout into sections, allowing the framework to extend whichever section you want.

```php
<!DOCTYPE html>
<html>
<?php $this->begin('head'); ?>
  <head>
    <link rel="stylesheet" href="<?= asset('css/style.css');?>" />
    <script type="text/javascript" src="<?= asset('js/script.js');?>"></script>
    <title>Website Title</title>
      <?php $this->begin('meta'); ?>
        <meta charset="utf-8">
      <?php $this->end(); ?>
  </head>
<?php $this->end(); ?>
    <body>
      <?php $this->begin('section'); ?>
        <section>
            <p>Website Page Section</p>
            <?= $this->import('card')->extend();?>
        </section>
      <?php $this->end(); ?>
      <?php $this->begin('footer'); ?>
        <footer>
            <p>&copy; Copyright 2023-2024 <a href="https://luminova.ng">Luminova</a></p>
            <?php $this->begin('social'); ?>
              <ul>
                <li><a href="#">FB</a></li>
                <li><a href="#">IG</a></li>
              </ul>
            <?php $this->end(); ?>
        </footer>
      <?php $this->end(); ?>
    </body>
</html>
```

#### Card

The file `/resources/views/layouts/card.php` contains another layout which our scaffolding will extend its contents.

```php
<table>
    <thead>
        <th>ID</th>
        <th>INFO</th>
    </thead>
    <tbody>
        <?php for($i = 0; $i < 5; $i++):?>
            <tr>
                <td>#<?= $i;?></td>
                <td>This is card number <?= $i;?></td>
            </tr>
        <?php endfor; ?>
    </tbody>
</table>
```

#### Extending Samples

In other to extend a layout or a section of layout file, you need to utilize the [global helper function](/global/functions.md#lmv-docs-layout) `layout()` to do that, see below examples.

To extend the whole contents of the our scaffolding layout:

```php
<?= layout('scaffolding')->extend();
```

To extend a specific layout section from `scaffolding`, you will need to pass the section name in `extend` method parameter.

```php
<?= layout('scaffolding')->extend('section');
```

Using nested extending, you must specify the nexting using dot annotation.
Example: here we extend `social`, the child section `footer` which is the parent section.

```php
<?= layout('scaffolding')->extend('footer.social');
```

***

## Methods

To define a layout sections, you must use `$this` keyword within the layout files whether you enabled view isolation or not.
See the `Samples` reference above.

### getInstance

Get the singleton instance of Layout.

```php
public static getInstance(): self
```

**Return Value:**

`self` - Return the instance of Layout class.

***

### import

Import a layout file file or section into another layout file.

```php
protected static import(string $layout): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$layout` | **string** | File name without extension (.php) |

**Return Value:**

`static` - Return the instance of Layout class.

***

### layout

Set the layout file name to extend.

```php
public layout(string $layout): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$layout` | **string** | File name without extension (.php). |

**Return Value:**

`self` - Return the instance of Layout class.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - Throws when layout file is not found.

***

### begin

The begin method, indicate where a new layout section begins till `end` method is called.

```php
protected begin(string $name): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | Section name to begin. |

***

### end

The end method, indicate that the current layout section has ended.

```php
protected end(string|null $name = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string&#124;null** | Optional section name to end. |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - Throws when end is called without active section to end.

***

### extend

This method allows you to inherit or extend a layout section into another view file.
When you pass `NULL`, you will inherit all the contents of the layout file passed in `layout` method, (e.g: `layout('scaffolding')->extend(null);`)

```php
public extend(string|null $section = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$section` | **string&#124;null** | Section name to extend or pass null to load all sections. |

**Return Value:**

`string` - Return the extended or inherited layout contents.

***

### get

The get method is equivalent with passing `NULL` to the `extend` method.
This will inherit all the contents of the layout file ignoring the defined `start` and `end` sections..

```php
public get(): string
```

**Return Value:**

`string` - Return the inherited layout contents.