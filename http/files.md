# HTTP File Upload Object

***

## Overview

HTTP File object encapsulates a file ready to be uploaded to a server. It offers customization to modify properties before finally uploading to the server.

***

## Introduction

The Luminova **HTTP File Object** represents an incoming file prepared for upload to the server. It provides detailed information about the file and allows customization before passing it to the local uploader handler (`Luminova\Utility\Storage\Uploader`) or the remote storage handler (`Luminova\Utility\Storage\Storage`) for final processing.

This class supports files uploaded either as raw binary blobs or as native PHP temporary files. It allows you to define structured validation rules such as:

- Supported file formats  
- Maximum and minimum allowed sizes  
- Behavior when a file with the same name already exists  
- Optional filename changes  
- Creation of symbolic links (symlinks) for files stored in private directories

The `File` class is optimized for easy integration with the local uploader handler (`Luminova\Utility\Storage\Uploader`), which efficiently handles both small and large file uploads. When passed to the uploader, detailed upload progress messages are provided, and all configured rules are automatically applied.

***

## Example

### Accessing the File Object

When a file is uploaded, the Luminova HTTP `Request` object (`Luminova\Http\Request`) automatically includes all details about the current request, including any uploaded files.

**Accessing Files in Controller Classes:**

```php
// app/Controllers/Http/UploadController.php
namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;
use Luminova\Attributes\Prefix;
use Generator;

#[Prefix('/api/(:root)')]
class UploadController extends Controller 
{
    #[Route('/api/upload', methods: ['POST'])]
    public function doUpload(): int 
    {
        $files = $this->request->getFile('image');

        // Handle multiple files
        if ($files instanceof Generator) {
            foreach ($files as $file) {
                // Process each uploaded file
            }

            return STATUS_SUCCESS;
        }

        // Handle a single uploaded file
        return STATUS_SUCCESS;
    }
}
```

> **Notes:**
> - Use `$this->request->getFile('field_name')` to access uploaded files.
> - If multiple files are uploaded using the same field name, a `Generator` is returned.
> - For single file uploads, a single `File` object is returned.

---

**Configuration:**

Setting upload configurations for the file object

```php
use Luminova\Http\Request;

// HTTP Request object
$request = new Request();

// Get file object from HTTP request object
$file = $request->getFile('image');

// Set various upload configurations
$file->setConfig([
    'upload_path'   => root('writeable/storages/uploads/'),
    'max_size'      => 10485760,
    'min_size'      => 1024,
    'allowed_types' => 'png|jpg|gif|pdf',
    'chunk_length'  => 5242880,
    'if_existed'    => File::IF_EXIST_RETAIN,
    'symlink'       => '/public/assets/symlink',
]);

// Validate the file before proceeding with upload
if ($file->validation()->isError()) {
    throw new InvalidException(sprintf(
        'File validation failed: (%d) %s.',
        $file->getError(),
        $file->getMessage()
    ));
    
} 

echo 'File is valid for upload';
```

**Explanation:**

1. **`upload_path`**: Specifies the `writeable/storages/uploads/` as the path where the file will be saved on the server.
2. **`max_size`**: Defines the maximum allowable file size. In this example, `10MB` is the maximum size.
3. **`min_size`**: Sets the minimum allowable size for the file, such as `1KB` in the example.
4. **`allowed_types`**: Restricts file types to specific extensions (`png`, `jpg`, `gif`, `pdf`).
5. **`chunk_length`**: Enables chunked file uploads, useful for large files (`5MB` chunks in this case).
6. **`if_existed`**: Rule to determines how to handle existing files (e.g, `File::IF_EXIST_RETAIN`) retains old versions of files.
7. **`symlink`**: Creates a symlink in the specified path `/public/assets/symlink` once the upload completes.

___

## Handling Binary Uploads

When a client-side library sends your image as raw binary data (BLOB), you may see:

- `$file->extension` returns `"bin"` instead of `"png"`.  
- `$file->type` (or `$file->getMime()`) returns `"application/octet-stream"` instead of `"image/png"`.

Uploading it without proper handling will result in your file being stored as `file-name.bin`.
Below is the recommended workflow to detect and correct the real extension and MIME type before saving the file.

---

### 1. Extend your MIME‑to‑extension map

In your framework's file configuration, add any custom mappings for BLOB‑only uploads:

```php
// /app/Config/Files.php

namespace App\Config;

use Luminova\Base\Configuration;

final class Files extends Configuration
{
    /**
     * Map additional MIME types to file extensions.
     *
     * @var array<string,string>
     */
    protected static array $extensions = [
        'image/png'  => 'png',
        'image/jpeg' => 'jpg',
        // add other custom mappings here
    ];
}
```

> **Note:** 
> The base **Configuration** already includes common types. Add only the extra ones you need.

---

### 2. Allow `bin` as a temporary extension

Configure your uploader to accept `.bin` files so raw uploads pass initial validation:

```php
// Get file object from HTTP request
$file = $request->getFile('file');

$file->setConfig([
    'allowed_types' => 'png|jpg|gif|bin'
    // Other options...
]);
```

---

### 3. Post‑process and correct the extension

After the file is received, check for `.bin` and use your detector to find the real MIME → extension:

```php
use App\Config\Files;

// If the client sent a BLOB, extension is 'bin'
if ($file->getExtension() === 'bin') {
    // 1) Detect actual MIME from temp file or raw data
    $mime = $file->getMimeFromFile() 
        ?? $file->getMime() 
        ?? '';

    // 2) Lookup the correct extension
    $ext = Files::getExtension($mime);

    // 3) Build new filename (preserving original basename)
    $filename = rtrim($file->getName(), '.bin');

    // Or create a new name entirely
    //  $filename = uniqid('file_');

    // Update file name and extension
    $file->setName("{$filename}.{$ext}", true);
}
```

---

### 4. Validate and upload

Finally, run your normal validation and upload logic:

```php
use Luminova\Utility\Storage\Uploader;
use Luminova\Exceptions\InvalidException;

if (!$file->valid()) {
    // Handle validation error
    throw new InvalidException(sprintf(
        'File validation failed: (%d) %s.',
        $file->getError(),
        $file->getMessage()
    ));
}

// Upload the corrected file
$status = Uploader::upload($file);

// Or upload using chunking
// $status = Uploader::chunk($file, null, $chunkIndex, $totalChunks);

// Output final upload message
echo $file->getMessage();
```

> This ensures your server always saves images (and other files) with the correct extension and MIME metadata.

***

## Class Definition

* Class namespace: `Luminova\Http\File`
* This class implements:  [Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)

***

## Constants 

#### Custom Upload Error Codes

These constant define additional custom error code. 

| Constant              | Type   | Value      | Description                                               |
|-----------------------|--------|------------|-----------------------------------------------------------|
| `UPLOAD_ERR_NO_SIZE`   | `int`  | 9        | Custom upload error: file has no size.                            |
| `UPLOAD_ERR_MIN_SIZE`  | `int`  | 10       | Custom upload error: file is below the minimum allowed size.      |
| `UPLOAD_ERR_NO_FILE_DATA`      | `int` | 11 | Upload error no temp file or data. |
| `UPLOAD_ERR_SKIPPED`   | `int` | 12 | Upload error no skip existing file.              | 

---

#### Existing File handling Rules

These constant are available rules for handling existing files. 

| Constant              | Type   | Value      | Description                                               |
|-----------------------|--------|------------|-----------------------------------------------------------|
| `IF_EXIST_RETAIN`      | `string` | retain | Keep the existing file and save the new one with a random prefix. |
| `IF_EXIST_OVERWRITE`   | `string` | overwrite | Overwrite the existing file if it already exists.                   | 
| `IF_EXIST_RENAME`      | `string` | rename | Rename the existing file with a random prefix and save the new one. |
| `IF_EXIST_SKIP`   | `string` | skip | Skip the upload if the file already exists.                  | 

***

***

### Properties

Represents a single uploaded file, with magic property access for its metadata.

Zero-based index when multiple files are uploaded.

```php
protected int $index = 0;
```

The original filename (basename).

```php
protected ?string $name = null;
```

The MIME type reported by the client (e.g., "image/jpeg").

```php
protected ?string $type = null,
```

The file size in bytes.

```php
protected int $size = 0,
```

The file extension (derived from name or MIME).

```php
protected ?string $extension = null,
```

The temporary filesystem path, if available.

```php
protected ?string $temp = null,
```

```php
protected int $error = UPLOAD_ERR_NO_FILE,
```

Raw binary file contents (for blob/chunked uploads).

```php
protected ?string $content = null,
```

True if uploaded as raw binary/blob or via chunking.
```php
protected bool $isBlob = false
```

A validation or error message, if any.

```php
protected ?string $message = null
```

---

Accessing file instance properties via `$file->propertyName`:

```php
echo $file->name;       // e.g. "photo.jpg"
echo $file->type;       // e.g. "image/jpeg"
echo $file->mime;       // e.g. "image/png" (after detect)
echo $file->size;       // e.g. 204800
echo $file->extension;  // e.g. "jpg"
echo $file->temp;       // e.g. "/tmp/phpYzdqkD"
echo $file->error;      // e.g. UPLOAD_ERR_OK (0)
echo $file->message;    // e.g. "File size exceeds limit."
echo $file->content;    // e.g. binary data as string
echo $file->index;      // e.g. 0
echo $file->isBlob;    // true|false
```

***

## Methods

### constructor

Constructs a File object.

```php
public __construct(
	int $index = 0,
    ?string $name = null,
    ?string $type = null,
    int $size = 0,
    ?string $extension = null,
    ?string $temp = null,
    int $error = UPLOAD_ERR_NO_FILE,
    ?string $content = null,
    bool $isBlob = false
)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **int** | The index of the file in the uploaded file array, typically representing the position in a multi-file upload scenario. |
| `$name` | **string&#124;null** | The original name of the uploaded file. This includes the file name and its extension (e.g., `document.pdf`). |
| `$type` | **string&#124;null** | The MIME type of the file (e.g., `image/jpeg`, `application/pdf`). This is used to identify the type of file uploaded for further processing or validation. |
| `$size` | **int** | The size of the uploaded file in bytes. This value is essential for checking file size limits and ensuring compliance with upload restrictions. |
| `$extension` | **string&#124;null** | The file extension (e.g., `jpg`, `png`, `pdf`). This allows quick identification of the file type based on its extension and can be used for validation or categorization. |
| `$temp` | **string&#124;null** | The temporary file path where the uploaded file is stored on the server. This is the location from which the file can be moved or processed. |
| `$error` | **int** | The error code associated with the file upload (e.g, `UPLOAD_ERR_OK`, `UPLOAD_ERR_INI_SIZE`). |
| `$content` | **string&#124;null** | The file's content in string format, typically used as an alternative to using the `temp` when the file data is stored directly in memory (e.g., blobs). |
| `$isBlob` | **bool** | Indicates whether the uploaded file is handled as a binary large object (BLOB), which is commonly used for in-memory file storage (default: `false`). |

#### Manual Instantiation File Object

**From a standard PHP upload:**

```php
$file = new File(
    index:    0,
    name:     $_FILES['photo']['name'],
    type:     $_FILES['photo']['type'],
    size:     $_FILES['photo']['size'],
    extension: pathinfo($_FILES['photo']['name'], PATHINFO_EXTENSION),
    temp:     $_FILES['photo']['tmp_name'],
    error:    $_FILES['photo']['error'],
    content:  null,
    isBlob:  false
);
```

**From raw binary (BLOB) data:**

```php
$binaryData = file_get_contents('php://input');
$file = new File(
    index:     0,
    name:      'upload.bin',
    type:      'application/octet-stream',
    size:      strlen($binaryData),
    extension: 'bin',
    temp:      null,
    error:     UPLOAD_ERR_OK,
    content:   $binaryData,
    isBlob:   true
);
```

***

### getIndex

Gets the index of the file.

```php
public getIndex(): int
```

**Return Value:**

`int` - Return the index of the file.

***

### getName

Gets the name of the file.

```php
public getName(): ?string
```

**Return Value:**

`string|null` - Return the name of the file.

***

### getType

Gets the MIME type of the file.

```php
public getType(): ?string
```

**Return Value:**

`string|null` - Return the MIME type of the file.

***

### getMime

Gets the MIME type of the file.

Alias: `getType`.

```php
public getMime(): ?string
```

**Return Value:**

`string|null` - Return the MIME type of the file.

***

### getMimeFromFile

Detect and cache the MIME type from a temporary file or raw binary content.

**This method will:**

1. If a temp file path (`$file->temp`) is set, use it for detection.
2. Otherwise, fall back to raw binary data (`$file->content`).
3. Cache the result in $file->mime and return it.

```php
public getMimeFromFile(): ?string
```

**Return Value:**

`string|null` - Return the detected MIME type (e.g., `image/png`), or null if no source is available or detection fails.

> Useful for cases where the file is uploaded as (`BLOB`), typically,
> the MIME type may default to `application/octet-stream`.

***

### getSize

Gets the size of the file in bytes.

```php
public getSize(): int
```

**Return Value:**

`int` - Return the size of the file in bytes.

***

### getExtension

Gets the file extension.

```php
public getExtension(): ?string
```

**Return Value:**

`string|null` - Return the file extension.

***

### getTemp

Gets the temporary file path.

```php
public getTemp(): ?string
```

**Return Value:**

`string|null` - Return the temporary file path.

***

### getError

Gets the error code of the file upload.

```php
public getError(): int
```

**Return Value:**

`int` - Return the error code of the file upload.

***

### getMessage

Gets the validation message.

```php
public getMessage(): ?string
```

**Return Value:**

`string|null` - Return the validation message.

***

### getConfig

Gets the file upload configurations.

The returned configuration object may contain the following properties:

- **uploadPath** `(string|null)`: The target path where uploaded files will be saved.
- **maxSize** `(int|null)`: Maximum allowed file size in bytes.
- **minSize** `(int|null)`: Minimum allowed file size in bytes.
- **allowedTypes** `(array|string|null)`: List of permitted file extensions.
- **chunkLength** `(int|null)`: Length of each file chunk in bytes (used for chunked uploads).
- **ifExisted** `(string)`: Strategy to apply if the file already exists (e.g., `overwrite`, `retain`, `rename`, `skip`).
- **symlink** `(string|null)`: Path to create a symbolic link of the uploaded file.
- **base64Strict** `(bool)`: Whether to enforce strict Base64 decoding for Base64-encoded uploads.
- **data** `(mixed|null)`: Additional custom configuration information.

```php
public getConfig(): Luminova\Http\FileConfig
```

**Return Value:**

`Luminova\Http\FileConfig` - Returns the instance of upload configuration.

> **Note:** 
> The property value maybe be null if not configured.

***

### setName

Sets the file name, with an option to replace its extension.

```php
public setName(string $name, bool $replaceExtension = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The desired name of the file, without directory paths. |
| `$replaceExtension` | **bool** | (optional) If true, updates the file extension based on the provided name (default: true). |

**Return Value:**

`self` - Returns the current file instance.

**Throws:**

- [Luminova\Exceptions\StorageException](/error-handlers/exceptions.md#storageexception) - Throws if the file name contains directory paths or, when `$replaceExtension` is enabled, lacks a valid file extension.

***

### setConfig

Set file configurations for upload behavior.

```php
public setConfig(array<string,string|int> $config): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **array<string,string\|int>** | An associative array of file configuration key and value.|

**Return Value:**

`self` - Returns the current file instance.

> **Note:** The configurations will be used in validating file before uploading to server.

**Supported Configurations Keys**

| **Configuration** | **Type** | **Description** |
|-------------------|----------|-----------------|
| `upload_path` | **string** | The path where files will be uploaded. |
| `max_size` | **int** | Maximum allowed file size in bytes. |
| `min_size` | **int** | Minimum allowed file size in bytes. |
| `allowed_types` | **string\|string[]** | A list or string of allowed file types, separated by a pipe symbol (e.g., `png\|jpg\|gif`). |
| `chunk_length` | **int** | Write length of chunk in bytes. **Default:** `5242880`. |
| `if_existed` | **string** | Defines how to handle existing files (e.g., `File::IF_EXIST_RENAME`, `File::IF_EXIST_*`). **Default:** `File::IF_EXIST_OVERWRITE`. |
| `symlink` | **string** | A valid path to create a symlink after upload completion (e.g., `/writeable/storages/`, `/public/assets/`). |
| `data` | **mixed** | Additional custom configuration data. |
| `base64_strict` | **bool** | If true, `base64_decode()` will return false on invalid characters. |

***

### setMessage

Sets the file's error or feedback message and status code.

Commonly used by the `Luminova\Utility\Storage\Uploader` class to provide feedback on upload errors or processing issues.

```php
public setMessage(string $message, int $code = UPLOAD_ERR_CANT_WRITE): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The descriptive error or feedback message. |
| `$code` | **int** | The status code (e.g., `UPLOAD_ERR_*` or `File::UPLOAD_ERR_*`) Defaults to `UPLOAD_ERR_CANT_WRITE`. |

**Return Value:**

`self` - Returns the current file instance.

***

### isBlob

Determines if the file is uploaded as a BLOB (Binary Large Object).

This method checks whether the file was uploaded as a BLOB,
typically used for large file uploads or when the file's content is handled directly in binary form.

```php
public isBlob(): bool
```

**Return Value:**

`bool` - Returns true if the file is a BLOB, otherwise false.

***

### isBase64Encoded

Determines if the uploaded content string is likely to be Base64-encoded.

```php
public isBase64Encoded(): bool
```

**Return Value:**

`bool` - Returns true if the content is likely to be Base64-encoded, false otherwise.

**Example:**

Setting base64 strict validation:

If `true`, base64_decode() will return false on invalid characters.

```php
$file->setConfig([
    'base64_strict' => true
]);
```

***

### isError

Checks if an error occurred during the file upload process.

```php
public isError(): bool
```

**Return Value:**

`bool` - Returns true if an error occurred; false otherwise.

***

### free

Clears all file-related data, resets configuration, and removes the temporary file if it exists.

This method is typically called after processing or canceling an upload to ensure no temporary resources are left behind and the file object is safely reset.

```php
public free(): void
```

***

### validate

Validates the uploaded file using configured rules such as file size, type, and upload status. 

This method performs a full validation using `valid()` and returns the current file instance.
Use `isError()` to determine if validation failed.

```php
public validate(): self
```

**Return Value:**

`self` - Returns the current File instance.

**Example:**

Validate a file:

```php
if ($file->validate()->isError()) {
    echo $file->getMessage(); // Error message
    echo $file->getCode(); // Error code
}
```

***

### valid

Executes file validation checks against the defined configuration rules.

This method performs a comprehensive check to verify if the file aligns with custom configuration constraints. 

- Ensures upload completed without native PHP errors.
- Checks for non-zero size and temporary file/content availability.
- Validates against maximum/minimum file size constraints.
- Verifies the file extension against allowed types (if defined).

```php
public valid(): bool
```

**Return Value:**

`bool` - Returns true if the file passes all validations; otherwise false.

> If a validation rule fails, an appropriate error code and message are set call `$file->getMessage()` for error or upload feedback message. And `$file->getCode()` or `$file->getError()` for upload error code.

---

### toArray

Get array representation of uploaded file object.

This method creates an array from file-object properties into an associative array.

```php
public toArray(): array
```

**Return Value:**

`array<string, mixed>` - Returns an array of file upload.