# Rate Limiter and Throttling Middleware

***

## Overview

Rate Limiter class provides a lightweight and flexible methods for controlling the frequency of requests made by clients to your application.

***

## Introduction

Rate Limiter is a lightweight and flexible class that provides methods for controlling the frequency of requests made by clients to your application. It is designed to prevent abuse or overuse of resources by enforcing rate limits based on a unique client key, such as a user ID, IP address, or identifier key.

This class supports a plug-and-play approach with various caching backends like `FileCache`, `Redis`, `Memcached`, `CacheItemPoolInterface` and`CacheInterface`. You can define how many requests are allowed (`limit`) within a given time window (`ttl`), and enforce these rules globally, per controller, or per individual route.

The `RateLimiter` tracks each request and stores usage data in the provided cache instance. When the request threshold is exceeded, it offers clean response handling and headers that inform clients of their remaining quota and retry wait time.

It also offers execution throttling, when the limit is reached, the throttler can delay further requests, making is ideal for quick integration where flexible request regulation is needed, and it fits naturally into application-level hooks, controllers, middlewares or route handlers.

---

### Key Features:

- Works with any PSR-compatible cache or Luminova cache classes.
- Prevents request flooding and abuse.
- Supports dynamic limit policies across different scopes (global, controller, or route).
- Easy to integrate into `onPreCreate`, middlewares, or specific methods.
- Provides customizable response messages and HTTP headers.

---

## Usage Examples

### Supported Cache Backends

The `RateLimiter` supports multiple caching backends, allowing flexible implementation based on your environment or preferences.

**Using Luminova's FileCache (default fallback):**

```php
use Luminova\Cache\FileCache;

$path = root('/writeable/caches/path/to/limiter');
$file = new FileCache($path);
```

**Using Redis:**

```php
use Redis;

$redis = new Redis();
$redis->connect('127.0.0.1', 6379);
```

**Using Memcached:**

```php
use Memcached;

$memcached = new Memcached();
$memcached->addServer('127.0.0.1', 11211);
```

---

### Basic Rate Limiting

```php
use Luminova\Security\RateLimiter;
use Memcached;

$key = 'user-id';

$cache = new Memcached();
$cache->addServer('127.0.0.1', 11211);

$limiter = new RateLimiter(cache: $cache, limit: 10, ttl: 60);

if (!$limiter->check($key)->isAllowed()) {
    $limiter->message('Rate limit exceeded: {limit} requests. Retry after {retry}. Remaining: {remaining}.')
         ->respond();

    // Or throw an exception
    // throw new TooManyRequestException('Too many requests');
}
```

Delay request operation.

```php
if (!$limiter->check($key)->isAllowed()) {
    $limiter->throttle(5); // Wait 5 seconds
}
```

Updating request limit with new limit.

```php
if (!$limiter->check($key)->isAllowed()) {
    // Just an example
    if(!$limiter->isIpAddress()){
        $limiter->continue(2);
    }else{
        $limiter->message('Rate limit exceeded: {limit} requests. Retry after {retry}. Remaining: {remaining}.')
            ->respond();
    }
}
```

---

**Synchronous Rate Limiting:**

Perform a rate‑limit check and block execution until the check completes.

```php
use Luminova\Security\RateLimiter;
use Memcached;

$key = 'user-id';

$cache = new Memcached();
$cache->addServer('127.0.0.1', 11211);

$limiter = new RateLimiter(cache: $cache, limit: 10, ttl: 60);

// Check and wait until the rate‑limit check resolves
$limiter->check($key)->wait();

if (!$limiter->isAllowed()) {
    $limiter->message('Rate limit exceeded: {limit} requests. Retry after {retry}. Remaining: {remaining}.')
         ->respond();
}
```

---

## Advanced Usage

### Application Scope Rate Limiting

You can enforce global rate limiting by placing the logic inside the `onPreCreate()` method of your application class. This ensures rate limits are checked at the earliest stage—before any routing or service initialization occurs—allowing the application to terminate immediately if the request exceeds the allowed threshold. This approach is ideal for protecting all incoming API traffic with a consistent policy.

```php
// /app/Application.php
namespace App;

use Luminova\Core\CoreApplication;
use Luminova\Security\RateLimiter;
use Memcached;

class Application extends CoreApplication 
{
    protected function onPreCreate(): void 
    {
        $key = request()->header->get('X-Api-User-Id');

        $cache = new Memcached();
        $cache->addServer('127.0.0.1', 11211);

        $limiter = new RateLimiter(cache: $cache, limit: 10, ttl: 60);

        if (!$limiter->check($key)->isAllowed()) {
            $limiter->message('Rate limit exceeded.')
                 ->respond();

            $this->terminate(true, [
                'userId' => $key
            ]);
        }
    }

    protected function onTerminated(array $info): void 
    {
        logger('debug', 'Application was terminated', $info);
    }
}
```

---

### Controller Scope Rate Limiting

Apply rate limiting to specific controllers or routes when different policies are needed per endpoint, or to exclude certain routes entirely.

#### In `onCreate()` Method (Controller-wide)

```php
// /app/Controllers/Http/ApiController.php
namespace App\Controllers\Http;

use Luminova\Bae\BaseController;
use Luminova\Security\RateLimiter;
use Memcached;

class ApiController extends BaseController 
{
    protected function onCreate(): void 
    {
        $key = request()->header->get('X-Api-User-Id');

        $cache = new Memcached();
        $cache->addServer('127.0.0.1', 11211);

        $limiter = new RateLimiter(cache: $cache, limit: 10, ttl: 60);

        if (!$limiter->check($key)->isAllowed()) {
            $limiter->message('Rate limit exceeded.')
                 ->respond();
            exit;
        }
    }
}
```

---

#### As Middleware (Before Route Execution)

Apply rate limiting within a controller's **before middleware** to enforce limits before any route-specific logic is executed. This ensures that all requests to the controller or a specific URL prefix attached to the controller class are evaluated early—before any view or template rendering occurs.

```php
// /app/Controllers/Http/ApiController.php
namespace App\Controllers\Http;

use Luminova\Bae\BaseController;
use Luminova\Security\RateLimiter;
use Luminova\Attributes\Route;
use Luminova\Attributes\Prefix;
use Luminova\Http\Request;

#[Prefix(pattern: '/api(:root)')]
class ApiController extends BaseController 
{
    #[Route('/api/(:root)', methods: ['ANY'], middleware: Route::BEFORE_MIDDLEWARE)]
    public function middleware(Request $request): int
    {
        $key = $request->header->get('X-Api-User-Id');

        return $this->rate->check($key)->isAllowed()
            ? STATUS_SUCCESS
            : STATUS_ERROR;
    }

    protected function onMiddlewareFailure(string $uri, array $classInfo): void 
    {
        $key = request()->header->get('X-Api-User-Id');

        response(429, $this->rate->getHeaders())->json([
            'error' => 'Rate limit exceeded'
        ]);
    }
}
```

---

#### Per-Route Rate Limiting

Apply rate limiting directly within a specific controller method to enforce limits on a per-endpoint basis. This allows requests to be evaluated just before the view logic is executed, ensuring only permitted clients can access the response.  

```php
// /app/Controllers/Http/ApiController.php
namespace App\Controllers\Http;

use Luminova\Bae\BaseController;
use Luminova\Security\RateLimiter;
use Luminova\Attributes\Route;
use Luminova\Http\Request;
use App\Models\Users;

class ApiController extends BaseController 
{
    #[Route('/api/users/', methods: ['GET'])]
    public function users(Request $request): int 
    {
        $key = $request->header->get('X-Api-User-Id');

        $cache = new Memcached();
        $cache->addServer('127.0.0.1', 11211);

        $limiter = new RateLimiter(cache: $cache, limit: 10, ttl: 60);

        if ($limiter->check($key)->isAllowed()) {
            return response()->json([
                'data' => (new Users())->select()
            ]);
        }

        return response(429, $limiter->getHeaders())->json([
            'error' => 'Rate limit exceeded'
        ]);
    }
}
```

***

# Class Definition

* Class namespace: `\Luminova\Security\RateLimiter`
* This class implements:
[\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface)

***

## Methods

### constructor

Create a new RateLimiter instance.

This constructor allows flexible integration with different caching backends to track and limit request rates per client (typically IP-based).

**Supported Cache Instance**

- PSR-6: `CacheItemPoolInterface` (e.g., CustomCache)
- PSR-16: `CacheInterface` (e.g., Psr\SimpleCache\CacheInterface)
- `BaseCache`: Luminova's custom file or memory cache.
- `Memcached`: Native Memcached instance.
- `Redis`: PHP Redis extension instance.
- `PredisClient`: Predis library instance.

```php
public __construct(
    Psr\Cache\CacheItemPoolInterface|Psr\SimpleCache\CacheInterface|Luminova\Base\BaseCache|Memcached|Predis\Client|Redis|null $cache = null, 
    int $limit = 10, 
    DateInterval|int $ttl = 60
    string $persistentId = ''
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$cache` | **mixed** | Optional cache instance. If null, the default Luminova file-based cache will be used. |
| `$limit` | **int** | Maximum number of allowed requests within the TTL window (default: `10`). |
| `$ttl` | **DateInterval&#124;int** | Time-to-live for request tracking, in seconds or as a DateInterval (default: `60`). |
| `$persistentId` | **string** | Optional unique ID to be included with key. |

***

### setCache

Set the cache instance used for rate limiting.

This method allows you to provide a custom caching backend to store
rate-limiting data. Supported cache types include:

- PSR-6 (`CacheItemPoolInterface`) and PSR-16 (`CacheInterface`) implementations
- Native `Memcached` or `Redis` instances
- `PredisClient` instance
- Luminova's custom `BaseCache`

If no cache is set explicitly, the default fallback is Luminova's file-based cache.

```php
public setCache(
    Psr\Cache\CacheItemPoolInterface|Psr\SimpleCache\CacheInterface|Luminova\Base\BaseCache|Memcached|Predis\Client|Redis $cache
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$cache` | **mixed** | The cache instance used to store limiter data. |

**Return Value:**

`self` - Returns the instance of RateLimiter class.

***

### getHeaders

Get the proper headers for rate limitation.

```php
public getHeaders(): array<string,mixed>
```

**Return Value:**

`array<string,mixed>` - Return an associative array of headers.

***

### getExtendedLimit

Get the total number of extended requests limit added via (`continue`).

```php
public getExtendedLimit(): int
```

**Return Value:**

`int` - Return the extended request limit or `0` if none.

***

### getLimit

Get the maximum number of allowed requests within the time window.

```php
public getLimit(): int
```

**Return Value:**

`int` - Return the configured request limit.

***

### getTimestamps

Retrieve the recorded request timestamps.

Optionally filters and returns only valid (non-expired) timestamps within the current time window, based on the configured TTL (time-to-live).

```php
public getTimestamps(bool $filterValidOnly = false): array 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filterValidOnly` | **bool** | If true, only return timestamps that are still valid (not expired). |

**Return Value:**

`array` - Return an array of request timestamps, filtered by validity if requested.

***

### getRequests

Get the current number of request attempts made during the current time window.

```php
public getRequests(): int
```

**Return Value:**

`int` - Return the number of requests made so far.

***

### getRemaining

Get the number of remaining allowed requests in the current time window.

```php
public getRemaining(): int
```
**Return Value:**

`int` - Return the remaining number of requests before limit is reached.

***

### getRetryAfter

Get the number of seconds until the rate limit resets.

```php
public getRetryAfter(): int
```
**Return Value:**

`int` - Return the seconds until the next request will be allowed.

***

### getReset

Get the exact UNIX timestamp when the rate limit will reset.

```php
public getReset(): int
```
**Return Value:**

`int` - Return the timestamp of rate limit reset.

***

### getTimeWindow

Get the time window in seconds used for rate limiting.

```php
public getTimeWindow(): int
```
**Return Value:**

`int` - Return the duration of the rate-limiting window in seconds.

***

### getIp

Retrieves the stored client IP address associated with the current request context.

```php
public getIp(): ?string
```

**Return Value:**

`string|null` -  Return the IP address if available, or null if not set.

***

### isAllowed

Retrieves the result after performing the `check` method.

```php
public isAllowed(): bool
```
**Return Value:**

`bool` - Returns true if the request is allowed, false if the rate limit has been reached.

***

### isIpAddress

Determines whether the current request is from the same IP address that initialized the key.

This method returns true if this is the first request, or if the stored IP address matches the current IP.

```php
public isIpAddress(): bool
```

**Return Value:**

`bool` - Return true if the IP matches or it's the first request; otherwise, false.

> Useful for validating the continuity of a rate-limited session or key-bound identity.

***

### check

Checks if a request is allowed based on the rate limit for the given key.

This method generates a unique key based on the clients's IP address or default to provided key.
It uses MD5 hashing for simplicity in key hashing (e.g., `Rate-Limiter:{Persistent-Id}+{Custom-Key||Ip-Address}`).

```php
public check(?string $key = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | An optional custom identifier to check the rate limit for (e.g., `User-Id`). |

**Return Value:**

`self` - Returns the instance of the RateLimiter class, reflecting the status.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If the cache instance is invalid or the key is empty.
- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the cache is not connected.

***

### wait

Waits until the rate limiter finishes processing or the optional max wait timeout is exceeded.

This is a passive wait that periodically checks if the limitter decision is complete. It is useful when limitter involves asynchronous or deferred logic.

```php
public wait(float|int $interval = 1.0, ?int $maxWait = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$interval` | **float\|int** | The interval to sleep between checks (in seconds). Supports sub-second delays (e.g., 0.1 for 100ms). |
| `$maxWait` | **int&#124;null** | The maximum duration to wait (in seconds). Use `null` to wait indefinitely. |

***

### remaining

Generate the number of remaining allowed requests for the given key.

```php
public remaining(?string $key = null): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | An optional unique identifier to check (e.g, `User-Id`).<br/> Default to client IP address if key is not provided. |

**Return Value:**

`int` - Return the remaining number of requests before the limit is reached.

***

### continue

Resets the rate-limiting state to allow additional requests to continue processing.

This method updates the internal request counters by subtracting the given `$limit` from the total consumed request count and adding it back to the remaining allowance. It also resets the `finished` and `exceeded` flags to indicate that the current rate-limiting session is active and within bounds.

```php
public continue(int $limit = 1): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$limit` | **int** | The number of requests to re-allow or reassign (default is 1). |

**Return Value:**

`self` - Returns the instance of the RateLimiter class, reflecting the status.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If the cache instance is not valid or the key is empty.
- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If cache is not connected.

***

### reset

 Reset remaining rate to initial limit and consumed request to 0.

```php
public reset(?string $key = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | An optional unique identifier to rest requests (e.g, `User-Id`).<br/> Default to client IP address if key is not provided. |

**Return Value:**

`self` - Returns the instance of the RateLimiter class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If the cache instance is not valid or the key is empty.
- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If cache is not connected.

***

### has

Check whether the rate limiter has an entry for the given key.

```php
public has(?string $key = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;null** | An optional unique identifier to check (e.g, `User-Id`).<br/> Default to client IP address if key is not provided. |

**Return Value:**

`bool` - Return true if the key exists in the cache, false otherwise.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If the cache instance is not valid or the key is empty.
- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If cache is not connected.

***

### message

Sets the response message and its output format.

This method allows you to define the body of the rate limit response in various formats.
If the message is a string, you can include the following placeholders which will be replaced automatically:

**Placeholder Variables:**

- `{limit}` - Total number of allowed requests.
- `{remaining}` - Number of requests remaining.
- `{reset}` - Timestamp when the limit resets.
- `{retry}` - Seconds to wait before retrying.

**Supported Output Formats:**

- `json` - Outputs as JSON. Arrays/objects are encoded automatically.
- `html` - Outputs as HTML string.
- `xml` - Outputs as XML string.
- `text` - Outputs as plain text.
- `custom` - Outputs with custom format; you must manually set the `Content-Type` header.

```php
public message(string|array|object $body, string $type = 'json'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$body` | **string&#124;array&#124;object** | The response body or message to output. |
| `$type` | **string** | The response message format (e.g, `html`). |

**Return Value:**

`self` - Return instance of RateLimiter class.

**Examples:**

```php
// As JSON with message placeholders
$rateLimiter->message("You have {remaining} of {limit} requests left. Try again in {retry} seconds.");

// As plain text
$rateLimiter->message("Rate limit exceeded. Please retry later.", 'text');

// With structured array (auto-converted if not JSON format)
$rateLimiter->message([
    'error' => 'Rate limit exceeded',
    'retry_after' => 60
], 'html');
```

***

### respond

Send an HTTP 429 (Too Many Requests) response with appropriate rate-limit headers.

```php
public respond(array<string,string> $headers = []): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$headers` | **array<string,string>** | Additional headers to include in the response key-pare. |

**Return Value:**

`int` - Return response status (e.g, `STATUS_*`).

***

### throttle

Introduces an execution delay throttling if the rate limit was exceeded.

This can be used to enforce backoff or slow down abusive clients after a throttle breach. If the limit was not exceeded, this method exits immediately.

```php
public throttle(float|int $interval = 1.0): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$interval` | **float\|int** | Delay duration in seconds. Supports sub-second values (e.g., 0.5 for 500ms). |