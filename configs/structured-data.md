# Schema and Entity Properties Configuration

***

## Overview

Learn how to configure default SEO settings for your website using Luminova's Schema configuration. Define meta tags, structured data, and social media information to optimize search engine visibility

***

## Introduction

The **Schema configuration** defines default SEO settings for your website’s pages. These defaults are applied globally but can be overridden on individual pages as needed. Any setting not explicitly overridden will continue to use the default value.

To configure your default SEO settings, edit the file:

```
/app/Config/Schema.php
```

For programmatic usage on dynamic pages, refer to the [Schema Data Structure  Generator](/website-optimizations/seo-structured-data.md) documentation.

---

**File Details:**

* **File path:** `/app/Config/Schema.php`
* **Return type:** The configuration file **must return an array** containing all schema settings.

---

## Example

### Website Default Configuration

These defaults cover the most common structured data settings. You can extend or override them for specific pages if needed. For detailed property references, see [Full Array Configuration Properties](/configs/structured-data#lmv-docs-full-array-configuration-properties).

```php
// /app/Config/Schema.php

use Luminova\Funcs\{href, asset};

return [
    'id'          => href(null, true),
    'name'        => 'Example Inc.',
    'url'         => href('/', true),
    'language'    => 'en-US',
    'locale'      => 'en',
    'title'       => 'Example Inc. – Business Solutions',
    'description' => 'We provide world-class consulting and technology services.',
    'keywords'    => ['consulting', 'technology', 'business'],

    'image' => [
        'url'     => asset('images/banner.png', true),
        'width'   => 1200,
        'height'  => 630,
        'type'    => 'image/png',
        'caption' => 'Main company banner',
    ],

    'organization' => [
        'name'        => 'Example Inc.',
        'email'       => 'contact@example.com',
        'description' => 'Example Inc. delivers innovative business solutions worldwide.',
        'brands'      => ['BrandOne', 'BrandTwo'],
        'links'       => [
            'https://facebook.com/example',
            'https://twitter.com/example'
        ],
        'address' => [
            'locality' => 'Lagos',
            'country'  => 'NG',
            'postcode' => '100001',
            'street'   => '123 Example Street',
        ],
    ],

    'search' => [
        'query' => '/search?q={search_term_string}',
        'input' => 'search_term_string',
    ],

    'facebook' => [
        'type'  => 'website',
        'pages' => ['1234567890'],
    ],

    'twitter' => [
        'card'    => 'summary_large_image',
        'site'    => '@ExampleInc',
        'creator' => '@JohnDoe'
    ],
];
```

> **Tip:** 
> Keep your default settings meaningful and consistent. They act as a fallback whenever a page-specific override is not provided.

---

## Structured Data Configuration Properties

The following properties define the default structured data for your Luminova application. These settings can be overridden per page if required.

### General Website Details

**`id`**: `string`

Global unique identifier for the website (usually the homepage URL).

**Example:**

```php
'id' => 'https://example.com',
```

**`name`**: `string`

Website or business name.

**Example:**

```php
'name' => 'Example Inc.',
```

**`url`**: `string`

Webpage full request URL.

**Example:**

```php
'url' => 'https://example.com/about',
```

**`language`**: `string`

Primary language in IETF format (e.g., `en-US`).

**Example:**

```php
'language' => 'en-US',
```

**`locale`**: `string`

Short locale code (e.g., `en`, `fr`).

**Example:**

```php
'locale' => 'en',
```

**`title`**: `string`

Default webpage title (also used if no page-specific title is set).

**Example:**

```php
'title' => 'Welcome to Example',
```

**`description`**: `string`

Default webpage description.

**Example:**

```php
'description' => 'We provide excellent services.',
```

---

### General Page Image Settings

**`image`**: `array`

Default webpages image details for OG tags, Twitter cards, and schema.
  
**Example:**

```php
'image' => [
    'url'     => 'https://example.com/images/banner.png',
    'width'   => 1200,
    'height'  => 630,
    'type'    => 'image/png',
    'caption' => 'Company main banner',
    'assets'  => 'https://example.com/images/'
],
```
---

> **Note:** The **`assets`** property is used when the image URL is just a filename (e.g., `person.png`).
> Luminova will automatically prepend the `assets` base URL to generate the full image URL, assuming all images are stored in a central location.
> This default assets path is also applied to all image URLs across the schema, including:
>
> * `organization.logo.url`
> * `article.author.image.url`
> * `product.image.url`
> * `facebook.image.url`
> * `twitter.image.url`
  

**Example:**

```php
'image' => [
    'url'    => 'person.png',               // just the filename
    'assets' => asset('images', true),      // base URL for images
    'width'  => 200,
    'height' => 200,
    'type'   => 'image/png',
    'caption'=> 'Profile Picture'
],

// Full URL generated: https://example.com/assets/images/person.png
```

---

### Webpage Meta and Navigation

**`keywords`**: `array`

A global list of keywords for SEO.

**Example:**

```php
'keywords' => ['business', 'consulting', 'services'],
```

**`canonicals`**: `array`

A list of canonical URLs (primary or alias).

**Example:**

```php
'canonicals' => [
    'https://example.com',
    'https://www.example.com'
],
```

**`breadcrumbs`**: `array`

Structured navigation trail for schema markup.

**Example:**

```php
'breadcrumbs' => [
    [
        'url'         => 'https://example.com',
        'name'        => 'Home',
        'description' => 'Homepage'
    ],
    [
        'url'         => 'https://example.com/blog',
        'name'        => 'Blog',
        'description' => 'Our latest posts'
    ]
],
```

---

### Organization Details

**`organization`**: `array`

Information about your organization or company.

**Example:**

```php
'organization' => [
    'name'        => 'Example Inc.',
    'email'       => 'info@example.com',
    'duns'        => '123456789',
    'description' => 'Leading provider of consulting services.',
    'brands'      => ['BrandOne', 'BrandTwo'],
    'links'       => ['https://facebook.com/example', 'https://twitter.com/example'],
    'logo' => [
        'url'     => 'https://example.com/images/logo.png',
        'width'   => 300,
        'height'  => 100,
        'caption' => 'Example Logo'
    ],
    'address' => [
        'locality' => 'Lagos',
        'country'  => 'NG',
        'postcode' => '100001',
        'street'   => '123 Example Street',
    ],
],
```

---

### Article Data (when `isArticle` is true)

**`isArticle`**: `bool`

Enable article schema generation.

**Example:**

```php
'isArticle' => true,
```

**`article`**: `array`

Article-specific details.

**Example:**

```php
'article' => [
    'type'          => 'BlogPosting',
    'headline'      => 'How to Grow Your Business',
    'citation'      => 'https://source.com/reference',
    'license'       => 'https://creativecommons.org/licenses/by/4.0/',
    'section'       => ['Business', 'Growth'],
    'word_count'    => 1200,
    'comment_count' => 15,
    'reading_time'  => 8,
    'publisher'     => 'Example Inc.',
    'published_date'=> '2025-08-01',
    'modified_date' => '2025-08-20',
    'keywords'      => ['growth', 'business'],
    'author' => [
        'id'      => 'john-doe',
        'name'    => 'John Doe',
        'caption' => 'Business strategist',
        'url'     => 'https://example.com/authors/john-doe',
        'image'   => [
            'url' => 'https://example.com/images/john.png'
        ]
    ]
],
```

---

### Product Data (when `isProduct` is true)

**`isProduct`**: `bool`

Enable product schema generation.

**Example:**

```php
'isProduct' => true,
```

**`product`**: `array`

Product-specific details.

**Example:**

```php
'product' => [
    'type'        => 'Product',
    'name'        => 'Smartphone X',
    'description' => 'Latest generation smartphone with AI features.',
    'category'    => 'Electronics',
    'url'         => 'https://example.com/products/smartphone-x',
    'keywords'    => ['smartphone', 'AI phone'],
    'image' => [
        'url' => 'https://example.com/images/smartphone.png'
    ],
    'brand' => [
        'name' => 'Example Brand'
    ],
    'offers' => [
        'availability' => 'InStock',
        'condition'    => 'NewCondition',
        'price'        => [
            'value'  => '499.00',
            'currency'     => 'USD',
            'validUntil'     => '2025-12-31',
        ],
    ],
],
```
---

### Site Search Settings

**`search`**: `array`

Defines your website search URL pattern.

**Example:**

```php
'search' => [
    'query' => '/search?q={search_term_string}',
    'input' => 'search_term_string'
],
```

---

### Facebook Open Graph

**`facebook`**: `array`

Facebook metadata for Open Graph.

**Example:**

```php
'facebook' => [
    'type'        => 'website',
    'title'       => 'Example Inc.',
    'description' => 'We provide excellent services.',
    'url'         => 'https://example.com',
    'pages'       => ['1234567890', '1234567891'], // Facebook page IDs
    'image' => [
        'url'    => 'https://example.com/images/og.png',
        'width'  => 1200,
        'height' => 630
    ]
],
```

---

### Twitter Card Settings

**`twitter`**: `array`

Twitter metadata for summary or large image cards.

**Example:**

```php
'twitter' => [
    'card'        => 'summary_large_image',
    'site'        => '@ExampleInc',
    'creator'     => '@JohnDoe',
    'title'       => 'Example Inc.',
    'description' => 'We provide excellent services.',
    'image' => [
        'url' => 'https://example.com/images/twitter.png',
        'alt' => 'Example banner'
    ]
],
```
---

## Custom Entities and Properties

Custom entities let you add additional properties using valid **Schema.org** data types and values.
These are automatically merged into your generated JSON-LD, so you don’t need to manually edit templates or script tags.

### Entities in Default Configurations

The following schema objects support custom entities by adding an `entities` array:

* **organization**
* **article** (including nested `author`)
* **product**
* **all image objects** (`*=>image`)

**Example:**

```php
[
    'organization' => [
        '@type' => 'Organization',
        'name'  => 'Acme Corp',
        'url'   => 'https://acme.example',
        'entities' => [
            'duns' => '15-048-3782',            // custom property
            'founder' => 'John Smith',          // additional Schema.org field
            'slogan'  => 'Innovation First!'    // non-standard but valid property
        ]
    ]
]
```

---

### Global Entities

**`global.entities`**: `array`
Additional entities and properties appended to the root JSON-LD object.
Use this to define new `DataType` sections or inject identifiers across all schema types.

**Example:**

```php
'global.entities' => [
    'mainEntityOfPage' => 'https://acme.example',
    'copyrightYear'    => '2025'
]
```

---

### Website Entities

**`website.entities`**: `array`
Additional properties for the `WebSite` schema object.
Useful for adding identifiers, custom URLs, or alternate names.

**Example:**

```php
'website.entities' => [
    'alternateName' => 'Acme Global',
    'sameAs'        => [
        'https://twitter.com/acme',
        'https://linkedin.com/company/acme'
    ]
]
```

---

### Webpage Entities

**`webpage.entities`**: `array`

Additional properties for the `WebPage` schema object.
Good for highlighting special content, linking related pages, or attaching custom schema data.

**Example:**

```php
'webpage.entities' => [
    'significantLink' => [
        'https://acme.example/partners',
        'https://acme.example/about'
    ],
    'primaryImageOfPage' => [
        '@type' => 'ImageObject',
        'url'   => 'https://acme.example/images/cover.jpg',
        'width' => 1200,
        'height'=> 630
    ]
]
```

---

### HTML Head Tags (`@meta` and `@link`)

**`tags`**: `array`

Define a custom **HTML `<meta>` or `<link>` tags** for the `<head>` section of your pages. This is especially useful for other metadata that cannot be included in JSON-LD.

**Example:**

```php
'tags' => [
    [
        '@type' => 'meta',
        'attributes' => [
            'distribution' => 'Global', // Example: meta tag with custom attribute
        ]
    ],
    [
        '@type' => 'link',
        'attributes' => [
            'alternate' => 'https://example.com/zh', // Alternate page link
            'hreflang' => 'zh'                        // Language code
        ]
    ]
],
```

> **Notes:**
>
> * Each item in `tags` must have an **`@type`**: `'meta'` or `'link'`.
> * The **`attributes`** array defines the HTML attributes for the tag. For example:
>
> These tags are rendered automatically in templates when calling `Schema::getHeadTags()`.
>   * `<meta distribution="Global">`
>   * `<link alternate="https://example.com/zh" hreflang="zh">`

---

### Full Array Configuration Properties

The schema configuration defines metadata for your website or application, helping search engines better understand and index your pages.

```php
[
    // -------------------------------------------------
    // General Website Details
    // -------------------------------------------------
    'id'          => href(null, true),        // Unique ID (usually homepage URL)
    'name'        => APP_NAME,                // Website or business name
    'url'         => href('/', true),         // Root website URL
    'language'    => 'en-US',                 // Primary language code (IETF)
    'locale'      => 'en',                    // Short locale (e.g. 'en', 'fr')
    'title'       => 'Website Main Title',    // Default page title
    'description' => 'Webpage Description.',  // Default meta description
    'keywords'    => [],                      // Global keywords

    // -------------------------------------------------
    // General Page Image Settings
    // -------------------------------------------------
    'image' => [
        'url'     => null,                     // Default image URL
        'width'   => 0,
        'height'  => 0,
        'type'    => 'image/png',              // MIME type
        'caption' => 'Image caption',
        'assets'  => asset('images', true),    // Base URL for images
    ],

    // -------------------------------------------------
    // Webpage Meta and Navigation
    // -------------------------------------------------
    'canonicals'  => [],                      // Preferred or alias URLs
    'breadcrumbs' => [                        // Navigation trail
        [
            'url'         => null,
            'name'        => null,
            'description' => null,
        ],
    ],

    // -------------------------------------------------
    // Main Website or Webpage Custom Structured Data Entities
    // -------------------------------------------------
    'website.entities' => [       // Website-specific custom entities
        // Example: Alternative name for the website
        'alternateName' => 'Example Company',
        // More field ...
    ],

    'webpage.entities' => [       // Webpage-specific custom entities
        // Example: Highlighted important link
        'significantLink' => 'https://example.com/featured-resource',
        // More field ...
    ],

    // -------------------------------------------------
    // Add a Global Entities and Properties to Structured Data
    // -------------------------------------------------
    'global.entities' => [],

    // -------------------------------------------------
    // Organization Details
    // -------------------------------------------------
    'organization' => [
        'name'        => '',
        'email'       => '',
        'duns'        => '',
        'description' => '',
        'brands'      => [],
        'links'       => [],
        'logo' => [
            'url'     => null, // If null, fallback to default image
            'width'   => 0,
            'height'  => 0,
            'caption' => null  // Defaults to page title
        ],
        'address' => [
            'locality' => '',
            'country'  => '',
            'postcode' => '',
            'street'   => '',
        ],
        'entities'       => [] // Organization-specific custom entities
    ],

    // -------------------------------------------------
    // Article Data (Enabled if 'isArticle' => true)
    // -------------------------------------------------
    'isArticle' => false,
    'article' => [
        'type'          => 'Article',     // e.g. Article, TechArticle, BlogPosting
        'description'   => null,          // Defaults to global or organization description
        'headline'      => null,          // Defaults to article description
        'citation'      => '',
        'license'       => '',
        'section'       => [],            // Article topics or categories
        'word_count'    => 1500,
        'comment_count' => 0,
        'reading_time'  => null,          // Estimated minutes
        'publisher'     => null,          // Defaults to organization
        'published_date'=> null,
        'modified_date' => null,
        'keywords'      => [],            // Article-specific keywords (merged with global)
        'image'         => [],
        'thumbnail'     => [],             // Image URL
        'author' => [
            'id'      => 'username',
            'name'    => 'Author Name',
            'caption' => 'Profile caption',
            'url'     => 'Profile link',
            'image'   => [
                'url' => 'Profile image link'
            ],
            'entities'      => [] // Article Author-specific custom entities
        ],
        'entities'      => [] // Article-specific custom entities
    ],

    // -------------------------------------------------
    // Product Data (Enabled if 'isProduct' => true)
    // -------------------------------------------------
    'isProduct' => false,
    'product' => [
        'type'        => 'Product',       // e.g. Product, SoftwareApplication
        'name'        => null,            // Defaults to page title
        'description' => null,            // Defaults to global or organization description
        'category'    => 'Electronics',
        'url'         => null,            // Defaults to page URL
        'keywords'    => [],              // Product-specific keywords (merged with global)
        'image' => [
            'Product image link 1',
            'Product image link 2',
            // ....
        ],
        'brand' => [
            'name' => 'Brand Name',
        ],
        'offers' => [
            'availability' => 'InStock',
            'condition'    => 'NewCondition',
            'price'        => [
                'value'  => '0.00',
                'currency'     => 'NGN',
                'validUntil'     => '2025-12-31',
            ],
        ],

        'entities'  => [                  // Product-specific custom entities
            'seller' => [ // Example
                '@type' => 'Organization',
                'name' =>  'Example Store'
            ]
        ]
    ],

    // -------------------------------------------------
    // Site Search Settings
    // -------------------------------------------------
    'search' => [
        'query' => '/search?q={search_term_string}', // URL template
        'input' => 'search_term_string',             // Input placeholder
    ],

    // -------------------------------------------------
    // Facebook Open Graph
    // -------------------------------------------------
    'facebook' => [
        'type'        => 'website',
        'title'       => null,  // Defaults to page title
        'description' => null,  // Defaults to page description
        'url'         => null,  // Defaults to page URL
        'pages'       => [],    // Page IDs
        'image' => [
            'url'    => null,   // Defaults to global image
            'width'  => 0,
            'height' => 0,
        ],
    ],

    // -------------------------------------------------
    // Twitter Card Settings
    // -------------------------------------------------
    'twitter' => [
        'card'        => 'summary',  // Or summary_large_image
        'site'        => 'YourWebsiteHandle',
        'creator'     => 'AuthorHandle',
        'title'       => null,       // Defaults to page title
        'description' => null,       // Defaults to page description
        'image' => [
            'url' => null,           // Defaults to global image
            'alt' => null,           // Alternative text
        ],
    ],

    // -------------------------------------------------
    // Custom HTML meta (@meta) or link (@link) tags
    // -------------------------------------------------
    'tags' => [
        [
            '@type' => 'meta',
            'attributes' => [
                'distribution' => 'Global'
            ],
        ],
        [
            '@type' => 'link',
            'attributes' => [
                'alternate' => 'https://example.com/zh',
                'hreflang' => 'zh'
            ],
        ]
    ]
];
```