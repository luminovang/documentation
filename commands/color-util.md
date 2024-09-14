# Styling Command Text Outputs Using the Color Utils Module

***

## Overview

The Color Util class is a helper class for command line application for formatting and applying colors to your output text for vibrancy, it's useful when you want to display text with a background col

***

## Introduction

Introducing the Color Utils class, a valuable tool for command-line applications that adds a splash of vibrancy and clarity to your text output. With Color Utils, you can apply formatting and colors to your text, transforming ordinary command-line displays into visually stunning and highly readable experiences.

Whether you're highlighting important information, organizing output for better comprehension, or simply adding a touch of flair to your CLI tool, Color Utils offers the functionalities to meet your needs. Easily customize text and background colors to suit your preferences and enhance the visual appeal of your application.

***

* Class namespace: `\Luminova\Command\Utils\Color`

***

## ANSI Color Codes

### Foreground Colors

List of text foreground colors with their ANSI codes.

| Color Key     | Color Name     | ANSI Code |
|---------------|----------------|-----------|
| `black`       | Black          | `0;30`    |
| `darkGray`    | Dark Gray      | `1;30`    |
| `blue`        | Blue           | `0;34`    |
| `darkBlue`    | Dark Blue      | `0;34`    |
| `lightBlue`   | Light Blue     | `1;34`    |
| `green`       | Green          | `0;32`    |
| `lightGreen`  | Light Green    | `1;32`    |
| `cyan`        | Cyan           | `0;36`    |
| `lightCyan`   | Light Cyan     | `1;36`    |
| `red`         | Red            | `0;31`    |
| `lightRed`    | Light Red      | `1;31`    |
| `purple`      | Purple         | `0;35`    |
| `lightPurple` | Light Purple   | `1;35`    |
| `yellow`      | Yellow         | `0;33`    |
| `lightYellow` | Light Yellow   | `1;33`    |
| `lightGray`   | Light Gray     | `0;37`    |
| `white`       | White          | `1;37`    |

***

### Background Colors

List of text background colors with their ANSI codes.

| Color Key     | Color Name     | ANSI Code |
|---------------|----------------|-----------|
| `black`       | Black          | `40`      |
| `red`         | Red            | `41`      |
| `green`       | Green          | `42`      |
| `yellow`      | Yellow         | `43`      |
| `blue`        | Blue           | `44`      |
| `magenta`     | Magenta        | `45`      |
| `cyan`        | Cyan           | `46`      |
| `lightGray`   | Light Gray     | `47`      |

***

## Methods

### apply

Applies foreground and optional background colors to the given text.

```php
public static apply(string $text, int|null $format = null, string|null $foreground = null, string|null $background = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to color. |
| `$format` | **int&#124;null** | Optionally apply text formatting (e.g, `Text::ANSI_BOLD`). |
| `$foreground` | **string&#124;null** | The foreground color name. |
| `$background` | **string&#124;null** | Optional background color name. |

**Return Value:**

`string` - Return the formatted text with ANSI color codes, or the original text if unsupported.

***

### length

Calculates the length of the ANSI formatting codes applied to a given text. This method helps determine the exact length of ANSI sequences in a text, which can be useful when subtracting the ANSI codes from the overall text length.

```php
public static length(int|null $format = null, string|null $foreground = null, string|null $background = null): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$format` | **int&#124;null** | And optional text formatting to include (e.g., `Text::ANSI_*`). |
| `$foreground` | **string&#124;null** | An optional foreground color name to include (default: null). |
| `$background` | **string&#124;null** | An optional background color name to include (default: null). |

**Return Value:**

`int` - Return the total length of ANSI formatting codes that is included.

**Example**

Apply bold formatting with a red foreground and black background.

```php
<?php
$text = Text::apply('Hello, World!', Text::ANSI_BOLD, 'red', 'black');
$ansiLength = Text::length(Text::ANSI_BOLD, 'red', 'black');

echo $text; // Outputs: Hello, World! (with bold red text on a black background)
echo "\nANSI Length: " . $ansiLength; // Outputs: ANSI Length: <calculated length>
```
