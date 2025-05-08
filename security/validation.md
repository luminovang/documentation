# Rule-Based Input Validation

***

## Overview

The Validation class allows you to define and apply rules to user-submitted data, ensuring inputs meet specific criteria before processing or saving to the database.

***

## Introduction

The Input `Validation` class in the Luminova Framework offers a powerful and flexible way to validate user-submitted data before processing or storing it in a database. It enables you to define validation rules per input field, ensuring that each field meets the expected criteria and application standards. You can also assign custom error messages for better control and user-friendly feedback.

---

### Validation Rules

Luminova provides a wide range of built-in rules including `required`, `min`, `max`, `email`, `integer`, `alpha`, and more. Rules can be added as strings using pipe `|` separators, or programmatically as arrays. You can also register custom rules using callback functions to handle specific validation logic.

---

### Defining Rules

Rules are defined per field and support parameters within parentheses. You can apply rules either individually or as part of an associative array. This makes it easy to manage validation across multiple fields in a structured and maintainable way.

**Example Using Property:**

```php
$this->validate->rules = [
   'field' => 'required|max(5)',
   /* More fields*/ 
];
```

**Example Using `setRules` Method:**

```php
$this->validate->setRules([
   'field' => 'required|max(5)',
   /* More fields*/ 
]);
```

**Example of Adding Individual Field and Rules:**

```php
$this->validate->addField('field', 'required|max(5)');
```

---

### Error Message Formatting

Custom error messages support dynamic placeholders such as:

- `{field}` Will be replaced with the name of the field being validated.
- `{value}` Will be replaced with the actual value that failed validation.
- `{rule}` Will be replaced with the name of the validation rule that was violated.

These placeholders allows you to create detailed and context-aware feedback and improves the clarity of validation responses shown to users.

**Example Using Property:**

```php
$this->validate->messages = [
   'email' => [
      'required' => 'The {field} field is required.',
      'email' => 'The value "{value}" is not a valid {rule}.'
   ]
];
```

**Example Using `setMessages` Method:**

```php
$this->validate->setMessages([
   'email' => [
      'required' => 'The {field} field is required.',
      'email' => 'The value "{value}" is not a valid {rule}.'
   ]
]);
```

---

### Custom Validation

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

***

### Controller Example

Setting up validation rules and optional response messages:

```php
// /app/Controllers/Http/SignUpController.php

namespace App\Controllers\Http;

use Luminova\Base\BaseController;

class SignUpController extends BaseController
{
   protected function onCreate(): void 
   {
      $this->validate->rules = [
         'email' => 'required|email',
         'phone' => 'required|phone(10)',
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

   // Performing validation based on the defined rules:
   public function create(): int 
   {
      $body = $this->request->getBody();
      
      if ($this->validate->validate($body)) {
         echo 'Validation passed.';
         return STATUS_SUCCESS;
      } 

      echo 'Validation failed.';
      echo $this->validate->getErrorMessage();
      return STATUS_SUCCESS;
   }
}
```

---

### Error Response

Retrieving validation error information when validation fails.

Get all validation errors:

```php
var_dump($this->validate->getErrors());
```

Get the first validation error message:

```php
echo $this->validate->getError(0);
```

Get the first validation error by field and error index. 

```php
echo $this->validate->getErrorMessage(0);
```

Get information about the first field that failed validation:

```php
var_dump($this->validate->getErrorField(0));
```

***

## Class Definition

* Class namespace: `\Luminova\Security\Validation`
* This class implements: [\Luminova\Interface\ValidatorInterface](/interface/classes.md#validatorinterface), [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface)

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
public validate(array<string,mixed> $input, ?array<string,string> $rules = null): bool
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

Set validation fields and rules.

```php
public setRules(array<int,string> $rules): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$rules` | **array<int,string>** | The array of validation rules (e.g, `['email' => 'string\|email', 'name' => 'required']`). |

**Return Value:**

`self` - Return instance of the Validation class.

***

### setMessages

Set validation error messages based on fields and ruels.

```php
public setMessages(array<string,array> $messages = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$messages` | **array<string,array<string,string>>** | An error messages for validation rules (e.g, `['email' => ['string' => '...', 'email' => '...'], 'name' => ['required' => '...']]`). |

**Return Value:**

`self` - Return instance of the Validation class.

***

### addField

Set a field and rules with an optional error messages.

This method can be called multiple times with different field name and rule.
For `$messages` arguments, the array key will be the rule's name while the value is the message to display if form field fails the validation rule.

```php
public addField(string $field, string $rules, array<string,string> $messages = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$field` | **string** | The form input field name (e.g, `name`, `email`). |
| `$rules` | **string** | The input field validation rules (e.g, `required|string|email`). |
| `$messages` | **array<string,string>** | Optional validation error message for the each rule. |

**Return Value:**

`self` - Return instance of the Validation class.

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

Get the error message at the specified field and error rules index.

```php
public getError(string|int $fieldIndex = 0, int $errorIndex = 0): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fieldIndex` | **string\|int** | The input field by index position or the input filed name (default: 0). |
| `$errorIndex` | **int** | The error index for current field if has multiple rules (default: 0). |

**Return Value:**

`string` - Return an error message.

***

### getErrorMessage

Get the error message for a specific input field or using field index position.

```php
public getErrorMessage(string|int $fieldIndex = 0): string;
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fieldIndex` | **string\|int** | The input field by index position or the input filed name (default: 0). |

**Return Value:**

`string` - Return an error message for the specified field.

***

### getErrorField

Get the first field that has validation error.

```php
public getErrorField(string|int $fieldIndex = 0): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fieldIndex` | **string\|int** | The input field by index number or input filed name. |

**Return Value:**

`string` - Return the field name causing the validation error.

***

### getErrorFields

Get information about the validation error at the specified field index or field name.

When passed an integer index, it will return the first validation error.
When passed a string field name, it will return the validation error related to that field name.

```php
public getErrorFields(string|int $fieldIndex = 0): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fieldIndex` | **string\|int** | The input field by index number or input filed name. |

**Return Value:**

`array<int,array>` - Return array error information related to the field validation.

***

### Validation Rules

These rules provide useful validation for various types of data, ensuring that your application processes only valid and expected input.

| Rule            | Parameter    | Description                                                                                              |
|-----------------|--------------|----------------------------------------------------------------------------------------------------------|
| **none**        | void         | Ignore the field and return true or alias`nullable`.                                                                        |
| **required**    | void         | The field is required and cannot be empty.                                                               |
| **max()**| integer      | The field's maximum allowed length or alias `max_length()`.                                                       |
| **min()**| integer      | The field's minimum allowed length or alias `min_length()`.                                                       |
| **alphanumeric**| void         | The field should contain only alphanumeric characters (a-Z, A-Z, 0-9).                                   |
| **email**       | void         | The field must be a valid email address.                                                                 |
| **string**      | void         | The field must be a valid string value.                                                                  |
| **integer()**   | string       | The field must be an integer value. Optional parameter to specify if it should be positive or negative.  |
| **float()**   | string       | The field must be a float value. Optional parameter to specify if it should be positive or negative.  |
| **equals()**    | string       | The field value must match the value of another specified field.                                         |
| **not_equal()**    | string       | The field value must not match the value of another specified field.                                         |
| **url**         | void         | The field must be a valid URL.                                                                           |
| **alphabet**    | void         | The field must contain only alphabetic characters (a-Z, A-Z).                                            |
| **uuid()**      | integer      | The field value must be a valid UUID string. Optional parameter to specify the UUID version.             |
| **length()** | integer  | The field value must have the exact specified length, alias `exact_length()`.                                   |
| **in_array()**  | string       | The field value must be in the specified array list.    
| **is_value()**  | string       | The field value must be in the specified value list.                                                     |
| **is_list**     | void         | The field value must be a valid string list (e.g, `foo,bar,baz,bra`).                                                             |
| **keys_exist()**| string       | The field's array values must match the specified validation list.                                       |
| **callback()**  | callable     | A callback function `myFunction(value, field)` that returns a boolean value.                             |
| **ip()**        | integer      | The field value must be a valid IP address. Optional parameter to specify the IP version (`4`, `6` or `0` for any).       |
| **decimal**     | void         | The field must be a valid decimal value.                                                                 |
| **match()**     | regex        | The field value must match the specified regular expression pattern.                                     |
| **fallback()**  | mixed        | If the field value is empty, replace it with the default value. If the parameter is empty, an empty string will be used. |
| **phone**       | string\|void         | The field value must be a valid phone number, optionally pass minimum allow length for phone number                                                            |
| **binary**      | Void         | The field value must be a valid binary value.                                                            |
| **hexadecimal** | void         | The field value must be a valid hexadecimal value.                                                      |
| **array**       | void         | The field value must be a valid array.                                                                   |
| **json**        | void         | The field value must be a valid JSON object.                                                             |
| **path()**      | string\|void       | The field value must be a valid directory path. If the parameter is `readable`, it will check if the path is readable or `writable`. |
| **scheme()**    | string       | The field value must match the specified URL scheme.                                                     |