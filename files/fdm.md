# File Delivery Manager

***

## Overview

The FileDelivery class simplifies secure file delivery from a private storage location to the user's web browsers including generating temporal URLs for file previews.

***

## Introduction

The `FileDelivery` class provides a useful methods for handling and delivering files stored in a private storage directory to end-users via web requests. It offers methods to output files with appropriate headers, generating and handling temporal URLs for file access, and manage browser cache to ensure fast load when cache is needed.

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

$temporalUrl = FileDelivery::storage('images')->url('private-image.png', 3600);

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

> *Note*
> Your files must be stored in the storage directory located in `/writeable/storages/`.
> Additionally, you don't need to specify the `/writeable/storages/` in your `$basepath` parameter.

***

### output

Outputs the file content with appropriate headers in browser.

```php
public output(string $basename, int $expiry, array&lt;string,mixed&gt; $headers = []): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$basename` | **string** | The file name (e.g: image.png). |
| `$expiry` | **int** | Expiry time in seconds for cache control (default: 0), indicating no cache. |
| `$headers` | **array<string,mixed>** | An associative array for additional headers to set. |

**Return Value:**
`bool` - Returns true if file output is successfully, false otherwise.

> By default `304`, `404` and `500` headers will be set based file status and cache control.

***

### temporal

To processes and output a temporal file `URL` based on `URL` hash to determine the expiration.

```php
public temporal(string $url_hash, array $headers = []): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url_hash` | **string** | The encrypted URL hash. |
| `$headers` | **array** | Additional headers to set. |

**Return Value:**
`bool` - True if file output is successful, false otherwise.

> *Note:*
> This method uses `Encryption` and `Decryption` class to encrypt and decrypt `URL` hash.

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