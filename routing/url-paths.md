# Route URI Segment Object

***

## Overview

View Segments is a utility class designed to assist in accessing requested view URIs, simplifying processing tasks.

***

## Introduction

View **Segments** is a utility class designed to simplify the retrieval of specific segments from requested view URIs. It offers methods that allow easy access to different parts of the URI, making it convenient to extract and process relevant information.

***

**Usage in your controller class.**

```php
$segment = $this->app->router->getSegment();
```

Usage in routing context file (e.g, `/routes/web.php`);

```php
$segment = $router->getSegment();
```

***

## Class Definition

* Class namespace: `\Luminova\Routing\Segments`

***

## Methods

### index

Retrieve the view URI segment by index position.

```php
public index(int $index): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **int** | Position index to return segment. |

**Return Value:**

`string` - Return the view segment.

**Example:**

Request URL example `https://example.com/page/foo/bar/baz`.

```php 
echo $segment->index(1); // foo
```

***

### first

Retrieve the first segment of the current view URI.

```php
public first(): string
```

**Return Value:**

`string` - Return the first URI segment

**Example:**

Request URL example `https://example.com/page/foo/bar/baz`.

```php 
echo $segment->first(); // page
```

***

### current

Retrieve the last segment of the current view URI.

```php
public current(): string
```

**Return Value:**

`string` - Return the current URI segment.

**Example**

Rquest URL example `https://example.com/page/foo/bar/baz`.

```php 
echo $segment->current(); // baz
```

***

### previous

Retrieve the view segment before the last segment.

```php
public previous(): string
```

**Return Value:**

`string` - Return the segment before the last.

**Example:**

Request URL example `https://example.com/page/foo/bar/baz`.

```php 
echo $segment->previous(); // bar
```

***

### segments

Retrieve the current view segments as an array.

```php
public segments(): array<int,string>
```

**Return Value:**

`array` - Return an array list of URL segments.