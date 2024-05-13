# File Uploader

***

## Overview

File Uploader class is a simple PHP class shipped with Luminova Framework to handle file uploads as an alternative to the Storage class.

***

## Introduction

The File Uploader class is included with the Luminova's Framework. It serves as an alternative to the Storage class and is designed to handle local  file uploads using methods like `upload` for uploading a file to the server, and `chunk` for large file uploads using client-side chunking or `write` for Uploading content from string.

* Class namespace: `\Luminova\Storage\Uploader`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

***

## Examples

Upload file to server, you can optionally pass the `upload_path` in second parameter or set in file configuration using `setConfig` method.

```php
<?php 
$file = $this->request->getFile('file');
$upload = Uploader::upload($file, root(__DIR__, 'writeable/storages/foo/'));
if($upload){
    echo 'Upload was successful';
}
```

***

This example demonstrates how you can upload large file using chunk method and browser-side chunking like [Plupload](https://www.plupload.com/).

```php
<?php 
$file = $this->request->getFile('file');
$file->setConfig([
    'upload_path' => root(__DIR__, 'writeable/storages/foo/'),
    'chunk_length' => 5242880 //The length of each chunk in bytes (default is 5mb)
]);

$upload = Uploader::chunk($file, null, 1, 3);

if(is_bool($upload) && $upload === true){
    echo 'Upload was successful';
}elseif(is_bool($upload) && $upload === false){
    echo 'Upload failed';
}else{
    echo 'Upload chunk part completed';
}
```
> Each request to backend should contain total number of chunk parts, and the current index of current chunk uploading.

***
To write a content to file.

```php
<?php 
$upload = Uploader::write(root(__DIR__, 'writeable/storages/foo/'), 'Hello world');

if($upload){
    echo 'Upload was successful';
}
```

***

## Methods

### upload

Upload file to server.

```php
public static upload(\Luminova\Http\File $file, string|null $path = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **\Luminova\Http\File** | Instance of file being uploaded. |
| `$path` | **string&#124;null** | Upload file location if not already set in file setConfig method. |

**Return Value:**

`bool` - Return true if upload was successful false otherwise.

**Throws:**

- [\Luminova\Exceptions\StorageException](/exceptions/classes#StorageException) - If upload path is not specified in configuration.

***

### move

Moves an uploaded file to a new location using traditional PHP `move_uploaded_file`.

```php
public static move(\Luminova\Http\File $file, string|null $path = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **\Luminova\Http\File** | Instance of file being uploaded. |
| `$path` | **string&#124;null** | Upload file location if not already set in file setConfig method. |

**Return Value:**

`bool` - Return true if upload was successful false otherwise.

**Throws:**

- [\Luminova\Exceptions\StorageException](/exceptions/classes#StorageException) - If upload path is not specified in configuration.

***

### chunk

Uploads a file to the server using stream file upload, allowing large files to be uploaded in chunks.

```php
public static chunk(\Luminova\Http\File $file, string|null $path = null, int $chunk = 1, int $chunks = 1): bool|int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **\Luminova\Http\File** | Instance of file being uploaded. |
| `$path` | **string&#124;null** | The directory path where the file will be stored. |
| `$chunk` | **int** | The current chunk part index (start: 1). |
| `$chunks` | **int** | The total number of chunks parts the server will be expecting (start: 1). |

**Return Value:**

`bool|int` - Return true if upload was successful, false otherwise. If chunks are being uploaded, returns remaining chunks count.

**Throws:**

- [\Luminova\Exceptions\StorageException](/exceptions/classes#StorageException) - If upload path is not specified in configuration.

***

### write

Save contents to a file.

```php
public static write(string $filename, string $contents): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The file path and and name the to put content. |
| `$contents` | **string** | The contents to be written to the file. |

**Return Value:**

`bool` - Returns true if the file was successfully written, false otherwise.