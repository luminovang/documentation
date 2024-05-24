# URI View Segments

***

## Overview

View Segments is a utility class designed to assist in accessing requested view URIs, simplifying processing tasks.

***

## Introduction

View Segments" is a utility class designed to simplify the retrieval of specific segments from requested view URIs. It offers methods that allow easy access to different parts of the URI, making it convenient to extract and process relevant information.

* Class namespace: `\Luminova\Routing\Segments`

***

Usage in your controller class.

```php
$segment = $this->app->router->getSegment();
```

Usage in routing context file `/routes/my-context.php`;

```php
$segment = $router->getSegment();
```

***

## Methods

***

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

`string` - View segment

*Example*

```php 
https://example.com/page/foo/bar/baz

echo $segment->index(1);
// foo
```

***

### first

Retrieve the first segment of the current view URI.

```php
public first(): string
```

**Return Value:**

`string` - First URI segment

*Example*

```php 
https://example.com/page/foo/bar/baz

echo $segment->first();
// page
```

***

### current

Retrieve the last segment of the current view URI.

```php
public current(): string
```

**Return Value:**

`string` - Current URI segment.

*Example*

```php 
https://example.com/page/foo/bar/baz

echo $segment->current();
// baz
```

***

### previous

Retrieve the view segment before the last segment.

```php
public previous(): string
```

**Return Value:**

`string` - The segment before the last.

*Example*

```php 
https://example.com/page/foo/bar/baz

echo $segment->previous();
// bar
```

***

### segments

Retrieve the current view segments as an array.

```php
public segments(): array&lt;int,string&gt;
```

**Return Value:**

`array` - Array list of URL segments.