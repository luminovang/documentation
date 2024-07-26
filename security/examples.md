# Security Examples

***

## Overview

Learn how to effectively use Input validation and CSRF security to protect your application integrity, with working implementation examples. 

***

## Introduction

### Input Validation

To set validation rules you can separate each rule with a parenthesis and parameter in bracket notation.

Adding rule one after another

```php
<?php
$this->validate->addRule('name', 'required|max(10)|alphanumeric', [
    'required' => 'Error message for required',
    'max_length' => 'Error message for max_length',
    'alphanumeric' => 'Error message for alphanumeric'
]);
```

Setting rules from array.

```php
<?php
$rules = [
    'name' => 'required|max(10)|alphanumeric',
    'age' => 'required|integer|min(2)|max(3)',
    'email' => 'required|email',
];

$errorMessages = [
    'name' => [
        'required' => 'Error message for required',
        'max' => 'Error message for max_length',
        'alphanumeric' => 'Error message for alphanumeric'
    ],
    'email' => [
        'required' => 'The email field is required.',
        'email' => 'The email address [value] is not valid.'
    ],
    'age' => [
        'required' => 'The age field is required.',
        'integer' => 'The age must be an integer.',
        'min' => 'The age minimum length must 2.',
        'max' => 'The age maximum length must 3.'
    ]
];

$this->validate->setRules($rules, $errorMessages);
```

> If you didn't specify the array of error messages on `setRules`, it will use a default error message.
> > Additionally rules and errors can be set in a public variable `$this->validate->rules` and `$this->validate->errors`.

### Input Validation Controller

This simple controller examples shows how you can validate user entries using validator class.

```php
<?php
namespace App\Controllers;

use \Luminova\Base\BaseController;

class UserController extends BaseController
{
 	public function userLogin(): void 
	{
		$this->validate->rules = [
			'email' => 'required|email',
			'password' => 'required|min(6)',
		];

		$this->validate->messages = [
			'email' => [
				'required' => 'Email must not be empty',
				'email' => 'Email address must be a valid email.'
			],
			'password' => [
				'required' => 'Password must not be empty',
				'min' => 'Password must not be less than 6 characters'
			],
		];

		if($this->validate->validate($this->request->getBody())){
			echo "Validation passed";
		}else{
			echo 'Validation failed';
			echo $this->validate->getError();
		}
	}
}
```

### Callback

Defining your own callback validation.
> You can define your validation rule in your dev global helper file located in `/app/Utils/Global.php`.

```php
<?php 
function id_is_integer(mixed $value, string $field): bool
{
	if($field === 'my-user-id'){
	     return is_integer($value);
     }

     return true;
}

$this->validate->rules = [
     'my-user-id' => 'required|callback(id_is_integer)'
];
```