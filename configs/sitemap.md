# Nova-Kit Sitemap Generator Configurations

***

## Overview

Sitemap configuration provides flexibility and control over the sitemap generation, allowing you to set limits on the number of URLs to scan, specify URLs to ignore, and define pattern views.

***

## Introduction

The `Sitemap Configuration` class offers properties to customize the behavior of [sitemap generation](/seo/sitemap.md) in your application. With this class, you can:

- Set limits on the number of URLs to scan.
- Specify URLs to ignore.
- Define a scan prefix.
- Establish patterns for extracting the last-modified timestamps of URLs.

This flexibility allows you to customize the sitemap generation process to meet your application's specific needs.

***

* Class namespace: `\App\Config\Sitemap`
* File path: `/app/Config/Sitemap.php`
* This class is marked as **final** and can't be subclassed

## Properties

### maxScan

The maximum limit of URLs to scan when generating the sitemap.

```php
public int $maxScan = 0;
```

> A value of `0 (zero)` indicates no limit, meaning all accessible URLs will be scanned.
>
> If you encounter performance issues or memory constraints, consider setting a reasonable limit.

***

### scanUrlPrefix

Sets the allowed scan start URI prefix for sitemap generation. This option restricts the URLs included in the sitemap to those that match a specific prefix. The specified prefix is appended to your start URL.

```php
public string $scanUrlPrefix = '';
```

**How It Works**: 

- If your start URL is `http://localhost/example.com/` and you specify the prefix as `blog`, the generated sitemap will only include URLs that match the pattern `http://localhost/example.com/blog/*`.
- To include all URLs starting from the start URL, set this variable to an empty string (`''`).

### Example Usage:
Set prefix to restrict sitemap generation

```php
public string $scanUrlPrefix = 'blog'; 
// This will include only URLs like http://localhost/example.com/blog/*
```

> **Note:** Ensure that the `scanUrlPrefix` is configured according to the desired URL structure you want to include in your sitemap or leave blank to include all valid URLs.

***

### ignoreUrls

URLs, URL patterns, or full URLs to ignore when generating a sitemap.

```php
public array<int,string> $ignoreUrls = [];
```

This array defines specific URLs, URL patterns, or full URLs that should be ignored
when generating a sitemap. Ignored URLs will not be included in the sitemap XML output.

Each element in the array can be:

- A full URL to completely exclude from the sitemap.
- A URL pattern using wildcard characters (`*`) to match and exclude multiple URLs.
  Example: `✸/admin/login/✸` will match any URL containing `✸/admin/login/✸` and exclude them.

***

### viewUrlPatterns

URL patterns associated with views for determining last modified timestamps in a sitemap.

```php
public array<string,string> $viewUrlPatterns = [];
```

This array defines URL patterns corresponding to views within your application. When generating
a sitemap, these patterns are used to identify views and retrieve their last modified timestamps.

To utilize dynamic last-modified date in sitemap, register URL patterns that match your web routing patterns. Use the array
key to represent the view file name associated with each pattern, without the extension [.php, .tpl or .twig].

***

### Example

Assuming you create a blog website and you would like to include the last modify date in sitemap. 

In this example we assume your routing `web.php` look like below.

```php
<?php
  $router->get('/', 'BlogController::index');
  $router->bind('/blog', function(Router $router) {
  $router->get('/', 'BlogController::blogs');
  $router->get('/author/([0-9-.]+)', 'BlogController::author');
  $router->get('/([0-9-.]+)', 'BlogController::blog');
});
```

Now with the above routing setup, your `$viewUrlPatterns` patterns should look like below

```php
public array $viewUrlPatterns = [
  'index' => '/',
  'blogs' => '/blog',
  'author' => '/blog/author/([0-9-.]+)',
  'blog' => '/blog/([0-9-.]+)'
];
```
> In the above example, the array values are exactly same as the the routing of our blog website example.
>
> While the keys indicate the template view file name which is responsible in rendering the routes.
>> *Note:* The array keys are not same with the controller method name.