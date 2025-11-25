# Structured Data and Schema Objects Generator for Webpages

***

## Overview

The Schema Generator class is a powerful SEO module designed to simplify the creation of structured data for your web pages. It helps improve your website's visibility and indexing on search engines.

***

## Introduction

Website **Search Engine Optimization (SEO)** is essential in modern web development. It helps ensure that your website, product, or content is visible to potential customers when they search online.

With Luminova's **Schema** class, you can easily add **JSON-LD Structured Data** to your application pages. This structured data helps search engines better understand your content, improving how your site appears in search results.

The **Schema** class is designed to make embedding structured data simple and flexible. It offers easy-to-use methods and per-page configuration so you can automatically generate schema objects for every page of your application. This enhances your website’s visibility, indexing, and overall SEO performance.

## Default Configuration

The schema comes with a default configuration that applies globally across your website. These defaults ensure that important data is always present, even if no custom settings are added for a specific page. However, you can override these settings on a per-page basis whenever you need to customize the schema.

For detailed instructions, see the [Default Schema Configuration](/configs/structured-data.md).

***

## Examples

Structured Data and Schema Objects Generator for Webpages.

You can load the **Schema** class either directly in your **Controllers** (for page-specific data) or globally in your **Application** (to set defaults used site-wide).

---

### 1. Initializing the Schema Class

If you want to set up your Schema globally, initialize it inside your application's `onCreate()` method. This way, every page will automatically have a base configuration without needing to repeat the setup.

**Example: using `Application` in `onCreate`**

```php
// /app/Application.php
namespace App;

use Luminova\Component\Seo\Schema;
use Luminova\Foundation\Core\Application as CoreApplication;

class Application extends CoreApplication
{
    public ?Schema $schema = null;

    /**
     * Runs once when the application boots.
     * Ideal place to configure global SEO schema defaults.
     */
    protected function onCreate(): void
    {
        // Initialize the Schema generator
        $this->schema = new Schema(); 

        // Current request URI path (e.g, company/about)
        $uri = $this->getUri();

        // Set the current page URL
        $this->schema->setUrl(
            APP_URL,         // Base application URL (e.g. https://example.com)
            $uri  // Set current request URI
        );

        // Optionally set a canonical URL www alias (e.g. https://www.example.com)
        $this->schema->setCanonical(APP_URL_ALIAS . $uri);
    }
}
```

> **Note:**
>
> This setup can also be done in controller classes.
>
> **Why in Application class?**
>
> * Ensures every page has structured data by default.
> * Reduces repetitive setup in each controller.
> * You can still override schema details per page later.

---

### 2. Adding Page-Specific Structured Data

Not every webpage should share the same metadata. For example, blog posts, product pages, and landing pages often need unique SEO information. The **Schema** class lets you inject structured data dynamically—either in your **Controller methods**, in your **Application**, or directly **inside templates**.

#### Controller with Dynamic SEO Schema

The most common approach is to populate page-specific data in your controller before rendering a template.

**Example:**

```php
// /app/Controllers/Http/WebpageController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Prefix;
use Luminova\Attributes\Route;
use App\Errors\Controllers\ErrorController;

#[Prefix('/(:root)', onError: [ErrorController::class, 'onWebError'])]
class WebpageController extends Controller
{
    #[Route('/', methods: ['GET'])]
    public function index(): int
    {
        // Uses only the global schema defaults
        return $this->view('index');
    }

    #[Route('/blog/(:number)', methods: ['GET'])]
    public function blog(string $id): int
    {
        $fullUrl  = $this->request->getUrl();
        $blog = (new Blog)->find($id);

        // Add structured data specific to this blog article
        $this->app->schema->setObject([
            'isArticle'        => true,
            'url'              => $fullUrl,
            'article' => [
                'type'             => 'Article', /* Could also be 'TechArticle' */
                'description'      => $blog->intro,
                'headline'         => $blog->headline,
                'title'            => $blog->title,
                'word_count'       => strlen($blog->body), /*  Ideally store this with the blog e.g, $blog->word_count */
                'keywords'         => [$blog->category, ...$blog->tags],
                'section'          => [$blog->id],
                'published_date'   => $blog->published_on,
                'modified_date'    => $blog->modified_on,
                'author'           => [
                    'id'    => $blog->username
                    'name'  => $blog->author,
                    'url'   => "https://example.com/author/{$blog->author_username}",
                    'image' => [
                        'url' => "https://example.com/assets/authors/{$blog->author_username}.png",
                    ]
                ],
            ],
            'breadcrumbs'      => [
                [
                    'url' => APP_URL . '/blog/', 
                    'name' => 'All Our Blog',
                ],
                [
                    'url' => $fullUrl, 
                    'name' => $blog->title,
                ],
            ],
            /* Add more fields as needed... */
        ]);

        return $this->view('blog', [
            'id'   => $id,
            'blog' => $blog,
        ]);
    }
}
```

---

### 3. Embedding Structured Data on Pages

Once your Schema object is initialized, you can embed SEO meta tags and structured data directly inside your view templates. This will output the appropriate `<meta>` tags and JSON-LD script blocks automatically.

**Example:**

Within your template view files, add.

```html
<!-- /resource/Views/index.php -->
<!DOCTYPE html>
<html> 
<head>
    <!-- Add SEO meta tags automatically generated by Schema -->
    <?= $this->app->schema->getHeadTags(); ?>

    <!-- Set the page title -->
    <title><?= $this->app->schema->getTitle(); ?></title>

    <!-- Embed JSON-LD structured data -->
    <?= $this->app->schema->getJsonLdScript(); ?> 
</head> 
<body> 
    <p>Welcome to my webpage!</p>
</body>
</html>
```

---

#### Embedding Data Inside the Blog Template

The blog content passed to your view is available using framework's template options.

**Example template output:**

```html
<!-- /resource/Views/blog.php -->
<!DOCTYPE html>
<html> 
<head>
    <!-- Embed SEO meta tags for the blog page -->
    <?= $this->app->schema->getHeadTags();?>

    <!-- Add page title dynamically -->
    <title><?= $this->app->schema->getTitle();?></title>

    <!-- Embed structured JSON-LD schema -->
    <?= $this->app->schema->getJsonLdScript();?> 
</head> 
<body> 
    <article>
        <h1><?= $this->_blog->headline; ?></h1>
        <p><?= $this->_blog->body; ?></p>
    </article>
</body>
</html>
```

> With this setup, your blog page now outputs complete structured data for search engines.

**How it works:**

* `getHeadTags()` adds meta tags like description, OpenGraph, and Twitter card data.
* `getTitle()` outputs the properly formatted page title.
* `getJsonLdScript()` injects the JSON-LD structured data for search engines.

For more implementation examples, see [Schema Generator examples](/examples/seo-integrations.md) documentation.

---

## Class Definition

* Full namespace: `\Luminova\Component\Seo\Schema`
* This class implements:
[Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)

**See Also:**

See [Schema Properties](/website-optimizations/seo-structured-data.md) documentation

## Methods

### constructor

Creates a Schema instance with default configuration.

Loads schema defaults from your app config, sets the base URL for the current page, and prepares value filters used to remove empty entries from structured data.

```php
public __construct(?array $filterValues = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filterValues` | **array&#124;null** | Values to treat as empty and remove<br />from schema data. If `null`, the (default: `['', 0, null, [], ['']]`). |

**See Also:**

*  - /app/Config/Schema.php Application schema configuration.* https://luminova.ng/docs/0.0.0/configs/structured-data - Schema configuration guide.

***

### getInstance

Returns the shared singleton instance of this class.

Creates a new instance on first call and reuses the same instance for subsequent calls.

```php
public static getInstance(): self
```

**Return Value:**

`self` - Return a shared singleton instance.

***

### isArticle

Marks the current schema as an article.

```php
public isArticle(bool $article = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$article` | **bool** | Whether this schema represents an article (default: true). |

**Return Value:**

`self` - Returns the current Schema instance.

***

### isProduct

Marks the current schema as a product.

```php
public isProduct(bool $product = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$product` | **bool** | Whether this schema represents a product (default: true). |

**Return Value:**

`self` - Returns the current Schema instance.

***

### setUrl

Sets the URL for the current schema.

Use to link the schema object to a specific page or resource.

```php
public setUrl(string $url, string $uri = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | Base URL (e.g. `https://example.com`). |
| `$uri` | **string** | Optional URI path to append to the base URL. |

**Return Value:**

`self` - Returns the current Schema instance.

> This is not necessarily the canonical URL, it's simply a pointer or reference used within structured data.

***

### setObject

Merges custom schema data into the current page object.

Use this when you want to populate or override schema properties for the current page using an associative array. By default, empty values in the array are ignored, but you can choose to retain them.

```php
public setObject(array<string,mixed> $object, bool $keepEmpty = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$object` | **array<string,mixed>** | Key-value pairs representing schema properties (e.g., `['article' => ['headline' => 'My Page']`). |
| `$keepEmpty` | **bool** | Whether to retain empty values (default: false). |

**Return Value:**

`self` - Return the instance of schema.

**See Also:**

* \Luminova\Component\Seo\setValue() - To set a single property.
* https://luminova.ng/docs/0.0.0/configs/structured-data - See all supported properties.

***

### setValue

Sets or overrides a single schema property for the current page.

Use this when you need to update a specific schema value without replacing the entire configuration array.

```php
public setValue(string $key, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The schema property name (e.g., `headline`, `author`). |
| `$value` | **mixed** | The value to assign to this property. |

**Return Value:**

`self` - Return the instance of schema.

***

### setTitle

Sets the current page title with automatic site branding.

Appends the application name to the title if it's not already included.

```php
public setTitle(string $title): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$title` | **string** | The base title of the page (without app name). |

**Return Value:**

`self` - Return the instance of schema.

> Useful for consistent SEO page titles across your application.

***

### setDescription

Sets the current page description for schema object.

Adds or updates the meta description that search engines display in search results. Keep this concise **(around 150–160 characters)**.

```php
public setDescription(string $description): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$description` | **string** | The meta description for the page. |

**Return Value:**

`self` - Return the instance of schema.

***

### setHeadline

Sets article page headline for SEO and structured data.

The headline is often used by search engines and schema.org markup to summarize the content. 

> Headline should be short and clear.

```php
public setHeadline(string $headline): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$headline` | **string** | The headline text for the page. |

**Return Value:**

`self` - Return the instance of schema.

***

### setCanonical

Adds a canonical URL to avoid duplicate content issues.

Canonical URLs tell search engines which version of a page is authoritative. You may add multiple canonicals if necessary (e.g., for multilingual pages).

```php
public setCanonical(string $url): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The base canonical URL (e.g., `https://example.com`). |

**Return Value:**

`self` - Return the instance of schema.

> **Note:** 
> If multiple canonicals are added, ensure they are all valid and relevant to avoid conflicting signals to search engines.

***

### getObject

Retrieve a schema configuration value by key with optional query string processing.

Looks up a value from the both default and dynamic schema configuration.

* If the value is an array, it is returned as-is.
* If the value is a link or canonical, it may have query parameters appended.

```php
public getObject(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The configuration key to retrieve. |
| `$default` | **mixed** | Default value to return if the key is not found (default: `null`). |

**Return Value:**

`mixed` - Return the resolved configuration value, or $default if not found.

***

### getTitle

Get the current page title set in the schema.

Uses the internal schema data to fetch the `title` text. Returns an empty string if not set.

```php
public getTitle(): string
```

**Return Value:**

`string` - Return the current page title.

***

### getUrl

Get the current page link (URL) set in the schema.

Uses the internal schema data to fetch the `url` value. Returns an empty string if not set.

```php
public getUrl(): string
```

**Return Value:**

`string` - Return the current page link URL.

***

### getJsonLdString

Get the JSON-LD representation of the schema.

Converts the current schema data to a JSON string, using unescaped slashes
and pretty-print formatting for readability.

```php
public getJsonLdString(): string
```

**Return Value:**

`string` - Return the schema as a JSON-LD string.

***

### getJsonLdScript

Get the JSON-LD schema as an HTML `<script>` tag.

Useful for embedding structured data in a web page.
Should be called once per page to output the schema object inside an `application/ld+json` script tag.

```php
public getJsonLdScript(?string $id = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$id` | **string&#124;null** | Optional ID attribute for the <script> tag. |

**Return Value:**

`string` - Return the HTML `<script>` element containing the schema.

***

### getHeadTags

Generates SEO header tags including standard, Open Graph, and Twitter data.

Combines keywords, description, canonical URLs, social metadata and custom tags
into properly formatted HTML `<meta>` or `<link>` tags. Should be called once per
page to embed SEO information.

```php
public getHeadTags(): string
```

**Return Value:**

`string` - Return the generated HTML meta tags.

***

### toJsonLd

Get the complete structured data schema for the current page.

Builds and returns the schema.org data as an associative array.

This should typically be called internally or once per page to generate the JSON-LD or meta tags for SEO.

```php
public toJsonLd(): array<string,mixed>
```

**Return Value:**

`array<string,mixed>` - Return the structured data schema array.