# Integrating Third-Party Cloud Storage Services

***

## Overview

File Storage allows you to seamlessly integrate various storages like AWS S3, Azure Blob,  into your PHP application, providing flexibility and convenience in handling file and directory operations.

***

## Introduction

The Storage class provides an interface for interacting with various storage adapters such as local file storage, FTP servers, in-memory storage, and cloud storage services like AWS S3, Azure Blob, and Google Cloud Storage. This class serves as an extension of the powerful PHP library [League Filesystem](https://github.com/thephpleague/flysystem), which offers convenient methods for common file and directory operations.

To get started with the Storage class, ensure that you have installed the filesystem package by running the Composer command.

```bash
composer require league/flysystem:^3.0
```

Additionally, make sure to install the required dependencies for the chosen storage driver. You can refer to the configuration for the required drivers depending on the storage you want in the League [Storage Filesystem Configuration](/configs/storage.md) documentation.

***

### Examples

Base usage, write to local storage.

```php
use Luminova\Utility\Storage\Storage;
 
Storage::context(Storage::LOCAL)
    ->write('example.txt', 'Hello, World!');
```

***

To save file and generate a symbolic public URL on the go, use `toLink` method.

```php
use Luminova\Utility\Storage\Storage;
 
echo Storage::context(Storage::LOCAL)
    ->write('example.txt', 'Hello, World!')
    ->toLink();
```

> **Note:** This method is only for `local` storage context, for other cloud storage use `url` method.

***

To change directory, create the folder and write file to it.

```php
use Luminova\Utility\Storage\Storage;
 
echo Storage::context(Storage::LOCAL)
    ->disk('foo')
    ->write('example.txt', 'Hello, World!');
```

> Use the `disk` method to change directory and create a new directory.

***

If you want to change directory without creating the folder use `chdir` method. 

```php
use Luminova\Utility\Storage\Storage;
 
$dir = Storage::context(Storage::LOCAL)
    ->chdir('foo/bar/baz')
    ->mkdir(null); /* Pass null since we already prepare the dir */
```

> **Note:** The `chdir` method doesn't change your directory remotely, it only prepare the directory for next execution.

***

Upload file to server, you can optionally set file configuration using `setConfig` method.

```php
$file = $request->getFile('file');
$file->setConfig([
    'max_size' =>  1024,
    'min_size' =>  500,
]);

$uploaded = Storage::context(Storage::LOCAL)->upload($file);

if($uploaded){
    echo 'Upload was successful';
}
```

***

## Class Definition

* Class namespace: `Luminova\Utility\Storage\Storage`
* Parent class: `Luminova\Utility\Storage\Adapters\Adapters`

***

### Constants

Available storage contexts.

| Name                   | Value            | Description                                                                                      |
| ------------------------------- | ---------------- | ------------------------------------------------------------------------------------------------ |
| `LOCAL`        | `'local'`        | Local filesystem storage. Used for reading and writing files on the server's disk.               |
| `FTP`          | `'ftp'`          | FTP-based storage for remote file access via File Transfer Protocol.                             |
| `MEMORY`       | `'memory'`       | In-memory temporary storage. Files are lost when the process ends. Ideal for caching or testing. |
| `AWS_S3`       | `'aws-s3'`       | Amazon S3 cloud storage using synchronous operations.                                            |
| `AWS_ASYNC_S3` | `'aws-async-s3'` | Amazon S3 storage with asynchronous support for background uploads.                              |
| `AZURE_BLOB`   | `'azure-blob'`   | Microsoft Azure Blob Storage for scalable cloud file storage.                                    |
| `GOOGLE_CLOUD` | `'google-cloud'` | Google Cloud Storage (GCS) for storing and managing data in the Google Cloud ecosystem.          |
| `SFTP_V3`      | `'sftp-v3'`      | Secure File Transfer Protocol version 3. Provides encrypted SSH file transfers.                  |
| `WEB_DEV`      | `'web-dev'`      | WebDAV protocol-based storage. Useful for web-based file access and management.                  |
| `ZIP_ARCHIVE`  | `'zip-archive'`  | ZIP archive storage for writing and managing files inside compressed `.zip` files.               |

---

## Methods

### constructor

Constructs a new `Storage` instance using the specified storage adapter.

This initializes the storage system with the given adapter and its configuration.

**Supported Adapters:**

- `local`
- `ftp`
- `memory`
- `aws-s3`
- `aws-async-s3`
- `azure-blob`
- `google-cloud`
- `sftp-v3`
- `web-dev`
- `zip-archive`

```php
public __construct(string $adapter): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$adapter` | **string** | The name of the storage adapter to use. |

***

### context

Creates a new `Storage` instance for the given adapter context.

Provides a convenient way to instantiate the `Storage` class with a specific storage. 

```php
public static context(string $adapter = Storage::LOCAL): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$adapter` | **string** | The storage adapter context. Defaults to `local`. |

**Return Value:**

`self` - Return a new instance of the `Storage` class.

***

### getClient

Get the storage client instance.

```php
public getClient(): ?object
```

**Return Value:**

`object<\T>|null` - Return instance of storage client.

***

### disk

Sets the working directory and creates it if it doesn't exist.

This method changes the current working directory to the specified `$location`. If the directory does not exist, it will be automatically created.

```php
public disk(string $location): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$location` | **string** | The target disk path or directory name. |

**Return Value:**

`self` - Returns the instance of storage class.

**Throws:**

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If the path is invalid or the directory creation fails.

> **Note:** `$location` must not be blank or a relative symbol (`.`, `./`).

***

### chdir

Changes the current working storage directory.

If the provided `$directory` is an empty string, `.` or `./`, it resets back to the main storage root.

```php
public chdir(string $directory = './'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$directory` | **string** | The new relative directory path. |

**Return Value:**

`self` - Returns the instance of storage class.

> Use an empty string, `.` or `./` to return to root.

***

### url

Get remote file symbolic url.

```php
public url(string $file): ?string
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
public tempUrl(string $file, int $minutes = 1): ?string
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
public symbolic(string $target, string $link): ?bool
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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the write operation.

***

### upload

Upload file in working directory.

```php
public upload(Luminova\Http\File $file): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **Luminova\Http\File** | Instance of file being uploaded. |

**Return Value:**

`bool` - Return true if upload was successful false otherwise.

**Throws:**

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the upload operation.

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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the write operation.

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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the read operation.

***

### download

Download file from the current working directory.

```php
public download(string $filename, ?string $name = null, bool $steam = false, array $headers = []): mixed
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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the read operation.

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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the deletion operation.

***

### list

Lists the contents of a directory in the current working directory.

```php
public list(?string $directory = null, string $return = 'files', bool $recursive = false): array
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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the listing operation.

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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the existence check.

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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the existence check.

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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the existence check.

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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the existence check.

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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the retrieval.

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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the retrieval.

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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the retrieval.

***

### visibility

Retrieves or sets the visibility of a file or directory.

```php
public visibility(string $filename, ?string $visibility = null): false|string
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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the operation.

***

### mkdir

Create a directory in the current working path.

If no path is provided, the method will use the value set via `chdir()`.

```php
public mkdir(?string $path = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string\|null** | Optional directory path to create.<br/>If null, the previously set working path is used. |

**Throws:**

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the creation.

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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the move operation.

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

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - If an error occurs during the copy operation.