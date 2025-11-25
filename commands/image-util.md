# Image to ASCII Art Generator for Command-Line

***

## Overview

The Command Image Util converts images into ASCII art with customizable character sets, color mapping, and resizing for terminal applications.

***

## Introduction

The Luminova command-line **Image** class converts images into ASCII art by mapping RGB pixel data to ASCII characters. It offers customizable ASCII character sets, color mapping, and resizing options for flexible, visually appealing outputs in terminal applications.

For best results, experiment with various ASCII character sets to achieve the desired appearance for each image. Setting the `$ascii_grayscale` parameter to `true` can improve clarity, especially for images containing text or fine details. This class is particularly useful for generating terminal-friendly art from images, as it returns the ASCII art as a string, providing a unique, text-based visual representation.

![Luminova ASCII Art Example](/assets/images/ascii.png.md)

***

### Usage Examples

#### Basic Image to ASCII Conversion

Convert an image to ASCII art using the default inverted ASCII character set.

```php
<?php
$image = Image::draw(
    root('/public/assets/images/') . 'photo.png',
    Image::ASCII_INVERTED
);

Terminal::writeln($image);
```

#### Custom ASCII Characters and Color Mapping

In this example, we specify a custom ASCII character set and map each character to a specific color, enhancing the visual effect.

```php
<?php
$ascii = '+#0 ';
$image = Image::draw(
    root('/public/assets/images/') . 'logo.png',
    $ascii,
    0, 0,
    false,
    Image::colors($ascii, ['blue', 'red', 'white', 'green'])
);

Terminal::writeln($image);
```

#### Resizing and Detailed Customization

This example demonstrates resizing the ASCII output and fine-tuning pixel configurations for optimized performance and  output lazy printing effect. We specify color options for added control over output styling.

```php
<?php
$image = Image::draw(
    root('/public/assets/images/') . 'photo.png',
    Image::ASCII_CLASSIC,
    200, 200,
    true,
    ['red', 'blue', 'yellow', 'magenta', 'cyan'],
    [
        'pixel' => 10000,
        'pixels' => 100000 
    ]
);

Terminal::writeln($image);
```

**Output Example:**

```ascii
%%%%%%%%%%%%%%%%%%%%%%%%%%#+-:::::-+*#######################
%%%%%%%%%%%%%%%%%%%%%%*=-:.          -######################
%%%%%%%%%%%%%%%%%%%%#-  ..           :######################
%%%%%%%%%%%%%%%%%%%%+   ..::-===:.   :######################
%%%%%%%%%%%%%%%%%%%%+   :-===+++=-:. .*#####################
%%%%%%%%%%%%%%%%%%%%#.  --=+++**+=-: .*#####################
%%%%%%%%%%%%%%%%%%%#: ..:-===---+==-.-######################
%%%%%%%%####%%%%%%%+   .-+**+++=++=- *######################
%%%%%%#########%%%%+  ..:=+==++--==..:*#####################
%%%%%############%#+   ..-===++=--..  .*############********
%%%################*    ----===--:..   :###########*********
##%%##############**:::-=-=++*++=--=---=*+*****##***********
################+=+**=----++**+++==--=**+=-=+***************
##############*-:---=+*+==+++**+*++*#+*=:-:::=**************
#############+:::--:-==*#**##****#*#++-:::::::-*####********
*******#####*=+=::--::--=**###%%##*=--:--:--=-==#####*******
*********##*==+===::-:-----**%%%%*=--:-::-=*+#**+***********
***********+=**+#+=------+#%#%#*##%#=--:-+**+**+=***********
***********-=+****+#*++=##%%*##*#**%#+-=+=++=-:--+**********
**********+::::-==*#*#*#%#%#*######%%#*+*=::-::--=+****++***
++++******-:::::::-**#*#**=***+***+==+++=+::::---=++++++++++
++++++++++=:::::::::-===####%##%%%%%%%#+=:::--=-=+++++++++++
+++++++++++=-----::::::+%%%%%%%%%%%%%%%-:-..:----+++++++++++
+++++++++++=-::::::-:::%%%%%%%%%%%%%%%%#:::..:----++++++++++
+++++++++++=-::::::...=%%%%%%%%%%%%%%%%%+::. :----++++++++++
++++++++++++=-:::::.::*%%%%%%%%%%%%%%%%%*::..-----++++++++++
+++++++++++++=-------:%%%%%%%%%%%%%%%%%%%-:::=++--++++++++++
+++++++++++++=::------=+*#%#########**+*+::----=+-++++++++++
++++++++++++=:::::---=+==----------::::::---=====-++++++++++
```

***

## Class Definition 

* Class namespace: `Luminova\Command\Utils\Image`

## Constants

Predefined characters suitable for ASCII art, each constant defines a specific set varying in density or pattern to create distinct visual textures.

| Constant                  | Type   | Description |
|---------------------------|---------|-------------|
| `ASCII_DENSE`             | string | A dense ASCII character set with symbols for detailed shading. |
| `ASCII_EXTENDED_DENSE`    | string | An extended, dense ASCII set with a wide range of characters for fine-grained texture. |
| `ASCII_SIMPLIFIED`        | string | A simplified ASCII set offering distinct, high-contrast shading. |
| `ASCII_INVERTED`          | string | An inverted ASCII set that transitions from light to dark. |
| `ASCII_BLOCKS`            | string | A block-based ASCII set for bold, heavy shading and block-style textures. |
| `ASCII_CLASSIC`           | string | A classic ASCII set with simple symbols for moderate shading. |
| `ASCII_BINARY`            | string | A binary ASCII set for creating binary-based textures or representations. |
| `ASCII_BINARY_CLASSIC`    | string | A binary set with a space prefix to add contrast. |
| `ASCII_BINARY_SIMPLIFIED` | string | A binary set with spaces for enhanced spacing and readability. |

***

## Methods

### draw

Converts an image to ASCII art with optional color mapping and lazy printing support. 

This method processes an image by converting its pixel data into a grayscale representation, mapping each pixel's brightness to a specified set of ASCII characters. It allows for optional resizing, and users can assign foreground colors to the ASCII characters for enhanced visual effects. Additionally, lazy printing can be applied to gradually output the ASCII art to the terminal, creating a dynamic or animated effect.

Supported image formats include `JPEG`, `PNG`, `GIF`, and `WEBP`.

**Lazy Prining Options:**

- If an integer is provided, it defines the delay (in microseconds) between each row of ASCII output (lazy row printing).
- If an array is provided, it can define both pixel and row delays.
- - Example: `['pixel' =>; 10000, 'pixels' =>; 100000]` where `pixel` controls the delay between individual pixels and `pixels` controls the delay between rows.

```php
public static draw(
    string $path, 
    string $ascii = self::ASCII_CLASSIC, 
    ?int $width = null, 
    ?int $height = null, 
	bool $ascii_grayscale = false,
    array<string|int,string> $colors = [], 
    array{pixel: int, pixels: int}|int|null $lazy_print = null
): ?string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The full file path to the image (supported formats: JPEG, PNG, GIF, WEBP). |
| `$ascii` | **string** | A string of ASCII characters used to represent different brightness levels<br />(default: `Image::ASCII_CLASSIC`). |
| `$width` | **int&#124;null** | Optional target width to resize the image (default: null). |
| `$height` | **int&#124;null** | Optional target height to resize the image (default: null). |
| `$ascii_grayscale` | **bool** | Whether to use a weighted grayscale for enhanced contrast based on the ASCII character representation (default: false). |
| `$colors` | **array<string&#124;int,string>** | Optional mapping of ASCII characters to foreground colors (e.g, `[' ' => 'red', '@' =>; 'blue', '#' => 'green']`). |
| `$lazy_print` | **array{pixel: int, pixels: int}&#124;int&#124;null** | Optional lazy printing effect configuration (default: null). |

**Return Value:**

`string|null` - Returns the generated ASCII art as a string if lazy printing is disabled.
If lazy printing is enabled, an `NULL` is returned as the output is streamed directly to the terminal with delays.

***

### colors

Maps characters from an ASCII string to corresponding colors.

This method takes an ASCII string and an array of colors, mapping each character in the ASCII string to a color from the array.

```php
public static colors(string $ascii, string[] $colors): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ascii` | **string** | A string of ASCII characters to be mapped. |
| `$colors` | **string[]** | A list array of colors to map to the ASCII characters. |

**Return Value:**

`array<string,string>` Return an associative array mapping ASCII characters to their corresponding colors.

**Example:**

This will return an associative array where each ASCII character is mapped to a corresponding color from the provided `$colors` array:

```php
<?php
$ascii = '+#0 ';
$colors = ['blue', 'red', 'white', 'green'];
$mapping = Image::colors($ascii, $colors);

print_r($mapping);
```

**Example Output:**

In this example, each character in `$ascii` (`+`, `#`, `0`, and a space) is mapped to a color from `$colors`. The result provides a color-coded set of ASCII characters, useful for creating colorful ASCII art.

```php
Array
(
    [ + ] => blue
    [ # ] => red
    [ 0 ] => white
    [   ] => green
)
```