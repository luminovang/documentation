# Maths Helper

***

## Overview

This class has basic mathematical calculation methods to convert currency, units, averages, ratings, discounts or interest rates.

***

## Introduction

This class provides a set of fundamental mathematical calculation methods currency conversions and unit conversions. They are designed to assist developers in performing common mathematical tasks while we add more for advance usages. 

***

* Class namespace: `\Luminova\Functions\Maths`

***

## Methods

### toUnit

Converts bytes to the appropriate unit.

```php
public static toUnit(int $bytes, bool $add_name = false): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$bytes` | **int** | The number of bytes to convert. |
| `$add_name` | **bool** | Whether to include the unit name in the result. Default is false. |

**Return Value:**

`string` - The converted value with optional unit name.

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

`int` - The size in bytes.

***

### average

Calculate the average of a giving numbers.

```php
public static average(int|float $numbers): float|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$numbers` | **int&#124;float** | Input arguments integers or float values to calculate the average<br />- @example average(10, 20, 30, 40, 50) - return 30 as the average. |

**Return Value:**

`float|null` - The average of the passed numbers.

***

### rating

Calculate the average rating based on the number of reviews and total rating points.

```php
public static rating(int $reviews, float $rating, bool $round = false): float
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$reviews` | **int** | Total number of reviews. |
| `$rating` | **float** | Total sum of rating points. |
| `$round` | **bool** | Whether to round the average to 2 decimal places.<br /><br />- @example averageRating(5, 42.5, true) The average rating is: 8.50 |

**Return Value:**

`float` - The average rating.

***

### money

Formats currency with decimal places and comma separation.

```php
public static money(mixed $amount, bool $decimals = 2): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$amount` | **mixed** | Amount you want to format. |
| `$decimals` | **bool** | Decimals places. |

**Return Value:**

`string` - Formatted currency string.

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

`string|false` - The formatted currency string, or false if unable to format.

***

### crypto

Convert a number to cryptocurrency.

```php
public static crypto(int|float|string $amount, string $network = 'BTC'): float|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$amount` | **int&#124;float&#124;string** | The amount to convert. |
| `$network` | **string** | The cryptocurrency code (e.g., 'BTC', 'ETH', 'LTC'). |

**Return Value:**

`float|false` - The equivalent amount in cryptocurrency.

***

### distance

Calculate the distance between two points on the Earth's surface.

```php
public static distance(float|string $olat, float|string $olon, float|string $dlat, float|string $dlon, string $unit = 'km'): float|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$olat` | **float&#124;string** | The latitude of the origin point. |
| `$olon` | **float&#124;string** | The longitude of the origin point. |
| `$dlat` | **float&#124;string** | The latitude of the destination point. |
| `$dlon` | **float&#124;string** | The longitude of the destination point. |
| `$unit` | **string** | The unit of distance to be returned (default is 'km').<br />Supported units: 'km', 'mi', 'nmi'. |

**Return Value:**

`float|false` - The distance between the two points, or false on invalid input.

> If you are passing a string, make sure its a float string.

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

`string` - The formatted number.

***

### discount

Calculate the discounted amount.

```php
public static discount(float|int|string $total, float|int|string $rate): float
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$total` | **float&#124;int&#124;string** | The total amount you want to discount. |
| `$rate` | **float&#124;int&#124;string** | The discount rate (percentage) as an integer. |

**Return Value:**

`float` - The discounted amount.

***

### interest

Calculate the total amount after adding interest.

```php
public static interest(float|int|string $total, float|int|string $rate): float
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$total` | **float&#124;int&#124;string** | The amount to which interest will be added. |
| `$rate` | **float&#124;int&#124;string** | The interest rate as a percentage (float or int). |

**Return Value:**

`float` - The total amount after adding interest.
