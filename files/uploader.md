# Handling File Uploads with the Uploader Class

***

## Overview

File Uploader class is a simple PHP class shipped with Luminova Framework to handle file uploads as an alternative to the Storage class.

***

## Introduction

The File Uploader class is included with the Luminova's Framework. It serves as an alternative to the Storage class and is designed to handle local  file uploads using methods like `upload` for uploading a file to the server, and `chunk` for large file uploads using client-side chunking or `write` for Uploading content from string.

***

## Usage Examples

### Using Upload with File Object

Upload file to server, you can optionally pass the `upload_path` in second parameter or set in file configuration using `setConfig` method.

```php
use Luminova\Storages\Uploader;

$file = $request->getFile('file');

$upload = Uploader::upload($file, root(__DIR__, 'writeable/storages/foo/'));
if($upload){
    echo 'Upload was successful';
}
```

***

### Using Write with String Content

To write a content to file.

```php
<?php 
use Luminova\Storages\Uploader;

$upload = Uploader::write(root('writeable/storages/foo/'), 'Hello world');

if($upload){
    echo 'Upload was successful';
}
```

***

### Using Chunk with File Object

This example demonstrates how you can upload large file using chunk method and browser-side chunking like `Plupload`.

```php
// /app/Controller/Http/UploadController.php
<?php
namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Route;
use Luminova\Attributes\Prefix;
use Luminova\Storages\Uploader;

#[Prefix(pattern: '/api/v1/(:root)')]
class UploadController extends BaseController 
{
    #[Route('/api/v1/upload', method: ['POST'])]
    public function upload(): int
    {
        $file = $this->request->getFile('file');

        if (!$file) {
            return response(400)->json(['message' => 'No file uploaded or invalid file.']);
        }
				
		// Set upload configurations
        $file->setConfig([
            'upload_path' => root('writeable/storages/foo/'),
            'chunk_length' => 5242880 // The length of each chunk in bytes (default is 5MB)
        ]);

        if (!$file->valid()) {
            return response(400)->json(['message' => 'File validation failed: ' . $file->getError()]);
        }

        // Get the current chunk and total chunks from the request
        $chunk = (int) strict($this->request->getPost('chunk', 0), 'int');
        $chunks = (int) strict($this->request->getPost('chunks', 0), 'int');

        $status = Uploader::chunk($file, null, $chunk, $chunks);

        if ($status === true) {
            return response(201)->json(['message' => 'File uploaded successfully.']);
        }

        if (is_int($status)) {
            return response(206)->json(['message' => 'File partially uploaded.', 'remaining' => $status]);
        }

        return response(500)->json(['message' => 'File upload failed.']);
    }
}
```

> Each request to backend should contain total number of chunk parts, and the current index of current chunk uploading.

***

## Class Definition

* Class namespace: `\Luminova\Storage\Uploader`
* This class is marked as **final** and can't be subclassed

***

## Methods

### upload

Uploads a file to the server by transferring data from a temporary source or string content
of file-object to a specified destination. Supports chunked writing for resource management.

```php
public static upload(
    \Luminova\Http\File $file, 
    ?string $path = null, 
    int $delay = 0, 
    ?string &$destination = null
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **\Luminova\Http\File** | The instance of file object being uploaded. |
| `$path` | **string&#124;null** | Optional upload path, if not set in the file configuration. |
| `$delay` | **int** | Optional microsecond delay between chunks to limit resource usage (default: 0). |
| `&$destination` | **string&#124;null** | Output variable holding the final upload path or null if upload fails. |

**Return Value:**

`bool` - Return true if the upload is successful, false otherwise.

**Throws:**

- [\Luminova\Exceptions\StorageException](/running/exceptions.md#storageexception) - Throws if the upload path is not configured or permission to create it was denied.

***

### move

Moves an uploaded file from a temporary location to a permanent destination on server, using traditional PHP `move_uploaded_file`.

```php
public static move(\Luminova\Http\File $file, ?string $path = null, ?string &$destination = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **\Luminova\Http\File** | The instance of file object being uploaded. |
| `$path` | **string&#124;null** | Upload file location if not already set in file setConfig method. |
| `&$destination` | **string&#124;null** | Output variable holding the final destination path or null if move fails. |

**Return Value:**

`bool` - Return true if upload was successful false otherwise.

**Throws:**

- [\Luminova\Exceptions\StorageException](/running/exceptions.md#storageexception) - Throws if the upload path is not configured or permission to create it was denied.

***

### chunk

Uploads a file in chunks to a specified destination, supporting large file uploads by dividing the file into manageable parts. This chunk splitting is usually done from client side e.g, `JavaScript`, using libraries like [Plupload](https://www.plupload.com/) or similar that support chunk uploads.

```php
public static chunk(
    File $file, 
    ?string $path = null, 
    int $chunk = 0, 
    int $chunks = 0,
    int $delay = 0,
    ?string &$destination = null
): bool|int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **\Luminova\Http\File** | The instance of file object being uploaded. |
| `$path` | **string&#124;null** | Directory path for the upload, if not set in file configuration (default: null). |
| `$chunk` | **int** | The current chunk index, starting at 0 (default: 0). |
| `$chunks` | **int** | The total number of chunks to be uploaded, starting at 0 (default: 0). |
| `$delay` | **int** | Optional delay in microseconds between chunks to control resource usage (default: 0). |
| `&$destination` | **string&#124;null** | Output variable holding the final destination path or null if upload fails. |

**Return Value:**

`bool|int` - Return true if upload is complete, false otherwise. If upload is still in progress, returns remaining chunks count.

**Throws:**

- [\Luminova\Exceptions\StorageException](/running/exceptions.md#storageexception) - Throws if the upload path is not configured or permission to create it was denied.

***

### write

Wite contents to a file, this can be either a string or resource (e.g., a stream), use `stream_get_contents` to read the entire stream into a string before writing it.

```php
public static write(string $filename, resource|string $contents): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The path and filename to write the content (e.g, `path/to/text.txt`). |
| `$contents` | **string&#124;resource** | The string content or resource to be written. |

**Return Value:**

`bool` - Returns true if the file was successfully written, false otherwise.