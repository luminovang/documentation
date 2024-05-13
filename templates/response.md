## ViewResponse

***

## Overview

Render or download content with view response class or using its global procedural function.

***

The View Response class simplifies APs, and WEB content responses within your application's controller class or routing closures. With its global procedural function, you can easily access the class instance without instantiating the class.

* Class namespace: `\Luminova\Template\ViewResponse`

***

### Helper Function

The global helper of ViewResponse class will return instance of this class.

```php
<?php
response(int $status = 200)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | HTTP status code (default: 200 OK) |
| `$encode` | **bool** | Enable content encoding like gzip, deflate. |

***

### constructor

Initialize class constructor with HTTP status code .

```php
new ViewResponse(int $status = 200)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | HTTP status code (default: 200 OK) |

***

### setStatus

Set the HTTP status code using setStatus method.

```php
public setStatus(int $status = 200): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$status` | **int** | HTTP status code (default: 200 OK) |

**Return Value:**

`ViewResponse` - Instance of view response class.

***

### encode

Set enable or disable content encoding, otherwise it will use default flag in env file `enable.encoding`

```php
public encode(bool $encode): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$encode` | **bool** | Enable content encoding like gzip, deflate. |

**Return Value:**

`ViewResponse` - Instance of view response class.

***

### minify

Set enable or disable content minification, otherwise it will use default flag in env file `page.minification

```php
public minify(bool $minify): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$minify` | **bool** | Enable content minification. |

**Return Value:**

`ViewResponse` - Instance of view response class.

***

### render

Render any content format anywhere with your own customizations.
Using this method allows you to pass `encode` or `minify` without using the default from `env`.

```php
public render(mixed $content, $status = 200, array $headers = [],  bool $encode = true, bool $minify = false): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **mixed** | Response content |
| `$status` | **int** | HTTP status code (default: 200 OK) |
| `$headers` | **array** | Additional headers. |
| `$encode` | **bool** | Enable content encoding like gzip. |
| `$minify` | **bool** | Enable content minification and compress. |

**Return Value:**

`int` - Return status code success as STATUS_SUCCESS, otherwise failure as STATUS_ERROR.

> This can be used in router or APIs to respond content without an addition overhead in processing the contents.
> 
> *Note:* The default content type is `application/json` if not was provided in `$headers`.

***

### json

Send a JSON response.

```php
public json(array|object $content): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **array&#124;object** | Data to be encoded as JSON |

**Return Value:**

`int` - Return status code success, otherwise failure.

***

### text

Send a plain text response.

```php
public text(string $content): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | Text content |

**Return Value:**

`int` - Return status code success, otherwise failure.

***

### html

Send an HTML response.

```php
public html(string $content): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | HTML content |

**Return Value:**

`int` - Return status code success, otherwise failure.

***

### xml

Send an XML response.

```php
public xml(string $content): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | XML content |

**Return Value:**

`int` - Return status code success, otherwise failure.

***

### download

Download file from server or download response string content.

```php
public download(string $fileOrContent, string|null $name = null, array $headers = []): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | Path to the file or content to be downloaded |
| `$name` | **string&#124;null** | Optional name to be used for the downloaded file |
| `$headers` | **array** | Optional download headers. |

**Return Value:**

`bool` - Return true if the download was successful, false otherwise

***

### redirect

URL redirection, redirect to another URL.

```php
public redirect(string $url = '/', int $response_code): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | URL location to redirect. |
| `$response_code` | **int** | Response status code |