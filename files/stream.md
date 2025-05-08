# PSR Stream Interface Implementation and Management

***

## Overview

The Stream class provides a robust interface for working with stream resources. It supports various stream operations such as reading, writing, seeking, and managing metadata.

***

## Introduction

The `Stream` class provides a robust interface for working with stream resources in a consistent manner. It implements the `\Stringable` interface, allowing the object to be easily converted to a string. While the class supports all methods defined by the PSR `StreamInterface`, it is intentionally not implemented as a PSR interface. This design choice allows for seamless integration in [cURL network client response](http/response#lmv-docs-getbody) without requiring the installation of PSR client libraries, making it ideal for simple network request implementations.

***

## Class Definition

* Class namespace: `\Luminova\Storages\Stream`
* This class implements: `\Stringable`

***
## Examples

### Basic Usage

Here's how to create a stream and use its methods:

```php
use Luminova\Storages\Stream;

// Create a stream from a string
$stream = new Stream(fopen('php://temp', 'r+'));
$stream->write("Hello, Luminova!");

// Read the contents of the stream
$stream->rewind();
echo $stream->toString(); // Output: Hello, Luminova!
```

Get the size of the stream:

```php
echo $stream->getSize(); // Output: 19
```

Check if the stream is writable:

```php
if ($stream->isWritable()) {
    $stream->write(" Adding more data.");
}
```

Read remaining contents

```php
$stream->rewind();
echo $stream->getContents(); // Output: Hello, Luminova! Adding more data.

// Close the stream
$stream->close();
```

### Advanced Usage

You can extend the `Stream` class and implement the PSR StreamInterface if needed. Here's an example:

```php
// /app/Utils/MyStream.php

<?php
namespace App\Utils;

use Luminova\Storages\Stream;
use Psr\Http\Message\StreamInterface;

class MyStream extends Stream implements StreamInterface
{
    // Override the default method implementation
    public function close(): void
    {
        if(something){
            $this->doThat();
        }
        // Then call parent method if necessary
        parent::close();
    }

    // Additional methods can be implemented here as needed
}
```

***

## Methods

### constructor

Initialize the stream constructor with an underlying resource.

```php
public __construct(private resource $resource): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$resource` | **resource** | The stream resource. |

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - if the provided resource is not a valid stream.

***

### toString

Reads the entire stream and returns it contents as string.

```php
public toString(): string
```

**Return Value:**

`string` - Return the contents of the stream.

***

### close

Closes the stream and releases any resources associated with it.

```php
public close(): void
```

***

### detach

Detaches the underlying resource from the stream.

```php
public detach(): ?resource
```

**Return Value:**

`resource|null` - Return the detached resource, or null if none is present.

***

### getSize

Retrieves the size of the stream in bytes.

```php
public getSize(): ?int
```

**Return Value:**

`int|null` - Return the size of the stream, or null if the size cannot be determined.

***

### tell

Retrieves the current position of the stream pointer.

```php
public tell(): int
```

**Return Value:**

`int` - Return the position of the stream pointer.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - if the position cannot be determined.

***

### eof

Determines if the stream has reached the end of file.

```php
public eof(): bool
```

**Return Value:**

`bool` - Return true if the stream is at EOF, false otherwise.

***

### isSeekable

Checks if the stream is seekable.

```php
public isSeekable(): bool
```

**Return Value:**

`bool` - Return true if the stream is seekable, false otherwise.

***

### seek

Seeks to a position within the stream.

```php
public seek(int $offset, int $whence = SEEK_SET): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$offset` | **int** | The position to seek to. |
| `$whence` | **int** | The seek mode (default: `SEEK_SET`). |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - if the stream is not seekable or if the seek operation fails.

***

### rewind

Rewinds the stream to the beginning.

```php
public rewind(): void
```

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - if the rewind operation fails.

***

### isWritable

Checks if the stream is writable.

```php
public isWritable(): bool
```

**Return Value:**

`bool` - Return true if the stream is writable, false otherwise.

***

### write

Writes data to the stream.

```php
public write(string $string): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$string` | **string** | The data to write. |

**Return Value:**

`int` - Return the number of bytes written to the stream.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - if the stream is not writable or if the write operation fails.

***

### isReadable

Checks if the stream is readable.

```php
public isReadable(): bool
```

**Return Value:**

`bool` - Return true if the stream is readable, false otherwise.

***

### read

Reads data from the stream.

```php
public read(int $length): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$length` | **int** | The number of bytes to read. |

**Return Value:**

`string` - Return the data read from the stream.

**Throws:**
- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - if the stream is not readable or if the read operation fails.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - if the length parameter is negative.

***

### getContents

Retrieves the remaining contents of the stream as a string.

```php
public getContents(): string
```

**Return Value:**

`string` - Return the remaining contents of the stream.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - if the stream is not readable or if the content retrieval fails.

***

### getMetadata

Retrieves metadata about the stream or a specific key from the metadata array.

```php
public getMetadata(string|null $key = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | The specific metadata key to retrieve, or null to retrieve all metadata. |

**Return Value:**

`mixed` - Return the metadata value for the specified key, an array of all metadata if key is NULL, otherwise null or empty array.
