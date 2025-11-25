# Input Validation Rules

***

## Overview

Comprehensive list of all built-in input validation rules in Luminova Framework, with parameters, usage examples, and detailed explanations for each rule type.

***

## Introduction

The **Validation Rules** reference lists all built-in rules available in the [Luminova Input Validation Class](/security/validation.md).
These rules help ensure that input data follows the correct format before being processed or saved.

Luminova’s validation system is built to be **simple, predictable, and flexible**.
It supports numeric comparisons, string and format checks, file validation, and even custom logic through callbacks.

Each rule may accept parameters to refine how the validation behaves.
For instance, the `min(5)` rule ensures the input value has a minimum length of **5 characters**.

---

### Applying Rules

You can define validation rules directly within your controller or service class:

```php
$this->input->rules = [
  'email' => 'required|email',
  'username' => 'required|alphanumeric|min(3)|max(20)',
  'age' => 'required|between(18,50)',
];
```

Each field can contain one or more rules separated by a pipe `|`.
Rules may also include parameters inside parentheses — for example, `max(255)` or `between(10,100)`.

---

### Customizing Messages

Each rule supports a default error message, but you can override them easily:

```php
$this->input->messages = [
   'username' => [
      'required' => 'Please enter a username.',
      'alphanumeric' => 'Username can only contain letters and numbers.',
   ],
];
```

This allows you to make validation feedback more natural and user-friendly.

---

### Passing Rule Arguments

Most rules in **Luminova's input validation** can accept optional or required arguments. These arguments are passed as strings and automatically converted to the correct data type internally, so validation works as expected.

#### How Rule Arguments Work

1. **Strings**

   * `'rule("")'` → Empty string
   * `'rule(Text)'` → String `"Text"`
   * `'rule("Text")'` → String `"Text"`

2. **Numbers**
   Numbers are treated like strings initially, then converted for internal use:

   * `'rule(12345)'` → Integers `12345`
   * `'rule(0.5)'` → Decimal number `0.5`
   * `'rule(-10)'` → Negative number `-10`

3. **Booleans**

   * `'rule(true)'` → Truthy `true`
   * `'rule(false)'` → Falsy `false`

4. **Nulls**

   * `'rule(null)'` → Nulled `null`
   * `'rule()'` → Default value or null `null`

5. **Arrays**

   * `'rule([1, 2, 3])'` → Array `[1, 2, 3]`

6. **Objects / JSON**

   * `'rule({key: "value", id: 1})'` → Object key-pair `['key' => 'value', 'id' => 1]`

7. **Multiple Arguments**
   You can pass multiple arguments separated by commas:

   * `'rule([1, 2, 3], ["a", "b"])'` → Multiple arrays `[ [1, 2, 3], ["a", "b"] ]`
   * `'rule({key: "A", id: 1}, {key: "B", id: 2})'` → Multiple objects `[ ['key'=>'A','id'=>1], ['key'=>'B','id'=>2] ]`

8. **Mixed Arguments**
   Any combination of types is allowed:

   * `rule(1, true, null, [1,2,3], {key: "A", id: 1}, text)`

**Example Usage:**

This rule ensures that the `tags` input only contains values from the specified array.

```php
$this->input->rules = [
  'tags' => 'in_array([php, js, css, html], true)'
];
```

---

## Rules 

### none

Ignores the field completely. It always passes, even if empty.
Alias: `nullable`.

| Rule     | Parameter | Description                     |
| -------- | --------- | ------------------------------- |
| **none** | `void`    | Skips validation for the field. |

```php
$this->input->rules = [
   'middle_name' => 'none'
];
```

---

### required

The field must be present and not empty.

| Rule         | Parameter | Description                |
| ------------ | --------- | -------------------------- |
| **required** | `void`    | Makes the field mandatory. |

```php
$this->input->rules = [
   'email' => 'required'
];
```

---

### length

Requires the field string value to have an **exact length**.

| Rule               | Parameter | Description                      |
| ------------------ | --------- | -------------------------------- |
| **length(length)** | `int`     | Exact character length required. |

```php
$this->input->rules = [
   'username' => 'length(4)'
];
```

---

### maxlength

Specifies the **maximum allowed length** for a string value.

| Rule                  | Parameter | Description                           |
| --------------------- | --------- | ------------------------------------- |
| **maxlength(length)** | `int`     | Maximum number of characters allowed. |

```php
$this->input->rules = [
   'username' => 'maxlength(20)'
];
```

---

### minlength

Specifies the **minimum allowed length** for a string value.

| Rule                  | Parameter | Description                            |
| --------------------- | --------- | -------------------------------------- |
| **minlength(length)** | `int`     | Minimum number of characters required. |

```php
$this->input->rules = [
   'password' => 'minlength(8)'
];
```

---

### limit

Requires a **numeric value** to match an exact value-limit.

| Rule             | Parameter | Description                              |
| ---------------- | --------- | ---------------------------------------- |
| **limit(exact)** | `int\|float`      | Exact numeric value required. |

```php
$this->input->rules = [
   'age' => 'limit(30)'
];
```

---

### maxlimit

Specifies the **maximum allowed numeric value-limit**.

| Rule                | Parameter | Description                               |
| ------------------- | --------- | ----------------------------------------- |
| **maxlimit(limit)** | `int\|float`      | Maximum numeric value allowed. |

```php
$this->input->rules = [
   'age' => 'maxlimit(50)'
];
```

---

### minlimit

Specifies the **minimum allowed numeric value-limit**.

| Rule                 | Parameter | Description                                |
| -------------------- | --------- | ------------------------------------------ |
| **minlimit(offset)** | `int\| float`      | Minimum numeric value required. |

```php
$this->input->rules = [
   'age' => 'minlimit(18)'
];
```

---

### size

Requires an **array** to have an exact number of elements.

| Rule            | Parameter | Description                  |
| --------------- | --------- | ---------------------------- |
| **size(count)** | `int`     | Exact array length required. |

```php
$this->input->rules = [
   'tags' => 'size(5)'
];
```

---

### maxsize

Specifies the **maximum allowed number of elements** in an array.

| Rule               | Parameter | Description                   |
| ------------------ | --------- | ----------------------------- |
| **maxsize(limit)** | `int`     | Maximum array length allowed. |

```php
$this->input->rules = [
   'tags' => 'maxsize(10)'
];
```

---

### minsize

Specifies the **minimum allowed number of elements** in an array.

| Rule                | Parameter | Description                    |
| ------------------- | --------- | ------------------------------ |
| **minsize(offset)** | `int`     | Minimum array length required. |

```php
$this->input->rules = [
   'tags' => 'minsize(2)'
];
```

---

### max

Specifies the **maximum allowed value** for numeric values, string length, or array elements.

| Rule            | Parameter | Description                                              |
| --------------- | --------- | -------------------------------------------------------- |
| **max(length)** | `int\|float`      | Maximum value, string length, or array count. |

```php
$this->input->rules = [
   'age'      => 'max(40)',
   'tags'     => 'max(5)',
   'username' => 'max(20)'
];
```

---

### min

Specifies the **minimum allowed value** for numeric values, string length, or array elements.

| Rule            | Parameter | Description                                              |
| --------------- | --------- | -------------------------------------------------------- |
| **min(length)** | `int\|float`      | Minimum value, string length, or array count. |

```php
$this->input->rules = [
   'age'      => 'min(18)',
   'tags'     => 'min(2)',
   'username' => 'min(5)'
];
```

---

### fixed

Specifies the **exact allowed value** for numeric values, string length, or array elements.

| Rule              | Parameter | Description                                                        |
| ----------------- | --------- | ------------------------------------------------------------------ |
| **fixed(length)** | `int\|float`      | Value, string length, or array count must match exactly. |

```php
$this->input->rules = [
   'age'      => 'fixed(20)',
   'tags'     => 'fixed(3)',
   'username' => 'fixed(5)'
];
```

---

### between

Ensures a value lies **between two limits**. Works for numeric, string (length), and array elements.

| Rule                 | Parameter | Description                               |
| -------------------- | --------- | ----------------------------------------- |
| **between(min,max)** | `(float\|int, float\|int)`      | Value must be ≥ min and ≤ max. |

```php
$this->input->rules = [
   'age'       => 'between(18, 60)',  // numeric
   'tags'      => 'between(2, 10)',  // array elements
   'username'  => 'between(3, 20)',   // string length
];
```

---

### alphanumeric

Ensure that field value is only letters and numbers.

| Rule             | Parameter | Description                                |
| ---------------- | --------- | ------------------------------------------ |
| **alphanumeric** | `void`    | Only letters (a–z, A–Z) and numbers (0–9). |

```php
$this->input->rules = [
   'user_id' => 'alphanumeric'
];
```

---

### alphabet

Ensure that field value consist of only letters.

| Rule         | Parameter | Description              |
| ------------ | --------- | ------------------------ |
| **alphabet** | `void`    | Only letters (a–z, A–Z). |

```php
$this->input->rules = [
   'first_name' => 'alphabet'
];
```

---

### numeric

Ensure that the field value is numeric.

| Rule        | Parameter | Description                         |
| ----------- | --------- | ----------------------------------- |
| **numeric** | `void`    | Must be numeric (integer or float). |

```php
$this->input->rules = [
   'age' => 'numeric'
];
```

**Note:**
>
> Accepts strings that represent numbers, e.g., `42` or `3.14`.
> Uses PHP `is_numeric()` internally.

---

### digit

Ensure that field value consists of digits only.

| Rule        | Parameter | Description                         |
| ----------- | --------- | ----------------------------------- |
| **digit** | `void`    | Value must contain only digits (e.g, `1-0`). |

```php
$this->input->rules = [
   'age' => 'digit'
];
```

> **Notes:**
>
> Uses `ctype_digit()` internally.
> Only accepts non-negative integer (e.g., `0`, `123`).
> Does not accept negative numbers, floats, or numeric strings like `3.14`.

---

### integer

Ensures the value is an integer.
You can optionally specify whether it must be `positive` or `negative`.

| Rule              | Parameter       | Description                                                   |
| ----------------- | --------------- | ------------------------------------------------------------- |
| **integer(sign)** | `?string: none` | Must be integer. Optionally specify `positive` or `negative`. |

```php
$this->input->rules = [
   'quantity' => 'integer(positive)'
];
```

---

### float

Ensures the value is a floating-point number.
You can optionally specify `positive` or `negative`.

| Rule            | Parameter       | Description                                                 |
| --------------- | --------------- | ----------------------------------------------------------- |
| **float(sign)** | `?string: none` | Must be float. Optionally specify `positive` or `negative`. |

```php
$this->input->rules = [
   'rating' => 'float(positive)'
];
```

---

### decimal

Checks that the value is a **floating-point number containing a decimal point**.
Integers or scientific notation (e.g., `1e3`) are not considered valid decimals.

Optionally specify `positive` or `negative`.

| Rule              | Parameter       | Description                                     |
| ----------------- | --------------- | ----------------------------------------------- |
| **decimal(sign)** | `?string: none` | Must be a valid decimal number (e.g., `12.5`).  |

```php
$this->input->rules = [
   'price' => 'decimal(positive)'
];
```

---

### boolean

Validates typical **boolean values** such as `'true'`, `'false'`, `1`, or `0`.

| Rule        | Parameter | Description                                                  |
| ----------- | --------- | ------------------------------------------------------------ |
| **boolean** | `void`    | Must be a boolean-like value (`true`, `false`, `1`, or `0`). |

```php
$this->input->rules = [
   'is_active' => 'boolean'
];
```

---

### binary

Validates whether the value represents a **binary sequence** or a **PHP-style binary literal**.
By default, this rule accepts numbers containing only `0` and `1`, or strings like `0b1010`.
It does **not** allow non-text or raw binary data unless you explicitly enable printable fallback.

| Rule                               | Parameter                      | Description                                                                                                                                           |
| ---------------------------------- | ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **binary(strict, allowPrintable)** | `(?bool: false, ?bool: false)` | Validates binary digits or PHP-style binary literals (e.g., `1010`, `0b1010`). If `$allowPrintable` is `true`, printable ASCII text is also accepted. |

```php
$this->input->rules = [
   // Accepts "1010", "0b1010"
   'binary_code' => 'binary',

   // Strict mode: only "0" and "1" sequences allowed
   'pure_bits' => 'binary(true)',

   // Non-strict with printable ASCII fallback
   'file_content' => 'binary(false,true)'
];
```

---

### hexadecimal

Checks that the value is a **valid hexadecimal string** `(0–9, A–F, a–f)`.
It must be a string and cannot contain non-hex characters.

| Rule            | Parameter | Description                                          |
| --------------- | --------- | ---------------------------------------------------- |
| **hexadecimal** | `void`    | Must be a valid hexadecimal string (e.g., `a3f9b0`). |

```php
$this->input->rules = [
   'hash' => 'hexadecimal',
   'color_code' => 'hexadecimal'
];
```

---

### string

Ensures the value is a string.

| Rule       | Parameter | Description     |
| ---------- | --------- | --------------- |
| **string** | `void`    | Must be string. |

```php
$this->input->rules = [
   'title' => 'string'
];
```

---

### array

Ensures the value is an array.

| Rule      | Parameter | Description    |
| --------- | --------- | -------------- |
| **array** | `void`    | Must be array. |

```php
$this->input->rules = [
   'items' => 'array'
];
```

---

### json

Validates that the value is valid JSON.

| Rule     | Parameter | Description         |
| -------- | --------- | ------------------- |
| **json** | `void`    | Must be valid JSON. |

```php
$this->input->rules = [
   'config' => 'json'
];
```

---

### url

Validates that the value is a properly formatted URL.

| Rule    | Parameter | Description        |
| ------- | --------- | ------------------ |
| **url** | `void`    | Must be valid URL. |

```php
$this->input->rules = [
   'website' => 'url'
];
```

---

### scheme

Ensures the URL matches the specified scheme (e.g., `http`, `https`).

| Rule              | Parameter | Description                  |
| ----------------- | --------- | ---------------------------- |
| **scheme(value)** | `string`  | Must match given URL scheme. |

```php
$this->input->rules = [
   'site' => 'scheme(https)'
];
```

---

### ip

Validates that the value is a valid IP address.
You can specify the version: `4`, `6`, or `0` for any.

| Rule            | Parameter | Description                        |
| --------------- | --------- | ---------------------------------- |
| **ip(version)** | `?int:0`  | Must be valid IPv4, IPv6, or both. |

```php
$this->input->rules = [
   'client_ip' => 'ip(4)'
];
```

---

### email

Validates that the value is a properly formatted email address.

| Rule      | Parameter | Description                  |
| --------- | --------- | ---------------------------- |
| **email** | `void`    | Must be valid email address. |

```php
$this->input->rules = [
   'email' => 'email'
];
```

---

### latitude

**Alias:** `lat`

Ensures a field value is a valid latitude.
Latitude must be between **-90** and **90**.

* When `strict` is `true`, it also checks numeric format and decimal precision based on the `precision` parameter.

| Rule                                                             | Parameter               | Description                                                                  |
| ---------------------------------------------------------------- | ----------------------- | ---------------------------------------------------------------------------- |
| **latitude(isStrict, precision)** | `(?bool:false, ?int:6)` | Must be a valid latitude. Strict mode enforces numeric format and precision. |

```php
$this->input->rules = [
   'latitude' => 'lat(true, 6)'
];
```

---

### longitude

**Alias:** `lng`

Ensures a field value is a valid longitude.
Longitude must be between **-180** and **180**.

* When `strict` is `true`, it also checks numeric format and decimal precision based on the `precision` parameter.

| Rule                                                              | Parameter               | Description                                                                   |
| ----------------------------------------------------------------- | ----------------------- | ----------------------------------------------------------------------------- |
| **longitude(isStrict, precision)** | `(?bool:false, ?int:6)` | Must be a valid longitude. Strict mode enforces numeric format and precision. |

```php
$this->input->rules = [
   'longitude' => 'longitude(true, 6)'
];
```

---

### latlng

Ensures a field value is a valid **latitude,longitude** string.
Format: `latitude,longitude` (e.g., `12.971603,77.594605`).

* Both latitude and longitude are validated according to their respective ranges.
* When `strict` is `true`, decimal precision is enforced for both values.

| Rule                            | Parameter               | Description                                                                                       |
| ------------------------------- | ----------------------- | ------------------------------------------------------------------------------------------------- |
| **latlng(isStrict, precision)** | `(?bool:false, ?int:6)` | Must be a valid latitude and longitude string. Strict mode enforces numeric format and precision. |

```php
$this->input->rules = [
   'coordinates' => 'latlng(true, 6)'
];
```

---

### username

Ensure that field value is a valid usernames format.
Optionally allow uppercase letters and define a list of reserved names to reject.

| Rule                                       | Parameter                 | Description                                                        |
| ------------------------------------------ | ------------------------- | ------------------------------------------------------------------ |
| **username(allowUppercase, reservedList)** | `(?bool:true, ?array:[])` | Must be valid username; can restrict uppercase and reserved names. |

```php
$this->input->rules = [
   'username' => 'username(false, [admin,root])'
];
```

> The rule is useful for validating usernames during account creation, ensuring that new users don’t choose reserved or restricted names.

---

### phone

Ensure that field value is a valid phone number.
Supports **minimum and maximum length**.

| Rule                | Parameter            | Description                 |
| ------------------- | -------------------- | --------------------------- |
| **phone(minLength, maxLength)** | `(?int:10, ?int:15)` | Must be valid phone number. |

```php
$this->input->rules = [
   'phone' => 'phone(10, 15)'
];
```

---

### uuid

Ensures that a field value is a valid **UUID** of the specified version.
Supported versions: `1–5`.

| Rule              | Parameter | Description                                |
| ----------------- | --------- | ------------------------------------------ |
| **uuid(version)** | `int`     | Must be a valid UUID of the given version. |

```php
$this->input->rules = [
   'identifier' => 'uuid(4)'
];
```

---

### match

Checks if a field value matches a given regular expression.

| Rule               | Parameter | Description                         |
| ------------------ | --------- | ----------------------------------- |
| **match(pattern)** | `regex`   | Value must match the regex pattern. |

```php
$this->input->rules = [
   'slug' => 'match(^[a-z0-9_-]+$)'
];
```

---

### equals

Checks if the field value matches another field’s value.

| Rule              | Parameter | Description                                  |
| ----------------- | --------- | -------------------------------------------- |
| **equals(field)** | `string`  | Value must equal the value of another field. |

```php
$this->input->rules = [
   'confirm_password' => 'equals(password)'
];
```

---

### not_equal

Ensures the value does not equal another field’s value.

| Rule                 | Parameter | Description                                |
| -------------------- | --------- | ------------------------------------------ |
| **not_equal(field)** | `string`  | Must not equal the value of another field. |

```php
$this->input->rules = [
   'new_password' => 'not_equal(old_password)'
];
```

---

### in_array

Validates that a field value exists within a specified list of values.
If `strict` is `true`, the type of the field value is also checked **(default is true)**.

| Rule               | Parameter     | Description                          |
| ------------------ | ------------- | ------------------------------------ |
| **in_array(values, strict)** | `(array, bool: true)` | Field value must match one of the listed values. |

```php
$this->input->rules = [
   'role' => 'in_array([admin,user,guest])'
];
```

---

### is_value

Ensures the field value matches exactly the given value.

| Rule                | Parameter | Description                     |
| ------------------- | --------- | ------------------------------- |
| **is_value(value)** | `mixed`  | Must equal the specified value. |

```php
$this->input->rules = [
   'status' => 'is_value(active)'
];
```

---

### is_list

Validates a **comma-separated list** (like `apple,banana,mango`) and optionally ensures a minimum number of items.

| Rule             | Parameter | Description                                                                                             |
| ---------------- | --------- | ------------------------------------------------------------------------------------------------------- |
| **is_list(min)** | `?int: 1` | Must be a valid comma-separated list with at least `min` items. Defaults to 1, allowing a single value. |

```php
$this->input->rules = [
   'tags' => 'is_list',       // At least 1 item required (default)
   'indexes' => 'is_list(3)', // At least 3 items required
];
```

**Examples:**

```php
// Valid inputs
'tags' => 'apple'          // true (1 item)
'tags' => 'apple,banana'   // true (2 items)
'indexes' => '1,2,3'       // true (3 items)

// Invalid inputs
'tags' => ''             // false (empty string)
'indexes' => '1,2'       // false (less than min 3)
'tags' => 'foo,,bar'     // false (empty item between commas)
```

---

### keys_exists

Validates that an array field contains the specified keys.
When `strict` is `true`, the array **must not contain any additional keys** beyond those specified.

| Rule                  | Parameter     | Description                           |
| --------------------- | ------------- | ------------------------------------- |
| **keys_exists(keys, strict)** | `(array, bool: false)` | Ensures the array has all specified keys. If `$strict` is true, no extra keys are allowed. |

```php
$this->input->rules = [
   // Must contain all specified keys, extra keys allowed
   'user_data' => 'keys_exists([name,email,age])',       

   // Must contain only the specified keys, no extra keys
   'user_data' => 'keys_exists([name,email,age], true)'  
];
```

---

### key_exists

Validates that an array field contains **at least one** of the specified keys.

| Rule                  | Parameter     | Description                           |
| --------------------- | ------------- | ------------------------------------- |
| **key_exists(keys)** | `array` | Ensures the array contains at least one of the specified keys. |

```php
$this->input->rules = [
   'options' => 'key_exists([name,email,age])'  
];
```

---

### path

Validates that the value is a valid file or directory path.
Optionally check if it’s `readable` or `writable`.

| Rule            | Parameter | Description                                                     |
| --------------- | --------- | --------------------------------------------------------------- |
| **path(access)** | `string`  | Must be valid path; check readability/writability if specified. |

```php
$this->input->rules = [
   'file_path' => 'path(readable)'
];
```

---

### callback

Executes a custom validation function on the field value.

The callback receives the field value and optionally the field name:

* `fn(mixed $value, string $field): bool` (default, 2 arguments)
* `fn(mixed $value): bool` (if `maxArgs` is set to 1)

The function **must return `true` or `false`** to indicate whether the value passes validation.

| Rule                      | Parameter            | Description                                          |
| ------------------------- | -------------------- | ---------------------------------------------------- |
| **callback(fn, maxArgs)** | `(callable, int: 2)` | Calls a user-defined function to validate the field. |

```php
$this->input->rules = [
   'amount' => 'callback(checkAmount)',      // 2 arguments by default
   'list'   => 'callback(is_array, 1)'      // Only value argument, avoid field name
];
```

**Supported Callable Formats:**

```php
'callable(myFunction)'                        // Plain function
'callable([\Foo\Namespace\MyClass, method])' // Static class method
'callable(\Foo\Namespace\MyClass@method)'    // Instance method (auto-instantiated)
'callable(\Foo\Namespace\MyClass::method)'   // Static method
```

**Signature Notes:**

* `callable(myFunction, 2)`: 

   ```php
   function myFunction(mixed $value, string $field): bool
   { 
      // ...
   }
   ```

* `callable(myFunction, 1)`: 

   ```php
   function myFunction(mixed $value): bool
   {
      // ...
   }
   ```

> **Note:**
> * **Instance methods** (`Class@method`) are automatically instantiated.
> * **Static methods** (`Class::method` or `[Class::class, method]`) are called directly.
> * Only **string function names, static arrays, and class method strings** are supported. Objects are **not supported**.

---

### default

Uses a fallback default value if the field is empty or not included in request data.

Alias: `fallback()`.

| Rule                | Parameter | Description                                        |
| ------------------- | --------- | -------------------------------------------------- |
| **fallback(value)** | `mixed` | Default value if empty (defaults to `null`). |

```php
$this->input->rules = [
   'country' => 'default(Unknown)'
];
```

> **Note:**
> Default cannot be used with other strict rule validation (e.g, `required`, `in_array` etc...).