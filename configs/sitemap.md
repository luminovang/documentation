# Configurations for Novakit Sitemap Generator

***

## Overview

Sitemap configuration provides flexibility and control over the sitemap generation, allowing you to set limits on the number of URLs to scan, specify URLs to ignore, and define pattern views.

***

## Introduction

![Sitemap Example](https://luminova.ng/assets/images/sitemap.gif)

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

This property allows you to limit the number of URLs to scan during the sitemap generation process.

```php
public int $maxScan = 0;
```

> A value of `0 (zero)` indicates no limit, meaning all accessible URLs will be scanned.
>
> If you encounter performance issues or memory constraints, consider setting a reasonable limit.

***

### scanSpeed

The number of seconds to wait before moving to the next URL during sitemap generation.

This value determines the delay for downloading content from each URL, impacting the overall speed of the sitemap generation process.

```php
public int $scanSpeed = 5;
```

> **Note:** The minimum allowed value is 1 second.

***

### maxExecutionTime

Sets the sitemap scrap script's maximum execution time.

If the provided timeout exceeds the current limit it adjust the time.

```php
public int $maxExecutionTime = 300;
```

> **Note:** Use `0` for not limit.

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

### changeFrequently

Indicates how frequently the pages are likely to change in the sitemap.

This property provides general information to search engines about the expected frequency of changes to the associated URL. While this value does not guarantee how often search engines will crawl the page, it helps them prioritize their crawling strategy based on the indicated frequency. 

```php
public ?string $changeFrequently = null;
```

**Valid values include:**
- `always`: The page is updated continuously.
- `hourly`: The page is updated at least once an hour.
- `daily`: The page is updated at least once a day.
- `weekly`: The page is updated at least once a week.
- `monthly`: The page is updated at least once a month.
- `yearly`: The page is updated at least once a year.
- `never`: The page is not expected to change.
- `null`, no frequency information will be provided for the page.

***

### includeStaticHtml

Determines whether to include a static `.html` version of URLs in the sitemap XML.

When set to `true`, this property ensures that each URL entry in the sitemap is accompanied by a corresponding `.html` static version. This is useful for sites that have static HTML alternatives for certain pages or sections, allowing search engines  to index both the dynamic and static versions. 

```php
public bool $includeStaticHtml = true;
```
**Example:**
- If the URL is `https://example.com/page`, the static `.html` version will be included as `https://example.com/page.html` in the sitemap if this setting is enabled.

**Output**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
   <url>
       <loc>https://example.com/page</loc>
       <lastmod>2024-09-21T09:22:41+00:00</lastmod>
       <priority>0.80</priority>
   </url>
   <url>
       <loc>https://example.com/page.html</loc>
       <lastmod>2024-09-21T09:22:41+00:00</lastmod>
       <priority>0.80</priority>
   </url>
</urlset>
```

> **Note:** By default the start URL will not include the `.html` (e.g, `https://example.com/`).

***

### skipStaticHtml

List of URL patterns to skip when generating static `.html` versions in the sitemap.

This array defines specific URL patterns that should not have static `.html` versions included in the sitemap XML. When a URL matches any of these patterns, the sitemap generator will exclude the static `.html` version for that URL. 

This is useful for excluding certain sections of the site, such as dynamic content that does not require a static HTML counterpart (e.g., documentation pages, forums, or external links).

```php
public array $skipStaticHtml = [];
```

**Supported Patterns:**
- Wildcard (`*`) is used to match any characters in the URL.

**Examples:**
- `'*/foo/bar/*'`: Excludes all URLs under the `docs/edit` path.
	
***

### ignoreUrls

URLs, URL patterns, or full URLs to ignore when generating a sitemap.

This array defines specific URLs, URL patterns, or full URLs that should be ignored
when generating a sitemap. Ignored URLs will not be included in the sitemap XML output.

```php
public array<int,string> $ignoreUrls = [];
```

**Each element in the array can be:**

- A full URL to completely exclude from the sitemap.
- A URL pattern using wildcard characters (`*`) to match and exclude multiple URLs.

**Example: **

- `*/admin/login/*` will match any URL containing `*/admin/login/*` and exclude them.
- `@(^|.*\/)?scheme:\/\/.*` will match any URL containing `scheme://*` and exclude them.

***

### viewUrlPatterns

URL patterns associated with views for determining last modified timestamps in a sitemap.

This array defines URL patterns corresponding to views within your application. When generating
a sitemap, these patterns are used to identify views and retrieve their last modified timestamps.

```php
public array<string,string> $viewUrlPatterns = [];
```

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