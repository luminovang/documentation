# Immutable Date and Time Operations

***

## Overview

The Luminova Time class is an extension of PHP DateTimeImmutable`and provides additional functionality and convenience methods for handling date and time operations.

***

## Introduction

Time class is a powerful extension of PHP's native DateTimeImmutable class, designed to enhance date and time operations within your application. By extending the functionality of the core DateTimeImmutable class, the Luminova's Time class offers range of additional features and convenience methods, making it easier to manipulate, format, and work with dates and times in your project.

Time class serves as a valuable addition to the Luminova's PHP developer's toolkit, to work more efficiently and effectively with date and time data, ultimately contributing to the reliability of your application development.

***

## Class Definition

* Class namespace: `Luminova\Time\Time`
* Parent class: [\DateTimeImmutable](https://www.php.net/manual/en/class.datetimeimmutable.php)
* Class implements: [\Stringable](https://www.php.net/manual/en/class.stringable.php)

***

## Methods

### constructor

Time constructor.

```php
public __construct(?string $datetime = null, ?\DateTimeZone|string $timezone = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$datetime` | **string&#124;null** | Optional datetime string. |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone. |

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if error occurs during DateTimeImmutable object construction.

***

### timezone

Converts string to timezone instance if not already a timezone instance.

```php
public static timezone(\DateTimeZone|string $timezone): \DateTimeZone
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timezone` | **\DateTimeZone&#124;string** | Optional timezone to associate with the current DateTime instance. |

**Return Value:**

`\DateTimeZone` - The timezone instance.

***

### now

Returns current DateTime instance with the timezone set.

```php
public static now(\DateTimeZone|string|null $timezone = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone. |

**Return Value:**

`self` - New DateTimeImmutable object.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurs.

***

### parse

Returns a new Time instance while parsing a datetime string.

```php
public static parse(string $datetime, \DateTimeZone|string|null $timezone = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$datetime` | **string** | Datetime string to parse. |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone. |

**Return Value:**

`self` - New DateTimeImmutable object.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurs.

***

### today

Return a new time with the time set to midnight.

```php
public static today(\DateTimeZone|string|null $timezone = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone. |

**Return Value:**

`self` - Return new DateTimeImmutable object.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurred.

***

### yesterday

Returns an instance set to midnight yesterday morning.

```php
public static yesterday(\DateTimeZone|string|null $timezone = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timezone` | **\DateTimeZone&#124;string&#124;null** |  |

**Return Value:**

`self` - Return new DateTimeImmutable object.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurred.

***

### setTimezone

Set the TimeZone associated with the DateTime, and returns a new instance with the updated timezone.

```php
public setTimezone(\DateTimeZone|string $timezone): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timezone` | **\DateTimeZone&#124;string** | Timezone to set. |

**Return Value:**

`self` - Return new DateTimeImmutable object.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurred.

***

### getYear

Gets year from the current datetime format.

```php
public getYear(): string
```

**Return Value:**

`string` - Return year

***

### getMonth

Gets month from the current datetime format.

```php
public getMonth(): string
```

**Return Value:**

`string` - Return month.

***

### getDay

Gets day from the current datetime format.

```php
public getDay(): string
```

**Return Value:**

`string` - Return day of the month

***

### getHour

Gets hour (in 24-hour format), from the current datetime format.

```php
public getHour(): string
```

**Return Value:**

`string` - Return hours of the day.

***

### getMinute

Gets minutes from the current datetime format.

```php
public getMinute(): string
```

**Return Value:**

`string` - Return minutes.

***

### getSecond

Gets seconds from the current datetime format.

```php
public getSecond(): string
```

**Return Value:**

`string` - Return seconds.

***

### getDayOfYear

Gets day of the year, from the current datetime format.

```php
public getDayOfYear(): string
```

**Return Value:**

`string` - Return day of the year.

***

### getWeekOfMonth

Gets week of the month, from the current datetime format.

```php
public getWeekOfMonth(): string
```

**Return Value:**

`string` - Return week of the month.

***

### getWeekOfYear

Gets week of the year from the current datetime format.

```php
public getWeekOfYear(): string
```

**Return Value:**

`string` - Return week of the year.

***

### getDayOfWeek

Gets week of the day from the current datetime format.

```php
public getDayOfWeek(): string
```

**Return Value:**

`string` - Return day of the week.

***

### getQuarter

Gets quarter of the year, from the current datetime format.

```php
public getQuarter(): string
```

**Return Value:**

`string` - Return quarter of the year.

***

### datetime

Return string datetime of this format 'yyyy-mm-dd H:i:s'.

```php
public static datetime(\DateTimeZone|string|null $timezone = 'UTC'): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone string. |

**Return Value:**

`string` - Returns datetime string.

***

### isDaylight

Determine if the current time is already in daylight savings.

```php
public isDaylight(): bool
```

**Return Value:**

`bool` - Return true if the current time is already in daylight saving, false otherwise.

***

### isSystemTimezone

Check whether the passed timezone is the same as the application timezone.

```php
public isSystemTimezone(): bool
```

**Return Value:**

`bool` - true if the passed timezone is the same as the local timezone false otherwise.

***

### isUtc

Returns boolean whether object is in UTC.

```php
public isUtc(): bool
```

**Return Value:**

`bool` - Whether the timezone offset is UTC.

***

### getTimezoneName

Returns the name of the current timezone.

```php
public getTimezoneName(): string
```

**Return Value:**

`string` - The name of the current timezone.

***

### getMaxAge

Calculates the maximum age in seconds until a given timestamp.

```php
public getMaxAge(int $timestamp): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timestamp` | **int** | The target timestamp to calculate the maximum age against.. |

**Return Value:**

`int` - Return the number of seconds from the current timestamp to the given timestamp.

***

### getInstanceUtc

Returns Datetime instance of UTC timezone.

```php
public getInstanceUtc(\DateTimeInterface|string $datetime, ?string $timezone = null): \DateTime
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$datetime` | **\DateTimeInterface&#124;Luminova\Time\Time&#124;string** | Datetime object or string |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone. |

**Return Value:**

`\DateTime` - Return new datetime instance of UTC timezone.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurred.

***

### toFormat

Returns a formatted datetime to your preferred format.

```php
public toFormat(?string $format = null): false|string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$format` | **null&#124;string** | Format to return (default: `yyyy-MM-dd HH:mm:ss`). |

**Return Value:**

`false|string` - Formatted datetime string otherwise false.

***

### toTime

Returns a formatted time string (ex. 17:17:17).

```php
public toTime(): string
```

**Return Value:**

`string` - Formatted time string otherwise false.

***

### toDatetime

Converts the current instance to a mutable DateTime object.

```php
public toDatetime(): \DateTime
```

**Return Value:**

`\DateTime` - Return new datetime object.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurred.

***

### fromInstance

Takes an instance of DateTimeInterface and returns an instance of Time with it's same values.

```php
public static fromInstance(\DateTimeInterface $datetime): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$datetime` | **\DateTimeInterface** | An instance of DateTimeInterface. |

**Return Value:**

`self` - Return new DateTimeImmutable object.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurred.

***

### fromTimestamp

Returns a new instance with the datetime set based on the provided UNIX timestamp.

```php
public static fromTimestamp(int $timestamp, \DateTimeZone|string|null $timezone = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timestamp` | **int** | Timestamp to create datetime from. |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone to associate with current DateTime instance. |

**Return Value:**

`self` - Return new DateTimeImmutable object.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurred.

***

### fromDate

Returns a new instance based on the year, month and day. If any of those three are left empty, will default to the current value.

```php
public static fromDate(
    ?int $year = null, 
    ?int $month = null, 
    ?int $day = null, 
    \DateTimeZone|string|null $timezone = null
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$year` | **int&#124;null** | Year to pass. |
| `$month` | **int&#124;null** | Month to pass. |
| `$day` | **int&#124;null** | Day to pass. |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone to associate with current DateTime instance. |

**Return Value:**

`self` - Return new DateTimeImmutable object.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurred.

***

### fromTime

Returns a new instance with the date set to today, and the time set to the values passed in.

```php
public static fromTime(
    ?int $hour = null, 
    ?int $minutes = null, 
    ?int $seconds = null, 
    \DateTimeZone|string|null $timezone = null
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$hour` | **int&#124;null** | Hour to pass. |
| `$minutes` | **int&#124;null** | Minutes to pass. |
| `$seconds` | **int&#124;null** | Seconds to pass. |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone to associate with current DateTime instance. |

**Return Value:**

`self` - Return new DateTimeImmutable object.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurred.

***

### fromRelative

Returns a new datetime instance from a relative time format.

```php
public static fromRelative(string $datetime, \DateTimeZone|string|null $timezone = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$datetime` | **string** | Relative time string (2 days ago, -3 years etc..). |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone to associate with current DateTime instance. |

**Return Value:**

`string` - Return formatted DateTime string.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if invalid relative time format was passed.

***

### createFrom

Returns a new instance with the date time values individually set.

```php
public static createFrom(
    ?int $year = null, 
    ?int $month = null, 
    ?int $day = null, 
    ?int $hour = null, 
    ?int $minutes = null, 
    ?int $seconds = null, 
    ?\DateTimeZone|string|null $timezone = null
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$year` | **int&#124;null** | Year to pass. |
| `$month` | **int&#124;null** | Month to pass. |
| `$day` | **int&#124;null** | Day to pass. |
| `$hour` | **int&#124;null** | Hour to pass. |
| `$minutes` | **int&#124;null** | Minutes to pass. |
| `$seconds` | **int&#124;null** | Seconds to pass. |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone to associate with current DateTime instance. |

**Return Value:**

`self` - Return new DateTimeImmutable object.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurred.

***

### tomorrow

Returns an instance set to midnight tomorrow morning.

```php
public static tomorrow(\DateTimeZone|string|null $timezone = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone to associate with current DateTime instance. |

**Return Value:**

`self` - Return new DateTimeImmutable object.

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurred.

***

### fromFormat

Create new DateTimeImmutable object formatted according to the specified format.

```php
public static fromFormat(string $format, string $datetime, \DateTimeZone|string|null $timezone = null): \DateTimeImmutable|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$format` | **string** | Format to convert to |
| `$datetime` | **string** | datetime to convert to format. |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone to associate with current DateTime instance. |

**Return Value:**

`\DateTimeImmutable|false` - Returns new DateTimeImmutable false otherwise

**Throws:**

- [Luminova\Exceptions\DatetimeException](/error-handlers/exceptions.md#datetimeexception) - Throws if any error occurred.

***

### calendar

Returns an array representation of the given calendar month.

```php
public static calendar(?string $month = null, ?string $year = null, \DateTimeZone|string|null $timezone = null): array<int,mixed>
```

The array values are timestamps which allow you to easily format.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$month` | **?string** | Month default is `date('n)`. |
| `$year` | **?string** | Year default is `date('y')`. |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone to associate with current DateTime instance. |

**Return Value:**

`array<int,mixed>` - $calendar Calendar values.

***

### days

Get an array of dates for each day in a specific month.

```php
public static days(string|int|null $month = null, string|int|null $year = null, string $format = 'd-M-Y'): array<int,string>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$month` | **string&#124;int&#124;null** | The month (1-12). |
| `$year` | **string&#124;int&#124;null** | The year (e.g., 2023). |
| `$format` | **string** | The format for the returned dates (default is 'd-M-Y'). |

**Return Value:**

`array<int,string>` - An array of dates within the specified month.

***

### months

Get an array of months for each month in a specific year.

```php
public static months(string $format = 'M'): array<int,string>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$format` | **string** | The format for the returned dates (default is &quot;M&quot;). |

**Return Value:**

`array<int,string>` - An array of month within the specified year.

***

### years

Generate a list of years between a start and end year.

```php
public static years(string|int|null $start = null, string|int|null $end = null): array<int,int>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$start` | **string&#124;int&#124;null** | Starting year. Defaults to the current year if not provided. |
| `$end` | **string&#124;int&#124;null** | Ending year. Defaults to the current year minus 3 years if not provided. |

**Return Value:**

`array<int,int>` - Return the list of years.

***

### hours

Get a list of time hours in 12-hour format with customizable intervals.

```php
public static hours(int $interval = 30): array<int,string>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$interval` | **int** | The interval in minutes. Default is 30. |

**Return Value:**

`array<int,string>` - Return an array of time hours.

***

### ago

Convert datetime to relative a human-readable representation of the time elapsed since the given datetime.

```php
public static ago(
    \DateTimeImmutable|string|int $datetime, 
    bool $full = false, 
    \DateTimeZone|string|null $timezone = null
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$datetime` | **\DateTimeImmutable&#124;string&#124;int** | The datetime string, Unix timestamp, or time string. |
| `$full` | **bool** | Return full relative time (e.g. 1 hour, 3 minutes, 5 seconds ago) default is false. |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone to associate with current DateTime instance. |

**Return Value:**

`string` - Return a string representing the time elapsed since the given datetime, in human-readable format.

> If a string is provided, it must be a valid datetime string or time string.

***

### agoToDatetime

Convert relative time ago to datetime instance.

```php
public static agoToDatetime(string $ago, \DateTimeZone|string|null $timezone = null): \DateTime
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ago` | **string** | Social time ago. |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone to associate with current DateTime instance. |

**Return Value:**

`\DateTime` - Return new datetime instance.

**Throws:**

- [Luminova\Exceptions\DateTimeException](/error-handlers/exceptions.md#datetimeexception) - If invalid time unit was found in the ago format.

***

### passed

Check if a certain amount of minutes has passed since the given timestamp.

```php
public static passed(
    \DateTimeImmutable|string|int $datetime, int $minutes, 
    \DateTimeZone|null|string $timezone = 'UTC'
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$datetime` | **\DateTimeImmutable&#124;string&#124;int** | Either a Unix timestamp, DateTimeImmutable or a string representing a date/time. |
| `$minutes` | **int** | The number of minutes to check against. |
| `$timezone` | **\DateTimeZone&#124;string&#124;null** | Optional timezone to use. |

**Return Value:**

`bool` - Return true if the specified minutes have passed, false otherwise.

**Throws:**

- [Luminova\Exceptions\DateTimeException](/error-handlers/exceptions.md#datetimeexception) - If invalid datetime was passed.

***

### suffix

Get the suffix for a given day (e.g., 1st, 2nd, 3rd, 4th).

```php
public static suffix(string|int $day): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$day` | **string&#124;int** | The day for which to determine the suffix. |

**Return Value:**

`string` - Return the day with its appropriate suffix.

***

### isRelative

Check if time string has a relative time keywords.

```php
public static isRelative(string $datetime): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$datetime` | **string** | The datetime string to check. |

**Return Value:**

`bool` - Return true if the string contains relative time keywords otherwise, false.

***

### isAgo

Check if time string has a human-readable relative time keywords from `ago` method.

```php
public static isAgo(string $datetime): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$datetime` | **string** | The datetime string to check. |

**Return Value:**

`bool` - Return true if the string contains relative time keywords otherwise, false.

***

### isAbsolute

Check if time string is a valid absolute time string.

```php
public static isAbsolute(string $datetime): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$datetime` | **string** | The datetime string to check. |

**Return Value:**

`bool` - Return true if the string contains absolute time time otherwise, false.