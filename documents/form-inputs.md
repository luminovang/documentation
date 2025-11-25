# Inputs Form Elements Builder

***

## Overview

The form inputs builder class provides static methods for programmatically generating HTML markups for form input elements.

***

## Introduction

The Luminova `Inputs` class offers a set of static methods for dynamically generating HTML form input elements, providing a simple and efficient way to build forms programmatically. It abstracts the manual creation of form elements by handling various input types, attributes, and custom options through its flexible method signatures.

For detailed instructions on generating elements, refer to the [Form Input Examples Documentation](/examples/html-form-inputs-builder.md) to learn how to use the class for creating various form elements.

#### Key Features:

- **Wide Input Support**: Generate standard HTML inputs like `text`, `email`, `password`, `radio`, `checkbox`, and more, using the `element` method.
- **Dynamic Element Generation**: Automatically handles different input types by calling methods like `Inputs::text()`, `Inputs::email()`, etc., or even additional custom types `Inputs::FooInputBar()`.
- **Customizable Attributes**: Easily add custom attributes like `class`, `id`, `data-*`, or `aria-*` through an associative array of attributes.
- **Optgroup & Datalist Support**: Provides methods to handle advanced input elements like select dropdowns with option groups and searchable datalists.
- **Flexible Naming and Value Handling**: Automatically handle input names, values, and types in a flexible and scalable way for various form scenarios.

***
## Class Declaration

* Class namespace: `Luminova\Component\Widget\Inputs`
* Parent class: `Luminova\Component\Widget\Document`

***

## Properties

### template

The document element style to use (e.g, `Inputs::HTML5`, `Inputs::BOOTSTRAP5`).

```php
public static int $template = Inputs::HTML5;
```

***

### encoding

Use encoding for escaping document element, contents and attributes.

```php
public static string $encoding = 'UTF-8'
```

***

### xhtmlStrictTagNames

This property determines whether element tag names should be automatically converted to lowercase (in strict XHTML mode) or preserved as they were passed, which can be useful for applications using custom template elements or case-sensitive tags (such as when working with XML-like data structures).

```php
public static bool $xhtmlStrictTagNames = true;
```

**Usage Examples:**

```php
Inputs::$xhtmlStrictTagNames = true;

echo Inputs::element('InputPasswordText', 'password', 'password'); 
// Generates: <inputpasswordtext type="password" name="password" />
```

```php
Inputs::$xhtmlStrictTagNames = false;

echo Inputs::element('InputPasswordText', 'password', 'password'); 
// Generates: <InputPasswordText type="password" name="password" />
```

***

## Methods

### __callStatic

Dynamically generates an HTML input element, the type is derived based on the called static method name.

```php
public static __callStatic(string $method, array $arguments): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The input type (method name being called). |
| `$arguments` | **array** | The arguments: (?string $name, ?mixed $value, bool $close, array $attributes).<br />- $name (string\|null): The name attribute of the input.<br />- $value (string\|null): The value attribute of the input (optional).<br />- $close (bool): Whether the input is self-closing (default: false).<br />- $attributes (array): Additional attributes for the input element (optional). |

**Return Value:**

`string` - Return the generated HTML input element.

> **Supported Method Names:**
> Method names should correspond to valid input types. For input types like `datetime-local`, use underscores (e.g., `Inputs::datetime_local()` for `datetime-local`).

**Example Usage:**

```php
<?php
echo Inputs::email(
	'user-email', 
	'peter@example.com', 
	['class' => 'form-control user-input', 'id' => 'email']
);
```

***

### element

Generate an HTML element (input,  button, etc...) with the specified attributes.

```php
public static element(
    string $tag, 
    ?string $type = null, 
    ?string $name = null, 
    ?string $value = null, 
    bool $closeElement = false, 
    array<string,mixed> $attributes = []
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$tag` | **string** | The HTML tag to create (e.g., 'input', 'button'). |
| `$type` | **string&#124;null** | Optional input type (e.g., 'text', 'password', 'submit'). |
| `$name` | **string&#124;null** | Optional name attribute of the element. |
| `$value` | **string&#124;null** | Optional value for the input or the text content for the button. |
| `$closeElement` | **bool** | Whether to close the tag with content (e.g., `&lt;button&gt;`) or self-close (default: false). |
| `$attributes` | **array<string,mixed>** | Additional HTML attributes (e.g., 'class', 'id', 'data-*'). |

**Return Value:**

`string` - Return the generated HTML tag string.

***

### elements

Generates multiple HTML form inputs elements based on an array of element specifications.

Each element in the array should be an associative array containing the following keys:

- **tag** (string): The HTML tag to generate (e.g., 'input', 'button').
- **type** (string): Optional type attribute for elements like 'input' (default: empty string).
- **name** (string): Optional name attribute for the element (default: empty string).
- **value** (string): Optional value to be placed inside the element, or for 'input' elements (default: empty string).
- **closeElement** (bool): Whether to close the tag with content or self-close it (default: false).
- **attributes** (array): Optional associative array of HTML attributes for the element (default: empty array).

```php
public static elements(array $inputs): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$inputs` | **array<int,string>** | An array of associative arrays where each represents an HTML element and its attributes. |

**Return Value:**

`string` - Returns the generated HTML input elements as a concatenated string.

**Example usage:**

```php
$inputs = [
    [
        'tag' => 'input',
        'type' => 'text',
        'name' => 'username',
        'value' => 'JohnDoe',
        'closeElement' => false,
        'attributes' => ['class' => 'form-control']
    ],
    [
        'tag' => 'input',
        'type' => 'password',
        'name' => 'password',
        'attributes' => ['class' => 'form-control']
    ]
];

echo Inputs::elements($inputs);
```

***

### form

Generate an HTML form block.

```php
public static form(
    string|array<int,array> $inputs, 
    string $action = '', 
    string $method = 'GET',
    array<string,mixed> $attributes = []
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$inputs` | **string&#124;array<int,array>** | The form inputs (pre-generated) or an array of inputs. |
| `$action` | **string** | The form action URL (default: ''). |
| `$method` | **string** | The form submission method (e.g, 'POST', 'GET'). |
| `$attributes` | **array<string,mixed>** | Additional HTML attributes for the form tag. |

**Return Value:**

`string` - Return the generated form HTML.

***

### input

Generate an HTML input field.

```php
public static input(string $type, string $name, string $value = '', array<string,string> $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The type of input (e.g., 'text', 'password', 'email'). |
| `$name` | **string** | The name of the input. |
| `$value` | **string** | The value of the input field. |
| `$attributes` | **array<string,string>** | Additional HTML attributes (e.g, `classNames`, `id's`, `data-attributes`). |

**Return Value:**

`string` - Return the generated input HTML.

***

### file

Generates an HTML file input element for uploading files, with optional capture settings.

```php
public static file(string $name, ?string $capture = null, array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name attribute for the input element. |
| `$capture` | **string&#124;null** | Specifies the camera to be used for capturing files (default: null):<br />- 'front': Use the front camera (user-facing).<br />- 'back': Use the back camera (environment-facing).<br />- 'switch': Allow the user to choose which camera to use.<br />- null: No specific camera preference. |
| `$attributes` | **array** | Optional additional attributes for the input element. |

**Return Value:**

`string` - Returns the generated HTML file input element as a string.

***

### label

Generates an HTML label element with associated attributes.

```php
public static label(string $for, string $text, array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$for` | **string** | The ID of the input element this label is for. |
| `$text` | **string** | The text displayed inside the label. |
| `$attributes` | **array** | Optional additional HTML attributes (e.g., 'class', 'style'). |

**Return Value:**

`string` - Return the generated HTML label element.

***

### button

Generate a button element.

```php
public static button(string $type, string $name = '', string $text = 'Submit', array<string,string> $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The type of the button (e.g., 'submit', 'button'). |
| `$name` | **string** | The name attribute of the button. |
| `$text` | **string** | The button text. |
| `$attributes` | **array<string,string>** | Additional HTML attributes (e.g, `classNames`, `id's`, `data-attributes`). |

**Return Value:**

`string` - Return the generated button HTML.

***

### textarea

Generate a textarea field.

```php
public static textarea(string $name, string $value = '', array<string,string> $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the textarea. |
| `$value` | **string** | The value inside the textarea. |
| `$attributes` | **array<string,string>** | Additional HTML attributes (e.g, `classNames`, `id's`, `data-attributes`). |

**Return Value:**

`string` - Return the generated textarea HTML.

***

### checkbox

Generate a checkbox input field.

```php
public static checkbox(string $name, string $value = '', bool $checked = false, array<string,string> $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the input. |
| `$value` | **string** | The value of the checkbox. |
| `$checked` | **bool** | Whether input is checked (default: false). |
| `$attributes` | **array<string,string>** | Additional HTML attributes (e.g, `classNames`, `id's`, `data-attributes`). |

**Return Value:**

`string` - Return the generated checkbox input HTML.

***

### radio

Generate a radio input field.

```php
public static radio(string $name, string $value = '', bool $checked = false, array<string,string> $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the input. |
| `$value` | **string** | The value of the radio input. |
| `$checked` | **bool** | Whether input is checked (default: false). |
| `$attributes` | **array<string,string>** | Additional HTML attributes (e.g, `classNames`, `id's`, `data-attributes`). |

**Return Value:**

`string` - Return the generated checkbox input HTML.

***

### option

Generate a single HTML `&lt;option&gt;` element.

```php
public static option(string|int $key, ?string $value = null, bool $selected = false, bool $disabled = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;int** | The key-value attribute of the option element. |
| `$value` | **string&#124;null** | The display text for the option element or null for self-closing. |
| `$selected` | **bool** | Whether the option should be marked as selected (default: false). |
| `$disabled` | **bool** | Whether the option should be marked as disabled (default: false).  |

**Return Value:**

`string` - Return the generated HTML `&lt;option&gt;` element with proper escaping.

***

### optgroup

Generate an HTML `&lt;optgroup&gt;` element with options.

```php
public static optgroup(
    array<string|int,string>|string $options, 
    bool $indexedKey = true, 
    string|array $selected = '', 
    string|array $disabled = '', 
    array<string,string> $attributes = []
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<string&#124;int,string>&#124;string** | An HTML of options or Array options as key-value pairs,<br />where the key is the option value and the value is the display text. |
| `$indexedKey` | **bool** | If true, use the original keys; if false, use values as keys on indexed option keys. |
| `$selected` | **string&#124;array** | The selected value(s). Can be a string for single select or an array for multiple select. |
| `$disabled` | **string&#124;array** | The disabled value(s). Can be a string for single select or an array for multiple select. |
| `$attributes` | **array<string,string>** | Additional HTML attributes for the `&lt;optgroup&gt;` element. |

**Return Value:**

`string` - Return the generated HTML `&lt;optgroup&gt;` element containing the option elements.

***

### select

Generate an HTML &lt;select&gt; dropdown element with optional optgroup support.

This method creates a &lt;select&gt; element with a name attribute, and can handle both regular options and grouped options using &lt;optgroup&gt;. Selected options can be pre-defined by providing their values in the $value parameter.
Additional HTML attributes can be supplied for the select element.

```php
public static select(
    string $name, 
    array<string|int,string|array<string|int,string>|string $options,
    array<int,string|int|float>|string $selected = '', 
	array<int,string|int|float>|string $disabled = '', 
    array<string,string> $attributes = [], 
    bool $indexedKey = true
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name attribute of the &lt;select&gt; element. |
| `$options` | **array<string&#124;int,string&#124;array<string&#124;int,string>>&#124;string** | An HTML string of options or an array of options,<br />where the key is the option value and the value is the display text, or an array with optgroup entries (e.g., `[['foo' => 'Foo', 'bar' => 'Bar'], ...]`). |
| `$selected` | **array<int,string&#124;int&#124;float>&#124;string** | The selected value(s). Can be a string for single select or an array for multiple select. |
| `$disabled` | **array<int,string&#124;int&#124;float>&#124;string** | The disabled value(s). Can be a string for single select or an array for multiple select. |
| `$attributes` | **array<string,string>** | Additional HTML attributes for the `&lt;select&gt;` element. |
| `$indexedKey` | **bool** | Whether to retain the option array index key as option value or use the text as the value (default: true). |

**Return Value:**

`string` - Return the generated HTML string for the &lt;select&gt; dropdown.

***

### datalist

Generate an HTML &lt;datalist&gt; dropdown element with searchable input support.

This method creates a &lt;select&gt; element with a name attribute, and can handle
both regular options and grouped options using &lt;optgroup&gt;. Selected options
can be pre-defined by providing their values in the $value parameter.
Additional HTML attributes can be supplied for the select element.

```php
public static datalist(string $name, array<int,string>|string $options, array<string,string> $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name attribute of the &lt;input&gt; element. |
| `$options` | **array<int,string>&#124;string** | An HTML string of options or an array of options values. |
| `$attributes` | **array<string,string>** | Additional HTML attributes for the `&lt;datalist&gt;` element. |

**Return Value:**

`string` - Return the generated HTML string for the &lt;datalist&gt; dropdown.

**See Also:**

* https://www.w3schools.com/html/tryit.asp?filename=tryhtml_elem_datalist