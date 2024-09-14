# Command Execution and Formatting with the Terminal Module

***

## Overview

NovaKit Terminal class seamlessly facilitates user prompts and manages input with built-in validation like a web form capabilities, it enhances the CLI experience for efficient workflow &amp; productivity

***

## Introduction

The Luminova `Terminal` class is a command utility helper designed to facilitate command operations for `NovaKit`, `BaseCommand`, and `BaseConsole`. While it can be initialized and used independently, most of its methods are defined statically for easy access. However, not all methods can be executed correctly without proper constructor initialization.

This class offers a powerful set of methods that enhance command-line interactions for developers utilizing the Luminova framework. Key features include:

- **Table Generation**: Create and display formatted tables.
- **User Input Prompts**: Easily prompt users for input, including secure password entries.
- **Write Operations**: Read from and write in command line tool using various methods.
- **Command Argument Extraction**: Efficiently extract command-line arguments.
- **Command Execution**: Execute commands directly within the terminal.
- **Output Formatting and Styling**: Format and style command text output for improved readability.

The `Terminal` class is inherited by the `BaseCommand` class, making its methods available for use within your controller classes, either statically or through the `this` keyword.

***

* Class namespace: `\Luminova\Command\Terminal`

***

## Basic Usage Examples

### Using the Terminal Class in Command Controllers

When calling methods from the `Terminal` class within your command controller, use `$this` or `self`. This approach assumes that the `Terminal` instance is initialized within the command controller.

For example, avoid the following within the command controller class:

```php
<?php
$terminal = new Terminal();
```

Instead, use:

```php
<?php
$this->methodName(); // or self::methodName();
```

***

### Table Display

Display a simple table with names and emails:

```php
<?php
$this->table(
     ['Name', 'Email'], 
     [
          ['Name' => 'Peter', 'Email' => 'peter@example.com'],
          ['Name' => 'Hana', 'Email' => 'hana@example.com']
     ]
);
```

**Output:**

```bash
+-------+-------------------+
| Name  | Email             |
+-------+-------------------+
| Peter | peter@example.com |
| Hana  | hana@example.com  |
+-------+-------------------+
```

***

### Multiple Chooser Prompts

Ask the user to choose an option or default to the first element:

```php
<?php
$array = $this->chooser(
    'Choose your programming languages?', 
    ['PHP', 'JAVA', 'SWIFT', 'JS', 'SQL', 'CSS', 'HTML']
);
```

Require the user to choose at least one option is required:

```php
<?php
$array = $this->chooser(
    'Choose your programming languages?', 
    ['PHP', 'JAVA', 'SWIFT', 'JS', 'SQL', 'CSS', 'HTML'], 
    true
);
``` 

***

### User Prompts

Prompt the user with color-coded options and is required:

```php
<?php
$color = $this->prompt(
    'Are you sure you want to continue?', 
    [
        'green' => 'YES', 
        'red' => 'NO'
    ]
);
```

Prompt the user to select their gender, without colored text nor validation required:

```php
<?php
$gender = $this->prompt(
    'What is your gender?', 
    ['male', 'female'], 
    false
);
```

Prompt the user to select an option with custom validation rules:

```php
<?php
$email = $this->prompt(
    'Are you sure you want to continue?', 
    ['YES', 'NO'], 
    'required|in_array(YES,NO)'
);
```
***

### User Input Field 

Request user to enter an input.

```php
$name = $this->input('What is your name? ');
echo $name;
```

Request user to enter password without displaying while typing:

```php
$password = $this->password('Enter your password? ');
echo $password;
```

Using prompt to request the user to enter their an input:

```php
<?php
$email = $this->prompt('What is your email?');
echo $email;
```

***

### Waiting Examples

Display a waiting message until the user presses any key:

```php
<?php
$this->waiting();
```

Show a waiting message for 20 seconds with a countdown:

```php
<?php
$this->waiting(20);
```

Show a custom downloading message for 20 seconds:

```php
<?php
$this->waiting(20, 'Downloading... (%d seconds)');
```

***

### Freezing Examples

Freeze the console screen for 20 seconds:

```php
<?php
$this->freeze(20); 
```

Freeze the console and clear all output and user input for 20 seconds:

```php
<?php
$this->freeze(20, true);
```

***

### Progress Bar Examples

Show a progress bar with step updates:

```php
<?php
for ($i = 0; $i <= 100; $i++) {
    $step = $this->progress($i, 100);
    usleep(100000);
    
    if ($step >= 100) {
        echo 'Am done';
        break;
    }
}
```

Display a progress bar up to 50% without completing:

```php
<?php
for ($i = 0; $i <= 100; $i++) {
    $step = $this->progress($i, 100);
    usleep(100000);
    
    if ($step >= 50) {
        echo 'Am done halfway';
        break;
    }
}
```

***

### Watcher Examples

Show 100 lines of progress with callbacks and a beep upon completion:

```php
<?php
$this->watcher(100, function() {
    $this->writeln("Am done here");
}, function(float|int $step) {
    // Avoid adding a new line here to prevent progress bars from misaligning except is intended
    $this->write(" Current {$step}");
});
```
**Example Output:**

<div class="command-body">
	<div class="command-container">
					<span class="command-line"><span class="command-text">index.php foo --watch</span></span>
					<span class="command-status">[##########]</span> 100% Current 99
					<span class="d-block">Current 100</span>
					<span class="d-block">Am done here</span>
	 </div>
 </div>
 
***

### Animation Example

Show a spinning animation with 5 spins, a 100ms sleep between frames, and a custom completion message:

```php
<?php
$this->spinner(['-', '\\', '|', '/'], 5, 100000, function() {
    $this->write("Finished spinning!\n");
});
```

**Example Output:**

<div class="command-body">
	<div class="command-container">
					<span class="command-line"><span class="command-text">index.php foo --spin</span></span>
					<span class="command-status">Animating:</span> <span class="spinner"></span>
	 </div>
 </div>

***

## Methods

### constructor

Initialize command line instance before running any commands.

```php
public __construct(): mixed
```

***

### waiting

Displays a countdown timer in the console, with a custom message pattern, or prompts the user to press any key to continue.

```php
final public static waiting(int $seconds, string $pattern = 'Waiting...(%d seconds)'): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$seconds` | **int** | The number of seconds to wait before continuing (default: 0 seconds). |
| `$pattern` | **string** | A custom message pattern to display during the waiting countdown (default: `Waiting...(%d seconds)`).<br />Use `%d` as a placeholder for the remaining seconds. |

**Return Value:**

> **Note:**
> During count down, the CLI screen will be wiped out of any output.
> If number of seconds is less than 1, it will prompt message `Press any key to continue...`.

***

### freeze

Freeze and pause execution for a specified number of seconds, optionally clear the screen and the user input during the freeze.

```php
final public static freeze(int $seconds = 10, bool $clear = true): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$seconds` | **int** | The number of seconds to freeze execution (default: 10). |
| `$clear` | **bool** | Weather to clear the console screen and input while freezing (default: false). |

***

### spinner

Displays a rotating spinner animation in the CLI.

```php
final public static spinner(array $spinners = [...], int $spins = 10, int $sleep = 100000, \Closure|bool|null $onComplete = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$spinners` | **array** | An array of characters representing each frame of the spinner animation, (default: `Array('-', '\', '|', '/')`). |
| `$spins` | **int** | The number of full rotations (i.e., cycles through the spinner array) to display (default: 10). |
| `$sleep` | **int** | The delay in microseconds between each frame of the spinner animation to control animation speed, default is `100,000 (0.1 seconds)`. |
| `$onComplete` | **\Closure&#124;bool&#124;null** | A callback or boolean indicating what should happen after the spinner finishes (default: null).<br />If true, it outputs &quot;Done!\n&quot;. If a Closure is provided, it will be executed. |

***

### progress

Displays a progress bar in the console for a given number of steps.

```php
final public static progress(int|false $step = 1, int $steps = 10, bool $beep = true): float|int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$step` | **int&#124;false** | The current step in the progress, set to false to indicate completion. |
| `$steps` | **int** | The total number of steps for the progress. |
| `$beep` | **bool** | Whether to beep when the progress is complete (default: true). |

**Return Value:**

`float|int` - Return the percentage of completion between (0-100) or 100 if completed.

> **Note:** Progress should be called in a loop otherwise use `watcher` method.

***

### watcher

Displays a progress bar on the console and executes optional callbacks at each step and upon completion.

```php
final public static watcher(int $limit, \Closure|null $onFinish = null, \Closure|null $onProgress = null, bool $beep = true): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$limit` | **int** | The total number of progress steps to display. |
| `$onFinish` | **\Closure&#124;null** | A callback to execute when the progress reaches 100% (default: null). |
| `$onProgress` | **\Closure&#124;null** | A callback to execute at each progress step (default: null). |
| `$beep` | **bool** | Indicates whether to emit a beep sound upon completion. Defaults to true. |

**Progress Callback Signature:**

Receiving the current progress percentage using `$onProgress` Closure.

```php
function(float|int $step): void {
    // Your implementation here
}
```

> This method is designed to be called without a loop, as it handles the iteration internally.
> It is useful for showing progress while performing a task and executing subsequent actions when complete.

***

### beeps

Beep or make a bell sound for a certain number of time.

```php
final public static beeps(int $total = 1): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$total` | **int** | The total number of time to beep. |

***

### prompt

Prompt user to type something, optionally pass an array of options for user to enter any.
Additionally, you can make a colored options by using the array key for color name (e.g,`['green' => 'YES', 'red' => 'NO']`).

```php
final public static prompt(string $message, array $options = [], string|false|null $validations = null, bool $silent = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The message to prompt. |
| `$options` | **array** | Optional array options to prompt for selection. |
| `$validations` | **string&#124;false&#124;null** | Optional validation rules to ensure only the listed options are allowed (default: null), resulting to validation with rules as `required\|in_array(foo,bar,baz)`.<br />To disable validation pass `false` as the value. |
| `$silent` | **bool** | Weather to print validation failure message if wrong option was selected (default: false). |

**Return Value:**

`string` - Return the client input value.

**See Also:**

* Read the user input validation module [documentation here](/security/validation.md) for more details.

***

### chooser

Request user with multiple option selection.
This method will use the array index key as the option identifier to select, even if the array is an associative array users will still see indexed key instead.

```php
final public static chooser(string $text, array $options, bool $required = false): array<string|int,mixed>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The chooser description message to prompt. |
| `$options` | **array** | The list of options to prompt (e.g, `['male' => 'Male', 'female' => 'Female']` or `['male', 'female']`). |
| `$required` | **bool** | Require user to choose any option else the first array will be return as default. |

**Return Value:**

`array<string|int,mixed>` Return the client selected option array key and values.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throw if options is not specified or an empty array.

***

### password

Prompts the user to enter a password, with options for retry attempts and a timeout.

```php
final public static password(string $message = 'Enter Password', int $retry = 3, int $timeout = 0): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The message to display when prompting for the password. |
| `$retry` | **int** | The number of retry attempts if the user provides an empty password (default: 3). |
| `$timeout` | **int** | The number of seconds to wait for user input before displaying an error message (default: 0). |

**Return Value:**

`string` - Return the client inputted password.

***

### terminate

Terminates the script execution with an optional message and status code.

This method provides a consistent way to end script execution, optionally printing a message before termination. The script exits with the provided status code, which defaults to a success code. If a non-success status code is provided, the script will still terminate, but the status code can be used to indicate an error or abnormal termination to the system.

```php
final public static terminate(?string $message = null, int $statusCode = STATUS_SUCCESS): never
```

**Parameters:**

| Parameter     | Type             | Description                                |
|---------------|------------------|--------------------------------------------|
| `$message`    | `string|null`    | Optional message to display before termination (default: null).              |
| `$statusCode` | `int`            | An exit status code to terminate the script with (default: `STATUS_SUCCESS`). |

***

### timeout

Execute a callback function after a specified timeout when no input or output is received.

```php
final public static timeout(\Closure $callback, int $timeout, mixed $stream = STDIN): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **\Closure** | The callback function to execute on timeout. |
| `$timeout` | **int** | Timeout duration in seconds. If &lt;= 0, callback is invoked immediately (default: 0). |
| `$stream` | **mixed** | Optional stream to monitor for activity (default: `STDIN`). |

**Return Value:**

`bool` - Returns true if the timeout occurred and callback was executed, otherwise false.

***

### execute

Execute a system command.

```php
final public execute(string $command): array|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The command to execute. |

**Return Value:**

`array|false` - Return the output of executed command as an array of lines, or false on failure.

***

### _exec

Executes a command via `exec` and redirect error output to null based on the platform (Windows or Unix-like).

```php
public static _exec(string $command, array& $output = [], int &$result_code = STATUS_ERROR): string|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The command to execute. |
| `$output` | **array** | Output lines of the executed command (default: `[]`). |
| `$result_code` | **int** | The exit status of the executed command, passed by reference (default: `STATUS_ERROR`). |

**Return Value:**

`string|false` - Return the last line of the command output, or false on failure.

***

### _shell

Executes a shell command via `shell_exec` and return the complete output as a string.
Also it redirects error output to `NUL` based on the platform (Windows or Unix-like).

```php
public static _shell(string $command): string|bool|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The command to execute. |

**Return Value:**

`string|bool|null` - Return the output of the command, or null on error.

***

### visibility

Toggles the terminal visibility of user input.

```php
final public static visibility(bool $visibility = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$visibility` | **bool** | True to show input, False to hide input. |

**Return Value:**

`bool` - Return true if visibility toggling was successful, false otherwise.

***

### wrap

Wrap a text with padding left and width to a maximum number.

```php
final public static wrap(string|null $text = null, int $max, int $leftPadding): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string&#124;null** | The text to wrap. |
| `$max` | **int** | The maximum width to use for wrapping. |
| `$leftPadding` | **int** | Additional left padding to apply. |

**Return Value:**

`string` - Return wrapped text with padding left and width.

***

### card

Generate a card like with text centered within the card.

```php
final public static card(string $text, int|null $padding = null): string
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

Attempts to determine the width of the viewable command line window.

```php
final public static getWidth(int $default = 80): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$default` | **int** | Optional default width (default: `80`). |

**Return Value:**

`int` - Return terminal window width or default.

***

### getHeight

Attempts to determine the height of the viewable command line window.

```php
final public static getHeight(int $default = 24): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$default` | **int** | Optional default height (default: 24). |

**Return Value:**

`int` - Return terminal window height or default.

***

### input

Get user input from the shell, after requesting for user to type or select an option.

```php
final public static input(?string $prompt = null, bool $useFopen = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prompt` | **string&#124;null** | Optional message to prompt the user after they have typed. |
| `$useFopen` | **bool** | Weather to use `fopen`, this opens `STDIN` stream in read-only binary mode (default: false).<br />This creates a new file resource. |

**Return Value:**

`string` - Return user input string.

***

### validate

Command user input validation on prompts.

```php
final public static validate(string $value, array $rules): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **string** | The user input value. |
| `$rules` | **array** | The validation rules, array key name `input` value `rules` (e.g. `['input' => 'required\|email']`). |

**Return Value:**

`bool` - Return true if validation succeeded, false if validation failed.

***

### escape

Escape command arguments for safe execution.

```php
final public static escape(string|array $argument): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$argument` | **string&#124;array** | The command argument to escape. |

**Return Value:**

`string` - Return the escaped command string.

***

### replace

Replace command placeholders with environment variables.

```php
final public static replace(string $command, array $env, bool $escape = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The command to replace. |
| `$env` | **array** | The environment variables to replace. |
| `$escape` | **bool** | Weather escape command after replacements (default: false) |

**Return Value:**

`string` - Return replaced command string to be executed.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if an error occurs.

***

### error

Display card error message using red background and white text as default.

```php
final public static error(
    string $text, 
    string|null $foreground = 'white',
    string|null $background ='red'
): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to output. |
| `$foreground` | **string&#124;null** | Foreground color name. |
| `$background` | **string&#124;null** | Optional background color name. |

***

### success

Display card success message, using green background and white text as default.

```php
final public static success(
    string $text, 
    string|null $foreground = 'white', 
    string|null $background = 'green'
): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to output. |
| `$foreground` | **string&#124;null** | An optional foreground color name (default: `white`). |
| `$background` | **string&#124;null** | An optional background color name (default: `green`). |

***

### writeln

Print text to in a newline using stream `STDOUT`.

```php
final public static writeln(string $text = '', ?string $foreground = null, ?string $background = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to write in a new line. |
| `$foreground` | **string&#124;null** | An optional foreground color name (default: `null`). |
| `$background` | **string&#124;null** | An optional background color name (default: `null`). |

***

### write

Print text to without a newline applied using stream `STDOUT`.

```php
final public static write(string $text = '', ?string $foreground = null, ?string $background = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to write. |
| `$foreground` | **string&#124;null** | An optional foreground color name (default: `null`). |
| `$background` | **string&#124;null** | An optional background color name (default: `null`). |

***

### print

Print a message to using using `echo`.

```php
final public static print(string $text, ?string $foreground = null, ?string $background = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to print. |
| `$foreground` | **string&#124;null** | An optional foreground color name (default: `null`). |
| `$background` | **string&#124;null** | An optional background color name (default: `null`). |

***

### fwrite

Write text to stream resource with any handler as needed,
If called in non-command context, it will output text using `echo`.

```php
final public static fwrite(string $text, resource $handle = STDOUT): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to output or write. |
| `$handle` | **resource** | The resource handler to use (e.g. `STDOUT`, `STDIN`, `STDERR` etc...). |

***

### clear

Clears all the output in console screen.

```php
final public static clear(): void
```

***

### flush

Clears console output to update new text.

```php
final public static flush(): void
```

***

### color

Apply color and text formatting to the given text if color is supported.

```php
final public static color(
    string $text, 
    string|null $foreground, 
    ?string $background = null, 
    ?int $format = null
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to apply color to. |
| `$foreground` | **string&#124;null** | The foreground color name (e.g, `red`, `blue` etc..). |
| `$background` | **string&#124;null** | Optional background color name (e.g, `red`, `blue` etc..). |
| `$format` | **int&#124;null** | Optionally apply text formatting style (e.g., `Text::ANSI_*`). |

**Return Value:**

`string` - Return colored text if color is supported, otherwise return default text.

**See Documentation Reference**

- For more details about color formatting [read documentation here](/commands/color-util.md).
- For more details about text formatting [read documentation here](/commands/text-util.md).

***

### newLine

Print new lines based on specified count.

```php
final public static newLine(int $count = 1): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$count` | **int** | The number of new lines to print. |

***

### oops

Oops! command, show an error message for unknown executed command.

```php
public static oops(string $command, string|null $color = 'red'): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The command that was executed. |
| `$color` | **string&#124;null** | Text color for the command (default: `red`). |

**Return Value:**

`int` - Return status code `STATUS_ERROR`.

***

### table

Generate and print a formatted table header and rows to the console.

```php
public static table(
    string[] $headers, 
    array<string,string> $rows, 
    ?string $headerColor = null, 
    int $headerPadding = 1
): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$headers` | **array<int,string>** | The headers for the table columns. |
| `$rows` | **array<string,string>** | The rows of data to display in the table. |
| `$headerColor` | **string&#124;null** | The table heading columns text color (default: null). |
| `$headerPadding` | **int** | Optional table heading columns padding (default: 1). |

***

### streamSupports

Checks whether the current stream resource supports or refers to a valid terminal type device.

```php
final public static streamSupports(string $function, resource|string $resource): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$function` | **string** | Function name to check. |
| `$resource` | **resource&#124;string** | Resource to handle (e.g. `STDIN`, `STDOUT`). |

**Return Value:**

`bool` - Return true if stream resource is supported, otherwise false.

***

### toString

Convert executed command array arguments to their original string form.

```php
public static toString(array $arguments): ?string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$arguments` | **array** | The command arguments from server variable `args`. |

**Return Value:**

`string|null` - Returns the parsed command arguments as a string, or null if the array is empty.

***

### getArgument

Get command argument by index number.

```php
final public static getArgument(int $index): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **int** | The index position to get. |

**Return Value:**

`mixed` - Return command argument by index number.

***

### getArguments

Get command arguments.

```php
final public static getArguments(): array
```

**Return Value:**

`array` - Return command arguments.

***

### getCommand

Get command name.

```php
final public static getCommand(): ?string
```

**Return Value:**

`string|null` - Return the command name.

***

### getCaller

Get command caller command string.

```php
final public static getCaller(): ?string
```

**Return Value:**

`string|null` - Return the full passed command, options and arguments.

***

### getOption

Get options value from command arguments.
If option key is passed with an empty value true will be return otherwise the default value.

```php
final public static getOption(string $key, mixed $default = false): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | Option key to retrieve. |
| `$default` | **mixed** | Default value to return (default: false). |

**Return Value:**

`mixed` - Return option value, true if empty value, otherwise default value.

***

### getAnyOption

Get options value from command arguments with an alias key to lookup if main key isn't found.
If option key is passed with an empty value true will be return otherwise the default value.

```php
final public static getAnyOption(string $key, string $alias, mixed $default = false): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | Option key to retrieve. |
| `$alias` | **string** | Option key alias to retrieve. if main key is not found. |
| `$default` | **mixed** | Default value to return (default: false). |

**Return Value:**

`mixed` - Return option value, true if empty value, otherwise default value.

**Get Any Option Example**

Assume you have a command, such as `php index.php my-command --which=a`. 
If you want to also accept an alias of `--which` as `-w`, then `getAnyOption` is what you need.

```php
<?php
$which = $this->getAnyOption('which', 'w');
echo $which;
```

> In the above example, `getAnyOption('which', 'w')` checks for both `--which` and `-w`, allowing you to use either option in your command.

***

### getMethod

Returns the command controller class method name.

```php
final public static getMethod(): ?string
```

**Return Value:**

`string|null` - Return the command controller class method name, otherwise null.

***

### getOptions

Returns the array of executed command option key values.

```php
final public static getOptions(): array
```

**Return Value:**

`array` - Return array of executed command options.

***

### getQuery

Gets a single value from executed command information by the array option key name, if it doesn't exists return null.

```php
final public static getQuery(string $name): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The option key name (e.g, `group`, `arguments`, `command`, `caller`, `options`, `exe_string`). |

**Return Value:**

`mixed` -  Return the command option key value.

***

### getQueries

Returns the entire command associative that was executed.
This is a complied array of executed command details which has been processed for final use.

```php
final public static getQueries(): array
```

**Return Value:**

`array<string,mixed>` - Return an associative array of the entire command information.

***

### isColorSupported

Check if the command line supports color formatting for specific `STD`.

```php
final public static isColorSupported(resource|string $resource = STDOUT): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$resource` | **resource&#124;string** | The resource handler to check on (e.g, `STDIN`, `STDOUT`). |

**Return Value:**

`bool` - Return true if the resource supports color formatting.

> **Note:** On windows you must specify the stream resource (e.g, `STDIN`, `STDOUT` or `STDERR`).

***

### isMacTerminal

Checks whether the current terminal is mac terminal.

```php
final public static isMacTerminal(): bool
```

**Return Value:**

`bool` - Return true if is mac, otherwise false.

***

### isWindowsTerminal

Determine whether the current terminal is windows os terminal by passing the stream `STD` resource to check on.

```php
final public static isWindowsTerminal(resource|string $resource): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$resource` | **resource&#124;string** | The resource type to check (e.g. `STDIN`, `STDOUT`). |

**Return Value:**

`bool` - Return true if is windows terminal, false otherwise.

***

### isLinuxTerminal

Determines if the current terminal is a supported Linux terminal.

```php
final public static isLinuxTerminal(): bool
```

**Return Value:**

`bool` - Return true if the terminal is a supported Linux terminal, false otherwise.

***

### isAnsiSupported

Checks if the current terminal supports ANSI escape sequences.

```php
final public static isAnsiSupported(): bool
```

**Return Value:**

`bool` - Return true if ANSI is supported, false otherwise.

***

### isPtySupported

Determines if PTY (Pseudo-Terminal) is supported on the current system.

```php
final public static isPtySupported(): bool
```

**Return Value:**

`bool` - Return true if PTY is supported, false otherwise.

***

### isTtySupported

Checks if the current system supports TTY (Teletypewriter).

```php
final public static isTtySupported(): bool
```

**Return Value:**

`bool` - Return true if TTY is supported, false otherwise.

***

### hasCommand

Checks whether framework has the requested command.

```php
final public static hasCommand(string $command): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | Command name to check. |

**Return Value:**

`bool` - Return true if command exist, false otherwise.

***

### isHelp

Check if command is help command.

```php
final public static isHelp(string|array $command): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string&#124;array** | Command name to check or command array options. |

**Return Value:**

`bool` - Return true if command is help, false otherwise.

***

### header

Print NovaKit Command line header information.

```php
final public static header(): void
```