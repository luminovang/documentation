# Global Procedural Helper Functions

***

## Overview

Global helper functions are collections of procedural functions that provide commonly used functionality across different parts of your application enhancing code reusability and productivity.

***

## Introduction

Global helper functions are procedural utilities that offer reusable solutions for common tasks across the application. They are defined in the global scope, making it easily accessible from anywhere in the codebase.

#### Code Reusability  
They reduce duplication by centralizing frequently used logic into named functions.

#### Productivity  
Pre-built helpers accelerate development by handling routine operations efficiently.

#### Abstraction of Complexity  
Helpers simplify complex processes, improving code readability and maintainability through clear, descriptive function names.

---

### Custom Function

By default, custom procedural functions initialization is disabled. To enable it, set the following in your `.env` file:

```ini
feature.app.dev.functions = enable
```

Once enabled, define your custom helper functions or constants in `/app/Utils/Global.php`:

```php
// /app/Utils/Global.php

// Define constant
defined('MY_FOO') || define('MY_FOO', 'bar');

// Define function
function myFunction(int $foo): int 
{
  return $foo;
}
```

> **Tip:** To avoid overriding existing PHP or Luminova functions, wrap your function in a check:  
> `if (!function_exists('myFunction')) { ... }`

***

## Core Functions

### app

Get application container class shared instance or a new instance if not shared.

```php
function app(bool $shared = true, mixed ...$arguments): \App\Application
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$shared` | **bool** | Return a shared instance (default: true). |
| `...$arguments` | **mixed** | Optional arguments to pass to the application constructor. |

**Return Value:**

`\Luminova\Core\CoreApplication<\App\Application>` - Returns the shared instance if $shared is true, or a new instance if $shared is false.

**See Also**

[Core Application](/core/application.md) - See the documentation for base application methods and usages.

> **Note:** Avoid re-initializing application class if possible, always use the `app` function or grab the shared instance of your application by `Application::getInstance()`

***

### request

Retrieve a shared or new instance of HTTP request object.

```php
function request(bool $shared = true): HttpRequestInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$shared` | **bool** | Return a shared instance (default: true). |

**Return Value:**

`\Luminova\Interface\HttpRequestInterface` - Returns an incoming HTTP request object.

**See Also**

[HTTP Request](/http/request.md) - See the documentation for http request methods and usages.

***

### session

Return session data if key is present, otherwise return the session class instance.

```php
function session(
   ?string $key = null, 
   bool $shared = true, 
   ?\Luminova\Interface\SessionManagerInterface $manager = null
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** |Optional key to retrieve the data (default: null). |
| `$shared` | **bool** | Weather to use shared instance (default: true). |
| `$manager` | **SessionManagerInterface<class-object>&#124;null** | The session manager interface to use (default: `SessionManager`). |

**Return Value:**

`\Luminova\Sessions\Session|mixed` - Return the session data if key is provided, otherwise the instance of session class.

**See Also**

[Session Manager](/sessions/session.md) - See the documentation for session methods.
  
***

### cookie

Create and return a cookie instance.

```php
function cookie(string $name, string $value = '', array $options = [], bool $shared = false): \Luminova\Cookies\Cookie
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the cookie. |
| `$value` | **string** | The value of the cookie. |
| `$options` | **array** | Options to be passed to the cookie. |
| `$shared` | **bool** | Use shared instance (default: false). |

**Return Value:**

`Cookie` - Return new cookie instance.

**See Also**

[Client Cookie Manager](/cookies/cookie.md) - See the documentation for client-side cookie methods.

***

### factory

To initialize a class available in factory and returns a shared instance of the class.
Optionally you can pass `NULL` to context parameter in other to return factory instance instead.

```php
function factory(?string $context = null, bool $shared = true, mixed ...$arguments): ?object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$context` | **string&#124;null** | The factory context name, e.g: `session`. (default: null). |
| `$shared` | **bool** | Weather to return a shared instance (default: `true`). |
| `$arguments` | **mixed** |  Optional class constructor initialization arguments. |

**Return Value:**

`class-object<\T>|Factory|null` - Return instance of factory class, instance of class called, otherwise null.

**Throws:**

- [\Luminova\Exceptions\AppException](/running/exceptions.md#appexception) - Throws an exception if factory context does not exist or error occurs.

**Available Factory Context Names**

-  `task`  -  Returns instance of `\Luminova\Time\Task`
-  `session`  - Returns instance of `\Luminova\Sessions\Session`
-  `cookie`  - Returns instance of `\Luminova\Cookies\Cookie`
-  `functions`  - Returns instance of `\Luminova\Base\CoreFunction`
-  `modules`  - Returns instance of `\Luminova\Library\Modules`
-  `language`  - Returns instance of `\Luminova\Languages\Translator`
-  `logger`  - Returns instance of ` \Luminova\Logger\Logger`
-  `fileManager`  - Returns instance of `\Luminova\Storages\FileManager`
-  `validate`  - Returns instance of `\Luminova\Security\Validation`
-  `response`  - Returns instance of `\Luminova\Template\Response`
-  `services`  - Returns instance of `\App\Config\Services`
-  `request`  - Returns instance of `\Luminova\Http\Request`
-  `notification`  -  Returns instance of `\Luminova\Notifications\Firebase\Notification`
-  `caller`  -  Returns instance of `\Luminova\Application\Caller`
-  `escaper`  -  Returns instance of `\Luminova\Functions\Escape`

**Example**

This example shows how to initialize session class instance with a new constructor argument using factory.

```php
$session = factory('session', false, new SessionManager());
```
 
 **See Also**

[Application Factory](/app/factory.md) - See the documentation for factory methods and usages.
 
***

### service

Service on the other hand focus more on your business logic, It allows you to returns a shared instance of a class registered in your service `bootstrap` method. To return an instance of service pass null as the service parameter.

```php
function service(
   ?string $service = null, 
   bool $shared = true, 
   bool $serialize = false, 
   mixed ...$arguments
): ?object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$service` | **class-string<\T>&#124;string&#124;null** | The service class name or alias. |
| `$shared` | **bool** | Weather to return a shared instance of class (default: true). |
| `$serialize` | **bool** | Weather to serialize and store class object as cache (default: false). |
| `$arguments` | **mixed** | Additional parameters to pass to class constructor. |

**Return Value:**

`class-object<\T>|Services|null` - Return service class instance or instance of called class.

**Throws:**

- [\Luminova\Exceptions\AppException](/running/exceptions.md#appexception) - Throws an exception if service does not exist or error occurs.

 **See Also**

[Application Service](/app/service.md) - See the documentation for service methods and usages.

***

### remove_service

Delete a service or clear all services.

```php
function remove_service(?string $service = null): bool
```

> If `NULL` is passed all cached and serialized services will be cleared.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$service` | **class-string<\T>&#124;string** | The class name or alias, to delete and clear it cached. |

**Return Value:**

`bool` - Return true if the service was removed or cleared, false otherwise.

***

### layout

PHP Template layout helper class, to extend, inherit and import layout sections while using default template engine.

```php
function layout(string $file): \Luminova\Template\Layout
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **string** | Layout filename without the extension path. |

**Return Value:**

`\Luminova\Template\Layout` - Returns the layout class instance.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) -Throws if layout file is not found.

**See Also**

[Template Layout](/templates/layout.md) - See the documentation for default `PHP` template layout methods and usages.

 > All layouts must be stored in `resources/Views/layout/` directory.
 > Examples: `layout('foo')` or `layout('foo/bar/baz')`

***

### response

Initiate a view response object and render any response content.
It allows you to render content of any format in view or download content within the view controller.

```php
function response(int $status = 200, ?array $headers = null, bool $shared = true): ViewResponseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | HTTP status code (default: 200 OK) |
| `$headers` | **array<string,mixed>\|null** | Additional response headers (default: null). |
| `$shared` | **bool** | Weather to return shared instance (default: true). |

**Return Value:**

`\Luminova\Interface\ViewResponseInterface` - Return vew response object. 

**See Also**

[View the Response Class documentation](/templates/response.md) for detailed information on methods and usage examples.

***

### func

Return instance a specific context if specified, otherwise get the instance anonymous class which extended `CoreFunction`.

```php
function func(?string $context = null, mixed ...$arguments): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$context` | **string&#124;null** | The context to return it's instance (default: null). |
| `$arguments` | **mixed** | [, mixed $... ] Optional initialization arguments based on context. |

**Supported contexts:**

 -   `ip` - Return instance of `Luminova\Functions\IP`.
 -   `document` - Return instance of `Luminova\Functions\IP`.
 -   `tor` - Return instance of `Luminova\Functions\Tor`,
 -   `math` - Return instance of `Luminova\Functions\Maths`.

**Return Value:**

`class-object<CoreFunction>|class-object<\T>|mixed` - Returns an instance of Functions, object, string, or boolean value depending on the context.

**Throws:**

- [\Exception](/running/exception-handling.md) - If an error occurs.
- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If unable to call method.

**See Also**

[Core Functions](/base/functions.md) - Learn more about the Luminova's `CoreFunction` documentation.

***

### shared

Temporarily stores and retrieves values within the same scope.
It allows you to set and get shared values. It uses a static array to store the shared preference, which persists across multiple calls to the function within the same script execution.

```php
function shared(string $key, mixed $value = null, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to identify the value. |
| `$value` | **mixed** | The value to store (optional). |
| `$default` | **mixed** | The default value return if key not found (default: NULL). |

**Return Value:**

`mixed` - Returns the value associated with the key, or default value if the key does not exist.

***

### browser

Get the information about user's browser, based on the user-agent string.

```php
function browser(?string $userAgent = null, string $return = 'object', bool $shared = true): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$userAgent` | **string&#124;null** | The user agent string to analyze. |
| `$return` | **bool** | Set the return type, if `instance` return userAgent class object otherwise return array or json object.<br />-   Return Types: [`array`, `object`, `instance`] |
| `$shared` | **bool** | Allow shared instance creation (default: true). |

**Return Value:**

`array<string,mixed>|object<string,mixed>|UserAgent|false` - An array, object containing browser information or user-agent class instance.

 **See Also**

[User Agents](/http/user-agent.md) - See the documentation for browser user-agent methods and usages.

***

### href

Create a hyperlink to another view or file.
This function creates a link to a view or file, ensuring the base starts from your application's public document root.

```php
function href(?string $view = null, bool $absolute = false): string 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string\|null** | The view path or file to create the link for (default: null).<br/> If null, an empty string is used. |
| `$absolute` | **bool** | Whether to use an absolute URL (default: false). |

**Return Value:**

`string` - Return link of view, If `$absolute` is true, the absolute URL is returned; otherwise, a relative URL is returned.

> If an absolute URL is requested, concatenates the base URL (`APP_URL`) with the view path.
> If a relative URL is requested, it concatenates it with the view path.

***

### asset

Create a link to a file in the assets folder.
This function generates a `URL` to a file within the assets folder, ensuring the base starts from your application's public document root.

```php
function asset(?string $filename = null, bool $absolute = false): string 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string\|null** | The filename or path within the assets folder (default: null).<br/> If null, an empty string is used. |
| `$absolute` | **bool** | Whether to use an absolute URL (default: false). |

**Return Value:**

`string` - Return asset link of view, If `$absolute` is true, the absolute URL is returned; otherwise, a relative URL is returned.

***

### root

Get the application root directory of your project anywhere, optionally pass a path to append the the root directory, all return path will be converted to `unix` or `windows` directory separator style.

```php
function root(?string $suffix = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$suffix` | **string\|null** | Optional suffix to prepend to the root directory. |

**Return Value:**

`string` - Return application document root, and optional appended suffix.

***

### path

Get system or application path, compatible with `unix` or `windows` directory separator style.

**Available Paths:**

- **`app`** - Application root directory.
- **`system`** - Luminova Framework and third-party plugins root directory.
- **`plugins`** - Third-party plugins root directory.
- **`library`** - Custom libraries root directory.
- **`controllers`** - Application controllers directory.
- **`writable`** - Application writable directory.
- **`logs`** - Application logs directory.
- **`caches`** - Application cache directory.
- **`public`** - Application public directory (front controller).
- **`assets`** - Application public assets directory.
- **`views`** - Application template views directory.
- **`routes`** - Application method-based routes directory.
- **`languages`** - Application language pack directory.
- **`services`** - Application cached services directory.

```php
function path(string $file): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **string&#124;null** | The path file name to return. |

**Return Value:**

`string` - Return directory path, windows, unix or windows style path. 

**See Also**

[File Manager](/files/manager.md) - Also checkout the documentation for file manager methods and usages.

***

### env

Get environment variable value from registered `ENV` variables, its a wrapper that combines both `$_ENV`, `$_SERVER` and `getenv`, with an additional feature to ensure the accuracy of the return type and default value if the key was not found in the environment.

```php
function env(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The environment variable key to retrieve.. |
| `$default` | **mixed** | Optional default value to return if the key is not found (default: null). |

**Return Value:**

`mixed` - Return the value of the specified environment key or default value if not found.

**See Also**

[Environment Variables](/running/env.md) - Learn more from documentation about Luminova's `env` variable keys.

> If your variable value is like: `foo = [1, 2, 4]`, this function will return an array representation of the value.

***

### setenv

Set an environment variable if it doesn't already exist.

```php
function setenv(string $key, string $value, bool $append_to_env = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the environment variable. |
| `$value` | **string** | The value of the environment variable. |
| `$append_to_env` | **bool** | Weather to save or update the value in .env file (default: false). |

**Return Value:**

`bool` - Return true on success, otherwise false on failure.

> By default when you set an environment variable, it doesn't permanently stored in other to access it in entire code-base.
>
> If you wish to write the key and value in your environment variables file `.env`, then you must specify the third argument `true`. 
>
> More efficient method of doing this is by utilizing the [NovaKit Command](/commands/novakit.md) to write the key and value to `env` file. using command `php novakit env:add --key="my_new_key" --value="my key value"`.

***

### import

Import a custom library into your project.

```php
function  import(string $library): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$library` | **string** | The name of the library. |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the library could not be found

**Return Value:**

`bool` - Return true if the library was successfully imported.

> You must place your external libraries in `libraries/libs/ directory` in other to use file import.

***

### logger

Logs a message to a specified destination using the configured preferred PSR logger class if define in `App\Config\Preference`, otherwise it will use default `Luminova\Logger\NovaLogger`.

The destination can be a log level, email address, or URL endpoint. This function delegates the logging action to the dispatch method, which handles the asynchronous or synchronous execution as needed.

```php
function logger(string $to, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$to` | **string** | The destination for the log (e.g, log level, email address, or URL). |
| `$message` | **string** | The log message. |
| `$context` | **array** | Additional context data (optional). |

**Log levels** 

- `emergency` - Log urgent errors requiring immediate attention.
- `alert` - Log important alert messages.
- `critical` - Log critical issues that may disrupt application functionality.
- `error` - Log minor errors.
- `warning` - Log warning messages.
- `notice` - Log messages that require attention but are not urgent.
- `info` - Log general information.
- `debug` - Log messages for debugging purposes.
- `exception` - Log exception messages.
- `php_errors` - Log PHP-related errors.
- `metrics` - Log performance metrics for `APIs`.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if an error occurs while logging or an invalid destination is provided.

> All non-network and email logs are located in `/writeable/log/`, each log level has it own file name (e.g, `warning.log`).
>
> To set your own logging handler class, it can be done in `App\Config\Preference`, your logger must implement `PSR` logger interface.

***

### locale

Set application locale or return current application local.

```php
function locale(?string $locale = null): string|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$locale` | **?string** | The locale to set. |

**Return Value:**

`string|bool` - If locale is passed it will set it and return true, else return previous locale.

***

### uuid

Generates a `UUID` string of the specified version such as `1, 2, 3, 4, or 5`.

```php
function uuid(int $version = 4, ?string $namespace = null, ?string $name = null): string 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$version` | **int** | The version of the UUID to generate (default: 4).. |
| `$namespace` | **string\|null** | The namespace for versions 3 and 5. |
| `$name` | **string\|null** | The name for versions 3 and 5. |

**Return Value:**

`string` -Return the generated UUID string.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - f the namespace or name is not provided for versions 3 or 5.

> To check if `UUID` is valid use `func()->isUuid(string, version)`

***

### escape

Escapes a string or array of strings based on the specified context.

This is particularly useful when dealing with escaping and sanitizing inputs and output to prevent cross-site scripting `(XSS)` and other injection attacks, the `escape` context parameter accept values like `html`, `js`, `css`, `url`, `attr`, and `raw` are used to specify the context in which the data is being output. Each context requires different escaping rules to ensure the output is safe.

```php
function escape(string|array $input, string $context = 'html', string|null $encoding = 'utf-8'): array|string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string&#124;array&#124;null** | The string or array of strings to be escaped. |
| `$context` | **string** | The escaper context in which the escaping should be performed (default:'html'). |
| `$encoding` | **string&#124;null** | The escape character encoding to use (default: 'utf-8'). |

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) -  When an invalid, blank encoding is provided or unsupported encoding or empty string is provided.
- [\Luminova\Exceptions\BadMethodCallException](/running/exceptions.md#badmethodcallexception) - When an invalid context is called.

**Return Value:**

`array|string` - Return the escaped string or array of strings.

**Input Argument**

- **Associative Array** `array<string, string>`: Use the keys of the array to specify the escape context for each value. Each key-value pair determines the context in which the corresponding value should be escaped.

- **Indexed Array** `array<int, string>`: Apply the default escape context to all values in the array. The default context will be used for escaping each value uniformly.

**Supported Context**

Context names and a brief explanation of each:

1. **`html`**:
   - **Purpose**: Escapes characters that could be interpreted as HTML tags or entities. It replaces characters like `<`, `>`, and `&` with their corresponding HTML entities (`&lt;`, `&gt;`, `&amp;`), ensuring that they are displayed as plain text and not interpreted as HTML.

2. **`js`**:
   - **Purpose**: Escapes characters that have special meanings in `JavaScript`, such as quotes and backslashes, to prevent injection attacks when inserting data into `JavaScript` strings or variables.

3. **`css`**:
   - **Purpose**: Escapes characters that could affect `CSS` styling or lead to `CSS` injection attacks, such as special characters in style attributes or css rules.

4. **`url`**:
   - **Purpose**: Escapes characters that are not valid in `URLs` or could break URL structure. This ensures that user-provided data included in `URL`s does not lead to unexpected behavior or vulnerabilities.

5. **`attr`**:
   - **Purpose**: Escapes characters that could interfere with `HTML` attributes, such as quotes and angle brackets, to prevent breaking out of the attribute value or introducing additional attributes.

6. **`raw`**:
   - **Purpose**: No escaping is applied; the data is output as-is. This is used when you are certain that the data is safe and does not need to be sanitized.

***

### strict

Sanitize user input by removing disallowed characters, retaining only those permitted. Unlike the `escape` function, which encodes characters for safe output, the `strict` function replaces unwanted characters in the input string with `$replacer` and returns the sanitized value if the replacer parameter is passed null and exception will be throw if input is not an expected format. 

This method is particularly useful when you need to enforce strict input validation, ensuring that only expected characters are present.

```php
function strict(string $input, string $type = 'default', string|null $replacer = ''): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | The input string to be sanitized. |
| `$type` | **string** | The expected data type (e.g., 'int', 'email', 'username'). |
| `$replacer` | **string\|null** | The symbol to replace disallowed characters or null to throw and exception (default: `blank`). |

**Return Value:**

`string` -  Return the sanitized string.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if the input does not match the expected type and no replacement is provided.

**Available Filter Types**

- **int**: Accepts only integer values.
- **numeric**: Accepts only digit values including decimals and negatives.
- **key**: Accepts only alphanumeric characters, underscores, and dashes.
- **password**: Accepts alphanumeric characters, underscores, dashes, and special characters: @, !, *, and _.
- **username**: Accepts alphanumeric characters, underscores, dashes, and periods.
- **email**: Accepts valid email addresses containing alphanumeric characters, underscores, dashes, periods, and @.
- **url**: Accepts valid URLs containing alphanumeric characters, underscores, dashes, periods, question marks, hashtags, ampersands, plus signs, equal signs, colons, slashes, and spaces.
- **money**: Accepts only numeric values, including decimals and negative numbers.
- **double**: Accepts only numeric values, including decimals.
- **alphabet**: Accepts only alphabetical characters (uppercase and lowercase).
- **phone**: Accepts only phone numbers containing numeric characters, dashes, and plus signs.
- **name**: Accepts names containing letters, digits, spaces, underscores, periods, apostrophes, and hyphens.
- **timezone**: Accepts valid timezone strings containing alphanumeric characters, underscores, dashes, commas, colons, plus signs, and spaces.
- **time**: Accepts valid time strings containing alphanumeric characters, dashes, colons, and spaces.
- **date**: Accepts valid date strings containing alphanumeric characters, dashes, colons, slashes, commas, and spaces.
- **uuid**: Accepts valid UUID strings in the format: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX.
- **default**: Removes any HTML tags from the input string.

***

### lang

Translate multiple languages with support for nested arrays.

```php
function lang(string $lookup, ?string $default = null, ?string $locale = null, array $placeholders = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$lookup` | **string** | The key to lookup in the translation files. |
| `$default` | **string&#124;null** | The default fallback translation if not found. |
| `$locale` | **string&#124;null** | The locale to use for translation (optional). |
| `$placeholders` | **array** | Matching placeholders for translation. |

**Return Value:**
`string` - Return the translated text.

**Throws:**

- [\Luminova\Exceptions\NotFoundException](/running/exceptions.md#notfoundexception) - If translation is not found and no default is provided.

> To create a language translations you need to define it in `/app/Languages/`.
> 
> The file name format should be like `Example.en.php`, where the `Example` is the context and `en` is the locale.

**See Also**

[Translations](/languages/translate.md) - See the documentation for translation methods and usages.

**Example.en.php**

```php
return [
	'error' => 'Error your example encountered error',
	'users' => [
		'notFound' => 'Username "{username}" does not exist.',
		'password' => 'Error your password {0} doesn\'t match with {1}'
	]
];
```

Assuming your application locale is already set to `EN`.

```php
echo lang('Example.error'); // Error your example encountered error
```

```php
echo  lang('Example.error.users.notFound', null, null, [
	'username' => "peterujah"
]); 
// Username "peterujah" does not exist.
```

```php
echo  lang('Example.error.users.password', null, null, [
	'12345@199',
	"12345@123"
]); 
// Error your password 12345@199 doesn't match with 12345@123
```

***

### configs

Retrieves the configurations for the specified context.
This function can only be use to return configuration array stored in `app/Configs/` directory.

```php
function configs(string $filename, ?array $default = null): ?array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The configuration filename (without extension). |
| `$default` | **array&#124;null** | The default configuration if file could not be load (default: null). |

**Return Value:**

`array|null` - Return array of the configurations for the filename, or false if not found.

***

### cache

Initialize or retrieve a new instance of the cache class.

```php
function cache(string $driver, ?string $storage = null, ?string $persistentIdOrSubfolder = null): \Luminova\Base\BaseCache
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$driver` | **string** | The cache driver to return instance of [filesystem or memcached](default: `filesystem`). |
| `$storage` | **string&#124;null** | The name of the cache storage. If null, you must call the `setStorage` method later (default: null). |
| `$persistentIdOrSubfolder` | **string&#124;null** | Optional persistent id or subfolder for storage (default: null): |

**Persistent Id Or Subfolder Param**

- For Memcached: A unique persistent connection ID. If null, the default ID from environment variables is used, or `default` if not set.
- For Filesystem Cache: A subdirectory within the cache directory. If null, defaults to the base cache directory.

**Return Value:**

`\Luminova\Base\BaseCache` - Return new instance of instance of `FileCache` or `MemoryCache` cache based on specified driver.

**Throws:**

- [\Luminova\Exceptions\ClassException](/running/exceptions.md#classexception) - If unsupported driver is specified.
- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - If there is an issue initializing the cache.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If an invalid subdirectory is provided for the filesystem cache.

***

### write_content

Write or append string contents or stream to file.

This function is an alternative for `file_put_contents`, it uses `SplFileObject` to write contents to file. 

```php
function write_content(string $filename, mixed $content, int $flag, mixed $context = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | Path to the file to write contents. |
| `$content` | **string&#124;resource** | The contents to write to the file, either as a string or a stream resource. |
| `$flag` | **int** | The value of flags can be combination of flags, joined with the binary OR (&#124;) operator.<br/>FILE_APPEND, LOCK_EX, FILE_USE_INCLUDE_PATH, FILE_APPEND,  LOCK_NB, LOCK_SH, LOCK_UN |
| `$context` | **resource** | [optional] A valid context resource created with stream_context_create. |

**Return Value:**

`boo` - Return true if successful, otherwise false on failure.

**Throws:**

- [\Luminova\Exceptions\FileException](/running/exceptions.md#fileexception) - If unable to write file.

**See Also**

[File Manager](/files/manager.md) - Also checkout the documentation for file manager methods and usages.

***

### get_content

Reads the content of a file with options for specifying the length of data to read and the starting offset.
This function is an alternative for `file_get_contents`, it uses `SplFileObject` to open the file and read its contents. 
It can handle reading a specific number of bytes from a given offset in the file.

```php
function get_content(
   string $filename, 
   int $length = 0, 
   int $offset = 0, 
   bool $useInclude = false, 
   mixed $context = null
): string|bool;
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The path to the file to be read.. |
| `$length` | **int** | The maximum number of bytes to read, if set to `0`, it read `8192` bytes at a time (default: 0). |
| `$offset` | **int** | The starting position in the file to begin reading from (default: 0). |
| `$useInclude` | **bool** | If `true`, the file will be searched in the include path (default: false). |
| `$context` | **resource** | A context resource created with `stream_context_create()` (default: null).. |

**Return Value:**

`string|false` - Returns the contents of the file as a string, or `false` on failure.

**Throws:**

- [\Luminova\Exceptions\FileException](/running/exceptions.md#fileexception) - If unable to write file.

***

### make_dir

Attempts to create the directory specified by pathname if it does not exist.

```php
function make_dir(string $path, ?int $permissions = null, bool $recursive = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The path of the directory to create. |
| `$permissions` | **int&#124;null** | Optional. The permissions for the directory. Defaults to null. |
| `$recursive` | **bool** | Optional. Whether to create directories recursively. Defaults to true. |

**Return Value:**

`bool` - Return true if files already existed or was created successfully, otherwise false.

**Throws:**

- [\Luminova\Exceptions\FileException](/running/exceptions.md#fileexception) - If unable to create directory.
- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the path is not readable.

**See Also**

[File Manager](/files/manager.md) - Also checkout the documentation for file manager methods and usages.

***

### get_temp_file

Retrieves the path for temporary files or generates a unique temporary file name.

This function can return either the path to a temporary directory or a unique temporary filename with an optional specified extension. If `local` is set to true, it will create the directory in the local writable path. If `extension`  is provided, the returned filename will have that extension.

```php
function get_temp_file(?string $prefix = null, ?string $extension = null, bool $local = false): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prefix` | **string&#124;null** | Optional prefix for the temporary filename or a new sub-directory. |
| `$extension` | **string&#124;null** | Optional file extension for the temporary filename. |
| `$local` | **bool** | Indicates whether to use a local writable path (default: false). |

**Return Value:**

`string|false` - Returns the path of the temporary directory, a unique temporary filename with the specified extension, or false on failure.

***

### validate

Validate user input fields or get a validation instance.

```php
function validate(
   ?array $inputs = null, 
   ?array $rules = null, 
   array $messages = []
): Luminova\Interface\ValidationInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$inputs` | **array&#124;null** | Input fields to validate (e.g, `$_POST`, `$_GET` or `$this->request->getBody()`). |
| `$rules` | **array&#124;null** | Validation filter rules to apply on each input field. |
| `$messages` | **array** | Validation error messages to apply on each filter on input field. |

**Return Value:**

`ValidationInterface` - Return validation instance if NULL is passed on `$inputs` or `$rules`.

**See Also**

[Input Validations](/security/validation.md) - Also checkout the documentation for input validation methods and usages.

**Sample Usages**

Assuming you have a post or get request like below:

```php 
	$inputs = [
		'name' => 'Peter',
		'email' => 'peter@example.com',
	];
```
	
Configure validation rules as below:
	
```php
	$rules = [
		'name' => 'require|alphanumeric|max(50)',
		'email' => 'require|email'
	];
	$messages = [
		'name' => [
			'require' => 'Name is required',
			'alphanumeric' => 'Invalid name character',
			'max' => 'Invalid name cannot be more than 50 character'
		],
		'email' => [
			'require' => 'Email is required',
			'email' => 'Invalid email address "{value}"'
		]
	];
```
Validating request against rules.

```php
	$validation = validate($inputs, $rules, array $messages);

	if($validation->isPassed()){
		echo 'Success';
	}else{
		echo 'Error: ' . $validation->getErrorLine();
		var_export($validation->getErrors());
	}
```

***

### start_url

Get the start URL with an optional suffix and port hostname if available.
This function generates the base URL of the application, optionally appending a provided suffix. It includes the port hostname if available.

```php
function start_url(?string $suffix = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$suffix` | **string** | Optional suffix to append to the start URL (default: null). |

**Return Value:**

`string` Return the generated start URL of your project.

**Example** 
If your application path is like: `/Some/Path/To/htdocs/my-project-path/public/`.
It returns depending on your development environment and suffix if specified:

- `https://localhost:8080`
- `https://localhost/my-project-path/public/`
- `https://localhost/public`
- `https://example.com:8080`
- `https://example.com/`

> If the application is in production (`PRODUCTION`), it returns the base application URL (`APP_URL`) with the optional suffix.
> If not in production, it constructs the URL using the server's `hostname` and the `PROJECT_ID`, and appends the optional suffix.

***

### absolute_url

Convert an application-relative path to an absolute URL.
This function converts a given application-relative path to its corresponding absolute URL.

```php
function absolute_url(string $path): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The relative path to convert to an absolute URL. |

**Return Value:**

`string` - Return the absolute URL of the specified path.

**Examples:**

If on development environment.

```php
echo absolute_url('/Applications/XAMPP/htdocs/project-base/public/asset/files/foo.text');

//Output: http://localhost/project-base/public/asset/files/foo.text.
```

If on projection environment.

```php
echo absolute_url('/example.com/www/public/asset/files/foo.text');

//Output: http://example.com/asset/files/foo.text.
```

***

### ip_address

Retrieve the user's IP address or obtain IP address information using a third-party API service.

```php
function ip_address(bool $ipInfo = false, array $options = []): string|object|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ipInfo` | **bool** | If true, returns IP address information; otherwise, returns the IP address. |
| `$options` | **array** | Additional options to pass with IP information. |

**Return Value:**

`string|object|null` - The IP address if `$ipInfo` is false, else return IP address information or null if IP address cannot be determined.

> Utilize a third-party API to fetch IP address information. Your API configuration can be done in `/app/Config/IPConfig.php`.

***

### get_class_name

Get class basename from namespace or class object.

```php
function get_class_name(string|object $from): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$from` | **string&#124;class-object** | The namespace or class object. |

**Return Value:**

`string` - Return the class basename.

> *Note:* This method is not same as PHP `get_class`, this method return the base name of a class no full qualified class.

***

### get_mime

Detect the MIME type of a file or raw data (e.g `text/plain`).

If the input string is a path to an existing file, it uses `finfo_file()`, otherwise it treats the input as raw binary and uses `finfo_buffer()`.

```php
function get_mime(string $input, ?string $magicDatabase = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | File path or raw binary string to extract mime from. |
| `$magicDatabase` | **string\|null** | Optional path to a custom magic database (e.g, `\path\custom.magic`). |

**Return Value:**

`string|false` -Return the detected MIME type, or false if detection fails.

***

### get_column

Return the values from a single column in the input array or an object.

```php
function get_column(array|object $from, string|int|null $property, string|int|null $index = null): array 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$from` | **array&#124;object** | Array or an object to extract column values from. |
| `$property` | **null&#124;string&#124;int** | The column property key to extract. |
| `$index` | **null&#124;string&#124;int** | An optional column to use as the index/keys for the returned array. |

**Return Value:**

`array` - Returns an array of values representing a single column from the input array or object.

> This function is a wrapper for  `PHP` `array_column`, but with additional optimization to handle both `object` or an `array` column.

***

### filter_paths

Filter the display path, to remove private directory paths before previewing to users.

```php
function filter_paths(string $path): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The path to be filtered. |

**Return Value:**

`string` - Return the filtered path.

> This method is useful when you want to throw an exceptions, use it to remove your server path and return only path which should be seen by users.
 
***

### to_array

Convert an object to an array.

```php
function to_array(mixed $input): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **mixed** | The object to convert to an array. |

**Return Value:**

`array` - The finalized array representation of the object.

***

### to_object

Convert an array or string list to a `JSON` object.

```php
function to_object(array|string $input): object|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **array&#124;string** | The array or string list to convert. |

**Return Value:**

`object|false` - Return JSON object, otherwise false.

***

### array_extend_default

This function is designed to merge two arrays, where the first array (`$default`) represents default values, and the second array (`$new`) contains updated or new values. The function recursively merges arrays for keys that exist in both arrays but leaves the default values untouched if no corresponding value is found in `$new`.

```php
function array_extend_default(array $default, array $new): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$default` | **array** | The default options array. |
| `$new` | **array** | The new options array to merge. |

**Return Value:**

`array` - Return the merged options array with defaults preserved.

**Usage Examples:**

```php
$default = ['a' => 1, 'b' => 2, 'c' => 3];
$new = ['b' => 20, 'd' => 4];

$result = array_extend_default($default, $new);
print_r($result);
```

**Output:**

```bash
Array
(
   [a] => 1
   [b] => 2
   [c] => 3
   [d] => 4
)
```

***

### array_merge_recursive_replace

This function is designed to merge multiple arrays recursively, but with a replacement strategy. If two arrays have the same key, the values in the second array will overwrite those in the first array. For numeric keys, it appends the values only if they don't already exist in the array.

```php
public array_merge_recursive_replace(array ...$array): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `...$array` | **array** | The array arguments to merge. |

**Return Value:**

`array` - Return the merged result array.

**Usage Examples:**

```php
$array1 = ['a' => 1, 'b' => 2, 'c' => 3];
$array2 = ['b' => 20, 'd' => 4];

$result = array_merge_recursive_replace($array1, $array2);
print_r($result);
```

**Output:**

```bash
Array
(
   [a] => 1
   [b] => 20
   [c] => 3
   [d] => 4
)
```

***

### array_merge_recursive_distinct

This function is designed to merge two arrays (`$array1` and `$array2`) in a way that is distinct from PHP's native array_merge_recursive. It avoids combining values under the same key as an array and instead replaces existing values if needed.

```php
public array_merge_recursive_distinct(array &$array1, array &$array2): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$array1` | **array<string&#124;int,mixed>** | The array to merge into, passed by reference. |
| `$array2` | **array<string&#124;int,mixed>** | The array to merge from, passed by reference. |

**Return Value:**

`array` - Return the merged array with unique values.

**Usage Examples:**

```php
$array1 = ['a' => 1, 'b' => 2];
$array2 = ['b' => 20, 'c' => 3];

$result = array_merge_recursive_distinct($array1, $array2);
print_r($result);
```

**Output:**

```bash
Array
(
   [a] => 1
   [b] => 20
   [c] => 3
)
```

***

### array_merge_result

Merges a response into the provided results variable while optionally preserving the structure of nested arrays.

This function checks the type of the `results` variable and appropriately merges or appends 
the `response` based on its type. It supports various scenarios:

- If `results` is null, it assigns `response` to it.
- If `results` is an array and `preserveNested` is true, it adds the `response` as a nested element if `response` is an array, or appends `response` directly otherwise.
- If `results` is an array and `preserveNested` is false, it merges `results` and `response` directly when `response` is an array.
- If `results` is not an array (like a string or scalar), it converts `results` into an array  and merges or appends the `response` based on the `preserveNested` flag.

```php
public array_merge_result(mixed &$results, mixed $response, bool $preserveNested = true): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `&$results` | **mixed** | The results variable to which the response will be merged or appended to, passed by reference. |
| `$response` | **mixed** | The response variable to merge with results. It can be an array, string, or other types. |
| `$preserveNested` | **bool** | Optional. Determines whether to preserve the nested structure of arrays when merging (default: true). |

**Usage Examples:**

```php
$results = null;

// Case 1: Merging a new string response.
$response1 = 'Task 1 completed';
array_merge_result($results, $response1);
var_export($results);

// Case 2: Merging an array response while preserving the nested structure.
$response2 = ['Task 2 completed'];
array_merge_result($results, $response2);
var_export($results);

// Case 3: Merging another array response without preserving nested structure.
$response3 = ['Task 3 completed', 'Task 4 completed', (object)[1,2,3]];
array_merge_result($results, $response3, false);
var_export($results);
```

**Output:**

```php
// Case 1: Output
'Task 1 completed'

// Case 2: Output
array (
   0 => 'Task 1 completed',
   1 => array (0 => 'Task 2 completed'),
)

// Case 3: Output
array (
   0 => 'Task 1 completed',
   1 =>  array (0 => 'Task 2 completed'),
   2 => 'Task 3 completed',
   3 => 'Task 4 completed',
   4 => (object) array(
      '0' => 1,
      '1' => 2,
      '2' => 3,
   ),
)
```

***

### string_length

Calculate string length based on different `charsets`.

```php
function string_length(string $content, ?string $charset = null): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The content to calculate length for. |
| `$charset` | **string&#124;null** | The character set of the content. |

**Return Value:**

`int` - Returns the string length.

***

### kebab_case

Convert a string to kebab case.

```php
function kebab_case(string $input, bool $lower = true): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | String to convert to kebab cased. |
| `$lower` | **bool** | Should convert to lower case (default: true). |

**Return Value:**

`string` - Return the kebab-cased string.

***

### camel_case

Convert a string to camel case.

```php
function  camel_case(string  $input): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | The string to convert |

**Return Value:**

`string` - Return string. as camel cased.

***

### pascal_case

Convert a string to PascalCase format.

Replaces spaces, underscores, and hyphens with word boundaries, capitalizes each word, and removes all delimiters.

```php
function  pascal_case(string $input): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | The input string to convert. |

**Return Value:**

`string` - Return the PascalCase version of the input string.

***

### which_php

Find the PHP script executable path.

```php
function which_php(): ?string
```

**Return Value:**

`string|null` - Return PHP executable path or null.

***

### status_code

Gets request status code from executed command or controller method.

```php
function status_code(mixed $result = null, bool $returnInt = true): int|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$result` | **mixed** | response from callback function or controller |
| `$returnInt` | **bool** | Return type (default: int) |

**Return Value:**

`int|bool` - Return the status code `int` or `bool`.

> If passed result is `boolean` (false), `integer` (1) or `NULL`, the method  will return `STATUS_ERROR`.
> 
> If passed result is `boolean` (true), `integer` (0), `VOID` or any other values, the method will return `STATUS_SUCCESS`.

***

### http_status_header

Sets the HTTP response status code and sends appropriate headers.

This function sets the HTTP status code and sends the corresponding status message header.
If the status code is not predefined, it returns `false`. For predefined status codes, it sends headers including the status message. The function determines the HTTP protocol version based on the server's protocol.

```php
function http_status_header(int $status): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | The HTTP status code to set (e.g., 200, 404). |

**Return Value:**

`bool` - Returns true if the status code is found in the predefined list and headers are set, otherwise false.

***

### text2html

Converts text characters in a string to HTML entities.

```php
function  text2html(string|null $text): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string&#124;null** | A string containing the text to be processed. |

**Return Value:**

`string` - Return processed text with HTML entities.

***

### nl2html

Converts newline characters in a string to HTML entities.

```php
function  nl2html(string|null $text): string
```

> This is useful when you want to display text in an HTML textarea while preserving the original line breaks.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string&#124;null** | A string containing the text to be processed. |

**Return Value:**

`string` - Return processed text with HTML entities.

***

### uppercase_words

Capitalize the first letter of each word in a string.

Preserves underscores, hyphens, and spaces as delimiters, and capitalizes the letter that follows each one.

```php
function uppercase_words(string $string): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The input string to convert. |

**Return Value:**

`bool` - Return the input string with the first character of each word capitalized.

**Usage Example:**

```php
echo uppercase_words('foo-bar_baz');
// Output: // Foo-Bar_Baz
```

**More Patterns:**

```php
$strings = [
   'foo-bar_baz'
   'hello-world_this_is_a_test'
   'example'
   '_leading_and_trailing_'
   '-leading-plus-trailing-'
   'multiple---dashes_and__underscores'
   'foo-bar baz_baz'
];

$results = array_map('uppercase_words', $strings);

// Print the results
echo implode("\n", $results);
```

**Output:**

```md
Foo-Bar_Baz
Hello-World_This_Is_A_Test
Example
_Leading_And_Trailing_
-Leading-Plus-Trailing-
Multiple-Dashes_And_Underscores
Foo-Bar Baz_Baz
```

***

### has_uppercase

Checks if a given string contains an uppercase letter.

```php
function has_uppercase(string $string): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The string to check uppercase. |

**Return Value:**

`bool` - Returns true if the string has uppercase, false otherwise.

***

### list_to_array

Convert a string list to an array.

```php
function list_to_array(string $list): array|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$list` | **string** | The string list to convert. |

**Return Value:**

`array|false` - The resulting array or false on failure.

> In Luminova, a string `list` typically consist of comma-separated values, which may or may not be enclosed in quotes.
> 
> Examples: `Foo, Bar, Baz`, `"Foo", "bar", "Baz"`
 
***

### list_in_array

Check if string list exists, matched an array.

```php
function list_in_array(string $list, array $array = []): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$list` | **string** | The string list to check. |
| `$array` | **array** | The array to search for list value. |

**Return Value:**

`bool` - Return true if the list exists in the array, otherwise false.

```php 
$list = "Foo, Bar, Baz";

list_in_array($list, ['Foo', 'Bra']); // false

list_in_array($list, ['Foo', 'Baz', 'Bar']); // true
```

> If any of the list value doesn't exist in the array, it will return false.

***

### is_list

Test if a string is in a valid list format.

```php
function is_list(string $input, bool $trim = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | The string to check. |
| `$trim` | **bool** | Whether to trim whitespace around the values. |

**Return Value:**

`bool` - Return true if the string is in a valid list format, false otherwise.

***

### is_platform

Tells which operating system platform your application is running on.

**Predefine OS Values:**

- `mac` - For macOS.
- `windows` - For Windows OS.
- `linux` - For Linux OS.
- `freebsd` - For FreeBSD OS.
- `openbsd` - For OpenBSD OS.
- `bsd` - For BSD OS.
- `solaris` - For Solaris OS.
- `aws` - For AWS OpsWorks.
- `azure` - For Azure environment.
- etc...

```php
function is_platform(string $os): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$os` | **string** | The platform name to check. |

**Return Value:**

`bool` - Return true if the application is running on the specified platform, false otherwise.

***

### is_tor

Determines if the provided IP address corresponds to a Tor exit node.

```php
function is_tor(?string $ip = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip` | **string&#124;null** | The IP address to check. If not provided, the current IP address is used. |

**Return Value:**

`bool` - Return true if the IP address is a Tor exit node, false otherwise.

***

  
### is_empty

Check if values are empty.

```php
function is_empty(mixed  $values): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$values` | **mixed** | The values to check. |

**Return Value:**

`bool` - True if any of the values are empty, false otherwise.

> This function treats 0 as non-empty. 
> 
> If you want different behavior, use the PHP `empty()` function instead.

***

### is_nested

Determine if an array is a nested array.
If recursive is false it only checks one level of depth.

```php
function is_nested(array $array, bool $recursive = false): bool 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$array` | **array** | The array to check. |
| `$recursive` | **bool** | Determines whether to check nested array values (default: false). |

**Return Value:**

`bool` - Return true if the array is nested, false otherwise.

> **Note:**
> An empty array is considered as none nested array, so it will return false.

***

### is_associative

Determine if an array is associative.

```php
function is_associative(array $array): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$array` | **array** | The array to check. |

**Return Value:**

`bool` - True if the array is associative, false otherwise.

  
***

### is_command

Check if the application is running in command-line interface `CLI` mode.

```php
function is_command(): bool
```

**Return Value:**
`bool` - Returns true if the execution was made in `CLI` mode, false otherwise.

***

### is_dev_server

Check if the application is running locally on any development environment.

```php
function is_dev_server(): bool
```

**Return Value:**

`bool` - Returns true if the application is running on the development server, false otherwise.

> This function utilizes server variable `SERVER_NAME` to check the server name matches any of `127.0.0.1` `::1` `localhost`.
>
> Additionally it checks a custom Luminova server variable `NOVAKIT_EXECUTION_ENV` which is set when using builtin `PHP` development server.

***

### is_blob

Determine whether the type of a variable is blob.

```php
function is_blob(mixed $value): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | The value to check. |

**Return Value:**

`bool` - Return true if the value is a blob, false otherwise.

***

### is_utf8

Determine weather a given string is `UTF-8` encoded.

```php
function is_utf8(string $input): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** | The string to check for UTF-8 encoding. |

**Return Value:**

`bool` - Returns true if the string is UTF-8 encoded, false otherwise.

***

### function_exists_cached

Checks if a function exists and caches the result to avoid repeated checks.

This function uses a static cache to store whether a function exists or not.
If the function's existence has been checked before, the cached result is returned.
Otherwise, it checks the function's existence using `function_exists()` and caches the result, improving performance by avoiding repeated function existence checks.

```php
function function_exists_cached(string $function): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$function` | **string** | The name of the function to check for existence. |

**Return Value:**

`bool` - Returns true if the function exists, false otherwise.

***

### set_function

Executes a PHP function dynamically, checking if it's disabled before execution.

This function is useful when you need to call a PHP function dynamically, but you want to ensure that the function is not disabled.

```php
function set_function(string $function, mixed ...$arguments): mixed 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$function` | **string** | The name of the PHP function to execute. |
| `$arguments` | **mixed** | Any optional arguments to pass to the PHP function. |

**Return Value:**

`mixed` - Return the result of the executed PHP function, or false if the function is disabled.

***Example:***

Call the 'set_time_limit' function dynamically:

```php
$limit = set_function('set_time_limit', 300);

if($limit === false){
   echo "Execution limit is disabled";
}
```

***

### set_max_execution_time

Sets the script's maximum execution time if the provided timeout exceeds the current limit.

This function checks the current `max_execution_time` and compares it with the provided timeout. If the timeout is greater than the current limit and the `set_time_limit` function is not disabled, it updates the execution time to the new value.

```php
function set_max_execution_time(int $timeout): bool 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timeout` | **int** | The maximum execution time in seconds. |

**Return Value:**

`bool` - Returns true if the execution time is successfully set, false otherwise.