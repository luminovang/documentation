# Input Rule-Based Validation

***

## Overview

The Validation class allows you to define and apply rules to user-submitted data, ensuring inputs meet specific criteria before processing or saving to the database.

***

## Introduction

**The **Validation** class in the **Luminova** provides an easy and powerful way to check user input before it's processed or stored.
It ensures that every field in your form or request meets the expected format, type, and constraints you define.

It allows you to apply built-in validation rules or create a custom validation. You can also set custom error messages to give users clear feedback.

---

## Validation Rules

Luminova includes a wide range of ready-to-use validation rules such as `required`, `min`, `max`, `email`, `integer`, `alpha`, `url`, and many more.

These rules help you verify user input quickly and safely, reducing the chance of invalid data entering your application.

For the complete list of available rules and their parameters, see the full [Validation Rules Reference](/security/validation-rules.md).

---

### Defining Validation Rules

Rules are defined for each field by specifying the field name and a rule string.
If a rule takes parameters, include them inside parentheses `()`.
To apply multiple rules to the same field, separate them with a pipe `|`.

**Example:**

```php
$this->input->rules = [
  'username' => 'required|min(3)|max(20)|alphanumeric',
  'email'    => 'required|email',
  'age'      => 'integer(positive)',
];
```

---

**Using the `setRules()` Method**

You can also define all your rules at once using the `setRules()` method:

```php
$this->input->setRules([
  'username' => 'required|min(3)|max(20)',
  'email'    => 'required|email',
  'password' => 'required|min(8)',
]);
```

> This is useful when defining multiple validations in one go, inside a controller's routable method.

---

### Adding Individual Field Rules

You can add or update validation rules for a single field at runtime with an optional messages, using the `addField()` method.

```php
$this->input->addField(
   'username',
   'required|alphanumeric|max(20)',
   [
      'required' => 'Username is required.',
      'alphanumeric' => 'Username must contain only letters and numbers.',
      'max' => 'Username cannot exceed 20 characters.'
   ]
);
```

This approach is useful when you need to define or adjust rules conditionally — for example, based on user role, request method, or specific form type.

---

### Defining Error Messages

You can define custom messages for your validation rules using the `$messages` property or the `setMessages()` method.

```php
$this->input->messages = [
  'username' => [
      'required' => 'Username is required.',
      'alphanumeric' => 'Username must be alphanumeric.',
      'max' => 'Username is too long.'
  ],
  'email' => [
      'required' => 'Email is required.',
      'email' => 'Invalid email format.'
  ],
];
```

Alternatively, set them dynamically:

```php
$this->input->setMessages([
  'username' => [
      'required' => 'Username is required.',
      'alphanumeric' => 'Username must be alphanumeric.',
      'max' => 'Username is too long.'
  ],
  'email' => [
      'required' => 'Email is required.',
      'email' => 'Invalid email format.'
  ],
]);
```

---

### Message Formatting

Custom error messages support dynamic placeholders for more descriptive feedback:

| Placeholder | Description                                                      |
| ----------- | ---------------------------------------------------------------- |
| `{field}`   | Replaced with the name of the field being validated.             |
| `{value}`   | Replaced with the actual value that failed validation.           |
| `{rule}`    | Replaced with the name of the validation rule that was violated. |

These placeholders let you generate detailed, context-aware validation messages that improve readability and user understanding.

**Example (Using Property):**

```php
$this->input->messages = [
   'email' => [
      'required' => 'The {field} field is required.',
      'email' => 'The value "{value}" is not a valid {rule}.'
   ]
];
```

**Example (Using `setMessages` Method):**

```php
$this->input->setMessages([
   'email' => [
      'required' => 'The {field} field is required.',
      'email' => 'The value "{value}" is not a valid {rule}.'
   ]
]);
```

---

### Custom Validation

You can define your own validation rules using **callback functions**.
A callback must be **callable** and return:

* `true` — if the value passes validation
* `false` — if the value fails validation

Your callback should accept **two parameters**:

| Parameter | Type   | Description                                  |
| --------- | ------ | -------------------------------------------- |
| `$value`  | mixed  | The value to validate.                       |
| `$field`  | string | The name of the input field being validated. |

**Example:**

Define your custom validation function:

```php
// app/Utils/Global.php

function myCustomRule(mixed $value, string $field): bool 
{
  return $value === 'expected_value';
}
```

Use the custom function in your validation rules:

```php
$this->input->rules = [
   'custom_field' => 'required|callback(myCustomRule)',
];
```

---

### Validating Input

After defining your validation rules, call the `validate()` method to check the incoming data.
If validation fails, you can access both the **first error message** and the **list of all field errors**.

```php
// /app/Controllers/Http/SignUpController.php

if (!$this->input->validate($this->request)) {
   return \Luminova\Funcs\response()->json([
      'message' => $this->input->getError(),
      'errors'  => $this->input->getErrors()
   ]);
}
```

If validation passes, your data is considered valid and safe to process.

---

### Controller Validation

Here's a complete controller example that defines validation rules, custom error messages, and handles user input validation before creating an account.

```php
// /app/Controllers/Http/SignUpController.php

namespace App\Controllers\Http;

use App\Models\User;
use Luminova\Base\Controller;
use Luminova\Attributes\Route;
use Luminova\Attributes\Prefix;
use App\Errors\Controllers\ErrorController;
use function Luminova\Funcs\response;

#[Prefix(pattern: '/api/(:root)', onError: [ErrorController::class, 'onApiError'])]
class SignUpController extends Controller
{
   protected function onCreate(): void 
   {
      // Define validation rules
      $this->input->rules = [
         'email' => 'required|email',
         'phone' => 'required|phone(10)',
         'name'  => 'required|alphabet',
      ];

      // Define custom error messages
      $this->input->messages = [
         'email' => [
            'required' => 'Email is required.',
            'email'    => 'Invalid email address.'
         ],
         'phone' => [
            'required' => 'Phone number is required.',
            'phone'    => 'Invalid phone number.'
         ],
         'name' => [
            'required' => 'Name is required.',
            'alphabet' => 'Name contains unsupported characters.'
         ],
      ];
   }

   #[Route('/api/signup/', methods: ['POST'])]
   public function signup(): int 
   {
      if (!$this->input->validate($this->request)) {
         return response()->json([
            'message' => $this->input->getError(),
            'errors'  => $this->input->getErrors()
         ]);
      } 

      $result = (new User())->createAccount($this->request);
      return response()->json($result);
   }
}
```

---

### Manual Validation

You can use the `Validation` class outside of controllers by creating an instance and working with it directly, just like you would with the protected object in a controller.

**Example:**

Validate multiple fields with built-in rules and custom logic.

```php
use Luminova\Security\Validation;

// Create a new Validation instance
$input = new Validation();

// Request object
$request = new Request(
   method: 'POST', 
   uri: '/',
   body: [
      'username' => 'peter123',
      'email'    => 'peter@example.com',
      'age'      => '25',
      'tags'     => 'php,luminova',
      'role'  => ''
   ]
);

// Validation rules
$rules = [
    'username' => 'username(false)',               // Lowercase username, no reserved words
    'email'    => 'required|email',                // Must be present and valid
    'age'      => 'required|integer(positive)|between(18,60)', // Integer between 18-60
    'tags'     => 'is_list(1)'                     // Comma-separated list, minimum 1 item
    'role'     => 'default(users)'                 // Set default role to user if not specified
];

// Perform validation
$isValid = $input->validate($request, $rules);

// Output results
var_export([
    'status' => $isValid,
    'errors' => $input->getErrors()
]);

// role will default to users if empty 
$role = $request->getPost('role') // Outputs: users
```

**Example Output (All valid):**

```php
array (
   'status' => true,
   'errors' => array(),
)
```

**Example Output (With errors):**

If `age` was `'17'` and `email` was `'invalid'`:

```php
array (
  'status' => false,
  'errors' => [
      'age' => [
         'between' => 'The age must be between 18 and 60.'
      ],
      'email' => [
         'email' => 'The value "invalid" is not a valid email.'
      ]
  ],
)
```

---

### Error Response

When validation fails, you can retrieve detailed information about the errors using the available helper methods.

**Get all validation errors:**

```php
var_dump($this->input->getErrors());
```

Returns an associative array of all fields and their corresponding error messages.

---

**Get the first validation error message:**

```php
echo $this->input->getError(0);
```

Returns the first error message that occurred during validation.

---

**Get a specific error message by field or index:**

```php
echo $this->input->getErrorMessage(0);
```

Returns a formatted error message for a specific field or by index if multiple errors exist.

---

**Get information about the first field that failed validation:**

```php
var_dump($this->input->getErrorField(0));
```

Returns the name and details of the first field that failed validation.

***

## Class Definition

* Class namespace: `Luminova\Security\Validation`
* This class implements: [Luminova\Interface\ValidatorInterface](/interface/classes.md#validatorinterface), [Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)

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

Validate input data against the applied rules.

This method validates incoming request data against validation rules defined via `$input->rules = [...]`, `$input->setRules()`, or `$input->addField()`.  

> You must define rules before calling `validate()`, or pass them as the second parameter.

```php
public validate(
  HttpRequestInterface|LazyObjectInterface|null $request = null, 
  ?array $rules = null
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$body` | **HttpRequestInterface<LazyObjectInterface>\|null** |The HTTP request instance containing input data to validate (default: `null`). |
| `$rules` | **array<string,string>\|null** | Optional validation rules if not previously applied (default `null`). |

**Return Value:**

`bool` - Returns true if all validation rules pass, otherwise false.

**Throws:**

[Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If passed object of `LazyObjectInterface` that does not contain `HttpRequestInterface` object. When `$request` is null without setting input data from `setBody`.

**Examples:**

```php
$request = new Luminova\Http\Request();

$isValid = $input->validate($request, [...]);
```

In HTTP Controllers.

```php
// /app/Controller/Http/*

$isValid = $this->input->validate($this->request, [...]);
```

Validate From Input Array:

```php
// /app/Controller/Http/*

$body = [...];

$isValid = $this->input->setBody($body)->validate(rules: [...]);
```

***

### isPassed

Check if all validation rules passed.

```php
public isPassed(): bool
```

**Return Value:**

`bool` - Returns true if all validation rules are passed, otherwise false.

***

### setBody

Set the input body to validate from array.

When the body is passed by reference, any fields modified by `default` or `fallback` rules are written back to the original array.

```php
public setBody(array<string,mixed> &$body): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$body` | **array<string,mixed>** | The input data to validate passed by reference. |

**Return Value:**

`self` - Return instance of validation object.

**Example:**

```php
$body = [
  'name' => 'Peter',
  'email' => 'peter@example.com'
];

$isValid = $input->setBody($body)->validate();

echo $body['name']; // fallback or default rule value if changed
```

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

Set validation error messages based on fields and rules.

```php
public setMessages(array<string,array> $messages = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$messages` | **array<string,array<string,string>>** | An error messages for validation rules<br/>(e.g, `['email' => ['string' => '...', 'email' => '...'], 'name' => ['required' => '...']]`). |

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

### getField

Get the first field that has validation error.

```php
public getField(string|int $fieldIndex = 0): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fieldIndex` | **string\|int** | The input field by index number or input filed name. |

**Return Value:**

`string` - Return the field name causing the validation error.

***

### getFields

Retrieve validation errors for a specific field by name or index.

This method returns the validation errors for a given field, identified either by 
its array index or its field name.

- If `$fieldIndex` is an integer, the corresponding field name is resolved by position.
- If `$fieldIndex` is a string, the method fetches the error data for that field.

```php
public getFields(string|int $fieldIndex = 0): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fieldIndex` | **string\|int** | The field name or index position in error list (default: 0). |

**Return Value:**

`array<int,array>` - Returns an array of validation error details for the specified field.

> **Note:**
>  Once retrieved, the field's error data is removed from `$this->failures`.