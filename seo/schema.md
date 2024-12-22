# Structured Data Generator for Schema Objects

***

## Overview

The Schema Generator class is a powerful SEO module designed to simplify the creation of structured data for your web pages. It helps improve your website's visibility and indexing on search engines.

***

## Introduction

You probably want to make your web application visible on google search, meet the `Schema` Structured Data Generator class is a powerful `SEO` module designed to simplify the creation of structured data for search engine optimization (SEO) purposes across your web pages. It provides asy-to-use methods to configure and create schema objects on every page of your web application to enhance the visibility and indexing of your website's on search engines.

## Default Configuration

The schema supports a default configuration, which displays across web pages, but these default settings can be overridden based on pages. For more details, refer to the [Application Schema Configuration](/configs/schema.md).

***

## Class Definition

* Class namespace: `\Luminova\Seo\Schema`
* This class implements:  [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface)
* This class is marked as **final** and can't be subclassed

## Methods

For more implementation help, see the documentation for [Schema Generator examples](/seo/examples.md).

### constructor

Initializes the `Schema` class with default configurations.

```php
public __construct(): mixed
```

***

### getInstance

Retrieves a singleton instance of the `Schema` class.

```php
public static getInstance(): static
```

**Return Value:**

`static` - Return a shared instance of the `Schema` class.

***

### setLink

Sets the URL for the current web page.

```php
public setLink(string $link): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$link` | **string** | The current page URL. |

**Return Value:**

`self` - Return schema class instance.

***

### setConfig

Configures the current page with an array of settings.
This method allow you to set the current page information from an array at once.

```php
public setConfig(array&lt;string,mixed&gt; $config): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **array<string,mixed>** | The page configuration settings. |

**Return Value:**

`self` - Return schema class instance.

#### Configuration options

 An array of configuration options for schema generation.

- `string` - **link**: The URL of the current page.
- `string` - **canonical**: The canonical URL of the current page.
- `array<int,array<string,string>>` - **breadcrumbs**: An array of breadcrumb items, each containing:
    - `string` - **link**: The URL of the breadcrumb item.
    - `string` - **name**: The name of the breadcrumb item.
    - `string` - **description**: The description of the breadcrumb item.
- `string` - **image_assets**: The base URL for image assets.
- `string[]` - **company_brands**: Array of organization brands.
- `string` - **company_name**: The name of the organization.
- `string` - **page_description**: The description of the current page.
- `string` - **company_description**: The description of the organization.
- `string` - **title**: The title of the current page.
- `string` - **headline**: The headline of the current page.
- `string` - **image_name**: The name of the current page image (accessible after prepending image_assets).
- `int` - **image_width**: The width of the current page image.
- `int` - **image_height**: The height of the current page image.
- `string` - **image_type**: The type of the current page image.
- `string` - **published_date**: The published date of the current page article.
- `string` - **modified_date**: The modified date of the current page article.
- `string[]` - **keywords**: An array of keywords for the current page.
- `bool` - **isArticle**: Whether the current page is an article `false` (e.g, `true` or `false`).
- `string` - **article_type**: The type of the article.
- `string[]` - **article_keywords**: An array of keywords for the article.
- `string[]` - **article_section**: An array of sections for the article.
- `int` - **word_count**: The word count of the article (default: 1500).
- `int` - **total_comments**: The total number of comments on the article (default: 0).
- `string` - **author**: The author name (default: `APP_NAME`).
- `bool` - **isProduct**: Whether the current page is a product `false` (e.g, `true` or `false`).
- `string` - **product_type**: The type of the product (default: `Product`).
- `string` - **product_image_link**: The full URL of the product image.
- `string` - **product_category**: The category of the product (default: `Electronics`).
- `string` - **availability**: The availability of the product (default: `InStock`).
- `string` - **currency**: The currency of the product price (default: `NGN`).
- `double` - **price**: The price of the product (default: 0.00).
- `string` - **brand**: The brand of the product.
- `string` - **twitter_name**: The Twitter handle.
- `string` - **search_query**: The search query URL format (default: `/search?q={search_term_string`).
- `string` - **search_input**: The name of the search input field (default: `search_term_string`).

> Initial default configurations can be set in your application's controller config directory, located at `/app/Config/Schema.php`.

***

### setTitle

Sets the title for the current page.

```php
public setTitle(string $title): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$title` | **string** | The page title. |

**Return Value:**

`self` - Return schema class instance.

***

### setDescription

Sets the description for the current page.

```php
public setDescription(string $description): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$description` | **string** | The page description. |

**Return Value:**

`self` - Return schema class instance.

***

### setCanonical

Sets the canonical URL for the current page.

```php
public setCanonical(string $canonical, string $view = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$canonical` | **string** | The canonical URL. |
| `$view` | **string** | Optional view paths to prepend to the canonical URL (default: `''`). |

**Return Value:**

`self` - Return schema class instance.

***

### getTitle

Retrieves the title of the current page.

```php
public getTitle(): string
```

**Return Value:**

`string` - Return the current page title.

***

### getLink

Retrieves the URL of the current page.

```php
public getLink(): string
```

**Return Value:**

`string` - Return the current page link URL.

***

### getGraph

Converts the schema data into a JSON string.

```php
public getGraph(): string
```

**Return Value:**

`string` - Return the JSON representation of the schema.

***

### getScript

Generates an HTML script tag containing the schema data for embedding on a web page.
Call this method once on page creation to generate schema object in javaScript tag for the current page.

```php
public getScript(): string
```

**Return Value:**

`string` - Return the HTML schema object for embedding.

***

### getMeta

Generates HTML meta tags for SEO purposes.
Call this method once on page creation to generate HTML meta tags for the current page.

```php
public getMeta(): string
```

**Return Value:**

`string` - Return the HTML meta tags.

***

### getSchema

Generates structured data for SEO purposes.
Call this method once on page load to generate structured data.

```php
public getSchema(): array&lt;string,mixed&gt;
```

**Return Value:**

`array&lt;string,mixed&gt;` - Return the structured data schema.