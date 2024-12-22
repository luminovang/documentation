# Form Input Elements Examples

***

## Overview

Base command, Luminova's Framework for command-line operations - controller base class for NovaKit CLI Tool.

***

## Introduction

These examples demonstrate various ways to generate form input fields using the [Form Inputs Element Builder Class](/documents/form-inputs.md), offering flexibility and efficiency for diverse use cases in form creation.

***

### Text Input

This example demonstrates a simple text input field with a placeholder.

```php
<?php 
use Luminova\Builder\Inputs;

echo Inputs::text('username', null, false, ['placeholder' => 'Enter your username']);
```
**Output:**
```html
<input type="text" name="username" id="username" placeholder="Enter your username" />
```

***

### Password Input
This example showcases a password input field with a custom class for styling.
```php
<?php 
use Luminova\Builder\Inputs;

echo Inputs::password('user-password', null, false, ['class' => 'password-field']);
```
**Output:**
```html
<input type="password" name="user-password" id="user-password" class="password-field" />
```

***

### Checkbox Input

This example demonstrates how to create a checkbox input with a label.
```php
<?php 
use Luminova\Builder\Inputs;

echo Inputs::checkbox('accept_terms', 1, false, ['id' => 'terms-checkbox']);
echo Inputs::label('terms-checkbox', 'I accept the terms and conditions');
```
**Output:**
```html
<input type="checkbox" name="accept_terms" id="terms-checkbox" value="1" />
<label for="terms-checkbox">I accept the terms and conditions</label>
```

***

### Radio Button Input
This example shows how to create a group of radio buttons.
```php
<?php 
use Luminova\Builder\Inputs;

$options = [
    'male' => 'Male',
    'female' => 'Female'
];

$inputs = '';
foreach ($options as $value => $label) {
    $inputs .= Inputs::radio('gender', $value, false, ['id' => 'gender-' . $value]);
    $inputs .= Inputs::label('gender-' . $value, $label);
}

echo Inputs::form($inputs, '/api/user', 'POST');
```
**Output:**
```html
<form action="/api/user" method="POST">
    <input type="radio" name="gender" id="gender-male" value="male" />
    <label for="gender-male">Male</label>
    <input type="radio" name="gender" id="gender-female" value="female" />
    <label for="gender-female">Female</label>
</form>
```

***

### Textarea Input
This example demonstrates a textarea for longer text input.
```php
<?php 
use Luminova\Builder\Inputs;

echo Inputs::textarea('message', 'Type your message here...', ['rows' => 4, 'cols' => 50]);
```
**Output:**
```html
<textarea name="message" id="message" rows="4" cols="50">Type your message here...</textarea>
```

***

### File Upload Input

This example shows how to create a file upload input.
```php
<?php 
use Luminova\Builder\Inputs;

echo Inputs::file('upload-file', 'front');
```
**Output:**
```html
<input type="file" name="upload-file" id="upload-file" capture="user" accept="image/*, video/*" />
```

***

### Button Input
This example demonstrates creating a submit button.
```php
<?php 
use Luminova\Builder\Inputs;

echo Inputs::button('submit', 'submit', 'Confirm', ['class' => 'submit-button']);
```
**Output:**
```html
<button type="submit" name="submit" class="submit-button">Confirm</button>
```

***

### Hidden Input
This example shows how to create a hidden input field.
```php
<?php 
use Luminova\Builder\Inputs;

echo Inputs::hidden('user_id', 123);
```
**Output:**
```html
<input type="hidden" name="user_id" value="123" />
```

***

### Color Picker Input
This example demonstrates a color input field.
```php
<?php 
use Luminova\Builder\Inputs;

echo Inputs::color('favorite_color', '#ff0000');
```
**Output:**
```html
<input type="color" name="favorite_color" value="#ff0000" />
```

***

### Range Slider Input
This example shows how to create a range input slider.
```php
<?php 
use Luminova\Builder\Inputs;

echo Inputs::range('volume', 50, false, ['min' => 0, 'max' => 100]);
```
**Output:**
```html
<input type="range" name="volume" id="volume" value="50" min="0" max="100" />
```

***

### Select Options

This example demonstrates a basic dropdown with predefined options and a selected value.

```php
<?php 
use Luminova\Builder\Inputs;
$options = [
    1 => 'Option 1',
    2 => 'Option 2',
    3 => 'Option 3'
];

$selectedValue = 2; // Pre-select 'Option 2'

echo Inputs::select('example-select', $options, $selectedValue);
```
**Output:**
```html
<select name="example-select" id="example-select">
    <option value="1">Option 1</option>
    <option value="2" selected="selected">Option 2</option>
    <option value="3">Option 3</option>
</select>
```

***

### Select Option Groups

This example demonstrates using optgroups to categorize options.

```php
<?php 
<?php 
use Luminova\Builder\Inputs;
$options = [
    'Select an Option',
    'Fruits' => [
        1 => 'Apple',
        2 => 'Banana'
    ],
    'Vegetables' => [
        3 => 'Carrot',
        4 => 'Lettuce'
    ]
];

$selectedValue = 3; // Pre-select 'Carrot'

echo Inputs::select('food-select', $options, $selectedValue, '', [
    'class' => 'food-dropdown'
]);
```
**Output:**
```html
<select name="food-select" id="food-select" class="food-dropdown">
    <option value="">Choose an Option</option>
    <optgroup label="Fruits">
        <option value="1">Apple</option>
        <option value="2">Banana</option>
    </optgroup>
    <optgroup label="Vegetables">
        <option value="3" selected="selected">Carrot</option>
        <option value="4">Lettuce</option>
    </optgroup>
</select>
```

***

### Select Multiple Options

In this example, multiple values are selected by default.
```php
<?php 
use Luminova\Builder\Inputs;
$options = [
    1 => 'Option 1',
    2 => 'Option 2',
    3 => 'Option 3'
];

$selectedValues = [1, 3]; // Pre-select 'Option 1' and 'Option 3'

echo Inputs::select('multi-select', $options, $selectedValues, '', [
		'multiple' => null
]);
```
**Output:**
```html
<select name="multi-select" id="multi-select" multiple>
    <option value="1" selected="selected">Option 1</option>
    <option value="2">Option 2</option>
    <option value="3" selected="selected">Option 3</option>
</select>
```

***

### Datalist Options Search

This example shows how to pass additional attributes, such as CSS classes and data attributes.
```php
<?php 
use Luminova\Builder\Inputs;
$options = [
    1 => 'Red',
    2 => 'Green',
    3 => 'Blue'
];

$attributes = [
    'class' => 'search-select',
    'data-category' => 'colors'
];

echo Inputs::datalist('color-select', $options, $attributes);
```
**Output:**
```html
<input type="search" list="search-select" name="search-select">
<datalist class="search-select" data-category="colors" id="search-select">
    <option value="Red"></option>
    <option value="Green"></option>
    <option value="Blue"></option>
</datalist>
```

These examples show various ways to use the `select` method, from simple dropdowns to advanced ones with optgroups, multiple selections, and custom HTML attributes.

***

### Select with Custom Placeholder

This example demonstrates a dropdown with a custom placeholder that acts as a prompt.
```php
<?php 
use Luminova\Builder\Inputs;
$options = [
    '' => 'Select a fruit',
    1 => 'Apple',
    2 => 'Banana',
    3 => 'Cherry'
];

$selectedValue = ''; // No pre-selected value
$disabledValue = ''; // No pre-selected value

$attributes = [
    'class' => 'form-select',
    'required' => 'required',
];

echo Inputs::select('fruit-select', $options, $selectedValue, $disabledValue, $attributes);
```
**Output:**
```html
<select name="fruit-select" id="fruit-select" class="form-select" required="required">
    <option value="" disabled selected>Select a fruit</option>
    <option value="1">Apple</option>
    <option value="2">Banana</option>
    <option value="3">Cherry</option>
</select>
```

***

### Select with Disabled Options
This example shows how to disable specific options within the select dropdown.
```php
<?php 
use Luminova\Builder\Inputs;
$options = [
    1 => 'Option 1',
    2 => 'Option 2 (disabled)',
    3 => 'Option 3'
];

$selectedValue = 1; // Pre-select 'Option 1'
$disabledValue = 2; // Pre-disable 'Option 2'

echo Inputs::select('disabled-option-select', $options, $selectedValue, $disabledValue);
```
**Output:**
```html
<select name="disabled-option-select" id="disabled-option-select">
    <option value="1" selected="selected">Option 1</option>
    <option value="2" disabled>Option 2 (disabled)</option>
    <option value="3">Option 3</option>
</select>
```

***

### Select with JavaScript Event Handling

This example demonstrates attaching a JavaScript event listener to the select element.
```php
<?php 
use Luminova\Builder\Inputs;
$options = [
    1 => 'Morning',
    2 => 'Afternoon',
    3 => 'Evening'
];

$selectedValue = 2; // Pre-select 'Afternoon'

echo Inputs::select('time-of-day', $options, $selectedValue, '', [
    'onchange' => 'alert("You selected: " + this.value)'
]);
```
**Output:**
```html
<select name="time-of-day" id="time-of-day" onchange="alert('You selected: ' + this.value)">
    <option value="1">Morning</option>
    <option value="2" selected="selected">Afternoon</option>
    <option value="3">Evening</option>
</select>
```