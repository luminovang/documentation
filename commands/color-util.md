## Command Color Util

***

## Overview

The Color Util class is a helper class for command line application for formatting and applying colors to your output text for vibrancy, it&#039;s useful when you want to display text with a background col

***

Introducing the Color Utils class, a valuable tool for command-line applications that adds a splash of vibrancy and clarity to your text output. With Color Utils, you can apply formatting and colors to your text, transforming ordinary command-line displays into visually stunning and highly readable experiences.

Whether you're highlighting important information, organizing output for better comprehension, or simply adding a touch of flair to your CLI tool, Color Utils offers the functionalities to meet your needs. Easily customize text and background colors to suit your preferences and enhance the visual appeal of your application.


***


* Class namespace: `\Luminova\Command\Colors`



## Properties


### foregroundColors

Text foreground color list

```php
protected static array&lt;string,string&gt; $foregroundColors = [
    'black'        => '0;30',
    'darkGray'     => '1;30',
    'blue'         => '0;34',
    'darkBlue'     => '0;34',
    'lightBlue'    => '1;34',
    'green'        => '0;32',
    'lightGreen'   => '1;32',
    'cyan'         => '0;36',
    'lightCyan'    => '1;36',
    'red'          => '0;31',
    'lightRed'     => '1;31',
    'purple'       => '0;35',
    'lightPurple'  => '1;35',
    'yellow'       => '0;33',
    'lightYellow'  => '1;33',
    'lightGray'    => '0;37',
    'white'        => '1;37',
];
```


***

### backgroundColors

Text background color list.

```php
protected static array&lt;string,string&gt; $backgroundColors = [
    'black'      => '40',
    'red'        => '41',
    'green'      => '42',
    'yellow'     => '43',
    'blue'       => '44',
    'magenta'    => '45',
    'cyan'       => '46',
    'lightGray'  => '47',
];
```


***

## Methods


### apply

Returns the given text with the correct color codes for a foreground and optional background color.

```php
public static apply(string $text, int|null $format = null, string|null $foreground = null, string|null $background = null): string
```


**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Text to color |
| `$format` | **int&#124;null** |  Optionally apply text formatting (ex: TextUtils::ANSI_BOLD). |
| `$foreground` | **string&#124;null** | Foreground color name |
| `$background` | **string&#124;null** | Optional background color name |


**Return Value:**

`string` - A colored text if color is supported