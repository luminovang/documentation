# Basic Mathematical Operations Helpers

***

## Overview

Provides basic methods for currency conversion, unit conversion, averages, ratings, discounts, distance and interest calculations.

***

## Introduction

The `Maths` class provides a set of static methods for essential mathematical operations. It includes utilities for currency and unit conversions, discount and interest calculations, and other common tasks. Designed to simplify development.

---

### Usages 

The methods can be accessed through global function  helper, `Factory` instance or call statically.

```php
func('math')->money(5000);
// 5000.00
```

Or directly call math method.

```php
func()->math()->money(5000);
// 5000.00
```

Or using factory instance to initialize function and call math method.

```php
use Luminova\Application\Factory;

Factory::functions()->math()->money(5000);
// 5000.00
```

Or continently call it statically.

```php
use Luminova\Functions\Maths;

Maths::money(5000);
```

---

## Class Definition

* Class namespace: `\Luminova\Functions\Maths`
* This class is marked as **final** and can't be subclassed

---

## Methods

***

### toUnit

Converts bytes to the appropriate unit.

```php
public static toUnit(int $bytes, bool $addName = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$bytes` | **int** | The number of bytes to convert. |
| `$addName` | **bool** | Whether to include the unit name in the result (default: false). |

**Return Value:**

`string` - Return the converted value with optional unit name.

***

### toBytes

Converts a unit name to bytes.

```php
public static toBytes(string $units): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$units` | **string** | The string representation of the byte size (e.g., '1KB', '2MB'). |

**Return Value:**

`int` - Return the size in bytes.

***

### toTimeUnit

Converts a given time in milliseconds to a human-readable format with appropriate time units (e.g., milliseconds, seconds, minutes, etc.).

```php
public static toTimeUnit(float|int $milliseconds, bool $addName = false, bool $fullName = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$milliseconds` | **float\|int** | The time duration in milliseconds to be converted. |
| `$addName` | **bool** | Whether to include the unit name in the output (default: false). |
| `$fullName` | **bool** | Whether to use the full name of the unit (e.g., 'seconds' instead of 's'). |

**Return Value:**

`string` - Return the formatted time duration with up to two decimal precision.

***

### average

Calculate the average of a giving numbers.

```php
public static average(int|float ...$numbers): float|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$numbers` | **int&#124;float** | The arguments integers or float values to calculate the average<br />- @example average(10, 20, 30, 40, 50) - return 30 as the average. |

**Return Value:**

`float|null` - Return the average of the passed numbers.

***

### rating

Calculate the average rating based on the number of reviews and total rating points.

```php
public static rating(int $reviews, float $rating, bool $round = false): float
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$reviews` | **int** | The total number of reviews. |
| `$rating` | **float** | The total sum of rating points. |
| `$round` | **bool** | Whether to round the average to 2 decimal places (default: false). |

**Return Value:**

`float` - Return the average rating.

**Example**

The average of the below rating is: `8.50`.

```php
echo Math::rating(5, 42.5, true)
```

***

### money

Formats currency with decimal places and comma separation.

```php
public static money(mixed $amount, int $decimals = 2): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$amount` | **mixed** | The amount you want to format. |
| `$decimals` | **int** | The decimals places. |

**Return Value:**

`string` - Return formatted currency string.

***

### currency

Format a number as a currency string using your application local as the default currency locale.

```php
public static currency(float $number, string $code = 'USD', string|null $locale = null): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$number` | **float** | The number to format. |
| `$code` | **string** | The currency code (optional). |
| `$locale` | **string&#124;null** | TOptional pass locale name to use in currency formatting. |

**Return Value:**

`string|false` - Return the formatted currency string, or false if unable to format.

***

### crypto

Format a number to it's cryptocurrency length.

```php
public static crypto(int|float|string $amount, string $network = 'BTC'): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$amount` | **int&#124;float&#124;string** | The amount to convert. |
| `$network` | **string** | The cryptocurrency code (e.g., 'BTC', 'ETH', 'LTC'). |

**Return Value:**

`string|false` - The equivalent amount in cryptocurrency.

**Available Cryptos**

- `BTC` - Bitcoin.
- `ETH` - Ethereum.
- `LTC` - Litecoin.
- `XRP` - Ripple Credits.
- `DOGE` - Doge Coin.

***

### distance

Calculate the distance between two points on the Earth's surface.

```php
public static distance(
  float|string $originLat, 
  float|string $originLng, 
  float|string $destLat, 
  float|string $destLng, 
  string $unit = 'km'
): float|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$originLat` | **float&#124;string** | The latitude of the origin point. |
| `$originLng` | **float&#124;string** | The longitude of the origin point. |
| `$destLat` | **float&#124;string** | The latitude of the destination point. |
| `$destLng` | **float&#124;string** | The longitude of the destination point. |
| `$unit` | **string** | The unit of distance to be returned (default is 'km'). |

**Return Value:**

`float|false` - Return the distance between the two points, or false on invalid input.

Supported units:

- 'km' - Kilometers,
- 'mi' - Miles,
- 'nmi' - Nautical miles.

> **Note:** If you are passing a string, make sure its a float string.

***

### fixed

Format a number with optional rounding.

```php
public static fixed(float|int|string $number, int|null $decimals = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$number` | **float&#124;int&#124;string** | The number you want to format. |
| `$decimals` | **int&#124;null** | The number of decimal places (null for no rounding). |

**Return Value:**

`string` - Return the formatted and rounded number.

***

### discount

Calculate the discounted amount based on rate given.

```php
public static discount(float|int|string $total, float|int|string $rate): float
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$total` | **float&#124;int&#124;string** | The total amount you want to discount. |
| `$rate` | **float&#124;int&#124;string** | The discount rate (percentage) as an integer. |

**Return Value:**

`float` - Return the discounted amount.

***

### interest

Calculate the total amount after adding an interest based on rete.

```php
public static interest(float|int|string $total, float|int|string $rate): float
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$total` | **float&#124;int&#124;string** | The amount to which interest will be added. |
| `$rate` | **float&#124;int&#124;string** | The interest rate as a percentage (float or int). |

**Return Value:**

`float` - Return he total amount with an interest rate.