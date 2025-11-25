# PHP Array Object Helper Class

***

## Overview

Luminova’s Array Helper class provides useful methods for initializing and managing arrays.

***

## Introduction

The **Arr** class provides a set of methods to facilitate common operations and manipulations on PHP arrays. It includes methods for checking array types, merging arrays, filtering elements, and more.

***
## Class Definition

* Class namespace: `Luminova\Utility\Collections\Arr`
* This class implements: 
    - [Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)
    - [\ArrayAccess](https://www.php.net/manual/en/class.arrayaccess.php)
    - [\IteratorAggregate](https://www.php.net/manual/en/class.iteratoraggregate.php)
    - [\Countable](https://www.php.net/manual/en/class.countable.php)
    - [\Stringable](https://www.php.net/manual/en/class.stringable.php)

***

## Methods

### constructor

Constructor to initialize array instance.

If no array is provided, an empty array will be initialized.

```php
public __construct(array<string|int,mixed> $array = []): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$array` | **array<string&#124;int,mixed>** | The array to initialize. |

**Example**:

Take note of these initialization variable names, we will make use of then in this documentation for demonstration.

An example of associative array.

```php
use Luminova\Utility\Collections\Arr;

$assoc = new Arr(['name' => 'Peter', 'age' => 33]);
```

An example of a nested array.

```php
use Luminova\Utility\Collections\Arr;

$nested = new Arr(['address' => ['country' => 'Nigeria'], 'about' => ['code' => 'PHP']]);
```

An example of a list array.

```php
use Luminova\Utility\Collections\Arr;

$list = new Arr([1, 2, 3]);
```

***

### fromJson

Create or Update the current array from a json string.

```php
public fromJson(string $json, ?bool $assoc = true, int $depth = 512, int $flags = 0): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$json` | **string** | The json string to update from. |
| `$assoc` | **bool&#124;null** | Whether to convert to an associative array (default: true). |
| `$depth` | **int** | Set the maximum recursion depth, must be greater than zero (default: 512). |
| `$flags` | **int** | The json decoding flags using bitwise OR (&amp;#124;) operators to combine multiple flags. |

**Return Value:**

`self` - Return the updated instance with the new array from json string.

**Throws:**

- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - Throws if unable to convert json string to array.

***

### fromStringList

Create or Update the current array from a string list.

```php
public fromStringList(string $list): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$list` | **string** | The string list to update or create from. |

**Return Value:**

`self` - Return the updated instance with the new array from string list.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - Throws if unable to convert string list to array.

**Example** 

Examples of a string list:

```php
use Luminova\Utility\Collections\Arr;

$arr = (new Arr)->fromList('name=Peter,age=33,[country=Nigeria;city=EN]'); 
```

```php
print_r($arr->get());

// Output:
// Array
// (
//     [name] => Peter
//     [age] => 33
//     [0] => Array
//         (
//             [country] => Nigeria
//             [city] => EN
//         )
// )
```

***

### isNested

Check if the current array is a nested array by containing array as value.
If the recursive argument is true, it will also check the values.

```php
public isNested(bool $recursive = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$recursive` | **bool** | Whether to check deeply nested array values (default: false). |

**Return Value:**

`bool` - Return true if the array is nested, false otherwise.

**Example**:

The nested array will pass.

```php
var_export($nested->isNested()); // true
```

The list and associative array will fail.

```php
var_export($assoc->isNested()); // false
var_export($list->isNested()); // false
```

***

### isAssoc

Check if the current array is associative, which mean it must use string a named keys.

```php
public isAssoc(): bool
```

**Return Value:**

`bool` - Return true if the array is associative, false otherwise.

**Example**:

The associative and nested array will pass because they have string key.

```php
var_export($assoc->isAssoc()); // true
var_export($nested->isAssoc()); // true
```

The list array will fail, because it has an integer key.

```php
var_export($list->isAssoc()); // false
```

***

### isList

Check if the current array is a list, which means it must be indexed by consecutive integers keys.

```php
public isList(): bool
```

**Return Value:**

`bool` - Return true if the array is a list, false otherwise.

**Example**:

The associative and nested array will fail because they both have a string key.

```php
var_export($assoc->isList()); // false
var_export($nested->isList()); // false
```

The list array will pass because it has integer keys.

```php
var_export($list->isList()); // true
```

***

### isEmpty

Check if array is empty.

```php
public isEmpty(): bool
```

**Return Value:**

`bool` - Return true if the array empty, false otherwise.

***

### get

Get an element from the array based on the provided key.

```php
public get(string|int|null $key = null, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;int&#124;null** | The key of the element to retrieve, or null to get the entire array (default: null). |
| `$default` | **mixed** | The default value if the array key was not found (default: null). |

**Return Value:**

`mixed` - Return the value for the specified key or default value, If key is null return the entire array.

**Example**:

Get Property key.

```php
var_export($assoc->get('name')); // Peter
```

To return all elements of the array

```php
print_r($assoc->get()); // Array
```

***

***

### getArray

Retrieve the modified array after processing.

This method returns the internally stored array, which may have been modified by other operations such as merging, filtering, or reordering.

```php
public getArray(): array
```

**Return Value:**

`mixed` - Return the processed array.

***

### getNested

Retrieves a nested value from the current array using a dot notation for nested keys.

```php
public getNested(string $notations, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$notations` | **string** | The dot notation path to the value. |
| `$default` | **mixed** | The default value to return if the path is not found. Defaults to null. |

**Return Value:**

`mixed` - Return the value for the specified notations, or the default value if not found.

**Example**:

Retrieve the country name from initialized nested array.

```php
echo $nested->getNested('address.country'); // Nigeria
```

***

### add

Add or update an element in the array.

```php
public add(string|int $key, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;int** | The key for the element. |
| `$value` | **mixed** | The value to be added. |

**Return Value:**

`self` - Return the updated instance with the new element.

***

### add

Add or update an element in the current array using nested dot notation.

```php
public addNested(string $notations, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;int** | The dot notation path to the nested key. |
| `$value` | **mixed** | The value to set at the nested key. |

**Return Value:**

`self` - Return the updated instance with the new element.

**Example**

This example will add new element to the nested initialized array.

```php
$nested->addNested('address.zip.code', '12345');
```

***

### count

Count the number of elements in the current array.

```php
public count(): int
```

**Return Value:**

`int` - Return the number of elements in the array.

***

### has

Check if the array contains an element with the given key.

```php
public has(string|int $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;int** | The array key to check. |

**Return Value:**

`bool` - Return true if array key exists, false otherwise.

***

### diff

Compares the current array with one or more arrays and returns the difference.
The values from the current array that are not present in any of the passed arrays.

```php
public diff(Luminova\Utility\Collections\Arr|array ...$arrays): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `...$arrays` | **Arr&#124;array** | One or more arrays or Arr instances to compare. |

**Return Value:**

`array` - Returns an array containing all the entries from the current array that are not present in any of the other arrays.

***

### sort

Sort the current array elements.

```php
public sort(int $flags = SORT_REGULAR): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flags` | **int** | The array sort flags (default: SORT_REGULAR). |

**Return Value:**

`self` - Return the updated instance with the sorted element.

***

### remove

Remove an array key if it exist.

```php
public remove(string|int $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;int** | The array key to remove. |

**Return Value:**

`bool` - Return true if array key was removed, false otherwise.

***

### clear

Clear all elements from the array.

```php
public clear(): bool
```

**Return Value:**

`bool` - Return true if the entire array was cleared, false otherwise.

***

### keys

Extract all the keys or a subset of the keys in the current array.

```php
public keys(mixed $filter, bool $strict = false): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filter` | **mixed** | If filter value is specified, then only keys containing these values are returned. |
| `$strict` | **bool** | Determines if strict comparison (===) should be used during the search (default: false). |

**Return Value:**

`array` - Return list of array keys in the current array.

***

### values

Extract all the values in the current array.

```php
public values(): array
```

**Return Value:**

`array` - Return list of array values in the current array.

***

### iterator

Extract all the values in the current array.

```php
public iterator(int $flags = 0): \ArrayIterator
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flags` | **int** | Optional flags to control the behavior of the ArrayObject object (default: 0). |

**Return Value:**

`\ArrayIterator` - Return a new instance of array iterator containing the current array elements.

***

### column

Extract a column from a multidimensional array.

```php
public column(string|int|null $property, string|int|null $index = null): Luminova\Utility\Collections\Arr
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$property` | **string&#124;int&#124;null** | The column to extract. |
| `$index` | **string&#124;int&#124;null** | The index to use for extraction. |

**Return Value:**

`Luminova\Utility\Collections\Arr` - Return a new instance of Arr containing the extracted column.

***

### pluck

Extracts values from an array of associative arrays or objects based on a specified key or property.

```php
public pluck(string $property): Luminova\Utility\Collections\Arr
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$property` | **string** | The key or property name to pluck values from. |

**Return Value:**

`Luminova\Utility\Collections\Arr` - Return a new instance of Arr containing the values corresponding to the specified property name.

**Example**:

```php
use Luminova\Utility\Collections\Arr;

$arr = new Arr([
    ['name' => 'Peter', 'age' => 33],
    ['name' => 'Hana', 'age' => 25],
]);

$names = $arr->pluck('name');
print_r($names);

// Output:
// Array
// (
//     [0] => Peter
//     [1] => Hana
// )
```

Using an object with pluck.

```php
class User
{
    public function __construct(public int $id, public string $name){}
}

$users = new Arr([
    new User(1, 'Peter'),
    new User(2, 'Hana'),
    new User(3, 'Doe')
]);

$names = $users->pluck('name');
print_r($names);

// Output:
// Array
// (
//     [0] => Peter
//     [0] => Hana
//     [1] => Doe
// )
```

***

### search

Searches for a value or key in the current array and returns the corresponding key or index.

```php
public search(mixed $search, bool $strict = true, bool $forKey = false): string|int|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$search` | **mixed** | The value or key to search for. |
| `$strict` | **bool** | Whether to also check the types of the search in the searching array haystack (default: true). |
| `$forKey` | **bool** | Whether to search for a key (defaults: false). |

**Return Value:**

`string|int|false` - Returns the key or index if found, otherwise returns false.

***

### replace

Replace the current array with another array.

```php
public replace(Luminova\Utility\Collections\Arr|array<string|int,mixed> $replacements): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$replacements` | **Arr\|array<string&#124;int,mixed>** | The array to replace with the current array. |

**Return Value:**

`self` - Return the updated instance with the merged array.

***

### merge

Merge another array with the current array.

```php
public merge(Luminova\Utility\Collections\Arr|array<string|int,mixed> $array): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$array` | **Arr\|array<string&#124;int,mixed>** | The array to merge with the current array. |

**Return Value:**

`self` - Return the updated instance with the merged array.

***

### mergeRecursive

Recursively merges multiple arrays. Values from later arrays will overwrite values from earlier arrays, including merging nested arrays.

```php
public mergeRecursive(Luminova\Utility\Collections\Arr|array<string|int,mixed> $array, bool $distinct = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$array` | **Arr\|array<string&#124;int,mixed>** | The array to merge with the current array. |
| `$distinct` | **bool** | Whether to ensure unique values in nested arrays (default: false). |

**Return Value:**

`self` - Return the updated instance with the merged array.

**Example**:

```php
$nested->mergeRecursive(['a' => ['y' => 2], 'c' => 3]);
```

***

### mergeIntervals

Merges the given array into the current array at specified intervals.

This method inserts elements from the provided array (`$array`) into the existing array (`$this->array`) at every `$position` interval. If there are remaining elements in `$array` after merging, they will be inserted randomly.

```php
public mergeIntervals(Luminova\Utility\Collections\Arr|array $array, int $intervals = 4): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$array` | **Arr\|array** | The array to merge into the current array. |
| `$intervals` | **bool** | The interval at which elements from `$array` should be inserted (default: `4`). |

**Return Value:**

`self` - Returns the instance with the modified array.

**Example:**

Merging Posts with Advertisements:

```php
$posts = [
	["id" => 1, "title" => "Post 1"],
	["id" => 2, "title" => "Post 2"],
	["id" => 3, "title" => "Post 3"],
	["id" => 4, "title" => "Post 4"]
];

$adverts = [
	["id" => 101, "title" => "Ad 1"],
	["id" => 102, "title" => "Ad 2"],
	["id" => 103, "title" => "Ad 3"],
];

$array = (new Arr($posts))->mergeIntervals($adverts, 2);
print_r($array->getArray());
```
***

### filter

Iterates over each value in the array passing them to the callback function to filter.

```php
public filter(?callable $callback = null, int $mode = 0): Luminova\Utility\Collections\Arr
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **callable&#124;null** | The filter callback function (default: null). |
| `$mode` | **int** | The array filter mode to use (default: 0). |

**Return Value:**

`Luminova\Utility\Collections\Arr` - Return a new instance of Arr containing the filters.

***

### map

Applies the callback to the elements of the given arrays.

```php
public map(callable $callback, array ...$arguments): Luminova\Utility\Collections\Arr
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **callable** | The filter callback function (default: null). |
| `$arguments` | **array** | The array of arguments to map. |

**Return Value:**

`Luminova\Utility\Collections\Arr` - Return a new instance of Arr containing the elements after applying callback.

***

### reduce

Iteratively reduce the array to a single value using a callback function.

```php
public reduce(callable $callback, mixed $initial = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$callback` | **callable** | The reduce callback function (default: null). |
| `$initial` | **mixed** | If the optional initial is available,<br />it will be used at the beginning of the process,<br />or as a final result in case the array is empty. |

**Return Value:**

`mixed` - Return the result of array reduce.

***

### reverse

Update the current array with elements in reverse order.

```php
public reverse(bool $preserve_keys = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$preserve_keys` | **bool** | Whether to preserve the original keys of the current array or reset resulting to sequential numeric keys. (default: false). |

**Return Value:**

`self` - Return the updated instance with the reversed array.

***

### chunk

Return new array instance by splitting the array into chunks.

```php
public chunk(int $size, bool $preserve_keys = false): Luminova\Utility\Collections\Arr
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$size` | **int** | The split chunk size. |
| `$preserve_keys` | **bool** | Whether to preserve the array keys or re-index the chunk numerically (default: false). |

**Return Value:**

`Luminova\Utility\Collections\Arr` - Return a new instance of Arr containing the chunked array.

***

### slice

Return new array instance containing a slice of current array.

```php
public slice(int $offset, ?int $length = null, bool $preserve_keys = false): Luminova\Utility\Collections\Arr
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$offset` | **int** | The array slice offset. |
| `$length` | **int&#124;null** | The array slice length (default: null). |
| `$preserve_keys` | **bool** | Whether to preserve the array keys or to reorder and reset the array numeric keys (default: false). |

**Return Value:**

`Luminova\Utility\Collections\Arr` - Return a new instance of Arr containing the slice of array.

**Note:**

- If length is given and is positive, then the sequence will have that many elements in it.
- If length is given and is negative then the sequence will stop that many elements from the end of the array.
- If it is omitted, then the sequence will have everything from offset up until the end of the array.

***

### __toString

Convert the current array to to json string representation.

```php
public __toString(): string
```

**Return Value:**

`string` - Return json string of the current array or blank string if error occurred.

***

### toString

Convert the current array to to json string representation.

```php
public toString(bool $throw = true): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$throw` | **bool** | Whether throw json exception if error occurs (default: true). |

**Return Value:**

`string` - Return json string of the current array, if throw is false return null on error.

**Throws:**

- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - Throws if unable to convert to json string.

***

### toStringList

Convert the current array to to comma string list representation.

```php
public toStringList(): string
```

**Return Value:**

`string` - Return string list representation of the current array.

***

### toJson

Convert the current array to json string representation.

```php
public toJson(int $flags, int $depth = 512, bool $throw = true): ?string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flags` | **int** | The json encoding flags using bitwise OR (&amp;#124;) operators to combine multiple flags. |
| `$depth` | **int** | Set the maximum depth, must be greater than zero (default: 512). |
| `$throw` | **bool** | Whether throw json exception if error occurs (default: true). |

**Return Value:**

`string|null` - Return json string of the current array, if throw is false return null on error.

**Throws:**

- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - Throws if unable to convert to json string.

***

### toObject

Convert the current array to json object representation.

```php
public toObject(bool|null $assoc = true, int $flags, int $depth = 512, bool $throw = true): ?object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$assoc` | **bool&#124;null** | Whether to convert to an associative array (default: true). |
| `$flags` | **int** | The json encoding flags using bitwise OR (&amp;#124;) operators to combine multiple flags. |
| `$depth` | **int** | Set the maximum depth, must be greater than zero (default: 512). |
| `$throw` | **bool** | Whether throw json exception if error occurs (default: true). |

**Return Value:**

`object|null` - Return json object of the current array, if throw is false return null on error.

**Throws:**

- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - Throws if unable to convert to json string or convert to object.