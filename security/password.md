# Secure Password Handling and Verification

***

## Overview

Securely manage and validate user passwords in your application. The Password class supports hashing, rehashing, verification, strength checking, and breach detection using HIBP Pwned API.

***

## Introduction

The **Password** class provides secure, modern, and convenient tools for handling user passwords in your Luminova application.
It simplifies everything from hashing and verifying passwords to checking strength, masking, and detecting compromised or weak passwords.

This class helps you safely manage user passwords without manually handling algorithms or security details.
It automatically selects the strongest available algorithm (like **Argon2id** or **BCRYPT**) and includes helper methods for password **validation, verification, and maintenance** — all built on PHP’s native cryptographic functions and best practices.

***

## Class Definition

* Full namespace: `\Luminova\Security\Password`
* This class is marked as **final** and can't be subclassed

---

## Methods

### hash

Generate a secure password hash using the specified or default algorithm.
	 
This method automatically selects the most secure available algorithm **(Argon2id, Argon2i, or BCRYPT**) unless overridden in `$options` using the keys `algorithm` or `algo`.
	 
The hashing process uses PHP's built-in `password_hash()` function, ensuring compatibility with `password_verify()` and `password_needs_rehash()`.

**Options:**

- **algorithm|algo**: One of `PASSWORD_DEFAULT`, `PASSWORD_BCRYPT`, `PASSWORD_ARGON2I`, or `PASSWORD_ARGON2ID`.  
- Any algorithm-specific options (e.g., `cost`, `memory_cost`, `time_cost`).

```php
public static hash(string $password, ?array $options = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | Plain password to hash. Must not be empty. |
| `$options` | **array&#124;null** | Optional password hash options<br/>(e.g, `['algorithm' => PASSWORD_BCRYPT, 'cost' => 12]`). |

**Return Value:**

`string` - Returns the hashed password string.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - Throws:
	- If the password is empty.
	- If contains leading or trailing spaces.
	- If contains invalid or invisible characters.
	- If contains tabs, carriage returns, or newlines.
	- If an unsupported algorithm is specified.

**Examples:**

Hashing client login password.

```php
// Default hashing (uses the most secure available algorithm)
$hash = Password::hash('StrongPass123!');

// Specify algorithm and cost
$hash = Password::hash('StrongPass123!', [
    'algorithm' => PASSWORD_BCRYPT,
    'cost' => 12
]);
```

---

### pin

Generate a random secure numeric PIN and its hashed value.

```php
public static pin(
    int $length = 6, 
    ?string $label = null,
    ?string $algo = null
): array 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$length` | **int** | The length of the PIN to generate (Default: `6 digits`). |
| `$label` | **string&#124;null** | Optional label (contextual identifier, e.g. user ID or card ID). |
| `$algo` | **string&#124;null** | The hashing algorithm e.g. `sha256`, `sha512` (default: `sha256`). |

**Return Value:**

`array{0:string,1:string,2:string}` - Returns an array with:
 - [0] => raw PIN  
 - [1] => hashed PIN  
 - [2] => label used

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the application key is missing (`env('app.key')`).

**Examples:**

Generate a secure PIN code.

```php
[$pin, $hash, $label] = Password::pin(6, 'user1234');

echo "PIN: {$pin}, HASH: {$hash}";
// PIN: 482193, HASH: 4bfa2e... (depends on app.key)
```

***

### verify

Verify a password against its stored hash and rehash if needed.

If the password matches and `$onNeedRehash` is provided, this method will check whether the hash should be rehashed with the current options. If so, the callback is invoked with the new hash so it can be stored.

```php
public static verify(
	string $password,
	string $hash,
	?callable $onNeedsRehash = null,
	?array $options = null
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | The plain password to verify. |
| `$hash` | **string** | The stored password hash to verify against. |
| `$onNeedsRehash` | **callable&#124;null** | Optional callback to handle rehash.<br/>Function signature: `callable(string $newHash): void` |
| `$options` | **array&#124;null** | Optional algorithm options for rehash (e.g, `['algorithm' => PASSWORD_BCRYPT, 'cost' => 12]`). |

**Return Value:**

`bool` - Return true if the password is valid, false otherwise.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the algorithm provided in options is invalid.

> **Note:**
> This method verifies only password created by `Password::hash()`, `Password::rehash()` or `password_hash()`.

**Examples**

Basic password verification

```php
use Luminova\Security\Password;

$storedHash = '$2y$10$OZs8HqQbYJkFt9f8kJQ5COnhQZNYa3LcvxK7DChN4F4R2j1U8qXn2';
$userPassword = $request->getPost('password');

if (Password::verify($userPassword, $storedHash)) {
    echo "Password is valid.";
} else {
    echo "Invalid password.";
}
```

Verify and automatically rehash if needed

```php
use Luminova\Security\Password;

$userId = $request->getPost('user_id');

$passwordHash = getUserHashFromDatabase($userId);
$userPassword = $request->getPost('password');

$isPasswordValid = Password::verify(
    $userPassword,
    $passwordHash,
    function (string $newHash) use ($userId): void {
        updateUserPassword($userId, $newHash);
    }
);

if ($isPasswordValid) {
    echo "Password is valid.";
} else {
    echo "Invalid password.";
}
```

---

Verify with custom hashing options

```php
use Luminova\Security\Password;

$options = ['cost' => 12];

Password::verify(
    'userPassword',
    $existingHash,
    options: $options
);
```

---

### isPin

Verify if a provided PIN matches its hashed version.

```php
public static function isPin(
    string $pin, 
    string $hash,
    ?string $label = null, 
    ?string $algo = null
): bool 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pin` | **string** | TThe plain numeric PIN. |
| `$hash` | **string** | The stored hashed PIN to verify against. |
| `$label` | **string&#124;null** | The label used during hashing. |
| `$algo` | **string&#124;null** | The hashing algorithm used. |

**Return Value:**

`bool` - Returns true if the PIN is valid, false otherwise.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the application key is missing (`env('app.key')`).

**Examples:**

Verify a PIN code.

```php
[$pin, $hash] = Password::pin(6, 'user1234');

if (Password::isPin($pin, $hash, 'user1234')) {
    echo "PIN verified!";
} else {
    echo "Invalid PIN.";
}
```

***

### rehash

Verify a password and return a new hash if rehashing is required.

```php
public static rehash(string $password, string $hash, ?array $options = null): ?string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | Plain password to verify. |
| `$hash` | **string** | Existing password hash. |
| `$options` | **array&#124;null** | Options for rehash. |

**Return Value:**

`string|null` - Returns a new hash if password is valid and rehashing needed, otherwise null.

**Examples**

Rehash when algorithm or cost changes

```php
use Luminova\Security\Password;

$oldHash = getUserHashFromDatabase($userId);
$userPassword = $request->getPost('password');

// Attempt to rehash if needed
if ($newHash = Password::rehash($userPassword, $oldHash)) {
    updateUserPassword($userId, $newHash);
    echo "Password rehashed successfully.";
} else {
    echo "No rehash needed.";
}
```

Rehash with custom options

```php
use Luminova\Security\Password;

$options = ['cost' => 12];

$newHash = Password::rehash('userPassword', $storedHash, $options);

if ($newHash) {
    updateUserPassword($userId, $newHash);
}
```

***

### salt

Generate a random salt.

```php
public static salt(int $length = 16): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$length` | **int** | Length of salt in bytes (default: 16). |

**Return Value:**

`string` - Return generated salt Base64-encoded.

**Example**

Generate a random salt

```php
use Luminova\Security\Password;

// Generate a 16-byte salt (default)
$salt = Password::salt();
echo $salt;

// Generate a longer 32-byte salt
$strongSalt = Password::salt(32);
echo $strongSalt;
```

***

### strength

Calculate how many character categories a password matches.

Categories checked:
- Minimum length (N+ characters)
- Numbers
- Uppercase letters
- Lowercase letters
- Special characters
- Not a common known password

```php
public static strength(string $password, int $min = 8): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | Password to analyze. |
| `$min` | **int** | Minimum password length (default 8). |

**Return Value:**

`int` - Return the number of matched categories (0–6).

**Examples:**

Calculate password strength score

```php
use Luminova\Security\Password;

// Get numeric strength score (0–6)
$score = Password::strength('MyPass123!');
echo "Strength score: $score";

// Check very weak password
$score = Password::strength('12345');
echo "Weak password score: $score";

// Check strong complex password
$score = Password::strength('Super!Secure2025');
if ($score >= 5) {
    echo "This password is very strong.";
}
```

***

### random

Generate a cryptographically secure random password.

Length must be greater than 0.

```php
public static random(int $length = 8): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$length` | **int** | Password length (default: 8). |

**Return Value:**

`string` - Return secure random password.

**Example:**

Generates a secure password of 16 characters.

```php
$password = Password::random(16);

echo $password; // e372yKdYu2jj@51!
```

***

### mask

Mask a password for safe display (e.g. in logs).

This method replaces all but the last *N* characters with `*`.

```php
public static mask(string $password, int $visible = 3): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | Password to mask. |
| `$visible` | **int** | The number of last characters to leave visible (minimum: 0). |

**Return Value:**

`string` - Returns the masked password string.

**Examples:**

Masking a Password

```php
use Luminova\Security\Password;

$password = 'SuperSecret123';
echo Password::mask($password);       
// Output: **********123

echo Password::mask($password, 4);    
// Output: *********t123

Password::mask('Secret123', 0);  
// Output: *********
```

> Use this when logging or debugging sensitive data without revealing full passwords.

***

### isValid

Check if a given password matches the user's old hashed password.

This method securely verifies whether the provided password is the same as the previously stored password by comparing it against the hash.

```php
public static isValid(string $password, string $hash): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | The plain-text password to verify. |
| `$hash` | **string** | The hashed password stored in the database. |

**Return Value:**

`bool` - Returns true if the password matches the old one, false otherwise.

> **Note:**
> This method verifies only password created by `Password::hash()`, `Password::rehash()` or `password_hash()`.

**Examples**

Simple login password verification.

```php
use Luminova\Security\Password;

// Plain password
$password = '12345@123';

// Hash plain password
$hash = Password::hash($password);

if (Password::isValid($password, $hash)) {
    echo "Password is valid.";
} else {
    echo "Invalid password.";
}
```

***

### isRehashable

Check if a password hash needs rehashing using current options.

```php
public static isRehashable(string $hash, ?array $options = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$hash` | **string** | Existing password hash. |
| `$options` | **array&#124;null** | Algorithm options to check against. |

**Return Value:**

`bool` - Return true if hash should be rehashed.

**Example:**

Check if a stored password hash needs rehashing

```php
use Luminova\Security\Password;

$storedHash = getUserHashFromDatabase($userId);

// Check with default algorithm and options
if (Password::isRehashable($storedHash)) {
    echo "Hash needs rehashing.";
}

// Check with custom cost option
$options = ['cost' => 12];
if (Password::isRehashable($storedHash, $options)) {
    echo "Rehash required due to cost update.";
}
```

***

### isStrong

Check if a password meets strength requirements.

Strength is measured by counting how many of the following categories appear:
- Numbers
- Uppercase letters
- Lowercase letters
- Special characters
- Minimum length (8+ characters)

```php
public static isStrong(string $password, int $complexity = 4, int $min = 8, ?int $max = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | Password to check. |
| `$complexity` | **int** | Required number of categories (1–6) (default 4). |
| `$min` | **int** | Minimum password length (default 8). |
| `$max` | **int&#124;null** | Optional maximum length. Null means no limit. |

**Return Value:**

`bool` - Return true if the password meets both length and complexity requirements.

**Examples**

Check if a password is strong

```php
use Luminova\Security\Password;

// Basic check with defaults
if (Password::isStrong('MyPass123!')) {
    echo "Password is strong enough.";
}

// Require higher complexity (e.g., all 6 categories)
if (Password::isStrong('MyPass123!', 6)) {
    echo "Password passes the strictest strength test.";
}

// Check with custom minimum and maximum length
if (Password::isStrong('Secure123@', 4, 10, 20)) {
    echo "Password meets custom length and complexity requirements.";
}
```

***

### isSecure

Check if a password is **not** in the list of known insecure passwords.

This method validates the given password against a predefined list of weak, common, or easily guessable passwords. You can also provide your own list of insecure passwords, or merge it with the default internal list.

```php
public static isSecure(string $password, array $insecure = [], bool $includeDefaults = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | The password to check. |
| `$insecure` | **array** | Optional custom list of insecure passwords. |
| `$includeDefaults` | **bool** | Whether to include the default insecure list (default: true). |

**Return Value:**

`bool` - Returns true if the password is considered secure (not found in any insecure list).

**Default Insecure Password List**

The default list of weak and commonly used passwords includes the following values:

`123456`, `123456789`, `password`, `12345678`, `qwerty`, `p@ssw0rd`, `12345`, `1234567`, `111111`, `123123`, `abc123`, `12345@123`, `@dmin`, `password1`, `1234`, `iloveyou`, `1q2w3e4r`, `admin`, `admin@123`, `welcome`, `monkey`, `login`, `letmein`, `princess`, `12345@12345`, `solo`, `passw0rd`, `starwars`, `dragon`, `sunshine`, `mypassword`, `master`, `hello`, `freedom`, `whatever`, `qazwsx`, `admin123`, `test123`, `guest`, `access`, `secret`, `654321`, `asdfgh`, `qwert`, `1q2w3e`, `football`, `welcome1`, `changeit`, `testtest`, `default`.

> **Note:**
> You can add more insecure passwords or disable this default list using the `$includeDefaults` parameter in `Password::isSecure()`.

**Examples:**

Check if a password is not among insecure ones

```php
use Luminova\Security\Password;

// Default check against built-in insecure list
if (Password::isSecure('password')) {
    echo "Password is secure.";
} else {
    echo "Password is commonly used and insecure.";
}

// Check with additional custom insecure list
$extraWeak = ['welcome123', 'letmein', 'admin'];
if (Password::isSecure('welcome123', $extraWeak)) {
    echo "Password is safe.";
} else {
    echo "Password found in insecure list.";
}

// Check without merging built-in weak passwords
if (Password::isSecure('mypassword', ['test123'], false)) {
    echo "Checked only against custom list.";
}
```

***

### isCompromised

Determine if a password has been exposed in public data breaches.

Uses the **Have I Been Pwned (HIBP)** Pwned Passwords API with the k-anonymity model: only the first 5 characters of the password's **SHA-1** hash are sent to the API, ensuring the full password is never
exposed over the network.

```php
public static isCompromised(string $password, float $timeout = 5.0, int $ttl = 432000): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | Plain text password to check. |
| `$timeout` | **float** | Request connection timeout in seconds (default: `5.0`). |
| `$ttl` | **int** | Time-to-live for cached results in seconds (default: `432000` = 5 days).<br/>Set 0 to disable caching. |

**Return Value:**

`bool` - Return true if the password is found in the breach database, false if not.

**Throws:**
- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the HIBP API request fails.

**Example:**

Checking if a Password Was Compromised

```php
use Throwable;
use Luminova\Security\Password;

try {
    if (Password::isCompromised('password123')) {
        echo 'This password was found in known data breaches.';
    } else {
        echo 'Password not found in breach database.';
    }
} catch (Throwable $e) {
    echo 'API check failed: ' . $e->getMessage();
}
```

To reduce repeated API calls, results are cached locally by prefix.

```php
use Luminova\Security\Password;

// quick check (in-memory + 5days file cache)
$compromised = Password::isCompromised('password123');

// disable file cache (still uses in-memory for this process)
$compromised = Password::isCompromised('password123', ttl: 0);

// use a shorter ttl (24 hours)
$compromised = Password::isCompromised('password123', ttl: 86400);
```

> **Note:**
> The `isCompromised()` method uses the **Have I Been Pwned** API (with privacy-safe k-anonymity)
> to verify if a password has appeared in public breaches.
> It only sends the first 5 characters of the SHA-1 hash — never the full password.

***

### hasSpaces

Check if a password contains whitespace.

By default, this method detects **any whitespace** in the password (spaces, tabs, line breaks). You can also restrict it to only check for **leading or trailing spaces** using the `$startAndEndOnly` flag.

```php
public static hasSpaces(string $password, bool $startAndEndOnly = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$password` | **string** | The plain-text password to check. |
| `$startAndEndOnly` | **bool** | If true, only check for leading/trailing spaces (default: false). |

**Return Value:**

`bool` - Returns true if the password contains whitespace as specified.

**Examples**

Check for any whitespace (spaces, tabs, newlines) anywhere in the password.

```php
use Luminova\Security\Password;

// Plain password
$password = 'pass word123';

if (Password::hasSpaces($password)) {
    echo "Password contains whitespace anywhere.";
} else {
    echo "Password does not contain any whitespace.";
}
```

Check for leading or trailing spaces only.

```php
use Luminova\Security\Password;

// Plain password
$password = '  secret123 ';

if (Password::hasSpaces($password, startAndEndOnly: true)) {
    echo "Password has leading or trailing spaces.";
} else {
    echo "Password has no leading or trailing spaces.";
}
```