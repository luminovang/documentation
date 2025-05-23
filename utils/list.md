# String Listification Implementation

***

## Overview

The Luminova string Lists class is a versatile tool for converting between string lists and arrays, providing a straightforward way to handle structured data in a string format. 

***

## Introduction

The `Listify` class provides utility methods for manipulating and validating string lists, converting between string representations and arrays. It includes methods to check if a string is a valid list format, convert between list strings and arrays, and perform other related operations.

### What is a String Listif?

In Luminova, a string list is a string format that represents an array in a specific format. This format allows for easy conversion between string and array forms. While not a standard PHP data structure, string listification are useful for handling comma-separated strings and performing conversions or validations. This can be particularly useful for configuration data, simple data storage, and scenarios where data needs to be easily readable and writable in a string format.

### How It Works

- **Conversion to List Format**: Converts an array to a string list using comma-separated values for non-nested arrays and semicolons for nested arrays. The string list can include empty arrays or strings.
  
- **Conversion from List Format**: Translates a string list into an array, parsing values based on their format (e.g., `null`, `true`, `false`) will be translated as string allowing to convert it back to it original form.

***

### Formatting

A valid string list in Luminova follows specific formats:

1. **Comma-Separated Non-Nested Arrays**: Use commas to separate elements in a flat array both key-value pairs.

    ```php
    $list = 'foo,bar,baz=100';
    $array = ['foo', 'bar', 'baz' => 100];
    ```

2. **Nested Arrays Separation**: Use semicolons for elements within nested arrays both key-value pairs.

    ```php
    $list = 'foo=bar,bar=2,baz=[1;2;3]';
    $array = [
        'foo' => 'bar', 
        'bar' => 2, 
        'baz' => [1, 2, 3]
    ];
    ```

    - **Key-Value Pair**: Use `key=value` syntax.
    - **Nested Arrays**: Use square brackets `[]` and semicolons `;` for nested values.

> **Note**: String Listify are optimized for small arrays. While they can handle large arrays, performance overhead increases with size. Use string listify primarily for smaller data sets or when necessary.

***

### Capabilities

While PHP’s built-in `implode` and `explode` functions are useful for basic string manipulation, the `Listify` class provides additional functionality that addresses more complex requirements for working with string representations of arrays. Here’s what makes the `Listify` class more powerful:

#### 1. **Nested Array Handling**

**PHP’s `explode` and `implode`**:

- Handle only flat, non-nested strings.
- Limited to basic string manipulation.
- Cannot process arrays within arrays.

    **PHP Explode**:

    With php explode, the output is not as expected.

    ```php
    $list = 'foo=bar,bar=2,baz=[1;2;3]';
    $array = explode(',', $list);

    //Output:
    Array
    (
        [0] => foo=bar
        [1] => bar=2
        [2] => baz=[1;2;3]
    )
    ```

**Listify Class**:

- Supports conversion between nested arrays and string lists with nested structures.
- Uses delimiters like `;` for nested elements.
- Converts complex string structures into multidimensional arrays.

    ```php
    $list = 'foo=bar,bar=2,baz=[1;2;3]';
    $array = Listify::toArray($list);

    // Output:
    Array
    (
        [foo] => bar
        [bar] => 2
        [baz] => Array (
            [0] => 1
            [1] => 2
            [2] => 3
        )
    )
    ```

    **Multidimensional Array**

    ```php
    $list = 'name=Peter,age=33,address=[country=Nigeria;city=EN]';
    $array = Listify::toArray($list);

    // Output: 
    Array
    (
        'name' => 'Peter', 
        'age' => 33, 
        'address' => Array(
            'country' => 'Nigeria', 
            'city' => 'EN'
        )
    )
    ```
***

#### 2. **Key-Value Pair Representation**

**PHP’s `explode` and `implode`**:

- Do not support key-value pairs inherently.
- Require manual processing for strings and keys.

**Listify Class**:

- Simplifies conversion of key-value pairs within lists.
- Supports both simple and complex key-value structures.

***

#### 3. **Validation of Listified Format**

**PHP’s `explode` and `implode`**:

- Lack built-in validation for list formats.

**Listify Class**:

- Includes methods to validate string conformity to list formats.
- Ensures adherence to rules for separators and nesting.

    ```php
    $list = 'foo=bar,bar=2,baz=[1;2;3]';
    $isValid = Listify::isList($list);

    // Output: true
    ```

***

#### 4. **Custom Parsing and Conversion**

**PHP’s `explode` and `implode`**:

- Basic parsing with fixed delimiters.
- Cannot handle custom formatting or complex structures.

**Listify Class**:

- Provides custom parsing rules for converting between lists and arrays.
- Supports specific formatting requirements and custom delimiters.

***

#### 5. **Flexible Array to String Conversion**

**PHP’s `implode`**:

- Limited to simple arrays.
- Cannot handle multidimensional or associative arrays directly.

    > Will encounter a `TypeError` because implode expects a simple array of values, not an associative array or multidimensional array.

    ```php
    $array = [
        'name' => 'Peter', 
        'age' => 33, 
        'address' => [
            'country' => 'Nigeria', 
            'city' => 'EN'
        ]
    ];
    $list = implode(',', $array); // Results in TypeError
    ```

**Listify Class**:

- Converts any array, including multidimensional and associative arrays, into a formatted string.
- Handles various data types and nested arrays gracefully.

    ```php
    $array = [
        'name' => 'Peter', 
        'age' => 33, 
        'address' => [
            'country' => 'Nigeria', 
            'city' => 'EN'
        ]
    ];
    $list = Listify::toList($array);

    // Output: 'name=Peter,age=33,address=[country=Nigeria;city=EN]'
    ```

***

## Class Definition

* Class namespace: `\Luminova\Arrays\Listify`
* This class implements: [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface)

## Methods

### isList

Determines if a string is a valid list based on the expected list format.

```php
public static isList(string $list): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$list` | **string** | The string to check. |

**Return Value:**

`bool` - Return true if the string is a valid list; otherwise, false.

***

### toArray

Converts a string list to its original array structure.

```php
public static toArray(string $list): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$list` | **string** | The string list to convert. |

**Return Value:**

`array` - Return the extracted array.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throws if invalid list format is detected.

***

### toList

Builds a string representation of an array.

```php
public static toList(array $array, string $delimiter = ','): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$array` | **array** | The input array to convert. |
| `$delimiter` | **string** | The delimiter to use between (default: ',').<br />- For none nested array use `,`.<br />- For nested array use `;`. |

**Return Value:**

`string` - Return the resulting string representation of the array.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if invalid delimiter was passed.

> It's recommended to leave the default delimiter to automatically decide which one to use.

***

### More Usages Examples

Here are some examples demonstrating the use of the `Listify` class methods:

**Convert Array to List**:

```php
$array = ['foo', 'bar', 'baz'];
$list = Listify::toList($array);

// Output: 'foo,bar,baz'
```

**Convert List to Array**:

```php
$list = 'foo,bar,baz';
$array = Listify::toArray($list);

// Output: ['foo', 'bar', 'baz']
```

**Check Valid List**:

```php
$list = 'foo=bar,bar=2,baz=[1;2;3]';
$isValid = Listify::isList($list);

// Output: true
```

**Handle Nested Arrays**:

```php
$list = 'foo=bar,[address=[New York;city=NY]]';
$array = Listify::toArray($list);

// Output: ['foo' => 'bar', 'address' => ['New York', 'city' => 'NY']]
```