# File Upload Integration with Uploader Module

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

This example demonstrates how you can upload large file using chunk method and browser-side chunking like `Plupload`.

```php
<?php
namespace App\Controllers;

use \Luminova\Base\BaseController;
use \Luminova\Storages\Uploader;

class UploadController extends BaseController 
{
	public function upload(): int
	{
		$file = $this->request->getFile('file');
		if($file === false){
			echo 'Invalid file';
			return STATUS_ERROR;
		}
		
		$file->setConfig([
			'upload_path' => root('writeable/storages/foo/'),
			'chunk_length' => 5242880 //The length of each chunk in bytes (default is 5mb)
		]);

		$chunk = (int) strict($this->request->getPost("chunk", 0), 'int');
		$chunks = (int) strict($this->request->getPost("chunks", 0), 'int');
	
		$status = Uploader::chunk($file, null, $chunk, $chunks);
		if($status === true){
			echo 'File was uploaded';
		}elseif(is_int($status)){
			// Part of file has been uploaded
		}else{
			echo 'Upload has failed.'
			return STATUS_ERROR;
		}
		return STATUS_SUCCESS;
	}
}
```

> Each request to backend should contain total number of chunk parts, and the current index of current chunk uploading.

***

To write a content to file.

```php
<?php 
$upload = Uploader::write(root('writeable/storages/foo/'), 'Hello world');

if($upload){
    echo 'Upload was successful';
}
```

***

## Methods

### upload

Upload an entire file to server, by first reading the file from temp, and slowing writing to destination in chunk.

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

- [\Luminova\Exceptions\StorageException](/running/exceptions.md#storageexception) - If upload path is not specified in configuration.

***

### move

Moves an uploaded file from temp to it new location using traditional PHP `move_uploaded_file`.

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

- [\Luminova\Exceptions\StorageException](/running/exceptions.md#storageexception) - If upload path is not specified in configuration.

***

### chunk

Uploads a file to the server using stream file upload, allowing large files to be uploaded in chunks.
This can be implemented with client side `JavaScript` like [Plupload](https://www.plupload.com/) or other that support chunk uploads.

```php
public static chunk(\Luminova\Http\File $file, string|null $path = null, int $chunk = 0, int $chunks = 0): bool|int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **\Luminova\Http\File** | Instance of file being uploaded. |
| `$path` | **string&#124;null** | The directory path where the file will be stored. |
| `$chunk` | **int** | The current chunk part index (start: 0). |
| `$chunks` | **int** | The total number of chunks parts the server will be expecting (start: 0). |

**Return Value:**

`bool|int` - Return true if upload was successful, false otherwise. If chunks are being uploaded, returns remaining chunks count.

**Throws:**

- [\Luminova\Exceptions\StorageException](/running/exceptions.md#storageexception) - If upload path is not specified in configuration.

***

### write

Wite contents to a file, this can be either a string or resource (e.g., a stream), use `stream_get_contents` to read the entire stream into a string before writing it.

```php
public static write(string $filename, string|resource $contents): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The file path and and name the to put content. |
| `$contents` | **string&#124;resource** | The contents to be written to the file. |

**Return Value:**

`bool` - Returns true if the file was successfully written, false otherwise.