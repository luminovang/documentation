# Novakit Sitemap Generator Configuration

***

## Overview

Set limits, exclude pages, or customize patterns to control how your application generates its sitemap for search engines.

***

## Introduction

The **Sitemap Configuration** class gives you control over how your application generates sitemaps. Using this class, you can:

* Limit the number of URLs to scan.
* Exclude specific URLs from the sitemap.
* Define a scan prefix to target certain sections of your site.
* Set patterns for extracting the last-modified timestamps of pages.

![Sitemap Example](https://luminova.ng/assets/images/sitemap.gif)

> These options let you fine-tune sitemap generation for better SEO and performance.

***

## Class Definition

* Class namespace: `\App\Config\Sitemap`
* File path: `/app/Config/Sitemap.php`
* This class is marked as **final** and can't be subclassed

## Properties

### maxScan

Defines how many URLs to scan when generating the sitemap.

```php
public int $maxScan = 0;
```

* A value of `0` means **no limit** — all accessible URLs will be scanned.
* If sitemap generation slows down or uses too much memory, set a reasonable upper limit to improve performance.

***

### scanSpeed

Specifies how many seconds to wait before scanning the next URL during sitemap generation.
This delay helps control the crawling speed and reduce server load.

```php
public int $scanSpeed = 5;
```

> **Note:** The minimum value is `1` second.

---

### maxExecutionTime

Defines the maximum time (in seconds) the sitemap generation script can run before timing out.
If the value exceeds the current PHP execution limit, the script will adjust it automatically.

```php
public int $maxExecutionTime = 300;
```

> **Note:** Use `0` for no limit.

***

### scanUrlPrefix

Defines the URI prefix to restrict which URLs are scanned during sitemap generation.
The prefix is appended to your start URL, so only matching URLs will be included in the sitemap.

```php
public string $scanUrlPrefix = '';
```

**How it works:**

* If your start URL is `http://localhost/example.com/` and you set the prefix to `blog`, only URLs like `http://localhost/example.com/blog/*` will be scanned.
* If left empty (`''`), all URLs under the start URL will be included.

**Example:**

```php
public string $scanUrlPrefix = 'blog';  
// Only scans URLs like http://localhost/example.com/blog/*
```

> **Note:** Use this setting to limit sitemap generation to a specific section of your site, or leave blank to include everything.

***

### changeFrequently

Specifies how often a page is expected to change, helping search engines decide how frequently to crawl it.
This setting is only a hint — search engines may still choose their own crawling schedule.

```php
public ?string $changeFrequently = null;
```

**Valid values:**

* `always` – updated continuously
* `hourly` – updated at least once an hour
* `daily` – updated at least once a day
* `weekly` – updated at least once a week
* `monthly` – updated at least once a month
* `yearly` – updated at least once a year
* `never` – not expected to change
* `null` – no frequency information provided

> **Note:** Use this to give crawlers general guidance on update frequency, not as a guarantee.

***

### includeStaticHtml

Controls whether static `.html` versions of URLs are added alongside the dynamic URLs in the sitemap XML.
Enable this if your site provides static HTML versions of certain pages to help search engines index both variants.

```php
public bool $includeStaticHtml = true;
```

**Example behavior:**

* URL: `https://example.com/page`
* Added to sitemap as:

  * `https://example.com/page`
  * `https://example.com/page.html` (if enabled)

**Sample output:**

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

> **Note:** The root/start URL (e.g., `https://example.com/`) is never appended with `.html`.

***

### skipStaticHtml

Defines URL patterns where static `.html` versions should **not** be added to the sitemap.

Use this when certain sections (like forums, dashboards, or API docs) don't need static HTML counterparts. Any URL matching a pattern in this array will only include its dynamic URL.

```php
public array $skipStaticHtml = [];
```

**Pattern rules:**

* Use `*` as a wildcard to match any part of the URL.

**Examples:**

* `'*/foo/bar/*'` → skips generating `.html` for all URLs under `/foo/bar/`.

> **Tip:** This is especially useful for excluding dynamic sections that should not be indexed twice.

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

Mapping last modified timestamps.

This is used to map your view templates to URL patterns so the sitemap can include the **last modified date** for each page.

The array stores pairs of:

* **Key** → the template file name (without `.php`, `.tpl`, or `.twig`)
* **Value** → the URL pattern that corresponds to that view

This helps your sitemap know which template is responsible for which URL and when it was last updated.

```php
public array<string,string> $viewUrlPatterns = [];
```

---

#### How to Use

1. Look at your web routes.
2. For each route, identify the view template it renders.
3. Add an entry in `$viewUrlPatterns` using:

   * **key** = view file name
   * **value** = route URL

**Example:**

Imagine you have a blog website with the following routes:

```php
// /app/Controllers/Http/BlogController.php

#[Route('/')]
public function index(): int
{
    return $this->view('index');
}

#[Route('/blog')]
public function blogs(): int
{
    return $this->view('blogs');
}

#[Route('/blog/author/(:username)')]
public function author(): int
{
    return $this->view('author');
}

#[Route('/blog/(:number)')]
public function blog(): int
{
    return $this->view('blog');
}
```

The `$viewUrlPatterns` array should look like this:

```php
public array $viewUrlPatterns = [
    'index'  => '/',
    'blogs'  => '/blog',
    'author' => '/blog/author/(:username)',
    'blog'   => '/blog/(:number)'
];
```

**Visual Guide: Routes → Views → Sitemap**

```
Routes (web.php)                  View Templates          $viewUrlPatterns
───────────────────────────      ────────────────       ─────────────────────────
/                                index.php             'index'  => '/'
/blog                            blogs.php             'blogs'  => '/blog'
/blog/author/(:username)         author.php            'author' => '/blog/author/(:username)'
/blog/(:number)                  blog.php              'blog'   => '/blog/(:number)'
```

> **Notes:**
>
> * Use **view file names** as keys (without extensions).
> * Use **routes** as values exactly as they appear in your routing file.
> * This lets the sitemap automatically pick the correct last modified date for each URL.
