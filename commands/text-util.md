# Command Text Util

***

## Overview

The command Text utils class is a helper class for Luminova Command. It provides a common useful method for output text processing to help while developing your CLI application.

***

## Introduction

Text Utils is one of the many powerful features offered by Luminova's Framework command-line interface. With this versatile class, you can enhance the appearance of your output text, taking your CLI tool to a new level of elegance and professionalism. Whether you're aiming to beautify your output for end users or simply improve readability during development, Text Utils provides functions to help you achieve your goals with ease. 

***

* Class namespace: `\Luminova\Command\TextUtils`

## Constants

| Constant | Visibility | Type | Value |
|:---------|:-----------|:-----|:------|
|`ANSI_RESET`|public|int|0|
|`ANSI_BOLD`|public|int|1|
|`ANSI_ITALIC`|public|int|3|
|`ANSI_UNDERLINE`|public|int|4|
|`ANSI_STRIKETHROUGH`|public|int|9|

***

## Methods

### padStart

Pads string left

```php
public static padStart(string $text, int $length, string $char = ' '): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | string to pad |
| `$length` | **int** |  |
| `$char` | **string** | Padding character |

***

### padEnd

Pads string right

```php
public static padEnd(string $text, int $max, string $char = ' '): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | string to pad |
| `$max` | **int** | maximum padding |
| `$char` | **string** | Padding character |

***

### border

Create a border around text.

```php
public static border(string $text, ?int $padding = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | string to pad |
| `$padding` | **?int** |  |

**Return Value:**

`string` - Return text with border round.

***

### center

Create a centered text

```php
public static center(string $text, int|null $padding = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | string to pad |
| `$padding` | **int&#124;null** | maximum padding |

**Return Value:**

`string` - Return centered text.

***

### padding

Pads string both left and right.

```php
public static padding(string $text, int $max, string $char = ' ', int $padd = STR_PAD_BOTH): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | string to pad |
| `$max` | **int** | maximum padding |
| `$char` | **string** | Padding character |
| `$padd` | **int** | Padding location default is both left and right |

***

### fit

Pads string to fit same length

```php
public static fit(string $text, int $max, int $extra = 2, int $indent): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | string to pad |
| `$max` | **int** | maximum padding |
| `$extra` | **int** | How many extra spaces to add at the end |
| `$indent` | **int** |  |

***

### strlen

Get the length of characters in a string and ignore styles

```php
public static strlen(string $string = null, string $encoding = 'UTF-8'): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | Optional string |
| `$encoding` | **string** | Text encoding |

**Return Value:**

`int` - The number of characters in the string

***

### style

Apply style format on text string

```php
public static style(string $text, int|null $format = null, bool $formatted = true): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Text to style |
| `$format` | **int&#124;null** | Style to apply text. |
| `$formatted` | **bool** | Return a formatted string or ansi style code |

**Return Value:**

`string` - A style formatted ansi string

***

### hasAnsi

Check if text already has ANSI method in place

```php
public static hasAnsi(string $text): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Text string |

**Return Value:**

`bool` - true or false

***

### largest

Get the largest line from text.

```php
public static largest(string $text): array&lt;int,mixed&gt;
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Text to process. |

**Return Value:**

`array&lt;int,mixed&gt;` - Return largest line from text and it length.