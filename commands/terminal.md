# Command Execution and Formatting Helper with Terminal Class

***

## Overview

A CLI helper class for building interactive command-line tools in NovaKit, with support for input prompts, table output, command execution, and styled messages.

***

## Introduction

The `Terminal` class is a command-line utility helper designed for CLI operations in **Novakit**, including both routable `BaseCommand` controllers and console-based `BaseConsole` controllers in the Luminova framework.

Its methods are defined statically for convenience, but **some require proper initialization**, either by calling the `init()` method or instantiating the class with `new Terminal()` to function correctly.

---

### Key Features

The `Terminal` class enhances command-line development by offering a wide range of features:

* **Table Generation** – Easily display structured data as formatted tables.
* **User Input Prompts** – Prompt for user input, including hidden fields for password entries.
* **Read/Write Operations** – Handle CLI I/O through various print and read utilities.
* **Argument Extraction** – Simplify parsing of command-line arguments and flags.
* **Command Execution** – Run shell commands directly from your CLI tool.
* **Formatted Output** – Style terminal output with colors and formatting for better readability.

---

> The `Terminal` class is extended by both `BaseCommand` and `BaseConsole`, making its methods directly available in controller classes. Static usage is also supported, but initialization is required in standalone contexts.

***

### Terminal Class Initialization

When using the `Terminal` class **outside** of command controllers (such as `Luminova\Base\BaseCommand` or `Luminova\Base\BaseConsole`), you must first **initialize** the class manually.

**Initialization Example:**

```php
use Luminova\Command\Terminal;

// Initialize via static method
Terminal::init();

// Or by creating a new instance
new Terminal();
```

---

### Usage Within Command Controllers

When working **inside** a command controller, the `Terminal` class is already initialized. You should use one of the following references:

* `$this`
* `self`
* `parent`
* `$this->term` (preferred inside console commands)

**Avoid manually initializing Terminal in command controllers:**

```php
// Not recommended inside command controllers:
$terminal = new Terminal();      

// static call
Terminal::methodName(); 
```

---

### Routable Command Controllers

In a **routable command controller** (a class extending `Luminova\Base\BaseCommand`), the `Terminal` methods can be accessed directly using `$this`, `self`, or `parent` keywords, as the base class exposes terminal functionality directly.

**Example:**

```php
// Recommended
$this->print('Foo'); 

// Static context
self::print('Foo');

// From inherited context
parent::print('Foo');
```

---

### Console Controllers

In a **console controller** (a class extending `Luminova\Base\BaseConsole`), use the `$this->term` property to access the `Terminal` instance.

**Example:**

```php
// Access via term instance
$this->term->print('Foo');
```

***

## Basic Usages

### Table Display

Display a simple table with names and emails:

```php
Terminal::table(
    ['Name', 'Email'], 
    [
        ['Name' => 'Peter', 'Email' => 'peter@example.com'],
        ['Name' => 'Hana', 'Email' => 'hana@example.com']
    ]
);
```

**Output:**

```bash
┌───────┬───────────────────┐
│ Name  │ Email             │
├───────┼───────────────────┤
│ Peter │ peter@example.com │
├───────┼───────────────────┤
│ Hana  │ hana@example.com  │
└───────┴───────────────────┘
```

***

### Multiple Chooser Prompts

Ask the user to choose an option or default to the first element:

```php
$array = Terminal::chooser(
    'Choose your programming languages?', 
    ['PHP', 'JAVA', 'SWIFT', 'JS', 'SQL', 'CSS', 'HTML']
);
```

Require the user to choose at least one option is required:

```php
$array = Terminal::chooser(
    'Choose your programming languages?', 
    ['PHP', 'JAVA', 'SWIFT', 'JS', 'SQL', 'CSS', 'HTML'], 
    true
);
``` 

***

### User Prompts

Prompt the user with color-coded options and is required:

```php
$color = Terminal::prompt(
    'Are you sure you want to continue?', 
    [
        'green' => 'YES', 
        'red' => 'NO'
    ]
);
```

Prompt the user to select their gender, without colored text nor validation required:

```php
$gender = Terminal::prompt(
    'What is your gender?', 
    ['male', 'female'], 
    false
);
```

Prompt the user to select an option with custom validation rules:

```php
$email = Terminal::prompt(
    'Are you sure you want to continue?', 
    ['YES', 'NO'], 
    'required|in_array(YES,NO)'
);
```
***

### User Input Methods 

Request user to enter an input.

```php
$name = Terminal::input('What is your name? ');
echo $name;
```

Request user to enter password without displaying while typing:

```php
$password = Terminal::password('Enter your password? ');

echo $password;
```

Using prompt to request the user to enter their email address:

```php
$email = Terminal::prompt('What is your email?');

echo $email;
```

***

### Waiting Examples

Display a waiting message until the user presses any key:

```php
Terminal::waiting();
```

Show a waiting message for 20 seconds with a countdown:

```php
Terminal::waiting(20);
```

Show a custom downloading message for 20 seconds:

```php
Terminal::waiting(20, 'Downloading... (%d seconds)');
```

***

### Freezing Examples

Freeze the console screen for 20 seconds:

```php
Terminal::freeze(20); 
```

Freeze the console and clear all output and user input for 20 seconds:

```php
Terminal::freeze(20, true);
```

***

### Progress Bar Examples

Draw a progress bar with with manual step updates:

```php
for ($i = 0; $i <= 100; $i++) {
    $step = Terminal::progress($i, 100);
    usleep(100000);
    
    if ($step >= 100) {
        echo 'Am done';
        break;
    }
}
```

Stope a progress bar halfway, up to 50% without completing:

```php
for ($i = 0; $i <= 100; $i++) {
    $step = Terminal::progress($i, 100);
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
Terminal::watcher(100, function() {
    Terminal::writeln("Am done here");
}, function(float|int $step) {
    // Avoid adding a new line here to prevent progress bars from miss-aligning except is intended
    Terminal::write(" Current {$step}");
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

Draw a spinning animation with an array list of any charters. Animate 5 spins, a 100ms sleep between frames, and a custom completion message:

```php
Terminal::spinner(['-', '\\', '|', '/'], 5, 100000, function() {
    Terminal::write("Finished spinning!\n");
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

## Class Definition

* Class namespace: `\Luminova\Command\Terminal`

***

## Constants

These constants represent the standard input, output, and error streams, often used in terminal or command-line applications to manage and control where data flows.

| Constant | Type | Value | Description |
|:---------|:-----|:------|:------------|
|`STD_OUT`|int|0|Standard output stream, typically used for displaying normal messages or output in a terminal.|
|`STD_ERR`|int|1|Standard error stream, used for displaying error messages and diagnostic information.|
|`STD_IN`|int|2|Standard input stream, used for receiving input from the user or another source in a terminal.| 

***

## Properties

Input validations object.

```php
protected static ?\Luminova\Security\Validation $validation = null;
```

## Methods

### constructor

Optionally initialize the Terminal instance using the constructor before executing any CLI commands.

```php
public __construct()
```

**Example:**

```php
use Luminova\Command\Terminal;

// Initialize by instantiating the class
$term = new Terminal();
$term->print("Hello from constructor!");
```

---

### init

Static method to initialize the Terminal instance. Use this if you're not instantiating the class manually.

```php
public static init(): void
```

**Example:**

```php
use Luminova\Command\Terminal;

// Initialize statically
Terminal::init();
Terminal::print("Hello from static init!");
```

***

### perse

Parses and processes command-line arguments and options, making them accessible in console controllers.

This method is used to extract and register command arguments, options, and flags passed to the CLI. Once parsed, you can retrieve values using helper methods like `getOption`, `getArgument`, and `getAnyOption`. It serves as an internal setter to store and expose parsed command data within extended controller classes.

```php
public static final perse(array $commands): void
```

#### Parameters

| Parameter   | Type                   | Description                                                         |
| ----------- | ---------------------- | ------------------------------------------------------------------- |
| `$commands` | `array<string, mixed>` | Command arguments, options, and flags extracted from CLI execution. |

**Usage Example:**

When executing a CLI command like:

```bash
php novakit my-command foo=Hello
```

The following controller example shows how to handle it:

```php
// /app/Console/MyCommand.php

use Luminova\Base\BaseConsole;

class MyCommand extends BaseConsole 
{
    public function run(?array $options = []): int
    {
        // Parse the received command-line options
        $this->term->perse($options);
        
        // Access the command name
        $command = $this->term->getCommand();

        // Access argument by position
        $arg1 = $this->term->getArgument(1);

        // Access named option
        $foo = $this->term->getOption('foo');

        // Access named option or alias
        $fooAlias = $this->term->getAnyOption('foo', 'f');

        return STATUS_SUCCESS;
    }
}
```

> **Note:** `perse()` is only required in `BaseConsole` command controllers. In routable `BaseCommand` controllers, the Luminova routing system handles parsing automatically.

***

### waiting

Displays a countdown timer in the console, with a custom message pattern, or prompts the user to press any key to continue.

```php
public static waiting(int $seconds, string $pattern = 'Waiting...(%d seconds)'): void
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
public static freeze(int $seconds = 10, bool $clear = true): void
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
public static spinner(
    array $spinners = [...], 
    int $spins = 10, 
    int $sleep = 100000, 
    \Closure|bool|null $onComplete = null
): void
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
public static progress(int|false $step = 1, int $steps = 10, bool $beep = true): float|int
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
public static watcher(
    int $limit, 
    ?\Closure $onFinish = null, 
    ?\Closure $onProgress = null, 
    bool $beep = true
): void
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
Terminal::watcher(5, function(float|int $step): void {
    echo "Progress: $step%";
});
```

> This method is designed to be called without a loop, as it handles the iteration internally.
> It is useful for showing progress while performing a task and executing subsequent actions when complete.

***

### beeps

Beep or make a bell sound for a certain number of time.

```php
public static beeps(int $total = 1): void
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
public static prompt(
    string $message, 
    array $options = [], 
    string|false|null $validations = null, 
    bool $silent = false
): string
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
public static chooser(
    string $text, 
    array $options, 
    bool $required = false,  
    bool $multiChoice = true
): array<string|int,mixed>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The chooser description message to prompt. |
| `$options` | **array** | The list of options to prompt (e.g, `['male' => 'Male', 'female' => 'Female']` or `['male', 'female']`). |
| `$required` | **bool** | Require user to choose any option else the first array will be return as default. |
| `$multiChoice` | **bool** | Weather multiple options are allowed (default: true). |

**Return Value:**

`array<string|int,mixed>` Return the client selected option array key and values.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throw if options is not specified or an empty array.

***

### password

Prompts the user to enter a password with hidden input. Supports retry attempts and optional timeout.

- On Windows, it uses a VBS script or PowerShell to hide the input.
- On Linux/Unix, it uses a bash command to hide the input.

```php
public static password(string $message = 'Enter Password', int $retry = 3, int $timeout = 0): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | Optional message to display when prompting for the password (default: 'Enter Password'). |
| `$retry` | **int** | The number of allowed retry attempts, set to `0` for unlimited retries (default: 3). |
| `$timeout` | **int** |  Optional time window for password input in seconds, set to 0 for no timeout (default: 0). |

**Return Value:**

`string` - Return the entered password or an empty string if the maximum retry attempts are exceeded.

***

### keyEvent

Captures a key press event and returns the corresponding key name.

Supports arrow keys, tab, enter, escape, and Ctrl+C.

```php
public static keyEvent(?callable $callback = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **callable\|null** | Optional callback function that receives the raw key and its name. |

**Return Value:**

`array<key:ansi,name:string>|mixed` - Returns an array with the **raw ANSI key sequence** and its **mapped name**, or the result of the callback function.

**Example Usage:**

```php
$result = Terminal::keyEvent();
print_r($result);
```

**Possible Output (if pressing the "Enter" key):**

```php
Array
(
    [key] => 
    [name] => enter
)
```

**Using a Callback Function**

```php
Terminal::keyEvent(function (string $key, string $name) {
    Terminal::writeln("Key Pressed: $name (Raw: " . bin2hex($key) . ")");
});
```
**User presses the "Arrow Up" key:**  
```
Key Pressed: up (Raw: 1b5b41)
```

**Handling Specific Keys**
```php
$event = Terminal::keyEvent();

if ($event['name'] === 'ctrl+c') {
    Terminal::writeln("Interrupt detected! Exiting...");
    exit;
}
```

***

### link

Highlights URL to indicate it clickable in terminal.

```php
public static link(string $url, ?string $title = null): void 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The url to be highlighted. |
| `$title` | **string\null** | Optional title to be displayed (default: null). |

***

### terminate

Terminates the script execution with an optional message and status code.

This method provides a consistent way to end script execution, optionally printing a message before termination. The script exits with the provided status code, which defaults to a success code. If a non-success status code is provided, the script will still terminate, but the status code can be used to indicate an error or abnormal termination to the system.

```php
public static terminate(?string $message = null, int $statusCode = STATUS_SUCCESS): never
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
public static timeout(\Closure $callback, int $timeout, mixed $stream = STDIN): bool
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
public execute(string $command): array|false
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
public static visibility(bool $visibility = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$visibility` | **bool** | True to show input, False to hide input. |

**Return Value:**

`bool` - Return true if visibility toggling was successful, false otherwise.

***

### cursorVisibility

Controls the cursor visibility in the terminal.

```php
public static cursorVisibility(bool $showCursor):  void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$showCursor` | **bool** | Set to true to show the cursor, false to hide it. |

***

### input

Read a single line of user input from the terminal or piped input.

This method is suitable for user prompts or single-line piped input. For multi-line input, use `readInput()` instead.

```php
public static input(?string $prompt = null, string $default = '', bool $newStream = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prompt` | **string&#124;null** | Optional message to display before reading input. |
| `$default` | **string** | Optional default value to return if no input is provided (default: empty-string). |
| `$newStream` | **bool** |  Whether to open a new `STDIN` stream in read-only binary mode as a separate handle (default: false). |

**Return Value:**

`string` - Return the input string or the default value if input is empty or reading fails.

***

### readInput

Read input from STDIN, supporting single or multi-line input.

This method is suitable for reading from piped data or long content. For simple user prompts or single-line input, use  `input()` directly instead.

```php
public static readInput(string $default = '', bool $eof = true, bool $newStream = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$default` | **string** |  Default value to return if reading fails or input is empty (default: empty-string). |
| `$eof` | **bool** | If true, reads the entire input until EOF. If false, reads a single line. |
| `$newStream` | **bool** | Whether to `open` a new STDIN stream in read-only binary mode as a separate handle (default: false). |

**Return Value:**

`string` - Return the input string or the default value if input is empty or reading fails.

**Example:**

Read From Pipe Input:

```bash
echo "Log Message From Pipe" | php index.php logger
php index.php list-something | php index.php logger
 ```

***

### tablist

Displays a selectable list in the terminal and allows navigation using arrow keys or tab.

```php
public static tablist(
    array $options, 
    int $default = 0,
	?string $placeholder = null,
    bool $clearOnSelect = true,
    string $foreground = 'green', 
    ?string $background = null
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<int,string\|int>** | The list of options to choose from. |
| `$default` | **int** | The default selected index (default: 0). |
| `$placeholder` | **string\|null** | Optional placeholder text to display. |
| `$clearOnSelect` | **bool** | Whether to clear the screen after selection (default: true). |
| `$foreground` | **string** | The foreground color for the highlighted selection (default: `green`). |
| `$background` | **string\|null** | The optional background color for the highlighted selection (default: `null`). |

**Return Value:**

`string` - Return the selected option value as a string.

***

### validate

Command user input validation on prompts.

```php
public static validate(string $value, array $rules): bool
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

Escapes a command argument to ensure safe execution in the shell.

```php
public static escape(?string $argument = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$argument` | **string&#124;null** | The command argument to escape. |

**Return Value:**

`string` - Return the escaped command string.

**Example:**

```php
$argument = 'example argument with "special" characters & symbols';
$escaped = Terminal::escape($argument);

Terminal::_exec("echo $escaped", $output);

print_r($output);
```

***

### replace

Replace placeholders in a command with environment variable values.

Placeholders follow the format `${:VAR_NAME}`, where `VAR_NAME` corresponds to a key in the `$env` array.
Optionally escapes each replacement for safe shell execution.

```php
public static replace(string $command, <string,mixed> $env, bool $escape = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **string** | The command string with placeholders to replace. |
| `$env` | **array<string,mixed>** | Associative array of environment variables for replacements. |
| `$escape` | **bool** | Whether to escape each replacement after substitution (default: false). |

**Return Value:**

`string` - Throws if a placeholder variable is not found in `$env` or is set to `false`.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if an error occurs.

**Example:**

Example of the environment variables:
```php
$command = 'echo "${:USER} is running a ${:TASK}"';
$env = [
    'USER' => 'Alice',
    'TASK' => 'test command'
];
```

Replace placeholders:

```php
$command = Terminal::replace($command, $env);
echo $command; 
// Output: echo "Alice is running a test command"
```

Execute the command:

```php
Terminal::_exec($escaped, $output);
print_r($output);
```

***

### error

Display an error message box with a default red background and white text.

```php
public static error(
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

> **Note:** The message is formatted as a block and written to `STDOUT`.

***

### success

Display a success message with a default green background and white text.

```php
public static success(
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

Print text followed by a newline to the specified stream, defaulting to `Terminal::STD_OUT`.

```php
public static writeln(
    string $text = '', 
    ?string $foreground = null, 
    ?string $background = null, 
    int $stream = self::STD_OUT
): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to write in a new line (default: blank). |
| `$foreground` | **string&#124;null** | An optional foreground color name (default: `null`). |
| `$background` | **string&#124;null** | An optional background color name (default: `null`). |
| `$stream` | **int** | The stream resource to write to (e.g, `Terminal::STD_OUT`, `Terminal::STD_IN`, `Terminal::STD_ERR`). |

***

### write

Print text without appending a newline to the specified stream, defaulting to `Terminal::STD_OUT`.

```php
public static write(
    string $text = '', 
    ?string $foreground = null, 
    ?string $background = null, 
    int $stream = self::STD_OUT
): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to write (default: blank). |
| `$foreground` | **string&#124;null** | An optional foreground color name (default: `null`). |
| `$background` | **string&#124;null** | An optional background color name (default: `null`). |
| `$stream` | **int** | The stream resource to write to (e.g, `Terminal::STD_OUT`, `Terminal::STD_IN`, `Terminal::STD_ERR`). |

***

### print

Print text directly using `echo` without any stream handling.

```php
public static print(string $text, ?string $foreground = null, ?string $background = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to print. |
| `$foreground` | **string&#124;null** | An optional foreground color name (default: `null`). |
| `$background` | **string&#124;null** | An optional background color name (default: `null`). |

***

### fwrite

Write text to the specified stream resource, without applying any colors defaulting to `STDOUT`.

```php
public static fwrite(string $text, resource|int $handler = self::STD_OUT): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to output or write. |
| `$handler` | **resource&#124;int** | The stream resource handler to write to (e.g. `STDOUT`, `STDERR`, `STDIN`). |

> **Note:** If the environment is non-command-based, the text will be output using `echo` instead of `fwrite`.

***

### clear

Clears the console screen with optional clearing modes.

This method refreshes the entire terminal window, removing all previously displayed command outputs.

**Modes**

- `default` - (default): Clears the entire screen.
- `partial` - Clears from the cursor position downward.
- `full` - Clears the entire screen and scrollback buffer.

```php
public static clear(string $mode = 'default'): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mode` | **string** | The clearing mode: `default`, `partial`, or `full`. |

***

### flush

Clears the last printed line or lines of text in the terminal output.

This method is useful for updating the terminal with new content without
clearing the entire screen, allowing for a more controlled output.

```php
public static flush(?string $lastOutput = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$lastOutput` | **string\|null** | An optional last output printed to the terminal (default: null). |

> If `$lastOutput` is provided,  the it calculates how many lines it occupied and clears them. 
> If not provided, it clears the current line only.

***

### newLine

Print new lines based on specified count.

```php
public static newLine(int $count = 1): void
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

Generate and print a formatted table with headers and rows to the console.

This method constructs a visually appealing table using the specified headers and data rows. 
It supports customization of text colors for both the header and the table content, as well as options for displaying borders after each table row.
     
```php
public static table(
	string[] $headers, 
	array<int,array<string,string>> $rows, 
	?string $foreground = null,
	?string $headerColor = null, 
	?string $borderColor = null,
	bool $border = true
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$headers` | **array<int,string>** | The headers for the table columns, where each header is defined by its index. |
| `$rows` | **array<int,array<string,string>>** | The rows of table data to display in the tables body, where each row is an associative array with keys representing the column headers and values containing the corresponding content. |
| `$foreground` | **string&#124;null** | An optional text color for the table's body content (default: null). |
| `$headerColor` | **string&#124;null** | An optional text color for the table header columns (default: null). |
| `$borderColor` | **string&#124;null** | An optional color for the table borders (default: null). |
| `$border` | **bool** | Indicate whether to display borders between each table raw (default: true). |

**Return Value:**

`string` - Return the formatted table as a string, ready to be output to the console.

***

### isStreamSupports

Checks whether the current stream resource supports or refers to a valid terminal type device.

```php
public static isStreamSupports(string $function, resource|string|int $resource = self::STD_OUT): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$function` | **string** | Function name to check. |
| `$resource` | **resource&#124;string&#124;int** | Resource to handle (e.g. `Terminal::STD_*`, `STDIN`, `STDOUT`). |

**Return Value:**

`bool` - Return true if stream resource is supported, otherwise false.

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

### getArgument

Get command argument by index number.

```php
public static getArgument(int $index): mixed
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
public static getArguments(): array
```

**Return Value:**

`array` - Return command arguments.

***

### getCommand

Get command name.

```php
public static getCommand(): ?string
```

**Return Value:**

`string|null` - Return the command name.

***

### getCaller

Get command caller command string.

```php
public static getCaller(): ?string
```

**Return Value:**

`string|null` - Return the full passed command, options and arguments.

***

### getOption

Get options value from command arguments.
If option key is passed with an empty value true will be return otherwise the default value.

```php
public static getOption(string $key, mixed $default = false): mixed
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
public static getAnyOption(string $key, string $alias, mixed $default = false): mixed
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
$which = Terminal::getAnyOption('which', 'w');
echo $which;
```

> In the above example, `getAnyOption('which', 'w')` checks for both `--which` and `-w`, allowing you to use either option in your command.

***

### getMethod

Returns the command controller class method name.

```php
public static getMethod(): ?string
```

**Return Value:**

`string|null` - Return the command controller class method name, otherwise null.

***

### getOptions

Returns the array of executed command option key values.

```php
public static getOptions(): array
```

**Return Value:**

`array` - Return array of executed command options.

***

### getQuery

Gets a single value from executed command information by the array option key name, if it doesn't exists return null.

```php
public static getQuery(string $name): mixed
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
public static getQueries(): array
```

**Return Value:**

`array<string,mixed>` - Return an associative array of the entire command information.

***

### getStd

Resolves the provided stream constant (`Terminal::STD_OUT`, `Terminal::STD_ERR`, or `Terminal::STD_IN`) and returns the corresponding PHP predefined stream (`STDOUT`, `STDERR`, or `STDIN`).

If the input does not match any of these constants, it returns the original input.

```php
final public static getStd(resource|int $std): resource|string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$std` | **resource&#124;int** | The stream identifier, which can be one of the predefined<br />constants `Terminal::STD_*` or another value. |

**Return Value:**

`resource|string` - Return the corresponding PHP stream resource (`STDOUT`, `STDERR`, `STDIN`)
or the original input if it doesn't match any standard streams.

***

### setColorOutputEnabled

Force enables color output for all standard streams (STDOUT, STDERR, STDIN) without checking whether the terminal or console supports colors.

```php
public static setColorOutputEnabled(): void
```

> This method bypasses any checks and ensures that color formatting will be applied to the output, regardless of compatibility.

***

### setAnsiSupportEnabled

Force enables ANSI escape codes (for formatting like text color, cursor movement, etc.) for all standard streams (STDOUT, STDERR, STDIN), without checking whether the terminal or console supports ANSI codes.

```php
public static setAnsiSupportEnabled(): void
```

> This method will ensure that ANSI sequences will be processed, regardless
of the actual terminal capabilities.

***

### isColorSupported

Checks if the terminal supports ANSI escape codes for color output.

This method determines whether the current terminal can display colored text,
based on the platform and the resource (e.g., STDOUT, STDERR, or STDIN).

```php
public static isColorSupported(int $std = self::STD_OUT): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$std` | **int** | The std resource to check for color support (e.g, `Terminal::STD_OUT`, `Terminal::STD_ERR` or `Terminal::STD_IN`). |

**Return Value:**

`bool` - Returns true if color output is supported, false otherwise.

> **Note:** On windows you must specify the stream resource (e.g, `Terminal::STD_*`).

***

### isAnsiSupported

Checks if the terminal supports ANSI escape codes, including color and text formatting.

This method detects whether the terminal supports ANSI features such as colors, bold, underline,
and cursor movement, which are used for enhanced CLI output.

```php
public static isAnsiSupported(int $std = self::STD_OUT): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$std` | **int** | The std resource to check for ANSI support (e.g, `Terminal::STD_OUT`, `Terminal::STD_ERR` or `Terminal::STD_IN`). |

**Return Value:**

`bool` - Returns true if ANSI escape codes are supported, false otherwise.

***

### isMacTerminal

Checks whether the current terminal is mac terminal.

```php
public static isMacTerminal(): bool
```

**Return Value:**

`bool` - Return true if is mac, otherwise false.

***

### isWindowsTerminal

Determine whether the current terminal is windows os terminal by passing the stream `Terminal::STD_*` resource to check on.

```php
public static isWindowsTerminal(resource|string|int $resource = self::STD_IN): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$resource` | **resource&#124;int&#124;string** | The resource type to check (e.g. `STDIN`, `STDOUT`, `Terminal::STD_*`). |

**Return Value:**

`bool` - Return true if is windows terminal, false otherwise.

***

### isLinuxTerminal

Determines if the current terminal is a supported Linux terminal.

```php
public static isLinuxTerminal(): bool
```

**Return Value:**

`bool` - Return true if the terminal is a supported Linux terminal, false otherwise.

***

### isPtySupported

Determines if PTY (Pseudo-Terminal) is supported on the current system.

```php
public static isPtySupported(): bool
```

**Return Value:**

`bool` - Return true if PTY is supported, false otherwise.

***

### isTtySupported

Checks if the current system supports TTY (Teletypewriter).

```php
public static isTtySupported(): bool
```

**Return Value:**

`bool` - Return true if TTY is supported, false otherwise.

***

### isColorDisabled

Checks whether the no color is available in environment, including command flag `--no-color`.

```php
public static isColorDisabled(): bool
```

**Return Value:**

`bool` - Return true if color is disabled, false otherwise.

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

### isAnsiDisabled

Determines if ANSI escape codes are disabled explicitly, including command flag `--no-ansi`.

This method checks environment variables and other indicators to determine
if ANSI escape codes (for colors, text formatting, etc.) should be disabled.

```php
public static isAnsiDisabled(): bool
```

**Return Value:**

`bool` Returns true if ANSI escape codes are disabled, false otherwise.

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

### header

Print NovaKit Command line header information.
This method also honors command flag `--no-header`.

```php
final public static header(): bool
```

**Return Value:**

`bool` - Return true if header was printed, false otherwise.