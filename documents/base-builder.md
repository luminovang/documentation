# Document Builder Base Helper Class

***

## Overview

Generate an HTML document and calendar using the Luminova document builder module.

***

## Introduction

The Luminova **Document Builder** class is a foundational helper for both `\Luminova\Builder\XHTML` and `\Luminova\Builder\Inputs`, which extend it to inherit dynamic configuration capabilities. The Document class includes core utilities, such as a `calendar` method for generating HTML calendar UIs, along with other helper functions.

For full instructions on generating elements, see the [XML & XHTML Documentation](/documents/xhtml.md) and [Form Input Documentation](/documents/form-inputs.md).

***

## Class Definition

* Class namespace: `\Luminova\Builder\Document`

***

## Constants

Defines the types of HTML document templates.

| Constant     | Value | Description                      |
|:-------------|:------|:---------------------------------|
| `HTML5`      | 1     | Represents a standard HTML5 document template. |
| `BOOTSTRAP5` | 2     | Represents a Bootstrap 5 document template with pre-defined styles and components. |

***

## Properties

### docTypes

List of html document types.

```php
public static array<string,string> $docTypes = [
    'html5' => '<!DOCTYPE html>',
    //...
]
```

***

### template

The document element style to use in `Luminova\Builder\Xhtml` class (e.g, `Xhtml::HTML5`, `Xhtml::BOOTSTRAP5`).

```php
public static int $template = 1;
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

***

## Methods

### doctype

Retrieve a `<!DOCTYPE >` based on the name-type.

```php
public static doctype(string $type = 'html5'): ?string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The type of your document retrieve. |

**Return Value:**

`string|null` - Returns content doctype based on passed type.

***

### calendar

Generates a calendar for a specified month and year, highlighting important seasonal days.

This method constructs an HTML representation of a calendar for the given month and year.
It allows the specification of significant seasonal days that are highlighted in the calendar.
The calendar is adjustable based on the provided timezone and allows for customization of the starting month and day of the season.

```php
public static calendar( 
    ?string $year = null, 
    ?string $month = null,
    DateTimeZone|string|null $timezone = null,
    array $season_days = [],
    int $season_start_month = 1,
    int $season_start_day = 3
): string|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$year` | **string\|null** | The year for which the calendar is generated (default: `currentYear`). |
| `$month` | **string\|null** | The month for which the calendar is generated (default: `currentMonth`). |
| `$timezone` | **\DateTimeZone\|string\|null** | The timezone to be used for date calculations (default: `systemTimezone`). |
| `$season_days` | **array<int,string>** | An optional array of names representing significant days or events in the season.. |
| `$season_start_month` | **int** | The starting month of the season (1-12) (default: `January`). |
| `$season_start_day` | **int** | The starting day of the season (1-31) (default: `March`). |

**Return Value:**

`string|false` - Returns the generated HTML calendar as a string, or false if the calendar cannot be generated.

**Throws:**
[\Luminova\Exceptions\DateTimeException](/running/exceptions.md#datetimeexception) Throws if invalid calendar date format is provided.

**Example:**
```php
<?php
use Luminova\Builder\Document;

echo Document::calendar(2024, 04, `Africa/Lagos`);
```
**Output:**

![Calender Example](/assets/images/calender.png.md)

***

### attributes

Generate HTML attributes as a string and escape values while generating.

```php
public static attributes(array<string,string|int|float|bool> $attributes): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$attributes` | **array<string,string\|int\|float\|bool>** | An array of attributes as key-value pairs. |

**Return Value:**

`string|null` - Return the attributes as a string.

***

### esc

Escapes input for safe output in HTML, including handling numeric values directly.

```php
public static esc(string|int|float $value): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **string\|int\|float** | The value to be escaped.. |

**Return Value:**

`string` - Return the safely escaped string or numeric value.

***

### getAttrType

Escape and retrieve attribute value type.

```php
protected static getAttrType(mixed $value): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | The value of the attribute. |

**Return Value:**

`string` - Return the safely escaped string or numeric value.