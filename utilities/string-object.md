# PHP String Object Helper Class

***

## Overview

An object-oriented, string object utility class for PHP. It simplifies string manipulation with built-in methods for casing, trimming, masking, pattern matching, slug generation, and prototype.

***

## Introduction

The `Str` class offers a **fluent, object-oriented interface** for string manipulation in PHP.
It combines readability, power, and flexibility, making it easy to perform common string operations without breaking flow or readability.

You can **transform, compare, extract, mask, or hash strings**, all through **chainable methods**.
In addition, `Str` implements the `PrototypeableInterface`, allowing you to dynamically **add new methods or properties at runtime**, similar to JavaScript’s prototype model.

---

### Key Features

* **Fluent API** – Most methods are chainable for cleaner code.
* **Safe and predictable** – Avoids mutating the original string unless explicitly set.
* **Prototypeable** – Extend the class at runtime with your own methods or properties.
* **Utility-rich** – Includes case conversions, trimming, masking, formatting, and more.
* **Object-friendly** – Implements PHP’s `Stringable` interface.

---

## Usage

The `Str` class provides an **object-oriented** way to work with strings in Luminova Application.
It makes string manipulation cleaner and more expressive.

### Creating a String Object

You can create a new `Str` instance in two ways,  directly using the constructor or the `of()` static helper method.

```php
use Luminova\Utility\String\Str;

// Using constructor
$str = new Str('Hello World');

// Using static helper
$str = Str::of('Hello World');
```

> Both examples initialize a string object that you can manipulate with class methods.

---

### Optional Array Access

The `Str` object allows you to access characters by index, just like an array.

```php
$str = Str::of('Hello');

// Access single characters by 0-based index
echo $str[1];  // Outputs: "e"
echo $str[3];  // Outputs: "l"

// Out-of-range indexes return an empty string
echo $str[10]; // Outputs: ""
```

> **Note:** 
> This works best with plain ASCII strings. For multibyte characters (like emojis or non-latin scripts), consider using `charAt()` or `at()` for safe Unicode support.

---

### Transforming Strings

Once you have an instance, you can perform various transformations easily:

```php
echo $str->toUpperCase();       // "HELLO WORLD"
echo $str->slugify();           // "hello-world"
echo $str->between('He', 'ld'); // "llo Wor"
echo $str->repeat(2);           // "Hello WorldHello World"
```

Each method returns a new `Str` object (unless otherwise noted), allowing **method chaining**.

**Example:**

```php
echo Str::of('  Hello World  ')
    ->trim()
    ->slugify()
    ->toUpperCase(); // "HELLO-WORLD"
```

---

### Extending with Prototype Methods

You can extend the `Str` class at runtime using **prototype methods**.
This allows you to dynamically add new methods or utilities without modifying the class itself.

```php
// Add a dynamic method
$str->prototype('greet', fn($name) => "{$this->valueOf}, {$name}!");
echo $str->greet('Peter'); // "Hello World, Peter!"
```

You can also add new utilities:

```php
$str->prototype('toArray', fn() => explode(' ', $this->valueOf));
print_r($str->toArray()); // ["Hello", "World"]
```

> **Note:**
> Inside a prototype function, use `$this->valueOf` to access the string value.
> The `$valueOf` property is protected and cannot be accessed outside the class scope.

---

## Class Definition

* Class namespace: `Luminova\Utility\String\Str`
* This class implements:
[\Stringable](/Stringable.md), [\ArrayAccess](/ArrayAccess.md), [Luminova\Interface\PrototypeableInterface](/interface/classes.md#prototypeableinterface)

> This class implements Luminova `PrototypeableInterface`, allowing you to dynamically add methods at runtime.

---

## Properties

### valueOf

The internal property holding the string value.

```php
protected string $valueOf = '';
```

---

### encoding

Optional string encoding (default: auto detect or fallback to `UTF-8`). 

```php
protected ?string $encoding = null;
```

---

### encodings

Auto encoding detection order.

```php
protected static array<int,string> $encodings = [...];
```

***

## Methods

### constructor

Create a new string object.

```php
public __construct(string $valueOf = '', ?string $encoding = null)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$valueOf` | **string** | The initial string value. |
| `$encoding` | **string\|null** | Optional string encoding (default: auto detect or fallback to `UTF-8`). |

**Example:**

```php
$str = new Str('Hello');
```

***

### of

Create a new `Str` instance from a string or any `Stringable` value.

This static factory method makes it easy to initialize the string object without using the `new` keyword directly.

```php
public static of(\Stringable|string $valueOf, ?string $encoding = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$valueOf` | **\Stringable&#124;string** | The string or `Stringable` object to wrap. |
| `$encoding` | **string\|null** | Optional string encoding (default: auto detect or fallback to `UTF-8`). |

**Return Value:**

`static` - Returns a new `Str` instance containing the given value.

**Example:**

```php
$str = Str::of('Hello World');

// From Object that implements Stringable
$str = Str::of(new SomeStringObject('From Another Stringable Class Object'));
```

***

### fromCharCode

Create a string from one or more Unicode values.

```php
public static fromCharCode(int ...$codes): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `...$codes` | **int** | Arguments of unicode code points. |

**Return Value:**

`static` - Returns a new `Str` instance representing the characters.

**Example:**

```php
$str = Str::fromCharCode(72, 101, 108, 108, 111); 

echo $str; // "Hello"
```

***

### fromCodePoint

Create a string from Unicode code points (like `String.fromCodePoint`).

```php
public static fromCodePoint(int $points): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$points` | **int** | Arguments of unicode code points. |

**Return Value:**

`static` - Returns a new `Str` instance representing the characters.

**Example:**

```php
$str = Str::fromCodePoint(9731, 9733, 9842); 

echo $str; // "☃★♲"
```

***

### value

Set or update the current string value.

```php
public value(\Stringable|string $value, ?string $encoding = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **\Stringable&#124;string** | The new string value. |
| `$encoding` | **string\|null** | Optional string encoding (default: auto detect or fallback to `UTF-8`). |

**Return Value:**

`self` - Returns the same instance for chaining.

**Example:**
```php
$str = new Str('Old value');

echo $str; // Old Value

// Update with a new value
$str->value('New value');

echo $str; // New Value
```

---

### at

Get the character at the specified position.
     
Supports multibyte characters and negative indexes, where `-1` represents the last character.
Returns an empty string if the index is out of range.

```php
public at(int $index): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **int** | The zero-based character position (can be negative). |

**Return Value:**

`static` - Returns a new `Str` instance containing the character, or an empty instance if not found.

**Example:**

```php
echo Str::of('Hello')->at(1);   // "e"
echo Str::of('Hello')->at(-1);  // "o"
echo Str::of('你好')->at(1);    // "好"
echo Str::of('Hello')->at(99);  // ""
```

***

### hash

Hash the string using the given algorithm.

```php
public hash(string $algo = 'md5', bool $binary = false, array $options = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$algo` | **string** | The hash algorithm (e.g., `md5`, `sha256`). |
| `$binary` | **bool** | If true, outputs raw binary data instead of hex. |
| `$options` | **array** | Optional hashing options. |

**Return Value:**

`self` - Returns the same instance with the hashed value.

> **Note:**
> This modifies the original string value.

**Throws:**
- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the algorithm is unsupported.

**Example:**

```php
echo Str::of('Some Secrete Message')
    ->hash('sha256')
    ->toString();
```

***

### length

Calculate length of the string value.

```php
public length(?string $encoding = null): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$encoding` | **string&#124;null** | Character encoding for multibyte support. |

**Return Value:**

`int` - Returns the number of characters in the string.

**Example:**
```php
echo Str::of('Hello')->length(); // 5
```

***

### encoding

Detect the character encoding of the current string.

This method uses `mb_detect_encoding()` to guess the encoding from a list of common encodings.
If detection fails, it returns the `$default` value.

> If `$strict` is true, `mb_detect_encoding` will only return a valid encoding if it can verify that the string is valid in that encoding.

```php
public encoding(bool $strict = true, string $default = 'UTF-8'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$strict` | **bool** | Whether to use strict mode for detection (default: `true`). |
| `$default` | **string** | The fallback encoding to return if detection fails (default: `UTF-8`). |

**Return Value:**

`string` - Return the detected or default encoding name.

**Example:**

```php
Str::of("Hello")->encoding(); 
// 'UTF-8'

Str::of(iconv('UTF-8', 'ISO-8859-1', "Olá"))->encoding();
// 'ISO-8859-1'
```

***

### split

Split the string into chunks of the specified length.

This converts the string value into an array of `$length` characters.

```php
public split(int $length = 1): array<int,string>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$length` | **int** | The length of each chunk. |

**Return Value:**

`array<int,string>` - Returns an array of string segments.

**Example:**

```php
$str = new Str('abcde');

print_r($str->split(2));
// Array("ab", "cd", "e")
```

***

### replace

Replace occurrences of a string or array of strings.

This method uses PHP `str_replace` to replace one or more occurrence.

```php
public replace(string|array $search, string|array $replacement): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$search` | **string&#124;array** | The string or array to search for. |
| `$replacement` | **string&#124;array** | The replacement string or array. |

**Return Value:**

`static` - Returns a new `Str` instance with replacements applied.

**Example:**

```php
echo Str::of('Hello World')->replace('World', 'PHP'); 
// "Hello PHP"

echo Str::of('I like cats and cats')->replace('cats', 'dogs');
// "I like dogs and dogs"
```

### replaceAll

Replace occurrences using a regular expression.

This method uses uses regex to find and replace all occurrence.

```php
public replaceAll(string|array $pattern, string|array $replacement): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string&#124;array** | A regular expression pattern or array of patterns. |
| `$replacement` | **string&#124;array** | The replacement string or array. |

**Return Value:**

`static` - Returns a new `Str` instance with replacements applied.

**Example:**

```php
echo Str::of('Hello World')->replaceAll('/World/i', 'PHP');
// "Hello PHP"

echo Str::of('I like cats and Cats')->replaceAll('/cats/i', 'dogs');
// "I like dogs and dogs"
```

***

### repeat

Repeat the string a given number of times.

It also support an optional suffix separator for the repeat process.

```php
public repeat(int $times, string $separator = ''): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$times` | **int** | The number of repetitions. |
| `$separator` | **string** | An optional repeat suffix separator. |

**Return Value:**

`static` - Returns a new instance with the repeated string.

**Example:**
```php
$str = new Str('Hi');

echo $str->repeat(3);
// HiHiHi

echo $str->repeat(3, '-');
// Hi-Hi-Hi
```

***

### pad

Pad the string to a new length using a given character.

```php
public pad(int $length, string $char = " ", string $type = 'right'): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$length` | **int** | The target length. |
| `$char` | **string** | The padding character (default: `empty-space`). |
| `$type` | **string** | The padding type (`left`, `right`, `both`). |

**Return Value:**

`static` - Returns a new instance with padding applied.

**Example:**

```php
$str = new Str('Hi');

echo $str->pad(5, '_');
// Hi___
```

***

### escape

Escape special characters in the string for HTML or attribute context.

```php
public escape(string $context = 'html', string $encoding = 'UTF-8'): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$context` | **string** | The value escaper content (Either `html` or `attr`). |
| `$encoding` | **string** | The character encoding (default `UTF-8`). |

**Return Value:**

`static` - Returns a new instance with escaped content.

**Example:**

```php
$str = new Str('<b>Unsafe</b>');

echo $str->escape();
// "&lt;b&gt;Unsafe&lt;/b&gt;"
```

***

### reverse

Reverse the string.

```php
public reverse(): static
```

**Return Value:**

`static` - Returns a new instance with reversed string.

**Example:**

```php
$str = new Str('abc');

echo $str->reverse(); // "cba"
```

***

### substring

Extracts a substring from the string using fixed start and optional length.

Unlike `slice()`, this method does **not** support negative indexes.
It behaves like JavaScript's `substring()` method — counting only from the beginning of the string.

```php
public substring(int $start, ?int $length = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$start` | **int** | The starting index (0-based). |
| `$length` | **int&#124;null** | The number of characters to extract (null for the rest of the string). |

**Return Value:**

`static` - Returns a new instance containing the substring.

**Example:**

```php
echo Str::of('abcdef')->substring(1, 3); // "bcd"
echo Str::of('abcdef')->substring(2);    // "cdef"
```

***

### slice

Extracts a section of the string by start and end indexes.

Supports **negative indexes**, which count from the end of the string. Works like JavaScript's `String.prototype.slice()`, more flexible than `substring()`, since you can use both positive and negative positions.

```php
public slice(int $start, ?int $end = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$start` | **int** | The starting index (can be negative). |
| `$end` | **int&#124;null** | The ending index (exclusive). Can be negative or null for full length. |

**Return Value:**

`static` - Returns a new `Str` instance containing the extracted substring.

**Example:**

```php
echo Str::of('The quick brown fox')->slice(4, 9);  // "quick"
echo Str::of('你好世界')->slice(1, 3);               // "好世"
echo Str::of('The quick brown fox')->slice(-3);    // "fox"
echo Str::of('abcdef')->slice(-4, -1);             // "cde"
```

***

### mask

Mask part of the string using a specified character.

```php
public mask(string $character = '*', string $position = 'center'): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$character` | **string** | The character to use for masking. |
| `$position` | **string** | The mask position (`left`, `right`;, `center`). |

**Return Value:**

`static` - Returns a new instance with the masked string.

**Example:**

```php
$str = new Str('SensitiveData');

echo $str->mask('*', 'center');
// "S******ata"
```

***

### words

Split the string into individual words.

Non-letter or non-number characters are treated as delimiters.

```php
public words(): array<int,string>
```

**Return Value:**

`array<int,string>` - Returns an array of string into words.

**Example:**
```php
$str = new Str('Hello, world! PHP rocks');

print_r($str->words());
// ["Hello", "world", "PHP", "rocks"]
```

***

### slugify

Convert the string into a URL-friendly slug.

```php
public slugify(string $delimiter = '-'): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$delimiter` | **string** | The character used to separate words. |

**Return Value:**

`static` - Returns a new instance containing the slug.

**Example:**
```php
$str = new Str('Hello World!');

echo $str->slugify();
// "hello-world"
```

***

### truncate

Truncate the string to a specified length.

If the string is longer than the limit, it is shortened gracefully.

```php
public truncate(int $limit): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$limit` | **int** | The maximum string length. |

**Return Value:**

`static` - Returns a new instance with the truncated string.

**Example:**
```php
$str = new Str('This is too long');

echo $str->truncate(7);
// "This is"
```

***

### wrap

Wrap the string with another string before and/or after.

```php
public wrap(string $before, string $after): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$before` | **string** | The value to prepend before string value. |
| `$after` | **string** | The value to append after string value. |

**Return Value:**

`static` - Returns a new instance with wrapped content.

**Example:**

```php
$str = new Str('text');

echo $str->wrap('<b>', '</b>');
// "<b>text</b>"
```

***

### between

Extract the text between two substrings.

```php
public between(string $start, string $end): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$start` | **string** | The starting delimiter. |
| `$end` | **string** | The ending delimiter. |

**Return Value:**

`static` - Returns a new instance containing the extracted text or empty string if not found.

**Example:**
```php
$str = new Str('Hello [John]');

echo $str->between('[', ']');
// "John"
```
***

### shuffle

Randomly shuffle or select characters from the string.

This method shuffles the string's characters or returns a random subset.
If `$unique` is true, duplicate characters are removed before shuffling.

```php
public shuffle(?int $length = null, bool $unique = false): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$length` | **int&#124;null** | Optional number of random characters to return.<br />If null, the entire string is randomized. |
| `$unique` | **bool** | If true, ensures characters do not repeat in the result. |

**Return Value:**

`static` - Returns a new instance containing the randomized string.

**Examples:**
```php
$str = new Str('abcdefa');

echo $str->shuffle();         // "dfabcea" (varies)
echo $str->shuffle(3);        // "bfa" (varies)
echo $str->shuffle(3, true);  // "cde" (no repeats)
echo $str->shuffle(unique: true); // "abcdef" (duplicates removed, then shuffled)
```

***

### concat

Concatenate one or more strings to the current string.

```php
public concat(\Stringable|string ...$strings): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `...$strings` | **\Stringable&#124;string** | Arguments of strings or Stringable objects to append. |

**Return Value:**

`static` - Returns a new `Str` instance containing the concatenated result.

** Example:
```php
$str = Str::of('Hello');
$str2 = Str::of('World');

echo $str->concat(' ', $str2); // "Hello World"
```

***

### equals

Check if the string equals another string.

```php
public equals(\Stringable|string $value, bool $caseSensitive = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **\Stringable&#124;string** | The string to compare with. |
| `$caseSensitive` | **bool** | Compare case-sensitively or not. |

**Return Value:**

`bool` - Returns true if equal, false otherwise.

**Example:**
```php
$str = new Str('Hello');

echo $str->equals('hello'); // false
echo $str->equals('hello', false); // true (case-insensitive)
```

***

### trim

Trim whitespace or custom characters from both ends.

```php
public trim(string $characters = " 	\t\n\r\0\x0B"): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$characters` | **string** | Optional characters to trim. |

**Return Value:**

`static` - Returns a new instance with trimmed string.

**Example:**
```php
$str = new Str('  hello  ');

echo $str->trim();
// "hello"
```

***

### trimStart

Trim from the beginning only.

```php
public trimStart(string $characters = " \t\n\r\0\x0B"): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$characters` | **string** | Optional characters to trim. |

**Return Value:**

`static` - Returns a new instance with trimmed start.

**Example:**
```php
echo Str::of('  hello  ')->trimStart(); // "hello  "
```

***

### trimEnd

Trim from the end only.

```php
public trimEnd(string $characters = " \t\n\r\0\x0B"): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$characters` | **string** | Optional characters to trim. |

**Return Value:**

`static` - Returns a new instance with trimmed end.

**Example:**
```php
echo Str::of('  hello  ')->trimEnd(); // "  hello"
```

***

### contains

Check if the string contains another string.

**Alias:** `includes()`

```php
public contains(\Stringable|string $needle, bool $caseSensitive = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$needle` | **\Stringable&#124;string** | The substring to find. |
| `$caseSensitive` | **bool** | Whether to match case-sensitively. |

**Return Value:**

`bool` - Return true if found, false otherwise.

**Example:**
```php
if(Str::of('Hello')->contains('ell')){
    echo 'Match found';
}

// Or with alias
if(Str::of('Hello')->includes('ell')){
    echo 'Match found';
}
```

***

### matches

Test if the string matches a regex pattern.

```php
public matches(string $pattern): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | The Regular expression to match. |

**Return Value:**

`bool` - Return true if it matches, false otherwise.

**Example:**
```php
if(Str::of('abc123')->matches('/\d+/')){
    echo 'Match found';
}
```

***

### search

Find the position of the first match for a regex pattern.

```php
public search(string $pattern): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pattern` | **string** | Regular expression to search for. |

**Return Value:**

`bool` - Returns the index of the first match, or `-1` if not found.

**Example:**

Works like JavaScript's `String.prototype.search()`.

```php
$str = Str::of("I think Ruth's dog is cuter than your dog!");

$pos = $str->search('/[^\w\s\']/'); // 41
echo $str[$pos]; // "!"
```

***

### position

Find the position of a substring.

Retrieves the position of the first occurrence of a substring within the string.

```php
public position(\Stringable|string $needle, int $offset = 0, bool $caseSensitive = true): int|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$needle` | **\Stringable&#124;string** | The substring to find within the string. |
| `$offset` | **int** | The position to start searching from (default: `0`). |
| `$caseSensitive` | **bool** | Whether the search should be case-sensitive (default: `true`). |

**Return Value:**

`int|false` - Returns the position of the first match, or `false` if not found.

**Example:**

```php
$str = Str::of('Hello World');

echo $str->position('World');           // 6
echo $str->position('world', caseSensitive: false); // 6 (case-insensitive)
echo $str->position('PHP');             // false
```

> This method works like PHP's native `strpos()`, but allows optional case-insensitive search when `$caseSensitive` is set to `false`.

***

### normalize

Normalize the string to a specified Unicode form.

This method ensures that visually identical Unicode strings are represented
consistently in memory (useful for comparisons, hashing, or storage).

```php
public normalize(string $form = 'NFC'): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$form` | **string** | The normalization form (Default: `NFC`).<br />Accepted values: `NFC`, `NFD`, `NFKC`, `NFKD`. |

**Return Value:**

`static` - Returns a new normalized string instance.

**Throws:**
- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If intl extension is not available.

**Example:**
```php
// Characters with combining accents may look identical but differ in code units
$str = new Str("e\u{0301}");   // "e" + "́"

echo $str->normalize('NFC')->toString();  // "é"
```

> **Note:**
> Requires the PHP `intl` extension.

***

### toLowerCase

Transform the string value to lowercase.

```php
public toLowerCase(): static
```

**Return Value:**

`static` - Returns a new instance with lowercase value.

**Example:**

```php
echo Str::of('Hello World')->toUpperCase(); // hello world
```

***

### toUpperCase

Transform the string value to uppercase.

```php
public toUpperCase(): static
```

**Return Value:**

`static` - Returns a new instance with uppercase value.

**Example:**

```php
echo Str::of('Hello World')->toUpperCase(); // HELLO WORLD
```

***

### toCamelCase

Transform the string value to camelCase.

```php
public toCamelCase(): static
```

**Return Value:**

`static` - Returns a new instance in camelCase format.

**Example:**

```php
echo Str::of('Hello World')->toCamelCase(); // helloWorld
```

***

### toPascalCase

Transform the string value to PascalCase.

```php
public toPascalCase(): static
```

**Return Value:**

`static` - Returns a new instance in PascalCase format.

**Example:**

```php
echo Str::of('Hello World')->toPascalCase(); // HelloWorld
```

***

### toSnakeCase

Transform the string value to snake_case.

```php
public toSnakeCase(): static
```

**Return Value:**

`static` - Returns a new instance in snake_case format.

**Example:**

```php
echo Str::of('Hello World')->toSnakeCase(); // Hello_World
```

***

### toKebabCase

Transform the string value to kebab-case.

```php
public toKebabCase(bool $toLowerCase = false): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$toLowerCase` | **bool** | Whether to force lowercase. |

**Return Value:**

`static` - Returns a new instance in kebab-case format.

**Example:**

```php
echo Str::of('Hello World')->toKebabCase(); // Hello-World
```

***

### toWellFormed

Ensure the string is well-formed UTF-8.

Invalid byte sequences will be replaced with the Unicode replacement character **(U+FFFD)**.

```php
public toWellFormed(): static
```

**Return Value:**

`static` - Returns a new well-formed string instance.

**Example:**
```php
echo Str::of("Hello\x80World")->toWellFormed(); // "Hello�World"
```

***

### toEncoding

Convert the string to a specific encoding.

```php
public toEncoding(string $toEncoding, ?string $fromEncoding = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$toEncoding` | **string** | The target encoding (e.g., `UTF-8`, `ISO-8859-1`). |
| `$fromEncoding` | **string&#124;null** | Optional source encoding. If null, detects automatically. |

**Return Value:**

`static` - Returns a new `Str` instance in the target encoding.

**Example:**
```php
echo Str::of('Héllo 🌍')->toEncoding('ISO-8859-1'); // "H?llo ?"
echo Str::of('Héllo 🌍')->toEncoding('UTF-8');      // "Héllo 🌍"
```

***

### toString

Get the transformed string value.

```php
public toString(): string
```

**Return Value:**

`string` - Return the final value of string object.

**Example:**

```php
echo Str::of('Hello')->toString(); // "Hello"
```

***

### endsWith

Check if the string ends with a specific substring.

If `$position` is provided, the string is treated as if it were only that long.

```php
public endsWith(\Stringable|string $needle, ?int $position = null, bool $caseSensitive = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$needle` | **\Stringable&#124;string** | The substring to check. |
| `$position` | **\int&#124;null** | The end position at which is expected to be found (default: `null` string length). |
| `$caseSensitive` | **bool** | Whether to match case-sensitively (default: `true`). |

**Return Value:**

`bool` - Returns true if the string ends with the given substring.

**Examples:**
```php
if(Str::of('Hello')->endsWith('o')){
    echo 'Value ends with o';
}

// More examples
Str::of("Hello world")->endsWith("world"); // true
Str::of("Hello world")->endsWith("World", caseSensitive: false); // true (case-insensitive)
Str::of("Hello world")->endsWith("Hello", 5); // true (checks "Hello")
```

***

### startsWith

Check if the string starts with a specific substring.

```php
public startsWith(\Stringable|string $needle, ?int $position = null, bool $caseSensitive = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$needle` | **\Stringable&#124;string** | The substring to check. |
| `$position` | **\int&#124;null** | The start position at which is expected to be found (default: `null` 0). |
| `$caseSensitive` | **bool** |Whether to match case-sensitively (default: `true`). |

**Return Value:**

`bool` - Returns true if the string starts with the given substring.

**Example:**
```php
if(Str::of('Hello')->startsWith('H')){
    echo 'Value starts with H';
}

// More examples

Str::of("Hello world")->startsWith("Hello"); // true
Str::of("Hello world")->startsWith("hello", caseSensitive: false); // true (case-insensitive)
Str::of("Hello world")->startsWith("world", 6); // true (starts at index 6)
```

***

### charAt

Get the character at a specific index.

Supports multibyte characters and returns a `Str` instance. Negative or out-of-range indexes return an empty string.

```php
public charAt(int $index): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **int** | The character position (0-based). |

**Return Value:**

`static` - Return a new `Str` instance containing the character, or empty if out of range.

**Example:**

```php
echo Str::of('Hello')->charAt(1);     // "e"
echo Str::of('你好')->charAt(1);       // "好"
echo Str::of('Hello')->charAt(10);  // ""
```

***

### charCodeAt

Get the UTF-16 code unite of the character at a specific index.

```php
public charCodeAt(int $index): ?int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **int** | The character position (0-based). |

**Return Value:**

`int|null` - Returns the 16-bit code unit or null if out of range

**Example:**
```php
echo Str::of("☃★♲")->charCodeAt(1);   // 9733 (★)
echo Str::of("😀")->charCodeAt(0);     // 55357 (high surrogate)
echo Str::of("😀")->charCodeAt(1);     // 56832 (low surrogate)
```

***

### codePointAt

Get the Unicode code point at the given position.

This differs from `charCodeAt()` in that it returns the full Unicode code point, even for surrogate pairs (e.g., emojis or rare symbols).

```php
public codePointAt(int $index): ?int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **int** | The position of the character. |

**Return Value:**

`int|null` - Returns the Unicode code point, or null if out of range.

**Example:**

```php
echo Str::of('A')->codePointAt(0);     // 65
echo Str::of('你')->codePointAt(0);     // 20320
echo Str::of('😀')->codePointAt(0);     // 128512
```

***

### isWellFormed

Check whether the string is a well-formed **UTF-8** sequence.

This method verifies that all bytes in the string are valid UTF-8 characters.
It does not modify the string — it only checks for encoding validity.

```php
public isWellFormed(): bool
```

**Return Value:**

`bool` - Returns `true` if the string is a valid UTF-8 sequence, or `false` if it contains invalid or malformed bytes.

**Example:**
```php
echo Str::of('Hello')->isWellFormed();          // true
echo Str::of("Hello\xC0World")->isWellFormed(); // false
```

***

### isEmpty

Check if the string is empty.

```php
public isEmpty(): bool
```

**Return Value:**

`bool` - Returns true if empty, false otherwise.

**Example:**
```php
if(Str::of('Hello')->isEmpty()){
    echo 'Is empty value';
}
```

***

### isNumeric

Check if the string is numeric.

```php
public isNumeric(): bool
```

**Return Value:**

`bool` - Returns true if numeric, false otherwise.

**Example:**
```php
if(Str::of('1234567')->isNumeric()){
    echo 'Is Numeric value';
}
```

***

### isHex

Check if value is a hexadecimal representation.

```php
public isHex(): bool
```

**Return Value:**

`bool` - Return true if value is a hexadecimal representation.

**Example:**
```php
if(Str::of('1234567')->isHex()){
    echo 'Is Hexadecimal value';
}
```