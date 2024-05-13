# Input Validation

***

## Overview

With the input Validation class, you can validate the user-submitted data before saving it to the database. To do that you will have to set validation rules for each input field and optional error me


***

## Introduction

The Input Validation class in the Luminova Framework provides a robust mechanism for validating user-submitted data before processing or storing it in a database. With this class, you can define validation rules for each input field along with optional error messages, ensuring that the data meets your application's requirements and standards.

### Validation Rules

You can set validation rules for input fields using a variety of predefined validation rules, including required fields, maximum and minimum lengths, alphanumeric characters, email addresses, integers, and more. Rules can be added individually or set from an array, allowing for flexible configuration. Additionally, custom validation rules can be defined using callback functions.

### Defining Rules

Validation rules are specified by separating each rule with a pipe `|` and providing parameters in parentheses when needed. Rules can be applied to individual fields or set globally using associative arrays, with the option to specify custom error messages for each rule.

### Error Message Formatting

Error messages can include placeholders such as [field], [value], and [rule] to provide dynamic information about the validation error. This allows for more informative and context-aware error messages, improving the user experience.

***

* Class namespace: `\Luminova\Security\InputValidator`
* This class implements:
[\Luminova\Interface\ValidatorInterface](/interface/validator-interface.md)

***

## Properties

Define your validation rules.

```php
public array $rules
```
Define your validation error messages per rule.

```php
public array $messages
```

***

## Methods

### validate

Validate input data against the defined rules.

```php
public validate(array&lt;string,mixed&gt; $input, array&lt;int,mixed&gt; $rules = []): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **array<string,mixed>** | Input data to validate. |
| `$rules` | **array<int,mixed>** | Optional array of rules to override default rules. |

**Return Value:**

`bool` - Returns true if all rules are passed, otherwise false.

***

### getErrors

Get validation error messages.

```php
public getErrors(): array
```

**Return Value:**

`array` - Validation error messages.

***

### getError

Get the error message for a specific field.

```php
public getError(string $field): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string** | Name of the field. |

**Return Value:**

`string` - Error message for the specified field.

***

### getErrorField

Get the field causing the validation error.

```php
public getErrorField(string $field): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string** | Name of the field. |

**Return Value:**

`string` - Field causing the validation error.

***

### setRules

Set validation rules with optional error messages.

```php
public setRules(array&lt;int,string&gt; $rules, array&lt;string,mixed&gt; $messages = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$rules` | **array<int,string>** | Validation rules. |
| `$messages` | **array<string,mixed>** | Optional error messages for validation rules. |

**Return Value:**

`ValidatorInterface` - Instance of the ValidatorInterface.

***

### addRule

Add a single rule with an optional error message.

```php
public addRule(string $field, string $rules, array&lt;string,string&gt; $messages = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string** | Field name. |
| `$rules` | **string** | Validation rules. |
| `$messages` | **array<string,string>** | Optional error message for the validation rule. |

**Return Value:**

`ValidatorInterface` - Instance of the ValidatorInterface.

***

### getErrorLine

Get the error message at the specified field and error indexes.

```php
public getErrorLine(int $indexField, int $indexErrors): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$indexField` | **int** | Field index. |
| `$indexErrors` | **int** | Error index. |

**Return Value:**

`string` - Error message.

***

### getErrorLineInfo

Get information about the validation error at the specified field index.

```php
public getErrorLineInfo(int $fieldIndex): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fieldIndex` | **int** | Field index. |

**Return Value:**

`array` - Information about the validation error.

***

### getErrorFieldLine

Get the field causing the current validation error.

```php
public getErrorFieldLine(string $prefix = ''): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prefix` | **string** | Prefix to prepend to the error field. |

**Return Value:**

`string` - Field causing the validation error.

***

### isPassed

Check if validation has passed.

```php
public isPassed(): bool
```

**Return Value:**

`bool` - True if all rules passed, false otherwise.

***

### Validation Rules

Rule           | Parameter    | Description
---------------|--------------|------------------------------------------------
none           |  Void        | Ignore filed and return true
required       |  Void        | Field is required, it cannot be empty
max_length()   |  Integer     | Field maximum allowed length alias `max()`
min_length()   |  Integer     | Field minimum allowed length alias `min()`
alphanumeric   |  Void        | Field should must be only alphanumeric [aZ-Az-0-9]
email          |  Void        | Field should must be a valid email address
integer()      |  String      | Field should must an integer value, optional parameter [positive or negative] [-0-9]
equals()       |  String      | Field value must match with anther specified field value 
url            |  Void        | Field must be a valid URL
alphabet       |  Void        | Field must be only an alphabet [aZ-Az]
uuid()         |  Integer     | Field value must be uuid string, optional parameter for uuid version 
exact_length() |  Integer     | Field value must be exact length alias `length()`
in_array()     |  String      | Field value must be in array list
is_list        |  Void        | Filed value must be a valid string list
keys_exist()   |  String      | Field array values must match in validation list
callback()     |  Callable    | Callback myFunction(value, field) return boolean value
ip()           |  Integer     | Field value must be a valid IP address, optional parameter for ip version. e.g `ip(6)`. Pass 0 to validate ipv4 or ipv6.
decimal        |  Void        | Field must be a valid decimal value
match()        |  Regex       | Field value must match the regular expression [/pattern/] 
fallback()     |  Mixed       | If the field value is empty, replace it with the default value, if parameter is empty any empty string will be used instead
phone          |  Void        | Check if field value is a phone number
binary         |  Void        | Check if field value is a binary
hexadecimal    |  Void        | Check if field value is a hexadecimal
array          |  Void        | Check if field value is a array
json           |  Void        | Check if field value is a JSON object
path()         |  String      | Check if field value is directory path, if parameter [true] is specified it will check if path is readable 
scheme()       |  String       | Check if field value URL scheme matched with the specified URL scheme.

> Defining your own custom rule using the callback rule method, your callback must be `callable` and must return `true` or `false` to indicate validation failure or passed.
> > Additionally the callback method should be design to accept 2 parameters `value` and `field` as second parameter.