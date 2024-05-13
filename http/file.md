## File Upload

***

## Overview

File object encapsulates a file ready to be uploaded to a server. It offers customization options before sending the file to the upload class, It allows you to modify properties like filename, etc...

***

The `File` object represents a file ready to be uploaded to the server. With this class, you can customize files before sending them to the `Uploader` or `Storage` class for final upload. Optionally, you can change the filename or set a `symlink` path to generate a symbolic link for files stored in the private storage directory.

***

* Class namespace: `\Luminova\Http\File`

***

## Methods

### constructor

Constructs a File object.

```php
public __construct(int $index, string|null $name = null, string|null $type = null, int $size, string|null $mime = null, string|null $extension = null, string|null $temp = null, int $error): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **int** | The index of the file. |
| `$name` | **string&#124;null** | The name of the file. |
| `$type` | **string&#124;null** | The MIME type of the file. |
| `$size` | **int** | The size of the file in bytes. |
| `$mime` | **string&#124;null** | The MIME type of the file. |
| `$extension` | **string&#124;null** | The file extension. |
| `$temp` | **string&#124;null** | The temporary file path. |
| `$error` | **int** | The error code of the file upload. |

***

### getters

Magic getter method to allow access to protected properties.

```php
<?php 
echo $file->name;
echo $file->type;
echo $file->size;
echo $file->mime;
echo $file->extension;
echo $file->temp;
echo $file->error;
echo $file->message;
echo $file->index;
```

***

### getIndex

Gets the index of the file.

```php
public getIndex(): int
```

**Return Value:**

`int` - The index of the file.

***

### getName

Gets the name of the file.

```php
public getName(): string|null
```

**Return Value:**

`string|null` - The name of the file.

***

### getType

Gets the MIME type of the file.

```php
public getType(): string|null
```

**Return Value:**

`string|null` - The MIME type of the file.

***

### getSize

Gets the size of the file in bytes.

```php
public getSize(): int
```

**Return Value:**

`int` - The size of the file in bytes.

***

### getMime

Gets the MIME type of the file.

```php
public getMime(): string|null
```

**Return Value:**

`string|null` - The MIME type of the file.

***

### getExtension

Gets the file extension.

```php
public getExtension(): string|null
```

**Return Value:**

`string|null` - The file extension.

***

### getTemp

Gets the temporary file path.

```php
public getTemp(): string|null
```

**Return Value:**

`string|null` - The temporary file path.

***

### getError

Gets the error code of the file upload.

```php
public getError(): int
```

**Return Value:**

`int` - The error code of the file upload.

***

### getMessage

Gets the validation error message.

```php
public getMessage(): string|null
```

**Return Value:**

`string|null` - Return the validation error message.

***

### getConfig

Gets file upload configurations.

```php
public getConfig(): \stdClass|null
```

**Return Value:**

`\stdClass|null` - Return upload configurations.

***

### setName

This method allows you to change the file name before finally uploading.

```php
public setName(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the file. |

**Return Value:**

`self` - Return instance of self.

**Throws:**

- [StorageException](../Exceptions/StorageException.md) - If the filename contains paths or does not have a valid file extension type.

***

### setConfig

To set file configurations before uploading.

```php
public setConfig(array&lt;string,mixed&gt; $config): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **array<string,mixed>** | Configuration data for the upload.|

**Return Value:**

`self` - Return instance of self.

> The configurations will be used in validating file before uploading to server.

**Options**

- `upload_path`     (string) The path where files will be uploaded.
- `max_size`            (int) Maximum allowed file size in bytes.
- `min_size`             (int) Minimum allowed file size in bytes.
- `allowed_types`  (string) Allowed file types separated by '&amp;#124;'.
-  `chunk_length`   (int) Length of chunk in bytes (default: 5242880).
- `if_existed`            (string) How to handle existing files [overwrite or retain] (default: overwrite).
- `symlink`                (string) Specify a valid path to create a symlink after upload was completed (e.g /public/assets/). 

***

### free

Reset file configuration and remove temp file.

```php
public free(): void
```

***

### valid

Checks if the file is valid according to the specified configuration.

```php
public valid(): bool
```

**Return Value:**

`bool` - True if the file is valid, false otherwise.