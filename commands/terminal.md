# Command Terminal

***

## Overview

NovaKit Terminal class seamlessly facilitates user prompts and manages input with built-in validation like a web form capabilities, it enhances the CLI experience for efficient workflow &amp; productivity

***

## Introduction

The Luminova Terminal class for NovaKit is a powerful utility designed to enhance the command-line interface (CLI) interaction for developers leveraging the Luminova framework. Designed to enhance CLI tool development and application operations within the framework, it offers an array of functionalities and utilities.

***

* Class namespace: `\Luminova\Command\Terminal`

***

### constructor

Initialize command line instance before running any commands.

```php
new Terminal()
```

***

## Methods

### waiting

Show a waiting countdown, intentionally freeze screen while waiting or ask user for a key press to continue.

```php
protected static waiting(int $seconds, bool $countdown = false, string $message='...'): void
```

Examples

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$seconds` | **int** | Number of seconds for waiting. |
| `$countdown` | **bool** | Show waiting countdown. |
| `$message` | **string** | Waiting message instruction. |

***

### progress

Displays a progress bar on the CLI.

```php
protected static progress(int|bool $progressLine = 1, int|null $progressCount = 10, bool $beep = true): float|int
```

> Progress should be called in a loop otherwise use `watcher()` method.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$progressLine` | **int&#124;bool** | Current loop index number or false to terminate the progress bar |
| `$progressCount` | **int&#124;null** | Total count of progress bar to show or null to on termination |
| `$beep` | **bool** | Beep when progress is completed, default is true |

***

### watcher

Displays a and watch progress bar on the CLI with a callback functions, this method shouldn't be called in a loop, you can pass your callable to execute in `$onProgress` callback function.

```php
protected static watcher(int $progressCount, ?Closure $onFinish = null, ?Closure $onProgress = null, bool $beep = true): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$progressCount` | **int** | Total count of progress bar to show |
| `$onFinish` | **?Closure** | Execute callback when progress finished |
| `$onProgress` | **?Closure** | Execute callback on each progress step |
| `$beep` | **bool** | Beep when progress is completed, default is true |

> This is useful when you just want to display a progress bar and execute next method when it finished

***

### execute

This method allows you to execute a system command.

```php
protected static execute(string $command): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The command to execute |

**Return Value** 

`mixed` - Returns the output of command as an array of lines, or STATUS_ERROR on failure

***

### beeps

Trigger beeps a certain number of times.

```php
protected static beeps(int $num = 1): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$num` | **int** | The number of times to beep |

***

### prompt

Prompt for user to input or select an option, pass options as an array `["YES", "NO]`.

```php
protected static prompt(string $message, array $options = [], string|null $validations = null, bool $silent = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | Prompt message |
| `$options` | **array** | Options to prompt selection, |
| `$validations` | **string&#124;null** | Validation rules |
| `$silent` | **bool** | Print validation failure message parameter is true (default: false) |

**Return Value:**

`string` - The user input

> You can make a colored options by use the array key for color name ["green" => "YES","red" => "NO"]

***

### chooser

Prompt for multi choice selection, display array index key as the option identifier to select.

```php
protected static chooser(string $text, array $options, bool $required = false): array&lt;string|int,mixed&gt;
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Display text description for your multiple options |
| `$options` | **array** | A list of options ['male' =&gt; 'Male', 'female' =&gt; 'Female] or ['male', 'female'] |
| `$required` | **bool** | Require user to choose any option else the first array will be return as default |

**Return Value:**

`array` - The selected array keys and values

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception)

> If you use associative array users will see index key instead.

***

### password

Prompts the user to enter a password, with options for retry attempts and a timeout.

```php
protected static password(string $message = 'Enter Password', int $retry = 3, int $timeout = 0): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prompt` | **string** | The message to display when prompting for the password.. |
| `$retry` | **int** | The number of retry attempts if the user provides an empty password (default: 3). |
| `$timeout` | **int** | The number of seconds to wait for user input before displaying an error message (default: 0). |

**Return Value:**

`string` - Return the entered password.

***

### timeout

Execute a callback function after a specified timeout when no input or output is received.

```php
public static timeout(Closure $callback, int $timeout = 0, mixed $stream = STDIN): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **Closure** | The callback function to execute on timeout. |
| `$timeout` | **int** | Timeout duration in seconds. If <= 0, callback is invoked immediately (default: 0). |
| `$stream` | **mixed** |Optional stream to monitor for activity (default: STDIN). |

**Return Value:**

`bool` - Returns true if the timeout occurred and callback was executed, otherwise false.

***

### wrap

Wrap it with padding left and width to a maximum

```php
public static wrap(string|null $string = null, int $max, int $leftPadding): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string&#124;null** | The text to wrap. |
| `$max` | **int** | The maximum width to use. |
| `$leftPadding` | **int** |The left padding to apply. |

**Return Value:**

`string` - Return wrapped text with padding left and width.

***

### card

Create a card text.

```php
public static card(string $text, int|null $padding = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text display in card. |
| `$padding` | **int&#124;null** | Optional maximum padding to use. |

**Return Value:**

`string` - Return beautiful card with text.

***

### getWidth

Attempts to determine the width of the viewable CLI window.

```php
protected static getWidth(int $default = 80): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$default` | **int** | Optional default width (default: 80) |

**Return Value:**

`int` - Width or fallback to default

***

### getHeight

Attempts to determine the height of the viewable CLI window.

```php
protected static getHeight(int $default = 24): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$default` | **int** | Optional default height (default: 24) |

**Return Value:**

`int` - Height or fallback to default

***

### input

Get user input from the shell, after requesting for user to type or select an option.

```php
protected static input(string|null $prompt = null, bool $useFopen = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prompt` | **string&#124;null** | You may specify a string to prompt the user after they have typed. |
| `$useFopen` | **bool** | Optional use file-read, this opens `STDIN` stream in read-only binary mode (default: false).<br/>This creates a new file resource. |

**Return Value:**

`string` - Return user inputed string.

***

### validate

Input validation on prompts

```php
protected static validate(string $value, array $rules): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **string** | The user Input value. |
| `$rules` | **array** | The validation rules, array key `input` value `ruels` (e.g. ['input' => 'required|email']). |

**Return Value:**

`bool` - Return true if validation succeeded, false if validation failed.

***

### visibility

Toggles the terminal visibility of user input.

```php
protected static visibility(bool $visibility = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$visibility` | **bool** | True to show input, False to hide input (default: true). |

**Return Value:**

`bool` - Return true if visibility toggling was successful, false otherwise.

***

### success

Display success text on CLI

```php
public static success(string $text, string|null $foreground = 'white', string|null $background = 'green'): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Error message |
| `$foreground` | **string&#124;null** | Foreground color name |
| `$background` | **string&#124;null** | Optional background color name |

***

### error

Display error message on CLI

```php
public static error(string $text, string|null $foreground = 'white', string|null $background = 'red'): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Error message |
| `$foreground` | **string&#124;null** | Foreground color name |
| `$background` | **string&#124;null** | Optional background color name |

***

### writeln

Print message to CLI in a new line.

```php
public static writeln(string $text = '', string|null $foreground = null, string|null $background = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Text to display |
| `$foreground` | **string&#124;null** | Optional foreground color name |
| `$background` | **string&#124;null** | Optional background color name |

***

### write

Print a message to CLI output without a newline.

```php
public static write(string $text = '', string|null $foreground = null, string|null $background = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Text to display |
| `$foreground` | **string&#124;null** | Optional foreground color name |
| `$background` | **string&#124;null** | Optional background color name |

***

### print

Echo / output a message to CLI.

```php
public static print(string $text, string|null $foreground = null, string|null $background = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | string to output |
| `$foreground` | **string&#124;null** | Optional foreground color name |
| `$background` | **string&#124;null** | Optional background color name |

### table

Prints a formatted table header abd rows to the console.

```php
table(array $headers, array $rows, ?string $headerColor = null, int $headerPadding = 1): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$headers` | **array<int,string>** | The headers for the table columns. |
| `$rows` | **array<string,string>** | The rows of data to display in the table. |
| `$headerColor` | **string&#124;null** | The table heading columns text color. |
| `$headerPadding` | **int** | Optional table heading columns padding. |

***

### fwrite

Write message to resource handler or output text if not in cli mode

```php
protected static fwrite(string $text, resource $handle = STDOUT): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | string to output or write |
| `$handle` | **resource** | resource handler |

***

### oops

The `oops` method can be use to display default error message "Unknown command "foo" not found".

```php
protected static oops(string $command, string|null $color = 'red'): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The executed command. |
| `$color` | **string&#124;null** | The command text color (default: red) |

**Return Value**

`int` - Return status code `STATUS_ERROR`.

***

### clear

Clears the CLI screen of output

```php
public static clear(): void
```

***

### flush

Clears CLI output to update new text.

```php
public static flush(): void
```

***

### color

Returns the given text with the correct color codes for a foreground and optionally a background color.

```php
public static color(string $text, string $foreground, string|null $background = null, int|null $format = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | Text to color |
| `$foreground` | **string** | Foreground color name |
| `$background` | **string&#124;null** | Optional background color name |
| `$format` | **int&#124;null** | Optionally apply text formatting. |

**Return Value:**

`string` - A colored text if color is supported

***

### newLine

Pring new lines to CLI

```php
public static newLine(int $count = 1): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$count` | **int** | Count of new lines to create |

***

### streamSupports

Checks whether the current stream resource supports or refers to a valid terminal type device.

```php
public static streamSupports(string $function, resource $resource): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$function` | **string** | Function name to check |
| `$resource` | **resource** | Resource to handle STDIN/STDOUT |

**Return Value:**

`bool` - if the stream resource is supported

***

### explain

Register command line queries to make available in using methods like `getOptions()`, `getArgument()` etc.

```php
public static explain(array $options): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array** | Command  arguments received in `CommandController -> run()` method |

***

### getArgument

Get command argument by index number

```php
public static getArgument(int $index): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **int** | Argument Index position |

**Return Value:**

`mixed` - Passed argument or null. 

***

### getArguments

Get command arguments

```php
public static getArguments(): array
```

**Return Value:**

`array` - Passed arguments. 

***

### getCommand

Get the passed command name.

```php
public static getCommand(): string|null
```

**Return Value:**

`string|&#124;null` - Command name or null. 

***

### getCaller

Get the full passed command, options and arguments as its original form.

```php
public static getCaller(): string|null
```

**Return Value:**

`string|&#124;null` - Command string or null. 

***

### getOption

Get options value from command arguments.
If option key is passed with an empty value true will be return otherwise the default value.

```php
public static getOption(string $key, string $default = false): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | Option key to retrieve. |
| `$default` | **string** | Default value to return (default: false). |

**Return Value:**

`mixed` - Return option value, true if empty value, otherwise default value.

***

### getAnyOption

Get options value from command arguments with an alias key to lookup if main key isn't found.
If option key is passed with an empty value true will be return otherwise the default value.

```php
public static getAnyOption(string $key, string $alias, mixed $default = false): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | Option key to retrieve. |
| `$alias` | **string** | Option key alias to retrieve. if main key is not found. |
| `$default` | **string** | Default value to return (default: false). |

**Return Value:**

`array` - Return option value, true if empty value, otherwise default value.

**Example**

Assume you have a command, such as `php index.php my-command --which=a`. If you want to also accept an alias of `--which` as `-w`, then `getAnyOption` is what you need.

```php
<?php
$which = $this->getAnyOption('which', 'w');
echo $which;
```

In this example, `getAnyOption('which', 'w')` checks for both `--which` and `-w`, allowing you to use either option in your command.

***

### getOptions

Returns array of command options and arguments.

```php
public static getOptions(): array
```

**Return Value:**

`array` - Return Options and arguments passed to cli as array.

***

### getQueries

Returns the raw array of requested query command structure.

```php
public static getQueries(): array
```

> This is a complied array of passed query which has been processed for final usage by Luminova framework.

**Return Value:**

`array` - Complied structured command queries.

***

### getQuery

Gets complied command queries by it's array key name, keys `(arguments, command, caller, options)`.

```php
public static getQuery(string $name): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | Query key name |

**Return Value:**

`mixed` - Query values or null if it doesn't exists.

***

### isColorSupported

Check if the your CLI terminal supports colors.
On windows you must specify the  stream resource (STDIN, STDOUT or STDERR).

```php
public static isColorSupported(resource $resource = STDOUT): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$resource` | **resource** | The  stream resource STDIN/STDOUT/STDERR, (default: STDOUT) |

**Return Value:**

`bool` - Return true if supported, false otherwise.

***

### isMacTerminal

Determine whether if the current terminal is mac os terminal.

```php
public static isMacTerminal(): bool
```

**Return Value:**

`bool` - Return true if mac terminal, false otherwise.

***

### isWindowsTerminal

Determine whether if the current terminal is windows os terminal by passing the stream resource.

```php
public static isWindowsTerminal(resource $resource): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$resource` | **resource** | STDIN/STDOUT |

**Return Value:**

`bool` - Return true if windows terminal, false otherwise.

***

### hasCommand

Checks whether framework has the requested command.

```php
public static hasCommand(string $command): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | Command name to check |

**Return Value:**

`bool` - Return true if command exist, false otherwise.

***

### header

Print NovaKit Command line header information

```php
public static header(): void
```

***

### isHelp

Check if command is help command.

```php
public static isHelp(string $command): boll
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | Command name to check |

**Return Value:**

`bool` - Return true if command is help, false otherwise.