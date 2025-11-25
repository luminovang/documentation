# Text Utils for Formatting Command Outputs

***

## Overview

The command Text utils class is a helper class for Luminova Command. It provides a common useful method for output text processing to help while developing your CLI application.

***

## Introduction

The Luminova **Text Utils** class is a powerful feature of the Luminova Framework's command-line tooling. It enhances the appearance of your output text, elevating your CLI tool with elegance and professionalism. Whether you want to beautify your output for end users or improve readability during development, Text Utils offers a range of functions to help you achieve your goals.

This class provides static methods for applying various text styles, including color, effects, and background colors, using ANSI codes for terminal output. You can style text similarly to how you would with `HTML` and `CSS`. Additionally, it provides a `block` layout method, allowing you to create structured designs similar to an HTML `DIV`. With this method, you can customize text positioning, add borders (including border color and radius), apply shadow effects, and set font colors and styles.

## Usage Examples 

An example string content.

```php
<?php
$string = sprintf("%s\n%s\n%s\n%s",
    'This is a Title',
    'This is the body of the console message. It contains a detailed explanation of long content content.',
    'Here is some additional medium-length content.',
    'Current Time: ' . date('Y-m-d H:i:s')
);
```

**Creating a Block Layout:**

In this example, we use the `Text::block` method to format the string within a styled block. 

```php
<?php
use Luminova\Command\Terminal;

$block = Text::block(
    $string, 
    Text::LEFT, 
    1, 
    'white',
    'green',
    Text::NO_FONT,
    Text::BORDER_THICKER | Text::BORDER_RADIUS,
    'red',
    'cyan'
);

Terminal::writeln($block);
```

**Output:**
```text
╭━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╮
┃                                                                                                      ┃
┃ This is a Title                                                                                      ┃
┃ This is the body of the console message. It contains a detailed explanation of long content content. ┃
┃ Here is some additional medium-length content.                                                       ┃
┃ Current Time: 2024-10-29 13:52:52                                                                    ┃
┃                                                                                                      ┃
╰━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╯
```

**Inline Text Formatting:**

This example demonstrates how to format the string in an inline style, aligning it to the right with specific text styles.

```php
<?php
$inline = Text::inline(
    $string,
    Text::RIGHT, 
    'white',
    'red',
    Text::FONT_UNDERLINE | Text::FONT_ITALIC
);

Terminal::writeln($block);
```

**Output:**
```text
                                                                                                                   This is a Title
                              This is the body of the console message. It contains a detailed explanation of long content content.
                                                                                    Here is some additional medium-length content.
                                                                                                 Current Time: 2024-10-29 13:37:29
```

**Plain Text Formatting:**

In this example, the `Text::plain` method is used to display the string content centered with underline and italic effects.

```php
$plain = Text::plain(
    $string,
    Text::CENTER,
    Text::FONT_UNDERLINE | Text::FONT_ITALIC
);
Terminal::writeln($plain);
```
**Output:**

```text
                                    This is a Title                                                                         
This is the body of the console message. It contains a detailed explanation of long content content.                                                         
					Here is some additional medium-length content.                                                                                     
			                Current Time: 2024-10-29 13:37:29                                                 
```

***

### Outputs Visual Representation

Here's how the output appears visually in a terminal:

![PHP Luminova Command Text Utils](/assets/images/text.png.md)

***

## Class Definition

* Class namespace: `Luminova\Command\Utils\Text`

***

## Constants

Each constant provides a specific visual or positional styling option to modify text appearance and layout dynamically.

| Constant               | Type | Value        | Description |
|------------------------|------|--------------|-------------|
| `ANSI_RESET`           | int  | `0b0`        | Resets all ANSI styling applied to text. |
| `NO_FONT`              | null | `0b100000000` | Removes any applied font styling, leaving the text unstyled. |
| `FONT_BOLD`            | int  | `0b1`        | Applies bold styling to the text. |
| `FONT_ITALIC`          | int  | `0b10`       | Applies italic styling to the text. |
| `FONT_UNDERLINE`       | int  | `0b100`      | Underlines the text for emphasis. |
| `FONT_STRIKETHROUGH`   | int  | `0b1000`     | Adds a strikethrough to indicate deleted or incorrect text. |
| `FONT_BLINK`           | int  | `0b10000`    | Causes text to blink to draw attention. |
| `FONT_INVERSE`         | int  | `0b100000`   | Swaps foreground and background colors. |
| `FONT_INVISIBLE`       | int  | `0b1000000`  | Renders the text invisible. |
| `FONT_DOUBLE_UNDERLINE`| int  | `0b10000000` | Adds a double underline for additional emphasis. |
| `RIGHT`                | int  | `0`          | Aligns text to the right. |
| `LEFT`                 | int  | `1`          | Aligns text to the left. |
| `CENTER`               | int  | `2`          | Centers text horizontally. |
| `UP`                   | int  | `3`          | Aligns text upwards. |
| `DOWN`                 | int  | `4`          | Aligns text downwards. |
| `BORDER`               | int  | `0b0`        | Adds a default border around the text. |
| `NO_BORDER`            | int  | `0b1011`     | Removes any applied border around the text. |
| `BORDER_RADIUS`        | int  | `0b1`        | Adds rounded corners to the text border. |
| `BORDER_THICKER`       | int  | `0b10`       | Makes the text border thicker for emphasis. | 

## Methods

***

### block

Create a block of text with optional padding, colors, and alignment.

```php
public static block(
  string $text, 
  int $position = self::LEFT, 
  int $padding = 0, 
  ?string $foreground = null, 
  ?string $background = null, 
  int $fonts = self::NO_FONT, 
  int $borders = self::NO_BORDER, 
  ?string $borderColor = null, 
  ?string $shadow = null
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to display. |
| `$position` | **int** | Text alignment (e.g., Text::LEFT, Text::RIGHT, Text::CENTER). |
| `$padding` | **int** | Optional padding around the text (default: 0). |
| `$foreground` | **string&#124;null** | Optional foreground color for the text (e.g, `white`, `cyan`). |
| `$background` | **?string** |  |
| `$fonts` | **int** | Optional font style(s) to apply, which can be combined using bitwise operators (e.g., Text::FONT_ITALIC). |
| `$borders` | **int** | The layout border style, supports combining using bitwise operator (e.g, `Text::BORDER_RADIUS`, `Text::BORDER_THICKER`). |
| `$borderColor` | **string&#124;null** | Optional layout border color (e.g, `white`, `cyan`). |
| `$shadow` | **string&#124;null** | Optional layout border shadow color (e.g, `white`, `cyan`). |

**Return Value:**

`string` - Return the formatted block layout, with text content.

***

### card

Create a card-like layout with optional formatting and alignment.

```php
public static card(
  string $text, 
  int $position = self::LEFT, 
  int $padding = 0, 
  ?string $foreground = null, 
  ?string $background = null, 
  int $fonts = self::NO_FONT, 
  int $borders = self::BORDER
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to display. |
| `$position` | **int** | Text alignment (e.g., Text::LEFT, Text::RIGHT, Text::CENTER). |
| `$padding` | **int** | Optional padding around the text (default: 0). |
| `$foreground` | **string&#124;null** | Optional foreground color for the text (e.g, `white`, `cyan`). |
| `$background` | **string&#124;null** | Optional background color for the text (e.g, `red`, `green`). |
| `$fonts` | **int** | Optional font style(s) to apply, which can be combined using bitwise operators (e.g., Text::FONT_ITALIC). |
| `$borders` | **int** | Optional layout border style, supports combining using bitwise operator (e.g, `Text::BORDER_RADIUS`, `Text::BORDER_THICKER`). |

**Return Value:**

`string` - Return the formatted card block, with text content.

***

### inline

Create an inline text with optional colors, and alignment.

```php
public static inline(
  string $text, 
  int $position = self::LEFT, 
  ?string $foreground = null, 
  ?string $background = null, 
  int $fonts = self::NO_FONT
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to display. |
| `$position` | **int** | Text alignment (e.g., Text::LEFT, Text::RIGHT, Text::CENTER). |
| `$foreground` | **string&#124;null** | Optional foreground color for the text (default: null). |
| `$background` | **string&#124;null** | Optional background color for the text (default: null). |
| `$fonts` | **int** | Optional font style(s) to apply, which can be combined using bitwise operators (e.g., Text::FONT_ITALIC). |

**Return Value:**

`string` - Return the formatted inline text block.

***

### plain

Create plain text with optional font styles and alignment.

```php
public static plain(string $text, int $position = self::LEFT, int $fonts = self::NO_FONT): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to be formatted. |
| `$position` | **int** | Text alignment (e.g., Text::LEFT, Text::RIGHT, Text::CENTER). |
| `$fonts` | **int** | Optional font style(s) to apply, which can be combined using bitwise operators (e.g., `Text::FONT_ITALIC &amp;#124; Text::FONT_BOLD`). |

**Return Value:**

`string` - Return the formatted plain text text with the applied alignment and optional font styles.

***

### marquee

Displays a scrolling marquee effect for the given text in the terminal.

The text scrolls based on the specified speed, direction, and repeat count,
with optional ANSI styling for foreground, background, and font.

```php
public static marquee(
  string $text, 
  int $speed, 
  int $direction = self::LEFT, 
  int $repeat = 1, 
  ?string $foreground = null, 
  ?string $background = null, 
  int $fonts = self::NO_FONT
): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to display as a marquee. |
| `$speed` | **int** | The speed of scrolling, with higher values resulting in slower scroll. |
| `$direction` | **int** | The direction of the scroll (e.g, `Text::LEFT`, `Text::RIGHT`, `Text::UP`, or `Text::DOWN`). |
| `$repeat` | **int** | The number of times the marquee scroll repeats. |
| `$foreground` | **string&#124;null** | Optional foreground color name for the text (e.g., 'red', 'blue'). |
| `$background` | **string&#124;null** | Optional background color name for the text (e.g., 'green', 'cyan'). |
| `$fonts` | **int** | Optional font style(s) for the text (e.g., `Text::FONT_BOLD`, `Text::FONT_ITALIC &amp;#124; Text::FONT_UNDERLINE`). |

***

### flash

Displays a flashing effect for the given text in the terminal for a specified duration.

The text alternates between visible and hidden at the defined speed, with optional
ANSI styling for foreground, background, and font.

```php
public static flash(
  string $text, 
  int $duration = 3, 
  int $speed = 500000, 
  ?string $foreground = null, 
  ?string $background = null, 
  int $fonts = self::NO_FONT
): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to flash. |
| `$duration` | **int** | The duration of the flashing effect in seconds. |
| `$speed` | **int** | The flashing speed in microseconds (higher values slow the flash). |
| `$foreground` | **string&#124;null** | Optional foreground color name for the text (e.g., 'red', 'blue'). |
| `$background` | **string&#124;null** | Optional background color name for the text (e.g., 'green', 'cyan'). |
| `$fonts` | **int** | Optional font style(s) for the text (e.g., `Text::FONT_BOLD`, `Text::FONT_ITALIC &amp;#124; Text::FONT_UNDERLINE`). |

***

### style

Applies specified ANSI font styles to the provided text.

This method allows for multiple font styles to be combined using bitwise operators,
such as `Text::FONT_BOLD | Text::FONT_ITALIC`. If no valid font style is specified
or if the text already contains ANSI codes, the original text is returned unmodified.

```php
public static style(string $text, int $fonts): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to style with ANSI font codes. |
| `$fonts` | **int** | Optional font style(s) to apply, which can be combined using bitwise operators (e.g., `Text::FONT_ITALIC`). |

**Return Value:**

`string` - Return the styled text with ANSI codes applied, or the original text if no valid styles are given.

***

### wrap

Wraps the given text to a specified maximum width, applying padding on all sides.

The text is wrapped based on the maximum width, and optional padding can be applied
to the left, right, top, and bottom of the text block.

```php
public static wrap(string $text, int $width, int $left, int $right, int $top, int $bottom): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to be wrapped and padded. |
| `$width` | **int** | The maximum width for each line of text after padding. |
| `$left` | **int** | The number of spaces to add as left padding. |
| `$right` | **int** | The number of spaces to add as right padding. |
| `$top` | **int** | The number of new lines to add above the text. |
| `$bottom` | **int** | The number of new lines to add below the text. |

**Return Value:**

`string` - Return the padded wrapped text.

***

### fit

Pads a string to fit a specified length by adding spaces.

This method takes a string and pads it with spaces to ensure that it reaches a
defined maximum length. It allows for additional space at the end of the string
and an optional indent at the beginning.

```php
public static fit(string $text, int $max, int $extra = 2, int $indent): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The string to pad. |
| `$max` | **int** | The maximum length to which the string should be padded. |
| `$extra` | **int** | The number of extra spaces to add at the end of the padded string (default: 2). |
| `$indent` | **int** | Optional indent to apply at the start of the string (default: 0). |

**Return Value:**

`string` - Returns the padded string, ensuring it meets the specified length.

***

### strlen

Calculate the character length of a string, ignoring any applied ANSI color or style codes.

This method removes any ANSI color codes from the text before calculating its length,
ensuring that only the visible characters are counted.

```php
public static strlen(string $string, string $encoding = 'UTF-8'): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The string to calculate its length. |
| `$encoding` | **string** | The text encoding to use (default: `UTF-8`). |

**Return Value:**

`int` - Return the number of visible characters in the string.

***

### length

Calculate the length of ANSI formatting codes applied to a given text.

This method returns the total length (in characters) of ANSI codes used to apply
color or style formatting, based on the specified fonts, foreground, and background.

```php
public static length(?int $fonts = null, ?string $foreground, ?string $background = null): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fonts` | **int&#124;null** | Optional text formatting (e.g., `Text::FONT_BOLD`). |
| `$foreground` | **string&#124;null** | Optional foreground color name (default: null). |
| `$background` | **string&#124;null** | Optional background color name (default: null). |

**Return Value:**

`int` - Return the total length of ANSI formatting characters included.

***

### height

Calculate the height of a string based on the number of lines (newline).

```php
public static height(string $text): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text whose height (line count) is calculated. |

**Return Value:**

`int` - Return the total number of lines in the text.

***

### largest

Finds the longest line from a multi-line text string, that may contain various newline formats.

```php
public static largest(string $text): array<int,mixed>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The input text to analyze. |

**Return Value:**

`array{0: string, 1: int}` - Returns an array containing the longest line.
       
- The longest line as the first element.
- Its length as the second element.

> **Note:** Each line is trimmed of leading and trailing whitespace before its length is calculated.

***

### lines

Splits text into an array of lines, normalizing different newline formats.

```php
public static lines(string $text): string[]
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The input text to be split into lines. |

**Return Value:**

`array<int,string>` - Returns an array of strings, where each string represents a line from the input text.

***

### line

Removes all line breaks (newlines) from the given text and replaces them with an optional specified string.

```php
public static line(string $text, string $replace = ' '): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The input text that may contain line breaks. |
| `$replace` | **string** | The string to replace line breaks with (default: ' '). |

**Return Value:**

`string` - Returns the processed string with all line breaks replaced by the specified string.
If the input text is empty, an empty string is returned.

***

### strip

Removes ANSI escape sequences from the provided text.

This method strips out any ANSI color or formatting codes from the given text,
leaving only the plain text content.

```php
public static strip(string $text): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The input text that may contain ANSI escape sequences. |

**Return Value:**

`string` - Return the text with all ANSI escape sequences removed.

***

### format

Format and align text with specified padding and alignment, accounting only for visible characters.

This method uses `sprintf` to align the text based on the specified alignment (left, right, center)
and pad it with a chosen character. ANSI codes are ignored in the padding calculation, so the length
is based on visible characters only.

```php
public static format(string $text, int $padding, int $alignment = self::LEFT, string $char = ' '): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The input text to be formatted. |
| `$padding` | **int** | The total width for the formatted text, including padding. |
| `$alignment` | **int** | Text alignment option (e.g., `Text::LEFT`, `Text::RIGHT`, `Text::CENTER`). |
| `$char` | **string** | The padding character to use (default: ' '). |

**Return Value:**

`string` - Return the formatted text with applied padding and alignment.

***

### padding

Pads a string on both the left and right sides based on alignment.

```php
public static padding(string $text, int $length, int $alignment = STR_PAD_BOTH, string $char = ' '): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The string to pad. |
| `$length` | **int** | The total length of the resulting string after padding. |
| `$alignment` | **int** | Padding alignment option (e.g., `STR_PAD_*` or `Text::LEFT`, `Text::RIGHT`, `Text::CENTER`). |
| `$char` | **string** | The padding character (default: space). |

**Return Value:**

`string` - Return the padded string.

***

### margin

Generates a margin with optional horizontal (left and right) padding and border character.

- If `$x` horizontal (left and right) padding is not provided, it will return a single line with the specified
  border and character on the right.
- If `$x` horizontal padding is specified, it will generate multiple lines with the horizontal padding
  between two border character, repeated for the given number of padding lines.

```php
public static margin(int $padding, string $char = ' ', ?int $horizontal = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$padding` | **int** | The number of lines to add as vertical padding (height of the margin). |
| `$char` | **string** | The margin character or string to place on the sides of the margin (default: `space`). |
| `$horizontal` | **int&#124;null** | Optional horizontal (left and right) margin, the number of spaces to add between border character as horizontal padding.<br />If null, no horizontal padding will be applied, only the vertical margin. |

**Return Value:**

`string` - Return the generated margin with vertical and optional horizontal padding.

***

### font

Retrieves the ANSI escape code for a specific font style.

```php
public static font(int $font): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$font` | **int** | The font style code to convert to an ANSI escape code (e.g., `Text::FONT_*`). |

**Return Value:**

`string` Return the ANSI escape sequence for the specified font style, or an empty string if the font is not recognized.

***

### fonts

Generates the ANSI escape sequence for a combination of font styles, or returns no styling if `Text::NO_FONT` is specified.

This method constructs a sequence of ANSI codes based on multiple font flags, allowing combined styles.

```php
public static fonts(?int $fonts): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fonts` | **int&#124;null** | A bitwise combination of font style constants (e.g., `Text::FONT_BOLD &amp;#124; Text::FONT_UNDERLINE`).<br />If null or `Text::NO_FONT` is specified, returns an empty string. |

**Return Value:**

`string` - Return the ANSI escape sequence representing the combined font styles, or an empty string if no style is applied.

***

### borders

Determines the border style flags based on the provided border type.

This can be a combination of border styles using bitwise operator,
such as `Text::BORDER_RADIUS` for rounded corners and `Text::BORDER_THICKER` for a thicker border.
Passing `Text::NO_BORDER` disables borders.

```php
public static borders(?int $borders): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$borders` | **int&#124;null** | The border style(s) to be applied (e.g, `Text::BORDER_RADIUS`, `Text::BORDER_THICKER`). |

**Return Value:**

`array{0: bool, 1: bool, 2: bool}` - Returns an array with three boolean values:

- **[0]**: `true` if any border is enabled, `false` if `NO_BORDER` is specified.
- **[1]**: `true` if the border has rounded corners (applies `BORDER_RADIUS`), otherwise `false`.
- **[2]**: `true` if the border is thicker (applies `BORDER_THICKER`), otherwise `false`.

***

### corners

Generates the appropriate box-drawing character for borders based on position,
corner rounding (radius), and line thickness.

```php
public static corners(string $position = 'topLeft', bool $radius = false, bool $thick = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$position` | **string** | The position of the box corner or edge (default: `topLeft`). |
| `$radius` | **bool** | Whether to use rounded corners (default: false). |
| `$thick` | **bool** | Whether to use thick borders (default: false). |

**Return Value:**

`string` - Return the character used to render the box at the given position with the specified style.

**Supported Positions Include:**

- **'topLeft'**: Top-left corner.
- **'topRight'**: Top-right corner.
- **'bottomLeft'**: Bottom-left corner.
- **'bottomRight'**: Bottom-right corner.
- **'horizontal'**: Horizontal edges.
- **'vertical'**: Vertical edges.
- **'crossings'**: Intersection or crossing of horizontal and vertical lines.
- **'leftConnector'**: Left T-connector.
- **'rightConnector'**: Right T-connector.
- **'topConnector'**: Top T-connector.
- **'bottomConnector'**: Bottom T-connector.
- **'topLeftAngle'**: Angled top-left corner.
- **'topRightAngle'**: Angled top-right corner.
- **'bottomLeftAngle'**: Angled bottom-left corner.
- **'bottomRightAngle'**: Angled bottom-right corner.
- **'plus'**: A plus sign, often used as a simple intersection.

***

### hasAnsi

Determine if the given text contains ANSI escape sequences.

```php
public static hasAnsi(string $text): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to check for ANSI codes. |

**Return Value:**

`bool` - Returns true if the text contains ANSI codes, otherwise false.

***

## Dynamic Static Magic Methods

The dynamic static methods allow you to apply fonts and background colors to text using any valid [foreground color name](/commands/color-util#lmv-docs-foreground-colors-text-colors-) as the method.

### colorName

Applies the specified color to the text and returns the styled text with ANSI code.

```php
public static {colorName}(
    string $text, 
    ?int $fonts = Text::NO_FONT, 
    ?string $background = null
): string
```

**Parameters:**

| Parameter   | Type            | Description                                          |
|-------------|-----------------|------------------------------------------------------|
| `$text`     | **string**      | The text to which the color will be applied.        |
| `$fonts`    | **int\|null**    | Optional font style (default: `Text::NO_FONT`).     |
| `$background`| **string\|null** | Optional background color (default: `null`).         |

**Return Value:**

`string` - Returns the styled text with the applied ANSI code.

***

### Examples

#### Applying Text Colors

Styles the text in various colors and returns the styled text with ANSI code.

```php
echo Text::red("Warning: This is an error message!");  // Highlight error messages in red.
echo Text::lightMagenta("This text is light magenta.");  // Output: Styled text in light magenta.
echo Text::white("This text is white.");  // Output: Styled text in white.
```

---

#### Applying Fonts and Background Colors

Styles the text with additional font styles and background colors.

```php
echo Text::red("Critical Error: System failure!", Text::FONT_BOLD | Text::FONT_ITALIC, "white"); 
// Red text with bold, italic fonts and white background.
```