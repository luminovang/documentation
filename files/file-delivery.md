# Private File Delivery Helper 

***

## Overview

The File Delivery class makes it easy to securely serve files from private storage, including support for generating temporary preview URLs that expire after a set time.

***

## Introduction

The **File Delivery** class provides essential tools for securely delivering files stored in private directories to end-users via web requests. It supports efficient file streaming with proper HTTP headers, temporary signed URLs for secure access, and browser cache control for faster repeated downloads.

Designed for high performance, it handles large file delivery with minimal overhead.

Additionally, it supports **image resizing** using the external library [Peterujah\NanoBlock\NanoImage](https://packagist.org/packages/peterujah/nano-image).

> **Note:** This class acts as a lightweight **CDN helper** for serving private files located in `/writeable/storages/`. Use it to build secure, scalable content delivery systems in your application.

***

### Examples

#### Displaying an Image in the Browser

To serve an image from private storage, you can define a route like this:

**Example URL:** `https://example.com/cdn/assets/images/image.png`

```php
// /app/Controllers/Http/CdnController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;
use Luminova\Attributes\Route;
use Luminova\Utility\Storage\FileDelivery;
use App\Errors\Controllers\ErrorController;

#[Prefix(pattern: '/cdn(:root)', onError: [ErrorController::class, 'onAssetsError'])]
class CdnController extends Controller
{
    #[Route('/cdn/assets/images/([a-zA-Z0-9-]+\.(?:png|jpg|jpeg|gif|svg|webp))', methods: ['GET'])]
    public function showImage(string $image): int 
    {
        FileDelivery::storage('images')->output($image);
        return STATUS_SUCCESS;
    }
}
```

---

#### Generating a Temporary Access URL

To generate a temporary signed URL that expires after a specified duration (e.g., 1 hour):

```php
use Luminova\Utility\Storage\FileDelivery;

$hash = FileDelivery::storage('images')
    ->url('private-image.png', 3600);

// Full preview link
echo 'https://example.com/cdn/private/image/' . $hash;
```

> **Tip:**
> The `url()` method only returns the signed hash string. You must append it to your route manually (or programmatically) to form a complete link.
>
> If the generated URL is too long, consider using a URL shortener service for cleaner sharing.

#### Previewing a Temporary Image

To display an image from a signed temporary URL, define a route to handle the hashed filename. Here’s how to handle the preview:

```php
// /app/Controllers/Http/CdnController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;
use Luminova\Attributes\Route;
use Luminova\Utility\Storage\FileDelivery;
use App\Errors\Controllers\ErrorController;

#[Prefix(pattern: '/cdn(:root)', onError: [ErrorController::class, 'onAssetsError'])]
class CdnController extends Controller
{
    #[Route('/cdn/private/image/(:string)', methods: ['GET'])]
    public function tempImagePreview(string $imageHash): int 
    {
        if (FileDelivery::storage('images')->temporal($imageHash)) {
            return STATUS_SUCCESS;
        }

        return STATUS_ERROR;
    }
}
```

> **Explanation:**
> This route captures the signed image hash and uses the `temporal()` method to validate and serve the file.
> If the hash is invalid or expired, it returns an error status. You can customize the error response as needed.

---

### Resizing and Serving Images

This example demonstrates how to resize and serve images dynamically using query parameters.

```php
// /app/Controllers/Http/CdnController.php

namespace App\Controllers\Http;

use Throwable;
use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;
use Luminova\Attributes\Route;
use Luminova\Utility\Storage\FileDelivery;
use App\Errors\Controllers\ErrorController;

#[Prefix(pattern: '/cdn(:root)', onError: [ErrorController::class, 'onAssetsError'])]
class CdnController extends Controller
{
    #[Route('/cdn/assets/images/([a-zA-Z0-9-]+\.(?:png|jpg|jpeg|gif|svg|webp))', methods: ['GET'])]
    private function imageResizePreview(string $filename): int 
    {
        // Fetch query parameters
        $width = (int) $this->request->getGet('width', 0);
        $height = (int) $this->request->getGet('height', 0);
        $quality = (int) $this->request->getGet('quality', 100);
        $keepRatio = (bool) $this->request->getGet('ratio', 1);
        $expiration = 3600;

        try {
            $storage = FileDelivery::storage('images');

            // If resizing or quality adjustments are requested
            if ($width > 0 || $height > 0 || $quality < 100) {
                return $storage->outputImage($filename, $expiration, [
                    'width'  => $width ?: null,
                    'height' => $height ?: null,
                    'quality'=> $quality,
                    'ratio'  => $keepRatio
                ], [
                    'Vary' => ''
                ]) ? STATUS_SUCCESS : STATUS_ERROR;
            }

            // Fallback to normal output if no modifications requested
            if ($storage->output($filename, $expiration, ['Vary' => ''])) {
                return STATUS_SUCCESS;
            }

        } catch (Throwable $e) {
            Luminova\Funcs\logger('debug', 'Image Delivery Error: ' . $e->getMessage());
        }

        return STATUS_ERROR;
    }
}
```

**Usage:**

Request resized image via query parameters:

```
GET /cdn/assets/images/sample.jpg?width=300&height=200&quality=80&ratio=1
```

> **Note:** This requires the external library `Peterujah\NanoBlock\NanoImage` to handle resizing.

***

## Class Definition

* Class namespace: `Luminova\Utility\Storage\FileDelivery`
* This class is marked as **final** and can't be subclassed

***

## Methods

### constructor

 Initialize the constructor to have full access to the path where your file is located without any limitation like using `storage` method.

```php
public __construct(string $filepath, bool $eTag = true, bool $weakEtag = false): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filepath` | **string** | Path to the private storage (e.g: `/writeable/storages/images/`). |
| `$eTag` | **bool** | Whether to generate ETag header and apply validation as needed (default: true). |
| `$weakEtag` | **bool** | Whether to use a weak ETag header or string (default: false). |

> **Note:** Set `$eTag` to true even if you are passing custom etag header.

***

### storage

To initialize the `FileDelivery` with a base path, `ETag` option and return static instance.

```php
public static storage(string $basepath, bool $eTag = true, bool $weakEtag = false): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$basepath` | **string** | Base path for file storage, (e.g: `/images/`). |
| `$eTag` | **bool** |  Whether to generate ETag header and apply validation as needed (default: true). |
| `$weakEtag` | **bool** | Whether to use a weak ETag header or string (default: false). |

**Return Value:**

`static` - Return Instance of the FileDelivery class.

> **Note**
> Your files must be stored in the storage directory located in `/writeable/storages/`.
> Additionally, you don't need to specify the `/writeable/storages/` in your `$basepath` parameter.
> Set `$eTag` to true even if you are passing custom etag header.

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

Modify and output image with appropriate headers. 

This method uses an external package `\Peterujah\NanoBlock\NanoImage`, to customize the image's `width`, `height`, `quality`, and resizing `ratio`.

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

[Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - Throws if NanoImage image is not installed or if error occurred during image processing.

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

[Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - Throws if decryption failed or an error is encountered.

***

### url

To generate a temporal signed `URL` with an expiration time for given filename.

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

[Luminova\Exceptions\EncryptionException](/error-handlers/exceptions.md#encryptionexception) - Throws if encryption failed or an error occurred.