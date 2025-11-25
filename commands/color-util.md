# Styling Command Outputs Using Color Utils

***

## Overview

The Color Util class is a helper class for command line application for formatting and applying colors to your output text for vibrancy, it's useful when you want to display text with a background col

***

## Introduction

Introducing the Color Utils class, a valuable tool for command-line applications that adds a splash of vibrancy and clarity to your text output. With Color Utils, you can apply formatting and colors to your text, transforming ordinary command-line displays into visually stunning and highly readable experiences.

Whether you're highlighting important information, organizing output for better comprehension, or simply adding a touch of flair to your CLI tool, Color Utils offers the functionalities to meet your needs. Easily customize text and background colors to suit your preferences and enhance the visual appeal of your application.

***

## Class Definition

* Class namespace: `Luminova\Command\Utils\Color`

***

## ANSI Color Codes

### Foreground Colors (Text Colors)

List of text foreground colors with their ANSI codes.

| Color Key     | Color Name     | ANSI Code |
|---------------|----------------|-----------|
| `black`       | Black          | `0;30`    |
| `darkGray`    | Dark Gray      | `1;30`    |
| `red`         | Red            | `0;31`    |
| `lightRed`    | Light Red      | `1;31`    |
| `darkRed`     | Dark Red       | `2;31`    |
| `green`       | Green          | `0;32`    |
| `lightGreen`  | Light Green    | `1;32`    |
| `darkGreen`   | Dark Green     | `2;32`    |
| `yellow`      | Yellow         | `0;33`    |
| `lightYellow` | Light Yellow   | `1;33`    |
| `darkYellow`  | Dark Yellow    | `2;33`    |
| `blue`        | Blue           | `0;34`    |
| `lightBlue`   | Light Blue     | `1;34`    |
| `magenta`     | Magenta        | `0;35`    |
| `lightMagenta`| Light Magenta  | `1;35`    |
| `cyan`        | Cyan           | `0;36`    |
| `lightCyan`   | Light Cyan     | `1;36`    |
| `lightGray`   | Light Gray     | `0;37`    |
| `white`       | White          | `1;37`    |
| `brightBlack` | Bright Black    | `90`      |
| `brightRed`   | Bright Red      | `91`      |
| `brightGreen` | Bright Green    | `92`      |
| `brightYellow`| Bright Yellow   | `93`      |
| `brightBlue`  | Bright Blue     | `94`      |
| `brightMagenta`| Bright Magenta | `95`      |
| `brightCyan`  | Bright Cyan     | `96`      |
| `brightWhite` | Bright White    | `97`      |

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
| `darkGray`    | Dark Gray      | `100`     |
| `lightRed`    | Light Red      | `101`     |
| `lightGreen`  | Light Green    | `102`     |
| `lightYellow` | Light Yellow   | `103`     |
| `lightBlue`   | Light Blue     | `104`     |
| `lightMagenta`| Light Magenta  | `105`     |
| `lightCyan`   | Light Cyan     | `106`     |
| `white`       | White          | `107`     |

***

## Methods

### get

Retrieves the ANSI code for a given color name based on the specified type.

```php
public static get(string $color, string $type = 'fgc'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$color` | **string** | The name of the color to retrieve (e.g., 'red', 'green'). |
| `$type` | **string** | The type of color, either 'fgc' for foreground or 'bgc' for background (default is 'fgc'). |

**Return Value:**

`string` Return the ANSI code for the requested color, or an empty string if the color is not defined.

**Example usage:**

Returns the ANSI code for foreground red `('0;31')`.
```php
<?php
echo Color::get('red', 'fgc')  
```

Returns the ANSI code for background blue `('44')`.
```php
<?php
echo Color::get('blue', 'bgc') 
```

***

### foreground

Retrieves the ANSI code for a foreground (text) color.

This method looks up the ANSI code for the specified foreground color from the predefined `$foregrounds` array.

```php
public static foreground(string $name): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the foreground color (e.g., 'red', 'green'). |

**Return Value:**

`string` - Return the ANSI code for the specified foreground color, or an empty string if not found.

***

### background

Retrieves the ANSI code for a background color.

This method looks up the ANSI code for the specified background color from the predefined `$backgrounds` array.

```php
public static background(string $name): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the background color (e.g., 'blue', 'yellow'). |

**Return Value:**

`string` - Return the ANSI code for the specified background color, or an empty string if not found.

***

### style

Styles the provided text with optional foreground or background colors using ANSI formatting.

This method applies `ANSI` color codes to the provided text using the specified
foreground and background colors. If the text already contains ANSI codes, it applies the colors regardless.

```php
public static style(string $text, ?string $foreground, ?string $background = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to be styled. |
| `$foreground` | **string&#124;null** | Optional foreground color name (e.g., 'red', 'green'). |
| `$background` | **string&#124;null** | Optional background color name (e.g., 'blue', 'yellow'). |

**Return Value:**

`string` - Return the styled text with ANSI color codes applied, or the original text if no valid colors are given.

**Example usage:**

Styles the text with red foreground and blue background.
```php
echo Color::style('Hello', 'red', 'blue')   
```

Styles the text with green foreground.
```php
echo Color::style('Hello', 'green')        
```

***

### apply

Applies optional font styles, foreground or optional background colors to the given text
This method will return the text unchanged if it already contains ANSI codes or if no formatting options are provided.

```php
public static apply(
    string $text, 
    ?int $font, 
    ?string $foreground = null, 
    ?string $background = null
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to color. |
| `$font` | **int&#124;null** | Optional text font style formatting (e.g., Text::FONT_BOLD). |
| `$foreground` | **string&#124;null** | The foreground color name. |
| `$background` | **string&#124;null** | Optional background color name. |

**Return Value:**

`string` - Return the formatted text with ANSI color codes, or the original text if unsupported.

**Example Usage:**

Apply bold formatting with a red foreground and black background.

```php
<?php
use Luminova\Command\Utils\Text;

echo Text::apply('Hello, World!', Text::FONT_BOLD | Text::FONT_ITALIC, 'red', 'black');
```

***

### has

Validates if the specified color name exists in the given color mapping array.

```php
public static has(?string $color, string $type = 'fg'): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$color` | **string&#124;null** | The color name to validate. |
| `$type` | **string** | The mapping of color names to ANSI codes (e.g, `fg`, `bg`). |

**Return Value:**

`bool` - Return true if the color exists, otherwise false.

***

## Dynamic Static Magic Methods

With methods like `fg{ColorName}()`, `bg{ColorName}()` `bgc{ColorName}()` and `bgc{ColorName}()`, you can replace `{ColorName}` with a specific color name, such as `red`, `lightYellow` etc... This approach is helpful for dynamically handling colors in code.

### fgColorName

Returns the specified foreground (text) color name if it exists within the foreground color mapping.

```php
public static fg{ColorName}(): ?string
```

**Return Value:**

`string|null` - Returns the foreground color name if it exists, otherwise `null`.

**Example:**

To set the foreground color to red, call the `fgRed()` method. This returns the color name "red" if it's valid.

```php
echo Color::fgRed();  // Output: "Text color: red"
```

***

### fgcColorName

Returns the ANSI code for the specified foreground color if it is defined.

```php
public static fgc{ColorName}(): ?string
```

**Return Value:**

`string|null` - Returns the ANSI code for the foreground color if it exists, otherwise `null`.

**Example:**

To apply ANSI codes for foreground colors, you can use methods like `fgcGreen()` for green text or `fgcWhite()` for bright white text.

```php
echo "\e[" . Color::fgcGreen() . "mThis text is green\e[0m";  // Applies green color to text
```

***

### bgColorName

Returns the specified background color name if it exists within the background color mapping.

```php
public static bg{ColorName}(): ?string
```

**Return Value:**

`string|null` - Returns the background color name if it exists, otherwise `null`.

**Example:**

To set the background color to cyan, call the `bgCyan()` method. This returns the color name "cyan" if it’s a valid background color.

```php
echo Color::bgCyan();  // Output: "Background color: cyan"
```

***

### bgcColorName

Returns the specified background color name if it exists within the background color mapping.

```php
public static bgc{ColorName}(): ?string
```

**Return Value:**

`string|null` - Returns the ANSI code for the background color if it exists, otherwise `null`.

**Example:**

To apply ANSI codes for background colors, you can use methods like `bgcRed()` for red background text.

```php
echo "\e[" . Color::bgcRed() . "mThis text has a red background\e[0m";
```