# View Caching and Static Content Serving

***

## Overview

Page content caching and static content serving. This technique allow page content to be cached and reused when next visitors visit the same URL which promotes performance

***

## Introduction

The Luminova framework features a built-in caching mechanism designed for caching page content and serving static content. This approach allows content to be cached and reused when visitors access the same URL, significantly improving performance.

***

### How It Works

When a visitor accesses a page on your website, the framework caches the page content—if caching is enabled—and stores it for future requests. By default, all cached content is stored in the root directory at `/writeable/caches/default`. You can change this location in your application's `Template` configuration class.

The cached content is organized in subdirectories based on your `app.version`. This is especially useful for versioned URLs, such as those on a documentation website. When you release a new version of your application, users can still access the content from older versions without encountering `404` errors.

The caching process also uses environment variables like `default.cache.control` and `page.cache.expiry` to manage content expiration and cache control settings. Additionally, `page.cache.app.versions` allows the application to serve cached content from older versions and `page.cache.latest.content` allows you to define URI patterns to always cache content based on latest application version code.

***

### Cache Versioning

Luminova offers an option to serve older cached content by listing your previous application version codes in `page.cache.app.versions`. For example, if you have content cached under `app.version = 1.0.0` and the current version is `1.2.0`, you can list all older versions like this:

```bash
page.cache.app.versions [1.0.0, 1.1.0]
```

This allows the framework to search for and serve content from the specified older versions when needed.

***

### Cache Latest Version Only

You can define a list of URI patterns that should only look for cached content in the application latest version caches. To do this, specify the URI patterns in the `page.cache.latest.content` environment variable.

```bash
page.cache.latest.content = ['/', 'blog', 'blog/*/foo', 'foo/*']
```

This instructs the framework to search for cached content matching these URL patterns only in the cache folder of the current application version.

***

### Page Caching Setup

1. **Enable Page Caching:** Ensure that `page.caching` is enabled in your `.env` file to cache pages upon request.
2. **Caching Directory:** Optionally, set the location for storing cached page content in your application’s [template configuration class](/configs/template.md), located at `/app/Config/Template.php`.

***

### Non-Expiring Content

To create static content that does not expire, you need to set the cache `default.cache.control` appropriately to ensure that the content is always served from the cache without any expiration. Here's how you can achieve this:

#### Steps to Create Non-Expiring Static Content

1. **Set Cache-Control:**

   In your application, set the `page.caching.immutable` to `true`. This tells the browser to cache the content for a long period based on `page.cache.expiry` (e.g., 1 year), and the `immutable` directive ensures that the content will not be revalidated during this time.

   **Using Environment Configuration**
   ```php
   page.caching.immutable = true
   ```
	 
	 **Example Header Output**
   ```text
   Cache-Control: public, max-age=31536000, immutable;
   ```

2. **Disable Expiration in the Cache System:**
   If your framework or application has a caching system that manages expiration, set the expiration time to a very large value, or disable it entirely for the specific static content you want to be non-expiring.

   Example in your Environment Configuration:
   ```php
    page.cache.expiry => 0, // Set to 0 to disable expiration
   ```

3. **Use Versioned URLs:**
   If you have an image or a CSS file that you want to cache indefinitely, to ensure that changes to the static content are properly served when updated, consider using versioned URLs for your static assets. For example, append a version number or param to the URL:
	 
    **Image Assets:**
    ```html
    <img src="<?= asset('images/logo.v' . APP_VERSION . '.png');?>" alt="Logo">
    <!--<img src="/assets/images/logo.v1.0.0.png" alt="Logo">-->
    ```

    **Style Assets:**
    ```html
    <link rel="stylesheet" href="<?= asset('css/styles.v' . APP_VERSION . '.css');?>">
    <!--<link rel="stylesheet" href="/assets/css/styles.v1.0.0.css">-->
    ```

   This way, when you update the static content, you can change the version number, ensuring that the new content is served while the old content remains cached, providing a faster loading experience for users..

4. **Serve Content with Long-Term Caching Strategy:**
   Ensure your web server (e.g., Apache, Nginx) is configured to serve static content with a long-term caching strategy. You can configure this in the server configuration files.

   Example for Nginx:
   ```nginx
   location /static/ {
       expires 1y;
       add_header Cache-Control "public, max-age=31536000, immutable";
   }
   ```

***

## Static Content URL Suffixing

Appending extensions to URLs when cache is enabled allows you to serve cached contents partially statically. This means that the `Router` and `ViewCache` must run first to load the cached page content and render the generated content. While considered partially static, this approach can reduce view response time by up to `10%` compared to not using the dot `extension` suffix for page caches. This is because your controller methods and other modules will not be loaded when using static page serving.

***

### Static Caching Setup

1. **Specify Allowed Types:** Specify the supported static content extension type(s) in `page.caching.statics`, using pipe `|` symbol for multiple type (e.g, `page.caching.statics = html|json`). Supported extension types: `html`, `json`, `text`, `xml`, `rdf`, `atom`, `rss`, `css`, `js`.
2. **Append Extension:** Add content extension type e.g, `.html` to your request URL. This ensures that the `Router` tries to load the cached page first if it is available and not expired. If not, it will render the view as usual. 

***

### Static Caching Examples

Suppose you have a `blog` page accessible at `https://example.com/blog/how-to-install-luminova`. To serve it as static content, simply append `.html` at the end, like this: `https://example.com/blog/how-to-install-luminova.html`.
This will load the cache if available, if not, it will render page normally and store compiled HTML content for later use.

Additionally when you tries to access the blog static content using invalid type like `https://example.com/blog/how-to-install-luminova.json`, it will fail except if you have a `JSON` page in your controller method. 

***

#### Be Aware Of Static Caching Limitation

When using a custom error page that you trigger manually based on certain conditions, ensure that this page is excluded from caching.

**Example Issue**:
- If you have a URL like `https://example.com/page/action/about` and access it via `https://example.com/page/action/about.json`, a `404` error may occur.
- If page caching is enabled for your custom `404` error page, it could be stored in the cache with the same key as the actual page (`about` or `about.html`).

**Result**:
- The next time you visit `https://example.com/page/action/about` or `https://example.com/page/action/about.html`, you might see the error page instead of the intended `about` page.

**Solution**:
- Exclude error pages from caching to prevent them from being incorrectly cached and served instead of the intended content.

> **Note:** When content `.extension` type is specified in the view request URL, application events, `middleware`, and `after` middleware will not be called, as this is treated as static content.

***

### Optional Static Canonical URL

Including a canonical link tag on your web pages is recommended to help search engines recognize the preferred URL for your content, especially if there are multiple versions of the same page. This ensures that search engines index the correct URL and avoid duplicate content issues. To implement this, simply add the static version of the page URL in the `<head>` section of your web page.

**Canonical Example:**

Below is an example using the `www` version of a URL:

```html
<link rel="canonical" href="https://www.example.com/blog/how-to-install-luminova"/>
<link rel="canonical" href="https://www.example.com/blog/how-to-install-luminova.html"/>
```

This example demonstrates two possible canonical URLs, one with and one without a `.html` extension.

***

### View Cache Content Storage Pattern

Luminova's view cache system utilizes a structured pattern for storing and retrieving cached content for faster page load times and improved performance. The system generates PHP functions that are responsible for storing cache metadata and rendering cached content.

Here.s a breakdown of the caching pattern used by Luminova:

#### 1. Cache Lock Function

The cache lock function is responsible for storing metadata about the cached content. This function is named using the pattern `__lmv_template_cache_lock_<hash>`, where `<hash>` is a unique identifier derived from the content or key. 

```php
function __lmv_template_cache_lock_97e2882f(string $key): array|bool 
{
    $lock = [
        '1591b3b' => [
            'Content-Type' => 'text/html',
            'Content-Encoding' => 'gzip',
            'viewType' => 'html',
            'MaxAge' => 604800,
            'CacheImmutable' => true,
            'Expiry' => 1725790818,
            'Date' => 'Sun, 01 Sep 2024 10:20:18 GMT',
            'Modify' => 1725186018,
            'ETag' => '25b820dce4638bd85c75f1a413ab69c3',
            'Func' => '__lmv_template_content_1591b3b',
        ]
    ];

    return $lock[$key] ?? false;
}
```

**Key Elements**:

- **`Content-Type`**: Specifies the MIME type of the cached content (e.g., `text/html`).
- **`Content-Encoding`**: Defines the content encoding used, such as `gzip`.
- **`viewType`**: Indicates the type of content being cached, such as `html`.
- **`MaxAge`**: The maximum age of the cache before it should be refreshed (in seconds).
- **`CacheImmutable`**: A flag indicating if the cache is immutable.
- **`Expiry`**: The timestamp when the cache will expire.
- **`Date`**: The date and time when the content was cached.
- **`Modify`**: The timestamp of the last modification of the content.
- **`ETag`**: A unique identifier for the cached content, often used for cache validation.
- **`Func`**: The name of the function that contains the cached content.

***

#### 2. Cache Content Function

The cache content function is responsible for rendering the cached content. This function is named using the pattern `__lmv_template_content_<hash>`, where `<hash>` matches the identifier used in the lock function.

```php
<?php function __lmv_template_content_1591b3b(): void { ?>
    <!DOCTYPE html>
    <html lang="en"> 
        <head>  
            <title>Hello world</title>
        </head> 
        <body>    
            <h1>Hello world!</h1>
        </body>
    </html> 
<?php } ?>
```

**Key Elements**:

- The content function directly outputs the cached content.
- The function is designed to be lightweight and quick to execute, ensuring minimal processing time during content retrieval.

***

### How Cache Rendering Works:

- **Content Retrieval**: When a page is requested, the framework checks if there is a cached version available by calling the cache lock function with a specific key. If a valid cache entry is found (i.e., the `Expiry` timestamp has not passed), the corresponding content function is called to serve the cached content.
- **Content Validation**: The framework uses metadata, such as `ETag` and `Modify`, to determine if the cached content is still valid or needs to be refreshed.

***

### Benefits:

- **Performance**: By caching the output of templates and serving pre-rendered content, Luminova significantly reduces server processing time and load.
- **Flexibility**: The use of functions allows for easy integration with the rest of the application, enabling custom logic to be applied when necessary.
- **Cache Control**: With options like `MaxAge` and `CacheImmutable`, developers have fine-grained control over how long content should be cached and whether it should be considered immutable.

This caching mechanism is ideal for applications where certain views are frequently accessed and do not change often, making it a powerful tool for optimizing performance and reducing server load.