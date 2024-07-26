# Input Validation

***

## Overview

With the input Validation class, you can validate the user-submitted data before saving it to the database. To do that you will have to set validation rules for each input field and optional error me

***

## Introduction

The Input `Validation` class in the Luminova Framework provides a robust mechanism for validating user-submitted data before processing or storing it in a database. With this class, you can define validation rules for each input field along with optional error messages, ensuring that the data meets your application's requirements and standards.

### Validation Rules

You can set validation rules for input fields using a variety of predefined validation rules, including required fields, maximum and minimum lengths, alphanumeric characters, email addresses, integers, and more. Rules can be added individually or set from an array, allowing for flexible configuration. Additionally, custom validation rules can be defined using callback functions.

### Defining Rules

Validation rules are specified by separating each rule with a pipe `|` and providing parameters in parentheses when needed. Rules can be applied to individual fields or set globally using associative arrays, with the option to specify custom error messages for each rule.

### Error Message Formatting

Error messages can include placeholders such as `{field}`, `{value}`, and `{rule}` to dynamically insert relevant information about the validation error. This makes error messages more informative and context-aware, enhancing the user experience.

#### Formatting Example

```php
$this->validate->messages = [
    'email' => [
        'required' => 'The {field} field is required.',
        'email' => 'The value "{value}" is not a valid {rule}.'
    ]
];
```

In this example:

- `{field}` will be replaced with the name of the field being validated.
- `{value}` will be replaced with the actual value that failed validation.
- `{rule}` will be replaced with the name of the validation rule that was violated.

***

### Usage Examples

Setting up validation rules and optional response messages:

```php
// app/Controllers/SignUpController.php

<?php 
namespace App\Controllers;

use Luminova\Base\BaseController;
class SignUpController extends BaseController
{
    protected function onCreate(): void 
    {
        $this->validate->rules = [
            'email' => 'required|email',
            'phone' => 'required|phone',
            'name' => 'required|alphabet',
        ];

        $this->validate->messages = [
            'email' => [
                'required' => 'Email is required.',
                'email' => 'Invalid email address.'
            ],
            'phone' => [
                'required' => 'Phone number is required.',
                'phone' => 'Invalid phone number.'
            ],
            'name' => [
                'required' => 'Name is required.',
                'alphabet' => 'Name contains unsupported characters.'
            ],
        ];
    }
}
```

Performing validation based on the defined rules:

```php
// app/Controllers/SignUpController.php

<?php 
public function create(): int 
{
    $body = $this->request->getBody();
    
    if ($this->validate->validate($body)) {
        echo 'Validation passed.';
    } else {
        echo 'Validation failed.';
    }
    //...
}
```

Retrieving validation error information when validation fails:

Get all validation errors:

```php
<?php 
var_dump($this->validate->getErrors());
```

Get the first validation error message:

```php
<?php 
echo $this->validate->getError(0, 'message');
```

Get the first validation error by field and error index. This will print the first field with an error and the first error rule:

```php
<?php 
echo $this->validate->getErrorLine(0);
```

Get information about the first field that failed validation:

```php
<?php 
var_dump($this->validate->getErrorField(0, 0));
```

These examples demonstrate how to set up and use validation rules, handle validation responses, and retrieve detailed error information in a structured and meaningful way.

***

## Validation Class 

* Class namespace: `\Luminova\Security\Validation`
* This class implements:
[\Luminova\Interface\ValidatorInterface](/interface/classes.md#validatorinterface)

***

## Properties

### rules

Define the validation rules for each form input field. The input field name should be the array key, and the validation rule(s) should be the value.

```php
public array<string,string> $rules = [];
```

### messages

Define custom error messages for each validation rule. The array key should be the input field name, and the value should be an array where each key is a validation rule name, and the value is the corresponding error message. 

If no custom error messages are provided, a default error message will be used, such as `Validation failed for field: 'email', while validating [email]`.

```php
public array<string,array<string,string>> $messages = [];
```

***

## Methods

### validate

Do the validation based on defined rules and input request body.

```php
public validate(array&lt;string,mixed&gt; $input, array&lt;string,string&gt;|null $rules = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **array<string,mixed>** | The input request body to validate (e.g, `$this->request->getBody()`, `$_POST`, `$_GET`, `$_REQUEST`). |
| `$rules` | **array<string,string>\|null** | Optional array of rules to override initialized rules (default `NULL`). |

**Return Value:**

`bool` - Returns true if all validation rules are passed, otherwise false.

***

### isPassed

Check if all validation rules passed.

```php
public isPassed(): bool
```

**Return Value:**

`bool` - Returns true if all validation rules are passed, otherwise false.

***

### setRules

Set validation rules from an array with optional error messages.

```php
public setRules(array&lt;int,string&gt; $rules, array&lt;string,array&gt; $messages = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$rules` | **array<int,string>** | The array of validation rules (e.g, `['email' => 'string\|email', 'name' => 'required']`). |
| `$messages` | **array<string,array<string,string>>** | Optional error messages for validation rules (e.g, `['email' => ['string' => '...', 'email' => '...'], 'name' => ['required' => '...']]`). |

**Return Value:**

`ValidatorInterface` - Return instance of the Validation class.

***

### addRule

Add a rule with an optional error message. This method can be called multiple times with different field name and rule.
For `$messages` arguments, the array key will be the rule's name while the value is the message to display if form field fails the validation rule.

```php
public addRule(string $field, string $rules, array&lt;string,string&gt; $messages = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string** | The form input field name (e.g, `name`, `email`). |
| `$rules` | **string** | The input field validation rules (e.g, `required|string|email`). |
| `$messages` | **array<string,string>** | Optional validation error message for the each rule. |

**Return Value:**

`ValidatorInterface` - Return instance of the Validation class.

***

### getErrors

Get all validation error messages.

```php
public getErrors(): array
```

**Return Value:**

`array<string,array>` - Return array of validation error messages, otherwise empty array.

***

### getError

Get the error message, rule or field for a specific input field or using field index position.

```php
public getError(string|int $fieldIndex = 0, string $type = 'message'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fieldIndex` | **string\|int** | The input field by index position or the input filed name (default: 0). |
| `$type` | **string** | The type of error detail to return (default: `message`). |

**Return Value:**

`string` - Return an error details based on `$type` for the specified field.

**Supported Types:**

 - `message` - Return the error message.
 - `rule` - Return the rule name associated with the error.
 - `field` - Return the input field name associated with the error.

***

### getErrorLine

Get the error message at the specified field and error indexes.
If `0` is passed on both arguments, it will return the first input field that has error, and the first error rule.

```php
public getErrorLine(string|int $indexField = 0, int $errorIndex = 0): string;
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fieldIndex` | **int** | The input field by index number or input filed name (default: 0). |
| `$errorIndex` | **string\|int** | The error index for current field if has multiple rules (default: 0). |

**Return Value:**

`string` - Error message.

***

### getErrorField

Get information about the validation error at the specified field index or field name.
When passed an integer index, it will return the first validation error.
When passed a string field name, it will return the validation error related to that field name.

```php
public getErrorField(string|int $fieldIndex = 0): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fieldIndex` | **string\|int** | The input field by index number or input filed name. |

**Return Value:**

`array<int,array>` - Return array error information related to the field validation.

**Return Keys:**

- `message` -  The error message associated with the field.
- `rule`  -  The rule associated with the field and error.
- `field` -  The validation field.

***

### Validation Rules

These rules provide useful validation for various types of data, ensuring that your application processes only valid and expected input.

| Rule            | Parameter    | Description                                                                                              |
|-----------------|--------------|----------------------------------------------------------------------------------------------------------|
| **none**        | Void         | Ignore the field and return true.                                                                        |
| **required**    | Void         | The field is required and cannot be empty.                                                               |
| **max_length()**| Integer      | The field's maximum allowed length, alias `max()`.                                                       |
| **min_length()**| Integer      | The field's minimum allowed length, alias `min()`.                                                       |
| **alphanumeric**| Void         | The field should contain only alphanumeric characters (a-Z, A-Z, 0-9).                                   |
| **email**       | Void         | The field must be a valid email address.                                                                 |
| **string**      | Void         | The field must be a valid string value.                                                                  |
| **integer()**   | String       | The field must be an integer value. Optional parameter to specify if it should be positive or negative.  |
| **equals()**    | String       | The field value must match the value of another specified field.                                         |
| **url**         | Void         | The field must be a valid URL.                                                                           |
| **alphabet**    | Void         | The field must contain only alphabetic characters (a-Z, A-Z).                                            |
| **uuid()**      | Integer      | The field value must be a valid UUID string. Optional parameter to specify the UUID version.             |
| **exact_length()** | Integer  | The field value must have the exact specified length, alias `length()`.                                   |
| **in_array()**  | String       | The field value must be in the specified array list.                                                     |
| **is_list**     | Void         | The field value must be a valid string list (e.g, `foo,bar,baz,bra`).                                                             |
| **keys_exist()**| String       | The field's array values must match the specified validation list.                                       |
| **callback()**  | Callable     | A callback function `myFunction(value, field)` that returns a boolean value.                             |
| **ip()**        | Integer      | The field value must be a valid IP address. Optional parameter to specify the IP version (4 or 6).       |
| **decimal**     | Void         | The field must be a valid decimal value.                                                                 |
| **match()**     | Regex        | The field value must match the specified regular expression pattern.                                     |
| **fallback()**  | Mixed        | If the field value is empty, replace it with the default value. If the parameter is empty, an empty string will be used. |
| **phone**       | Void         | The field value must be a valid phone number.                                                            |
| **binary**      | Void         | The field value must be a valid binary value.                                                            |
| **hexadecimal** | Void         | The field value must be a valid hexadecimal value.                                                      |
| **array**       | Void         | The field value must be a valid array.                                                                   |
| **json**        | Void         | The field value must be a valid JSON object.                                                             |
| **path()**      | String       | The field value must be a valid directory path. If the parameter is `true`, it will check if the path is readable. |
| **scheme()**    | String       | The field value must match the specified URL scheme.                                                     |

### Custom Validation Rule

To define your own custom validation rule using the callback method, ensure your callback is `callable` and returns `true` for a successful validation or `false` for a failed validation. 

Additionally, the callback function should be designed to accept two parameters: 

- `value` **(mixed)** - The first parameter is the value submitted.
- `field` **(string)** - The second parameter is the input field name.

#### Example

Function to perform your custom validation logic.

```php
// app/Utils/Global.php

function myCustomRule(mixed $value, string $field): bool 
{
    if ($value === 'expected_value') {
        return true;
    }
    return false;
}
```
Adding your function as a validation rule.

```php
$this->validate->rules = [
    'custom_field' => 'required|callback(myCustomRule)',
];
```