## Response Object

***

## Overview

The Response interface represents an HTTP network request response, including the status code, headers, body, and additional information.

***

The `Response` object allows you to work with HTTP network request responses in your application. It provides methods to access the status code, headers, body, and additional information associated with the response from `Network` class.

***

* Class namespace: `\Luminova\Http\Message\Response`

---

### getStatusCode

Get the HTTP status code of the response.

```php
public getStatusCode(): int
```

**Return Value:**

`int` - The status code.

***

### getHeaders

Get the response headers.

```php
public getHeaders(): array
```

**Return Value:**

`array` - The response headers.

***

### getBody

Get the response body.

```php
public getBody(): mixed
```

**Return Value:**

`mixed` - The response body.

***

### getContents

Get the response contents.

```php
public getContents(): mixed
```

**Return Value:**

`mixed` - The response contents.

***

### getInfo

Get the response info.

```php
public getInfo(): array
```

**Return Value:**

`array` - The response info.
