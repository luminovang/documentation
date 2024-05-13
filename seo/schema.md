## Schema Generator

***

## Overview

The Schema Generator class allows you to generate schema objects for your web pages. It provides methods to easily create schema on every page of your web application.

***

The SEO Schema Generator class is a powerful tool designed to simplify the creation of structured data objects for search engine optimization (SEO) purposes across your web pages. It provides methods to easily configure and create schema objects on every page of your web application to enhance the visibility and indexing of your website's content on search engine results pages.

***

To get started, see [Schema Example](/seo/examples.md).

- **Class namespace**: `\Luminova\Seo\Schema`

***

### constructor

Initializes the class constructor and loads configurations.

```php
new Schema();
```

***

## Methods

### getInstance

Get shared instance singleton class.

```php
public static getInstance(): static
```


**Return Value:**

`static` *$instance* - Class instance.


***


### setLink

Sets the link URL for current web page.

```php
public setLink(string $link): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$link` | **string** | The link URL. |

**Return Value:**

`\Luminova\Seo\Schema` - This Class instance.


***


### setConfig

Sets the dynamic configuration on pages where you want to include schema object generation..

```php
public setConfig(array $config): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **array<string, mixed>** | The extended configuration. |


#### Configuration options

 An array of configuration options for schema generation.

- `string` - link: The URL of the current page.
- `string` - canonical: The canonical URL of the current page.
- `array` - breadcrumbs: An array of breadcrumb items, each containing:
    - `string` - link: The URL of the breadcrumb item.
    - `string` - name: The name of the breadcrumb item.
    - `string` - description: The description of the breadcrumb item.
- `string` - image_assets: The base URL for image assets.
- `string|array` - company_brands: The organization brands.
- `string` - company_name: The name of the organization.
- `string` - page_description: The description of the current page.
- `string` - company_description: The description of the organization.
- `string` - title: The title of the current page.
- `string` - headline: The headline of the current page.
- `string` - image_name: The name of the current page image (accessible after prepending image_assets).
- `int` - image_width: The width of the current page image.
- `int` - image_height: The height of the current page image.
- `string` - image_type: The type of the current page image.
- `string` - published_date: The published date of the current page article.
- `string` - modified_date: The modified date of the current page article.
- `array` - keywords: An array of keywords for the current page.
- `bool` - isArticle: Whether the current page is an article (true/false).
- `string` - article_type: The type of the article.
- `array` - article_keywords: An array of keywords for the article.
- `array` - article_section: An array of sections for the article.
- `int` - word_count: The word count of the article.
- `int` - total_comments: The total number of comments on the article.
- `string` - author: The author name.
- `bool` - isProduct: Whether the current page is a product (true/false).
- `string` - product_type: The type of the product.
- `string` - product_image_link: The URL of the product image.
- `string` - product_category: The category of the product.
- `string` - availability: The availability of the product.
- `string` - currency: The currency of the product price.
- `double` - price: The price of the product.
- `string` - brand: The brand of the product.
- `string` - twitter_name: The Twitter handle.
- `string` - search_query: The search query URL format.
- `string` - search_input: The name of the search input field.

> Initial non-dynamic configurations can be set in your application's controller config directory, located at `/app/Controllers/Config/Schema.php`.


***


### setTitle

Sets the current page title for SEO purposes.

```php
public setTitle(string $title): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$title` | **string** | The page title. |


***


### setCanonical

Sets the current page canonical URL for SEO purposes.

```php
public setCanonical(string $link, string $view = ''): void
```

> This can be done once in your application controller class.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$link` | **string** | The canonical URL. |
| `$view` | **string** | The view URI segments. |


***


### getTitle

Gets the current page title.

```php
public getTitle(): string
```


**Return Value:** 

`string` -  The page title.


***

### getLink

Gets the current page link.

```php
public getLink(): string
```

**Return Value:** 

`string` -  The page link url.


***


### getGraph

Converts the schema data to JSON string format.

```php
public getGraph(): string
```

**Return Value:**

`string` - The JSON representation of the schema data.


***

### getScript

Get the HTML script tag containing the schema for embedding on a web page.

```php
public getScript(): string
```

**Return Value:**

`string` - The HTML code for embedding the schema.


***

### getMeta

Generates HTML meta tags for SEO purposes.

```php
public getMeta(): string
```

**Return Value:**

`string` - The HTML meta tags.


***

### getSchema

Generate structured data schema for SEO purposes.

```php
public getSchema(): array<string,mixed>
```


**Return Value:**

`array<string, mixed>`The structured data schema.