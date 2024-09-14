# Formatting Command Outputs Using the Text Utils Module

***

## Overview

The command Text utils class is a helper class for Luminova Command. It provides a common useful method for output text processing to help while developing your CLI application.

***

## Introduction

Text Utils is one of the many powerful features offered by Luminova's Framework command-line interface. With this versatile class, you can enhance the appearance of your output text, taking your CLI tool to a new level of elegance and professionalism. Whether you're aiming to beautify your output for end users or simply improve readability during development, Text Utils provides functions to help you achieve your goals with ease. 

***

* Class namespace: `\Luminova\Command\Utils\Text`

***

## Constants

| Constant | Description | Type | Value |
|:---------|:------------|:-----|:------|
| `ANSI_RESET` | ANSI character reset flag. | int | 0 |
| `ANSI_BOLD` | ANSI character bold flag. | int | 1 |
| `ANSI_ITALIC` | ANSI character italic flag. | int | 3 |
| `ANSI_UNDERLINE` | ANSI character underline flag. | int | 4 |
| `ANSI_STRIKETHROUGH` | ANSI character strikethrough flag. | int | 9 |

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
| `$text` | **string** | The text string to pad. |
| `$length` | **int** | The maximum length of padding. |
| `$char` | **string** | The padding character to use (default: `' '`). |

**Return Value:**

`string` - Return the the text with left padded.

***

### padEnd

Pads string right

```php
public static padEnd(string $text, int $length, string $char = ' '): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text string to pad. |
| `$length` | **int** | The maximum length of padding. |
| `$char` | **string** | The padding character to use (default: `' '`). |

**Return Value:**

`string` - Return the the text with right padded.

***

### border

Create a border around text.

```php
public static border(string $text, ?int $padding = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The string to add border. |
| `$padding` | **int** | Additional optional padding to apply (default: null). |

**Return Value:**

`string` - Return text with border round.

***

### center

Create a centered text

```php
public static center(string $text, ?int $padding = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text string to center. |
| `$padding` | **int** | Additional optional padding to apply (default: null). |

**Return Value:**

`string` - Return centered text.

***

### padding

Pads string both left and right.

```php
public static padding(string $text, int $length, string $char = ' ', int $position = STR_PAD_BOTH): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text string to apply padding. |
| `$length` | **int** | The maximum length of padding. |
| `$char` | **string** | The padding character to use (default: `' '`). |
| `$pad` | **int** | The position of the text to apply padding (e.g, `STR_PAD_*`), (default: `STR_PAD_BOTH`). |

***

### fit

Pads string to fit same length.

```php
public static fit(string $text, int $max, int $extra = 2, int $indent = 0): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text string to apply fit length. |
| `$max` | **int** | The maximum padding to apply. |
| `$extra` | **int** | How many extra spaces to add at the end (default: 2) |
| `$indent` | **int** | An optional indent to apply (default: 0). |

***

### strlen

Get the length of characters in a string and ignore `ANSI` that was applied to the text.

```php
public static strlen(?string $string = null, string $encoding = 'UTF-8'): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The string to calculate it's length. |
| `$encoding` | **string** | Text encoding to use (default: `UTF-8`). |

**Return Value:**

`int` - Return the number of characters in the string.

 > It replace all ANSI color codes and styles with an empty string before calculating the string length.

***

### style

Apply `ANSI` style formatting to a text string.

```php
public static style(string $text, int|null $format = null, bool $formatted = true): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Text to apply style. |
| `$format` | **int&#124;null** | The style to apply constant code identifier to use (e.g, `Text::ANSI_*`). |
| `$formatted` | **bool** | Weather to return a text with applied style or just style code (default: true). |

**Return Value:**

`string` - Return style formatted string or ansi string.

***

### hasAnsi

Check if text already has `ANSI` method in place

```php
public static hasAnsi(string $text): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text string to check. |

**Return Value:**

`bool` - Return true if text contains ANSI, otherwise false.

***

### largest

Get the longest line from a text string that may contain various newline formats.

```php
public static largest(string $text): array<int,mixed>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Text to process. |

**Return Value:**

`array<int,mixed>` - Returns the longest line as the first element and its length as the second element.
