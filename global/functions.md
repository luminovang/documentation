# Helper Functions

***

## Overview

Global helper functions are collections of procedural functions that provide commonly used functionality across different parts of your application enhancing code reusability and productivity.

***

## Introduction

Global helper functions are procedural functions that provide commonly used functionality across different parts of your application. They are typically defined at the global scope, making them easily accessible from anywhere within the codebase.

#### Code Reusability

By encapsulating commonly used operations into helper functions, developers can avoid repetitive code and promote code reuse.

#### Productivity

Helper functions can significantly improve development efficiency by providing pre-built solutions to common problems. Instead of reinventing the wheel each time a similar task needs to be performed.

#### Abstraction of Complexity

Helper functions abstract away the complexity of certain operations, making the code-base more readable and maintainable. By encapsulating logics within well-named functions, developers can focus on the high-level functionality of their code without getting bogged down in implementation details.

***

## Functions

### app

Get application container class shared instance or new instance if not shared.

```php
function app(): \Application
```

**Return Value:**

`\Application` - Return base application instance.

**See Also**

[Base Application](/base/application.md) - See the documentation for base application methods and usages.

***

### request

Get request object

```php
function request(bool $shared = true): ?\Luminova\Http\Request
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$shared` | **bool** | Return a shared instance (default: true). |

**Return Value:**

`class-object<Request>||null` - Return request object or null.

**See Also**

[HTTP Request](/http/request.md) - See the documentation for http request methods and usages.

***

### session

Return session data if key is present, otherwise return the session instance.

```php
function session(string|null $key = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | The key to retrieve the data. |

**Return Value:**

`mixed` - The session data if key is provided, otherwise the session class instance.

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

Returns a shared instance of a class in the factory or a factory instance if the context is null.

```php
function factory(class-string|string|null $context = null, mixed ...$arguments): ?object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$context` | **class-string&#124;string&#124;null** | The factory class name or alias (e.g: `Task::class` or `task`). |
| `$arguments` | **mixed** | Additional parameters to pass to class constructor.<br> - The Last parameter to pass to the factory constructor indicate if it should return a shared instance. |

**Available Context**

-   'task'      `\Luminova\Time\Task`
-   'session'   `\Luminova\Sessions\Session`
-   'functions' `\Luminova\Application\Functions`
-   'modules'   `\Luminova\Library\Modules`
-   'language'  `\Luminova\Languages\Translator`
-   'logger'    ` \Luminova\Logger\Logger`
-   'files'     `\Luminova\Application\FileSystem`
-   'validate'  `\Luminova\Security\InputValidator`
-   'response'  `\Luminova\Template\ViewResponse`
-   'services'  `\App\Controllers\Config\Services`
-   'request'   `\Luminova\Http\Request`

**Return Value:**

`class-object|Factory|null` - Return instance of factory class, instance of class called, otherwise null.

**Example**

This example shows how to reinitialize factory class instance with a new constructor argument.

```php
<?php 
$session = factory('session', new SessionManager(), false);
```

> *Note:* The last argument must be a `bool`, indicating whether to return a shared instance or not.
> If your class constructor accepts `boolean` argument, then you must add extra `boolean` to the factory constructor function to indicate shared or not shared instance.
 
 **See Also**

[Application Factory](/app/factory.md) - See the documentation for factory methods and usages.
 
***

### service

Returns a shared instance of a class in services or a service instance if the context is null.

```php
function service(?string $service = null, mixed ...$arguments): ?object
```

*Same as:*

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$service` | **class-string&#124;string&#124;null** | The service class name or alias. |
| `$arguments` | **mixed** |Additional parameters to pass to class constructor.<br/>The two bool argument indicate wether to return a shared instance or serialize and store as cache. |

**Return Value:**

`class-object|Services|null` - Return service class instance or instance of called class.

> *Note:*
> Indicate 2 last `bool` argument if you want to return a shared instance and serialize class and store for later use.
> 
> Example:  `(my, param, foo, true, false, [shared_bool] [serialize_bool])`
> 
> By default shared is `true` while serialize is `false`.

 **See Also**

[Application Service](/app/service.md) - See the documentation for service methods and usages.

***

### remove_service

Delete a service or clear all services.

```php
function remove_service(string|null  $service = null): bool
```

> If `NULL` is passed all cached and serialized services will be cleared.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$service` | **class-string&#124;string** | The class name or alias, to delete and clear it cached. |

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

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) -Throws if layout file is not found.

**See Also**

[Template Layout](/templates/layout.md) - See the documentation for default `PHP` template layout methods and usages.

 > All layouts must be stored in `resources/views/layout/` directory.
 > Examples: `layout('foo')` or `layout('foo/bar/baz')`

***

### response

Initiate a view response object and render any response content.
It allows you to render content of any format in view or download content within the view controller.

```php
function response(int $status = 200, bool $encode = true): \Luminova\Template\ViewResponse
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | HTTP status code (default: 200 OK) |
| `$encode` | **bool** | Enable content encoding like gzip, deflate. |

**Return Value:**

`ViewResponse` - Return vew response object. 

**See Also**

[View Response](/templates/response.md) - Also checkout the documentation for view response methods and usages.

***

### func

Return instance a specific context if specified, otherwise get the instance `BaseFunction`.

```php
function func(string|null $context = null, mixed $params): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$context` | **string&#124;null** | The context to return instance for. |
| `$params` | **mixed** | Additional parameters to be passed to context. |

**Supported contexts:**

 -   ip,
 -   document,
 -   escape,
 -   tor,
 -   math.

**Return Value:**

`mixed` - Returns an instance of Functions, object, string, or boolean value depending on the context.

**Throws:**

- [\Luminova\Exceptions\Exception](/exceptions/classes.md#exception) - If an error occurs.
- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - If unable to initialize method.

**See Also**

[Base Functions](/base/functions.md) - Learn more about the Luminova's `BaseFunction` documentation.

***

### browser

Get the information about user's browser, based on the user-agent string.

```php
function browser(?string $user_agent = null, string $return = 'object', bool $shared = true): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$user_agent` | **string&#124;null** | The user agent string to analyze. |
| `$return` | **bool** | Set the return type, if `instance` return userAgent class object otherwise return array or json object.<br />-   Return Types: [`array`, `object`, `instance`] |
| `$shared` | **bool** | Allow shared instance creation (default: true). |

**Return Value:**

`array<string,mixed>|object<string,mixed>|UserAgent|false` - An array, object containing browser information or user-agent class instance.

 **See Also**

[User Agents](/http/user-agent.md) - See the documentation for browser user-agent methods and usages.

***

### href

Create a hyperlink to another view or file.

```php
function href(string $view = ''): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$view` | **string** | To view or file. |

**Return Value:**

`string` - Return hyperlink of view or base controller if blank string is passed.

***

### asset

Create a link to assets folder file.

```php
function asset(string $filename = ''): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | Filename or path. |

**Return Value:**

`string` - Return assets file or base asset folder if blank string is passed.

***

### root

Get the application root directory of your project.
Optionally pass a path to append the the root directory, all return path will be converted to `unix` or `windows` directory separator style.

```php
function root(string $suffix = ''): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$suffix` | **string** | Append a path to the root directory. |

**Return Value:**

`string` - Return path to the root directory with the suffix appended.

***

### path

Get system or application path, compatible with `unix` or `windows` directory separator style.

```php
function path(string $file): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **string&#124;null** | The path file name to return.<br />Possible values: 'system', 'plugins', 'library', 'controllers', 'writeable', 'logs', 'caches',<br />'public', 'assets', 'views', 'routes', 'languages', 'services'. |

**Return Value:**

`string` - Return directory path, windows, unix or windows style path. 

**See Also**

[File Manager](/files/manager.md) - Also checkout the documentation for file manager methods and usages.

***

### env

Get environment variables from `env` file, its a wrapper that combines both `$_ENV`, `$_SERVER` and `getenv`, with an additional feature to ensure the accuracy of the return type and default value if the key was not found in the environment.

```php
function env(string  $key, mixed  $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to retrieve. |
| `$default` | **mixed** | The default value to return if the key is not found. |

**Return Value:**

`mixed` - The value of the environment variable or the default value if not found.

**See Also**

[ENV Variables](/running/env.md) - Learn more from documentation about Luminova's `env` variable keys.

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
| `$append_to_env` | **bool** | Save or update to .env file |

**Return Value:**

`bool` - True on success or false on failure.

> By default when you set an environment variable, it doesn't permanently stored in other to access it in other codebase.
>
> If you wish to write the key and value in your environment variables file `.env`, then you must specify the third argument `true`. 
>
> More efficient method of doing this is by utilizing the [NovaKit](/commands/novakit.md) cli command to write the key and value to `env` file by running command `php novakit env:add --key="my_new_key" --value="my key value"`.

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

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - If the library could not be found

**Return Value:**

`bool` - Return true if the library was successfully imported.

> You must place your external libraries in `libraries/libs/ directory` in other to use file import.

***

### logger

Log a message at the given level.

```php
function logger(string $level, string $message, array $context = []): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$level` | **string** | The log level.<br />- Log levels ['emergency, alert, critical, error, warning, notice, info, debug, exception, php_errors'] |
| `$message` | **string** | The log message. |
| `$context` | **array** | Additional context data (optional). |

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception)

> All loges are located in `/writeable/log/`, each log level has it own file name (e.x., `warning.log`).
>
> By default all logging will use your preferred `PSR` logger class specified in `App\Controllers\Config\Preference`.

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

### escape

Escapes a string or array of strings based on the specified context.

```php
function escape(string|array $input, string $context = 'html', string|null $encoding = null): array|string 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string&#124;array** | The string or array of strings to be escaped.<br /> *example*<br/>- array&lt;string, string&gt; - Use the key as the context.<br />- array&lt;int, string&gt; Use the default context for all values. |
| `$context` | **string** | The context in which the escaping should be performed. Defaults to 'html'.<br />Possible values: 'html', 'js', 'css', 'url', 'attr', 'raw'. |
| `$encoding` | **string&#124;null** | The character encoding to use. Defaults to null. |

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - When an invalid or blank encoding is provided.
- [\Luminova\Exceptions\BadMethodCallException](/exceptions/classes.md#badmethodcallexception) - When an invalid context is called
- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - When the string is not valid UTF-8 or cannot be converted.

**Return Value:**

`array|string ` - The escaped string or array of strings.

> You can optionally specify the context name `$context` in the array key when you want to escape array values.

***

### strict

Sanitize user input, unlike the `escape` function, this method replaces user input, retaining only the allowed characters.

```php
function strict(string $input, string $type = 'default', string $replacer = ''): string
```

> It removes unwanted characters from a given string and return only allowed characters.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string** |  Input to format |
| `$type` | **string** | The expected filter type. |
| `$replacer` | **string** | Replace with default is blank string. |

**Filters Types**

- **int**: Accepts only integer values.
- **digit**: Accepts only digit values including decimals and negatives.
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

**Return Value:**

`string` -  Return sanitized string.

***

### lang

Translate multiple languages with support for nested arrays.

```php
function lang(string $lookup, string|null $default = null, string|null $locale = null, array $placeholders = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$lookup` | **string** | The key to lookup in the translation files. |
| `$default` | **string&#124;null** | Fallback translation if not found. |
| `$locale` | **string&#124;null** | The locale to use for translation (optional). |
| `$placeholders` | **array** | Matching placeholders for translation.<br />- *examples*<br/> array ['Peter', 'peter@foo.com'] "Error name {0} and email {1}"<br />- array ['name' => 'Peter', 'email' => 'peter@foo.com'] "Error name {name} and email {email}" |

**Return Value:**
`string` - The translated text.

**Throws:**

- [\Luminova\Exceptions\NotFoundException](/exceptions/classes.md#notfoundexception) - If translation is not found and no default is provided.

> To create a language translations you need to define it in `/app/Controllers/Languages/`.
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
<?php
echo  lang('Example.error'); // Error your example encountered error
```

```php
<?php
echo  lang('Example.error.users.notFound', null, null, [
	'username' => "peterujah"
]); 
// Username "peterujah" does not exist.
```

```php
<?php
echo  lang('Example.error.users.password', null, null, [
	'12345@199',
	"12345@123"
]); 
// Error your password 12345@199 doesn't match with 12345@123
```

***

### write_content

Write or append contents to a file.

```php
function write_content(string $filename, string|resource  $content, int  $flag, resource $context = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | Path to the file where to write the data. |
| `$content` | **string&#124;resource** | The contents to write to the file, either as a string or a stream resource. |
| `$flag` | **int** | The value of flags can be combination of flags, joined with the binary OR (&#124;) operator.<br/>FILE_APPEND, LOCK_EX, FILE_USE_INCLUDE_PATH, FILE_APPEND,  LOCK_NB, LOCK_SH, LOCK_UN |
| `$context` | **resource** | [optional] A valid context resource created with stream_context_create. |

**Return Value:**

`boo` - Return true on success, false on failure.

**Throws:**

- [\Luminova\Exceptions\FileException](/exceptions/classes.md#fileexception) - If unable to write file.

**See Also**

[File Manager](/files/manager.md) - Also checkout the documentation for file manager methods and usages.

***

### make_dir

Attempts to create the directory specified by pathname if it does not exist.

```php
function make_dir(string  $path, int|null  $permissions = null, bool  $recursive = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The path of the directory to create. |
| `$permissions` | **int&#124;null** | Optional. The permissions for the directory. Defaults to null. |
| `$recursive` | **bool** | Optional. Whether to create directories recursively. Defaults to true. |

**Return Value:**

Returns true if the directory existed or was created, otherwise false.

**Throws:**

- [\Luminova\Exceptions\FileException](/exceptions/classes.md#fileexception) - If unable to create directory.
- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - If the path is not readable.

**See Also**

[File Manager](/files/manager.md) - Also checkout the documentation for file manager methods and usages.

***

### validate

Validate user input fields or get a validation instance.

```php
function validate(array|null $inputs, array|null $rules, array $messages = []): Luminova\Interface\ValidationInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$inputs` | **array&#124;null** | Input fields to validate.<br />@example [$_POST, $_GET or $this->request->getBody()] |
| `$rules` | **array&#124;null** | Validation filter rules to apply on each input field.<br />@example ['email' => 'required&#124;email&#124;max&#124;min&#124;length'] |
| `$messages` | **array** | Validation error messages to apply on each filter on input field.<br />@example [<br /> 'email' => [<br /> 'required' => 'email is required',<br /> 'email' => 'Invalid [value] while validating [rule] on [field]'<br /> ]<br />] |

**Return Value:**

`ValidationInterface` - Return validation instance if NULL is passed on `$inputs` or `$rules`.

**See Also**

[Input Validations](/security/validation.md) - Also checkout the documentation for input validation methods and usages.

**Sample Usages**

```php 
<?php 
	$inputs = [
		'name' => 'Peter',
		'email' => 'peter@example.com',
	];
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
	]

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

Get start url with port hostname suffix if available.

```php
function start_url(string $suffix = ''): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$suffix` | **string** | Optional pass a suffix to the start url. |

**Return Value:**

`string` Return public start URL with port if available.

***

### ip_address

Retrieve the user's IP address or obtain IP address information using a third-party API service.

```php
function ip_address(bool $ip_info = false, array $options = []): string|object|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ip_info` | **bool** | If true, returns IP address information; otherwise, returns the IP address. |
| `$options` | **array** | Additional options to pass with IP information. |

**Return Value:**

`string|object|null` - The IP address if `$ip_info` is false, else return IP address information or null if IP address cannot be determined.

> Utilize a third-party API to fetch IP address information. Your API configuration can be done in `/app/Controllers/Config/IPConfig.php`.

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
function to_array(mixed  $input): array
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

### which_php

Find the PHP script executable path.

```php
function which_php(): string|null
```

**Return Value:**

`string|null` - Return PHP executable path or null.

***

### status_code

Gets request status code from executed command or controller method.

```php
function status_code(mixed $result = null, bool $return_int = true): int|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$result` | **mixed** | response from callback function or controller |
| `$return_int` | **bool** | Return type (default: int) |

**Return Value:**

`int|bool` - Return the status code `int` or `bool`.

> If passed result is `boolean` (false), `integer` (1) or `NULL`, the method  will return `STATUS_ERROR`.
> 
> If passed result is `boolean` (true), `integer` (0), `VOID` or any other values, the method will return `STATUS_SUCCESS`.

***

### text2html

Converts text characters in a string to HTML entities.

```php
function  text2html(string|null  $text): string
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
function  nl2html(string|null  $text): string
```

> This is useful when you want to display text in an HTML textarea while preserving the original line breaks.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string&#124;null** | A string containing the text to be processed. |

**Return Value:**

`string` - Return processed text with HTML entities.

***

### has_uppercase

Checks if a given string contains an uppercase letter.

```php
function has_uppercase(string $string): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The string to check uppercase.. |

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
<?php
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

Tells which platform your application is running on.

```php
function is_platform(string $os): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$os` | **string** | The platform name (e.g., 'mac', 'windows', 'linux', 'freebsd', 'openbsd', 'solaris', 'aws', etc.). |

**Return Value:**

`bool` - Return true if the application is running on the specified platform, false otherwise.

***

### is_tor

Determines if the provided IP address corresponds to a Tor exit node.

```php
function is_tor(string|null $ip = null): bool
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

```php
function is_nested(array $array): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$array` | **array** | The array to check. |

**Return Value:**

`bool` - Return true if the array is nested, false otherwise.

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