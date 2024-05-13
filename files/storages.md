## Cloud Storages

***

## Overview

File Storage allows you to seamlessly integrate various storages like AWS S3, Azure Blob,  into your PHP application, providing flexibility and convenience in handling file and directory operations.

***

The Storage class provides an interface for interacting with various storage adapters such as local file storage, FTP servers, in-memory storage, and cloud storage services like AWS S3, Azure Blob, and Google Cloud Storage. This class serves as an extension of the powerful PHP library [League Filesystem](https://github.com/thephpleague/flysystem), which offers convenient methods for common file and directory operations.

To get started with the Storage class, ensure that you have installed the filesystem package by running the Composer command.

```bash
composer require league/flysystem:^3.0
```

Additionally, make sure to install the required dependencies for the chosen storage driver. You can refer to the configuration for the required drivers depending on the storage you want in the League [Storage Filesystem Configuration](/files/configuration) documentation.

***

### Examples

```php
<?php 
use Luminova\Storages\Storage;
 
Storage::context('local')->write('example.txt', 'Hello, World!');
```

To save file and generate a symbolic public URL on the go, use `toLink` method.
This method is only for `local` storage context, for other cloud storage use `url` method.

***

```php
<?php 
use Luminova\Storages\Storage;
 
echo Storage::context('local')->write('example.txt', 'Hello, World!')->toLink();
```

***

To change directory, create the folder and write file to it use `disk` method to change directory and create the directory.

```php
<?php 
use Luminova\Storages\Storage;
 
echo Storage::context('local')->disk('foo')->write('example.txt', 'Hello, World!');
```

***

If you want to change directory without creating the folder use `chdir` method. 

```php
<?php 
use Luminova\Storages\Storage;
 
$dir = Storage::context('local')->chdir('foo/bar/baz');

$dir->mkdir(null); //pass null since we already prepare the dir.
```

> *Note* this method doesn't change your directory remotely, it only prepare the directory for next executions.

***

Upload file to server, you can optionally set file configuration using `setConfig` method.

```php
<?php 
$file = $this->request->getFile('file');
$file->setConfig([
    'max_size' =>  1024,
    'min_size' =>  500,
]);

if(Storage::context('local')->upload($file)){
    echo 'Upload was successful';
}
```

***

* Class namespace: `\Luminova\Storages\Storage`
* Parent class: [StorageAdapters](#StorageAdapters)

***

## Methods

### constructor

Constructs a new `Storage` instance with the specified adapter.

```php
public __construct(string $adapter): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$adapter` | **string** | The storage adapter to use. |

>   You can either initialize a class instance or use the static `context` method for easy usage.

***

### context

Creates a new `Storage` instance for the specified adapter context.

```php
public static context(string $adapter = 'local'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$adapter` | **string** | The storage adapter context. |

**Return Value:**

`self` - The New `Storage` instance.

***

### disk

Creates a new storage disk / directory.

```php
public disk(string $location): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$location` | **string** | The disk path or name. |

**Return Value:**

`self` - Return class instance.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the creation.

***

### chdir

Change to another storage directory.

```php
public chdir(string $directory = './'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$directory` | **string** | The new current directory |

**Return Value:**

`self` - Return class instance.

> Shortcut to return to the main storage directory are `blank string`, `.` or `./`.

***

### url

Get remote file symbolic url.

```php
public url(string $file): string|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **string** | The file name and path to remove file. |

**Return Value:**

`string|null` - Return remote url to file otherwise null.

***

### tempUrl

Get a temporal remote url.

```php
public tempUrl(string $file, int $minutes = 1): string|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **string** | The file name and path to remove file. |
| `$minutes` | **int** | Expiry duration in minutes. |

**Return Value:**

`string|null` - Return remote url to file otherwise null.

***

### symbolic

Create a symlink to a target file or directory.

```php
public symbolic(string $target, string $link): bool|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$target` | **string** | The targe file or directory to link from. |
| `$link` | **string** | The location of the link. |

**Return Value:**

`bool|null` - Return true if the link was successfully created false otherwise.

***

### write

Writes contents to a file in current working directory.

```php
public write(string $filename, string|resource $contents, bool $steam = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The name of the file. |
| `$contents` | **string&#124;resource** | The contents to write string or resource for stream large uploads. |
| `$steam` | **bool** | The type of write operation (default: false). <br />- Passed true when writing using stream. |

**Return Value:**

`self` - Class instance.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the write operation.

***

### upload

Upload file in working directory.

```php
public upload(\Luminova\Http\File $file): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **\Luminova\Http\File** | Instance of file being uploaded. |

**Return Value:**

`bool` - Return true if upload was successful false otherwise.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the upload operation.

***

### toLink

Create a symbolic link after writing file to disk.

```php
public toLink(): string|false
```

**Return Value:**

`string|false` - The symbolic link location or false on failure.

> This method is only available on local filesystem.
> 
> Also it should only be called after method `write` has been called otherwise it will return false.

***

### checksum

Get the checksum for a file.

```php
public checksum(string $path, array $options = []): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** |  |
| `$options` | **array** |  |

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the write operation.

***

### read

Reads the contents of a file from the current working directory.

```php
public read(string $filename, bool $steam = false): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The name of the file. |
| `$steam` | **bool** | The type of read operation (default: false).<br />- Passed true when reading large files. |

**Return Value:**

`mixed` - The file contents.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the read operation.

***

### download

Download file from the current working directory.

```php
public download(string $filename, string|null $name = null, bool $steam = false, array $headers = []): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The name of the file. |
| `$name` | **string&#124;null** | Download file name. |
| `$steam` | **bool** | The type of read operation (default: false).<br />- Passed true when downloading large files. |
| `$headers` | **array** | Optional download headers. |

**Return Value:**

`mixed` - The file contents.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the read operation.

***

### delete

Deletes a file or directory in current working directory.

```php
public delete(string $filename, string $type = 'file'): void
```

.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The name of the file or directory. |
| `$type` | **string** | The type of deletion operation (file or dir). |

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the deletion operation.

***

### list

Lists the contents of a directory in the current working directory.

```php
public list(string $directory = null, string $return = 'files', bool $recursive = false): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$directory` | **string** | Optional directory path or it will list all in current directory. |
| `$return` | **string** | The type of result to return (files, dirs, or all). |
| `$recursive` | **bool** | Whether to list recursively. |

**Return Value:**

`array` - The list of files or directories.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the listing operation.

***

### fileExist

Checks if a file exists in the current working directory.

```php
public fileExist(string $filename): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The name of the file. |

**Return Value:**

`bool` - True if the file exists, false otherwise.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the existence check.

***

### dirExist

Checks if a directory exists in current working directory.

```php
public dirExist(string $path): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The directory path. |

**Return Value:**

`bool` - True if the directory exists, false otherwise.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the existence check.

***

### exist

Checks if a file or directory exists based on the provided type.

```php
public exist(string $filename, string $type = 'file'): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The name of the file or directory. |
| `$type` | **string** | The type of entity to check (file or dir). |

**Return Value:**

`bool` - True if the file or directory exists, false otherwise.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the existence check.

***

### has

Checks if a file or directory exists based on the provided URL or path.

```php
public has(string $filename): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The name of the file or directory, which can also be a URL. |

**Return Value:**

`bool` - True if the file or directory exists, false otherwise.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the existence check.

***

### modified

Retrieves the last modified timestamp of a file.

```php
public modified(string $filename): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The name of the file. |

**Return Value:**

`int` - The UNIX timestamp of the last modification time.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the retrieval.

***

### mime

Retrieves the MIME type of a file.

```php
public mime(string $filename): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The name of the file. |

**Return Value:**

`string` - The MIME type of the file.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the retrieval.

***

### size

Retrieves the size of a file.

```php
public size(string $filename): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The name of the file. |

**Return Value:**

`int` - The size of the file in bytes.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the retrieval.

***

### visibility

Retrieves or sets the visibility of a file or directory.

```php
public visibility(string $filename, string|null $visibility = null): false|string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The name of the file or directory. |
| `$visibility` | **string&#124;null** | Optional. The visibility to set ('public', 'private', or null to retrieve). Default is null. |

**Return Value:**

`false|string` - If $visibility is null, returns the current visibility as a string ('public' or 'private').
If $visibility is provided update visibility and returns the visibility that was set.
Otherwise returns false if operation failed.

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the operation.

***

### mkdir

Creates a new directory in the current working directory.

```php
public mkdir(string $path): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | The path of the directory to create. |

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the creation.

***

### move

Moves a file or directory from the current working directory to a new location.

```php
public move(string $source, string $destination): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$source` | **string** | The current path of the file or directory. |
| `$destination` | **string** | The new path for the file or directory. |

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the move operation.

***

### copy

Copies a file or directory from the current working directory to a new location.

```php
public copy(string $source, string $destination): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$source` | **string** | The path of the original file or directory. |
| `$destination` | **string** | The path of the destination file or directory. |

**Throws:**

- [StorageException](/exceptions/storage) - If an error occurs during the copy operation.

***

### getClient

Get the storage client instance.

```php
public getClient(): mixed
```

**Return Value:**

`mixed` - The client instance.