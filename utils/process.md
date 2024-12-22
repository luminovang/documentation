# Process Execution Handler

***

## Overview

Process Handler simplifies executing command processes. It supports multiple execution methods like popen, proc_open, and shell_exec, as well as running closures and working with stream interfaces. 

***

## Introduction

The **Process Handler** class provides a flexible interface for executing commands or custom code in PHP, with built-in support for various execution methods. It allows commands to be run through executors like `popen`, `proc_open`, `shell_exec`, and `exec`, as well as directly executing PHP closures and handle responses such generators, or stream resources, array, string and many more.

In addition, the process class leverages PHP 8.1 **Fiber** (if available) to enable asynchronous execution, allowing non-blocking interaction with commands or user-defined code. This enables the class to wait for processes to complete while offering optional timeouts for checking the status of the process. Once the process completes, the response can be retrieved efficiently.

***

### Features:
- **Multiple Executors**: The class supports running processes using different command execution mechanisms such as:
  - `popen`
  - `proc_open`
  - `shell_exec`
  - `exec`
- **Closure Support**: It can execute callable PHP functions or generators, allowing developers to easily run custom code or produce dynamic outputs.
- **Stream Interface**: It handles stream resources for reading or writing data through file descriptors, making it suitable for dealing with files, network streams, etc.
- **Process Lifecycle Management**: The class can manage the lifecycle of a process, including starting, running, waiting for completion, and retrieving responses or outputs.
- **Timeouts**: It includes the ability to wait for a process with a defined timeout to avoid long-running or hanging processes.

***

### Example Usages:

1. **Executing with proc_open**: 
   For advanced command execution scenarios, it supports running commands using `proc_open`, allowing more fine-grained control over input/output streams.

   ```php
   $process = Process::withCommand('sleep 10 && echo "Sleep is completed"', Process::EXECUTOR_PROC_OPEN);
   $process->run();
   $process->wait(15); // Wait for up to 15 seconds

   if ($process->isComplete()) {
      // Get and output the response
      $response = $process->getResponse();
      echo "Command output:\n";
      var_export($response);
   }
   ```

2. **Executing a Callable Function**: 
   It also supports running a PHP closure or callable, making it versatile for various tasks beyond shell commands.

   ```php
   $process = Process::withCallback(function() {
       return "Hello, World!";
   });
   $process->run();
   ```

3. **Shell Command Execution with shell_exec**: 
   This feature enables executing system commands using `shell_exec` and capturing their output.

   ```php
   $process = Process::withCommand('ls -la', Process::EXECUTOR_SHELL);
   $process->run();
   ```

4. **Handling Stream Resources**: 
   The class can read from or write to streams, like files or network resources, using the stream interface.

   ```php
   $stream = fopen('path/to/file.txt', 'r');
   $process = Process::withStream($stream);
   $process->run();
   $process->wait();

   fclose($stream);

   // Get the contents read from the stream
   if ($process->isComplete()) {
      $response = $process->getResponse();
      echo "Stream content:\n$response";
   }
   ```

5. **Running a Command with popen**: 
   The `Process` class allows for running shell commands using `popen`, providing control over the command output and process flow.

   ```php
   $process = Process::withCommand('ls -la', Process::EXECUTOR_POPEN);
   ```

6. **Waiting for Generator Completion**: 
   It can run and wait for PHP generators to complete, making it a versatile tool for handling asynchronous-like operations.

   ```php
   $generator = function() {
      yield "Part 1";
      yield "Part 2";
   };
   $process = Process::withCallback($generator);
   $process->run();
   $process->wait();

   // Get the generator's output
   if ($process->isComplete()) {
      $response = $process->getResponse();
      echo "Generator output:\n" . implode("\n", (array) $response);
   }
   ```

***

* Class namespace: `\Luminova\Utils\Process`

***

### Constants

These constants are predefined process executors which can be pass directory during initialization or along with `withCommand` method.

| **Constant**           | **Visibility** | **Type** | **Description**                                         |
|:-----------------------|:---------------|:---------|:--------------------------------------------------------|
| `EXECUTOR_POPEN`        | public         | string   | Executes a command using `popen`, opening a pipe for process I/O. |
| `EXECUTOR_EXEC`         | public         | string   | Executes a command using `exec`, running it directly via the shell. |
| `EXECUTOR_SHELL`        | public         | string   | Executes a command using `shell_exec`, capturing output from the shell. |
| `EXECUTOR_PROC_OPEN`    | public         | string   | Executes a command using `proc_open`, offering more control over process execution and I/O streams. |
| `EXECUTOR_CALLBACK`     | public         | string   | Executes process using a custom callback function, allowing flexible execution logic. |
| `EXECUTOR_STREAM`       | public         | string   | Executes a process using streams, enabling direct access to input/output streams during execution. |

***

## Methods

### constructor

Process constructor.

```php
public __construct(
    \Psr\Http\Message\StreamInterface|\Luminova\Storages\Stream|\Closure|resource|callable|array|string $input, 
    string $executor, 
    ?string $cwd = null, 
    ?array<string,mixed> $env = null
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **\Psr\Http\Message\StreamInterface&#124;\Luminova\Storages\Stream&#124;\Closure&#124;resource&#124;callable&#124;array&#124;string** | Command string, callable, or stream to be executed. |
| `$executor` | **string** | The process executor type (e.g, `Process::EXECUTOR_*`).<br />Can be one of the defined executor constants. |
| `$cwd` | **string&#124;null** | Optional working directory (default: null). |
| `$env` | **array<string,mixed>\|null** | Optional environment variables. |

***

### __sleep

Process serialization is not supported.

```php
public __sleep(): array
```

**Throws:**

- [\Luminova\Exceptions\BadMethodCallException](/running/exceptions.md#badmethodcallexception) - Throws when attempt to serialize process.

***

### __wakeup

Process deserialization is not supported.

```php
public __wakeup(): void
```

**Throws:**

- [\Luminova\Exceptions\BadMethodCallException](/running/exceptions.md#badmethodcallexception) - Throws when attempt to unserialize process.

***

### run

Runs the process based on the provided input and executor.

```php
public run(): mixed
```

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throws if the process fails to run or any error occurs.
- [\Luminova\Exceptions\BadMethodCallException](/running/exceptions.md#badmethodcallexception) - If trying to called while the process is running.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If an invalid command argument is specified.

***

### withCallback

Creates a Process instance with a callback.

```php
public static withCallback(callable $callable): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callable` | **callable** | The callable to be executed. |

**Return Value:**

`Process` - Return new static process instance.

***

### withStream

Creates a Process instance with a stream.

```php
public static withStream(\Psr\Http\Message\StreamInterface|\Luminova\Storages\Stream|resource $stream): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$stream` | **\Psr\Http\Message\StreamInterface&#124;\Luminova\Storages\Stream&#124;resource** | The stream resource to read from. |

**Return Value:**

`Process` - Return new static process instance.

***

### withCommand

Creates a Process instance with a command.

```php
public static withCommand(
    array|string $command, 
    string $executor = self::EXECUTOR_POPEN, 
    string|null $cwd = null,
    array<string,mixed>|null $envs = null
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$command` | **array&#124;string** | The command to be executed. |
| `$executor` | **string** | The executor method to use (default: `Process::EXECUTOR_POPEN`). |
| `$cwd` | **string&#124;null** | Optional working directory (default: null). |
| `$envs` | **array<string,mixed>&#124;null** | Optional environment variables (default: null). |

**Return Value:**

`Process` - Return new static process instance.

***

### isComplete

Checks if the process has completed execution.

```php
public isComplete(): bool
```

**Return Value:**

`bool` - Return true if the process has finished; otherwise, false.

***

### isRunning

Checks if the process has completed execution.

```php
public isRunning(): bool
```

**Return Value:**

`bool` - Return true if the process has finished; otherwise, false.

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

### getResponse

Retrieves the output response from the executed process.
The response output, which can be of any type (e.g., string, array).

```php
public getResponse(): mixed
```

**Return Value:**

`mixed` - Returns null if the process has not yet run or if no response is available.

***

### getPid

Returns the Pid (process identifier), if applicable.

```php
public getPid(): ?int
```

**Return Value:**

`int|null` - The process id if running, null otherwise

***

### getInfo

Returns the Pid (process identifier), if applicable.

```php
public getInfo(): array|false
```

**Return Value:**

`array|false` - The process id if running, null otherwise

***

### getStartTime

Retrieve the process start time in seconds.

```php
public getStartTime(): float
```

**Return Value:**

`float` - Return the process start time.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throws if process is not started

***

### getWorkingDirectory

Gets the working directory.

```php
public getWorkingDirectory(): ?string
```

**Return Value:**

`string|null` - Return the current working directory.

***

### getCommand

Sanitize, replace and retrieve command.

```php
public getCommand(): string
```

**Return Value:**

`string` - Return command string to be executed.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if an error occurs.

***

### setWorkingDirectory

Sets the current working directory.

```php
public setWorkingDirectory(string $cwd): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$cwd` | **string** | The current working directory. |

**Return Value:**

`self` - Return the instance of process class.

**Throws:**
- [\Luminova\Exceptions\BadMethodCallException](/running/exceptions.md#badmethodcallexception) - If trying to set working directory while the process is running.

***

### setEnvironment

Sets the environment variables.

```php
public setEnvironment(array<string,mixed> $envs): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$envs` | **array<string,mixed>** | The environment variables. |

**Return Value:**

`self` - Return the instance of process class.

**Throws:**

- [\Luminova\Exceptions\BadMethodCallException](/running/exceptions.md#badmethodcallexception) - If trying to set environment variables while the process is running.

***

### setMode

Sets the mode for opens process file pointer.

```php
public setMode(string $mode): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mode` | **string** | The mode to use. |

**Return Value:**

`self` - Return the instance of process class.

**Throws:**

- [\Luminova\Exceptions\BadMethodCallException](/running/exceptions.md#badmethodcallexception) - If trying to set mode while the process is running.

***

### setDescriptors

Sets the descriptors specifications.

```php
public setDescriptors(array<int,array> $descriptors): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$descriptors` | **array<int,array>** | The descriptors specifications. |

**Return Value:**

`self` - Return the instance of process class.

**Throws:**

- [\Luminova\Exceptions\BadMethodCallException](/running/exceptions.md#badmethodcallexception) - If trying to set descriptors while the process is running.

***

### setOptions

Set the process options before execution.

This method allows setting specific options for the process, such as blocking pipes or
creating a new process group. Options cannot be modified once the process is running.

```php
public setOptions(array<string,mixed> $options): 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<string,mixed>** | An associative array of options to set. |

**Return Value:**

`self` - Return the instance of process class.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If an invalid option is provided.
- [\Luminova\Exceptions\BadMethodCallException](/running/exceptions.md#badmethodcallexception) - If trying to set options while the process is running.

**Supported options include:**

- `blocking_pipes`: Controls whether the pipes should block during I/O.
- `create_process_group`: Determines if the process should be run in a new group.
- `create_new_console`: Launches the process in a new console window (Windows only).
- `bypass_shell`: Bypass the shell when launching the process (Linux/Windows).
- `detach_process`: Detaches the process, allowing it to run independently (Linux/Windows).
- `use_pty`: Use a pseudo-terminal (PTY) for the process (Unix systems only).
- `inherit_environment`: Inherit the environment variables from the parent process.
- `redirect_stderr`: Redirect stderr to stdout (combine output streams).
- `suppress_errors`: Suppress PHP errors or warnings during the process execution.

***

### getDescriptors

Gets the descriptors for the process.

```php
public getDescriptors(): array
```

**Return Value:**

`array` - Return the descriptors for the process based on the operating system.

***

### wait

Waits for the process to complete and collects output.

```php
public wait(int $timeout): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timeout` | **int** | Maximum time to wait in seconds (default: 0 for no timeout). |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the process has not been started.