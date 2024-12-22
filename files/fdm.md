# Secure File Access and Browser Delivery with FileDelivery Module

***

## Overview

The FileDelivery class simplifies secure file delivery from a private storage location to the user's web browsers including generating temporal URLs for file previews.

***

## Introduction

The `FileDelivery` class offers essential methods for handling and delivering files from private storage to end-users via web requests. It can output files with appropriate headers, generate and handle temporary `URLs` for file access, and manage browser cache for faster loading when needed.

Additionally, it provides an option to resize output images using the external library `NanoImage`.

This class functions as a `CDN` helper, designed to create an efficient content delivery system for serving files anywhere.

***

### Examples

This display image in browser, here is a simple example.

```php
<?php
use \Luminova\Storages\FileDelivery;
use \Luminova\Base\BaseViewController;

class CdnController extends BaseViewController
{
    //...

    public function showImage(?string $image = null): int 
    {
        $image ??= 'default-image.png';
        FileDelivery::storage('images')->output($image);

        return STATUS_SUCCESS;
    }
}
```

To generate a temporary URL for an image that expires after a specified duration, use the following example.

```php
<?php
use \Luminova\Storages\FileDelivery;

$temporalUrl = FileDelivery::storage('images')
    ->url('private-image.png', 3600);

// Output the complete URL for file preview
echo 'https://example.com/static/file/' . $temporalUrl; 
```

> *Note:* 
> The above code snippet will output only the hash for the selected image. You will need to append this hash to your site URL as required in your controller routing for capturing image previews. 
> 
> Optionally, you can use an online URL `shortener` to shorten the URL if it appears too long due to the encryption used.

To preview the temporal image generated above see below example.

```php
use \Luminova\Storages\FileDelivery;
use \Luminova\Base\BaseViewController;
class CdnController extends BaseViewController
{
    //...

    public function tempImagePreview(string $ImageHash): int 
    {
        if(FileDelivery::storage('images')->temporal($ImageHash)){
            return STATUS_SUCCESS;
        }

        return STATUS_ERROR;
    }
}
```

***

## Class Definition

* Class namespace: `\Luminova\Storages\FileDelivery`
* This class is marked as **final** and can't be subclassed

***

## Methods

### constructor

 Initialize the constructor to have full access to the path where your file is located without any limitation like using `storage` method.

```php
public __construct(string $filepath, bool $etag): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filepath` | **string** | Path to the private storage (e.g: `/writeable/storages/images/`). |
| `$etag` | **bool** | Whether to generate ETag headers. |

***

### storage

To initialize the `FileDelivery` with a base path, `ETag` option and return static instance.

```php
public static storage(string $basepath, bool $etag = true): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$basepath` | **string** | Base path for file storage, (e.g: /images/). |
| `$etag` | **bool** | Whether to generate ETag headers. |

**Return Value:**
`static` - Return Instance of the FileDelivery class.

> **Note**
> Your files must be stored in the storage directory located in `/writeable/storages/`.
> Additionally, you don't need to specify the `/writeable/storages/` in your `$basepath` parameter.

***

### output

Read and outputs any file content in the browser, with appropriate headers.

```php
public output(
    string $basename, 
    int $expiry = 0, 
    array $headers = [],
    int $length = (1 << 21),
    int $delay = 0
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$basename` | **string** | The file name (e.g: image.png). |
| `$expiry` | **int** | Expiry time in seconds for cache control (default: 0), indicating no cache. |
| `$headers` | **array<string,mixed>** | An associative array for additional headers to set. |
| `$length` | **int** | Optional size of each chunk to be read (default: 2MB). |
| `$delay` | **int** | Optional delay in microseconds between chunk length (default: 0). |

**Return Value:**

`bool` - Returns true if file output is successfully, false otherwise.

> By default `304`, `404` and `500` headers will be set based file status and cache control.

***

### outputImage

Modify and output image with appropriate headers. Using the external package `\Peterujah\NanoBlock\NanoImage`, you can customize the image's `width`, `height`, `quality`, and resizing `ratio`.

```php
public outputImage(string $basename, int $expiry = 0, array $options = [], array $headers = []): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$basename` | **string** | The file name (e.g: image.png). |
| `$expiry` | **int** | Expiry time in seconds for cache control (default: 0), indicating no cache. |
| `$options` | **array<string,mixed>** | Image filter options. |
| `$headers` | **array<string,mixed>** | An associative array for additional headers to set. |

**Filter Options**

- `width` **(int)**  -  Set new output width (default: 200).
- `height` **(int)** -  Set new output height (default: 200).
- `ratio` **(bool)** -  Specify weather to aspect ratio while resizing image (default: true).
- `quality` **(int)** - Set the output image quality (default: 100, 9 for PNG).

**Return Value:**

`bool` - Returns true if file output is successfully, false otherwise.

**Throws:**

[\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throws if NanoImage image is not installed or if error occurred during image processing.

> **Note:** This method relay on external image library to modify the width and height.
> To use this method you need to install Peter Ujah's `NanoImage` following the below instructions. 

**Composer Command:**

If you don't already have it, run this command 

```bash
composer require peterujah/nano-image
```

***

### temporal

Temporally output file based on the URL hash key if valid and not expired.

```php
public temporal(string $url_hash, array $headers = []): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url_hash` | **string** | The file encrypted URL hash. |
| `$headers` | **array** | Additional headers to set. |

**Return Value:**

`bool` - Return true if file output is successful, false otherwise.

> **Note:** This method uses `Encryption` and `Decryption` class to encrypt and decrypt `URL` hash.

**Throws:**

[\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - Throws if decryption failed or an error is encountered.

***

### url

To generate a temporal `URL` with an expiration time for given filename.

```php
public url(string $basename, int $expiry = 3600): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$basename` | **string** | The name of the file (e.g: filename.png). |
| `$expiry` | **int** | The expiration time in seconds (default: `1 hour`). |

**Return Value:**

`string|false` - Return based64 encrypted `HASH`, otherwise false.

**Throws:**

[\Luminova\Exceptions\EncryptionException](/running/exceptions.md#encryptionexception) - Throws if encryption failed or an error occurred.