# Local File Uploader

***

## Overview

The Uploader class handles local file uploads, supports chunked uploads for large files, and integrates with the File object to provide flexible configuration and detailed upload feedback.

***

## Introduction

The file **Uploader** class is built into the Luminova Framework as a lightweight alternative to the `Luminova\Utility\Storage\Storage` class. It is designed for handling local file uploads efficiently, offering methods like:

* `upload` – for standard file uploads,
* `chunk` – for large files using client-side chunking,
* `write` – for saving content directly from a string.

This class works well with the [File Upload Object](/http/files.md) (`Luminova\Http\File`), enabling customizable upload handling and clear, detailed feedback for each upload process.

***

## Usage Examples

### Using Upload with File Object

Upload file to server, you can optionally pass the `upload_path` in second parameter or set in file configuration using `setConfig` method.

```php
use Luminova\Utility\Storage\Uploader;
use function Luminova\Funcs\root;

$file = $request->getFile('file');

$upload = Uploader::upload($file, root(__DIR__, 'writeable/storages/foo/'));
if($upload){
    echo 'Upload was successful';
}

// Get message from file object
echo $file->getMessage();
```

***

### Using Write with String Content

To write a content to file.

```php
use Luminova\Utility\Storage\Uploader;
use function Luminova\Funcs\root;

$upload = Uploader::write(root('writeable/storages/foo/'), 'Hello world');

if($upload){
    echo 'Upload was successful';
}
```

***

### Using Chunk with File Object

This example demonstrates how you can upload large file using chunk method and browser-side chunking like **Plupload** or similar.

```php
// /app/Controller/Http/UploadController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;
use Luminova\Attributes\Prefix;
use Luminova\Utility\Storage\Uploader;
use function Luminova\Funcs\root;
use function Luminova\Funcs\strict;
use function Luminova\Funcs\response;

#[Prefix(pattern: '/api/v1/(:root)')]
class UploadController extends Controller 
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
            'chunk_length' => 5242880 /* The length of each chunk in bytes (default is 5MB) */
        ]);

        if (!$file->valid()) {
            return response(400)->json(['message' => 'File validation failed: ' . $file->getError()]);
        }

        // Get the current chunk and total chunks from the request
        $chunk = (int) strict($this->request->getPost('chunk', 0), 'int');
        $chunks = (int) strict($this->request->getPost('chunks', 0), 'int');

        $status = Uploader::chunk($file, null, $chunk, $chunks);

        if ($status === true) {
            // File was uploaded 
            return response(201)->json(['message' => 'File uploaded successfully.']);
        }

        if (is_int($status)) {
            // Check was uploaded
            return response(206)->json([
                'message' => 'File partially uploaded.', 
                'remaining' => $status
            ]);
        }

        // Failed to upload file
        return response(500)->json([
            'message' => 'File upload failed.',
            'error' => $file->getError()
        ]);
    }
}
```

> Each request to backend should contain total number of chunk parts, and the current index of current chunk uploading.

***

## Class Definition

* Class namespace: `Luminova\Storage\Uploader`
* This class is marked as **final** and can't be subclassed

***

## Methods

### upload

Uploads a file to the server by transferring data from a temporary source or string content
of file-object to a specified destination. Supports chunked writing for resource management.

```php
public static upload(
    File $file, 
    ?string $path = null, 
    int $delay = 0, 
    ?string &$destination = null
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **Luminova\Http\File** | The instance of file object being uploaded. |
| `$path` | **string&#124;null** | Optional upload path, if not set in the file configuration. |
| `$delay` | **int** | Optional microsecond delay between chunks to limit resource usage (default: 0). |
| `&$destination` | **string&#124;null** | Output variable holding the final upload path or null if upload fails. |

**Return Value:**

`bool` - Return true if the upload is successful, false otherwise.

**Throws:**

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - Throws if the upload path is not configured or permission to create it was denied.

***

### move

Moves an uploaded file from a temporary location to a permanent destination on server.

This method uses traditional PHP `move_uploaded_file`.

```php
public static move(File $file, ?string $path = null, ?string &$destination = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **Luminova\Http\File** | The file object being moved. |
| `$path` | **string&#124;null** | Optional upload path, if not set in the file configuration. |
| `&$destination` | **string&#124;null** | Output variable holding the final destination path or null if move fails. |

**Return Value:**

`bool` - Return true if the move is successful, false otherwise.

**Throws:**

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - Throws if the upload path is not configured or permission to create it was denied.

***

### chunk

Uploads a file in chunks to a specified destination.

This method supports large file uploads by splitting the file into smaller, manageable parts. The chunk splitting is typically done from client side e.g, `JavaScript`, using libraries like [Plupload](https://www.plupload.com/) or similar to handles chunking before sending data.

```php
public static chunk(
    File $file, 
    ?string $path = null, 
    int $chunkIndex = 0, 
    int $totalChunks = 0,
    int $uploadDelay = 0,
    ?string &$destination = null
): bool|int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$file` | **Luminova\Http\File** | The file instance being uploaded. |
| `$path` | **string&#124;null** | Optional directory path for the upload. If not provided, it falls back to the file configuration. |
| `$chunkIndex` | **int** | The current chunk's index, starting from 0 (default: 0). |
| `$totalChunks` | **int** | The total number of chunks for the file, starting from 0 (default: 0). |
| `$uploadDelay` | **int** | Optional microsecond delay between chunk writes to manage resource usage (default: 0). |
| `&$destination` | **string&#124;null** | Output variable storing the final destination path or null if the upload fails. |

**Return Value:**

`bool|int` - Returns `true` if the upload is complete, `false` on failure. If the upload is still in progress, returns the remaining chunk count.

**Throws:**

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - Throws if the upload path is not configured or lacks proper permissions.

***

### write

Writes content to a file at the specified path.

This method can handle either a string or resource (e.g., a stream), using `stream_get_contents` to read the entire stream into a string before writing it.

```php
public static write(string $filename, resource|string $contents, string $mode = 'w'): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The full file path where the content should be written (e.g, `path/to/text.txt`). |
| `$contents` | **string&#124;resource** | The content to write, either a string or a stream resource. |
| `$contents` | **string&#124;resource** | The file write mode (e.g., `w` for text, `wb` for binary). |

**Return Value:**

`bool` - Return true if the file is written successfully, false otherwise.