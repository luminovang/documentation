# Incoming Request User Agents Management

***

## Overview

The UserAgent class is a valuable tool for devs building web applications or services that require functionality based on client devices. By providing methods to parse and extract information from...

***

## Introduction

The `UserAgent` class is a utility class designed to parse and extract information from User-Agent strings. The User-Agent information are sent by web browsers and other clients to identify themselves to servers. This class makes it easy to work with User-Agent strings, allowing developers to extract useful information such as the browser name, version, operating system, and device type.

***

### Examples

Use global function helper.

```php
<?php 
echo  browser(null, 'instance');
```

Get the browser name.

```php
<?php 
browser(null, 'instance')->getBrowser();
```

Get the browser user-agent from request object.

```php
<?php 
request()->getUserAgent();
// OR
request()->agent;
```

Initialize user agent class.

```php
<?php 
$ua = new UserAgent();
echo $ua->getBrowser();
```

Accessing protected properties.

```php
<?php 
echo $ua->useragent;
echo $ua->browser;
echo $ua->version;
echo $ua->platform;
echo $ua->platformversion;
```

***

## Class Definition

* Class namespace: `\Luminova\Http\UserAgent`
* This class implements: [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface), [\Stringable](https://www.php.net/manual/en/class.stringable.php)

***

## Methods

### constructor

Constructor

```php
public __construct(?string $useragent = null): mixed
```

Sets the User Agent and runs the compilation routine.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$useragent` | **string&#124;null** | The User Agent string. If not provided, it defaults to `HTTP_USER_AGENT`. |

***

### getBrowser

Getter method for retrieving the browser information.

```php
public getBrowser(): string
```

***

### getUserAgent

Getter method for retrieving the user agent string.

```php
public getUserAgent(): string
```

***

### getPlatform

Getter method for retrieving the platform/operating system information.

```php
public getPlatform(): string
```

***

### getVersion

Getter method for retrieving the browser version.

```php
public getVersion(): string
```

***

### getRobot

Getter method for retrieving the robot name.

```php
public getRobot(): string
```

***

### getMobile

Getter method for retrieving the mobile device name.

```php
public getMobile(): string
```

***

### getReferrer

Getter method for retrieving the referrer hostname.

```php
public getReferrer(): string
```

***

### getPlatformVersion

Getter method for retrieving the platform/operating system version.

```php
public getPlatformVersion(): string
```

***

### toString

Get the Agent String.

```php
public toString(): string
```

**Return Value:**

`string` - The Agent String.

***

### parse

Parse the user agent string and extract browser information.
This method parses the user agent string and extracts information such as browser name, version,
operating system, and platform.

```php
public static parse(string|null $userAgent = null, bool $return_array = false): array|object|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$userAgent` | **string&#124;null** | The user agent string to parse. If not provided, it defaults to the<br />user agent string from the HTTP headers. |
| `$return_array` | **bool** | If set to true, this function will return an array instead of an object. |

**Return Value:**

`array|object|false` - Returns an array or object containing the parsed browser information.

***

### replace

Parse and replace user agent class properties with new user agent information.

```php
public replace(string $userAgent): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$userAgent` | **string** | The user agent string to parse and expose. |

***

### isReferral

Check if the referral hostname is from another site.

```php
public isReferral(): bool
```

**Return Value:**

`bool` - Return true if the referral hostname is from another site.

***

### isRobot

Check if the user agent string is from a known robot.

```php
public isRobot(string|null $keyword = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$keyword` | **string&#124;null** | Optional robot name, keyword or pattern.<br />- Pass `NULL` to check if robot is in array of robot keywords `Browser::$robotPatterns`. |

**Return Value:**

`bool` - Return true if the user agent is from known robot, false otherwise.

***

### isMobile

Check if the user agent string represents a mobile device.

```php
public isMobile(string|null $keyword = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$keyword` | **string&#124;null** | Optional mobile device name, keyword or pattern.<br />- Pass `NULL` to check if mobile is in array of mobile devices `Browser::$mobileKeywords`. |

**Return Value:**

`bool` - Return true if the user agent represents a mobile device, false otherwise.

***

### isBrowser

Check if the user agent string belongs to a browser.

```php
public isBrowser(string|null $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string&#124;null** | Optional browser name, keyword or pattern.<br />If `NULL` is passed it will check if the user agent is any valid browser. |

**Return Value:**

`bool` - Return true if the user agent belongs to a specific browser, or if the given name matches the browser name or user-agent, false otherwise.

***

***

### isTrusted

Check if the user agent string is trusted based on allowed browsers.

```php
public isTrusted(): bool
```

**Return Value:**

`bool` - Return true if the user agent matches any of the browser name / patterns in allowed browsers, false otherwise.

***

### is

Check if keyword or patterns matched with the user agent string, browser, mobile or robot name.

```php
public is(string $name, string|null $lookup = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The keyword or pattern to check if matched on user-agent string. |
| `$lookup` | **string&#124;null** | The context to lookup matches, if null it will search user-agent string.<br />- `browser`, `mobile` or `robot` |

**Return Value:**

`bool` - Return true if matched otherwise false.

**Examples**

Checking for a keyword in user agent string.

```php
<?php 
if(request()->getUserAgent()->is('Mozilla')){
    echo "YES";
}
```

Using pattern to match only `Runtime`.

```php
<?php 
if(request()->getUserAgent()->is('!(Postman)Runtime')){
    echo "YES";
}
```

***

### reset

Reset user agent class properties.

```php
protected reset(): void
```