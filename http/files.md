# HTTP Request Upload File Object

***

## Overview

File object encapsulates a file ready to be uploaded to a server. It offers customization options before sending the file to the upload class, It allows you to modify properties like filename, etc...

***

## Introduction

The `File` object represents a file ready to be uploaded to the server. With this class, you can customize files before sending them to the `Uploader` or `Storage` class for final upload. Optionally, you can change the filename or set a `symlink` path to generate a symbolic link for files stored in the private storage directory.

***

### Configuration Example

Setting upload configurations for the file object

```php
<?php
use Luminova\Http\Request;

$request = new Request();
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
if ($file->valid()) {
    echo 'File is valid for upload';
} else {
    echo 'File validation failed: (' . $file->getError() . ') ' . $file->getMessage();
}
```

**Explanation:**

1. **`upload_path`**: Specifies the `writeable/storages/uploads/` as the path where the file will be saved on the server.
2. **`max_size`**: Defines the maximum allowable file size. In this example, `10MB` is the maximum size.
3. **`min_size`**: Sets the minimum allowable size for the file, such as `1KB` in the example.
4. **`allowed_types`**: Restricts file types to specific extensions (`png`, `jpg`, `gif`, `pdf`).
5. **`chunk_length`**: Enables chunked file uploads, useful for large files (`5MB` chunks in this case).
6. **`if_existed`**: Determines how to handle existing files; `File::IF_EXIST_RETAIN` retains old versions of files.
7. **`symlink`**: Creates a symlink in the specified path `/public/assets/symlink` once the upload completes.

***

## Class Definition

* Class namespace: `\Luminova\Http\File`
* This class implements:  [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface)

***

## Constants 

These constant are available for custom error code and custom file configurations for upload behavior. 

| Constant              | Type   | Value      | Description                                               |
|-----------------------|--------|------------|-----------------------------------------------------------|
| `UPLOAD_ERR_NO_SIZE`   | `int`  | 9        | Custom upload error: file has no size.                            |
| `UPLOAD_ERR_MIN_SIZE`  | `int`  | 10       | Custom upload error: file is below the minimum allowed size.      |
| `IF_EXIST_RETAIN`      | `string` | retain | `setConfig` possible value for key `if_existed`: Retain the old version and create a new version of the file if it exists. |
| `IF_EXIST_OVERWRITE`   | `string` | overwrite | `setConfig` possible value for key `if_existed`: Overwrite the existing file if it exists.                   | 

***

## Methods

### constructor

Constructs a File object.

```php
public __construct(
	protected int $index = 0,
    protected ?string $name = null,
    protected ?string $type = null,
    protected int $size = 0,
    protected ?string $extension = null,
    protected ?string $temp = null,
    protected int $error = UPLOAD_ERR_NO_FILE,
    protected ?string $content = null,
    protected bool $is_blob = false
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
| `$is_blob` | **bool** | Indicates whether the uploaded file is handled as a binary large object (BLOB), which is commonly used for in-memory file storage (default: `false`). |

***

### getters

Magic getter method to allow access to protected properties.

```php
<?php 
echo $file->name; // File name
echo $file->type; // File MIME type
echo $file->mime; // File MIME type from temporary file
echo $file->size; // File size
echo $file->extension; // File extension type 
echo $file->temp; // File temporary filepath
echo $file->error; // File error code
echo $file->message; // File validation message
echo $file->content; // File string content 
echo $file->index; // File index 
echo $file->is_blob; // Is file uploaded as binary or from browser chunking
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

```php
public getMime(): ?string
```

**Return Value:**

`string|null` - Return the MIME type of the file.

> Alias of `getType`.

***

### getMimeFromTemp

Retrieves the MIME type directly from the temporary file path.

```php
public getMimeFromTemp(): ?string
```

**Return Value:**

`string|null` - Returns the MIME type of the file, or null if no temporary file exists.

> Useful for cases where the file is uploaded as a large object (BLOB).
> Typically, the MIME type may default to 'application/octet-stream'.

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

Gets file upload configurations.

```php
public getConfig(): ?\stdClass
```

**Return Value:**

`\stdClass|null` - Return upload configurations.

***

### setName

Sets the file name, with an option to replace its extension.

```php
public setName(string $name, bool $replace_extension = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The desired name of the file, without directory paths. |
| `$replace_extension` | **bool** | (optional) If true, updates the file extension based on the provided name (default: true). |

**Return Value:**

`self` - Return instance of file object.

**Throws:**

- [\Luminova\Exceptions\StorageException](/running/exceptions.md#storageexception) - Throws if the file name contains directory paths or, when `$replace_extension` is enabled, lacks a valid file extension.

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

`self` - Return instance of file object.

> **Note:** The configurations will be used in validating file before uploading to server.

**Supported Configurations Keys**

- **upload_path** - `(string)` The path where files will be uploaded.
- **max_size** - `(int)` Maximum allowed file size in bytes.
- **min_size** - `(int)` Minimum allowed file size in bytes.
- **allowed_types** - `(string|string[])` Array of allowed file types or String separated by pipe symbol (e.g, `png|jpg|gif`).
- **chunk_length** - `(int)` Length of chunk in bytes (default: `5242880`).
- **if_existed** - `(string)` How to handle existing files [`File::IF_EXIST_OVERWRITE` or `File::IF_EXIST_RETAIN`] (default: `File::IF_EXIST_OVERWRITE`).
- **symlink** - `(string)` Specify a valid path to create a symlink after upload was completed (e.g `/public/assets/`). 

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

### isError

Checks if an error occurred during the file upload process.

```php
public isError(): bool
```

**Return Value:**

`bool` - Returns true if an error occurred; false otherwise.

***

### free

Resets the file configuration, clears content, and deletes any temporary file.

```php
public free(): void
```

***

### valid

Validates the uploaded file against the defined configuration rules.

Unlike `isError()`, which only checks for basic upload errors, `valid()` performs a comprehensive check to verify if the file aligns with custom configuration constraints. For instance, it verifies the file's existence,
size, and type based on the configuration settings, offering additional checks beyond standard upload errors.

```php
public valid(): bool
```

**Return Value:**

`bool` - Returns true if the file is valid; false otherwise, with an appropriate error code and message set.