# Default Search Engine Schema Object Configurations

***

## Overview

This configuration defines the default SEO schema settings to apply on your web pages, to ensure consistent and optimized visibility across search engines, with options to customize per page.

***

## Introduction

The schema configuration settings allow you to define default SEO options for your web pages. These defaults can be overridden on individual pages, but the defaults will continue to be applied where not explicitly changed. To configure your SEO settings, modify the file located at `/app/Config/Storage.php`.

For more details, see the [Schema Generator documentation](/seo/schema.md).

***

* File path: `/app/Config/Schema.php`

***

### Schema Array Keys

This array contains key-value pairs used to define various attributes of your web application for search engine optimization (SEO) purposes.

**The basic default settings**

```php
<?php
return [
    'name'                  => APP_NAME,
    'short_name'            => 'Short Name',
    'company_brands'        => [
        'Technology',
        'Luminova'
    ],
    'company_name'          => 'Company Name',
    'company_duns'          => null,
    'company_email'         => null,
    'company_description'   => 'Company Description',
    'title'                 => 'Website Main Title',
    'page_description'      => 'Webpage Main Description.',
    'site_id'               => href(null, true),
    'start_url'             => href('/', true),
    'assets'                => asset('/', true),
    'image_assets'          => asset('images', true),
    'host_name'             => APP_HOSTNAME,
    'logo_image_name'       => 'app-square-logo.png',
    'logo_image_width'      => 512,
    'logo_image_height'     => 106,
    'image_name'            => 'app-square-icon.png',
    'image_width'           => 512,
    'image_height'          => 512,
    'image_type'            => 'image/png',
    'published_date'        => '2022-03-04T06:45:20+00:00',
    'modified_date'         => '2023-07-15T03:47:59+00:00',
    'category'              => 'Website Category',
    'article_keywords'      => [],
    'language'              => 'en-US',
    'locale'                => locale(),
    'article_section'       => [],
    'twitter_name'          => null,
    'social_media'          => [],
    'breadcrumbs'           => [],
    'keywords'              => [],
];
```

---

**The Full Schema Array Keys Descriptions**

- **`name`** **(string)**: The name of the application.  
  - Example: `APP_NAME`

- **`short_name`** **(string)**: A shortened version of the application name.  
  - Example: `'Luminova'`

- **`company_brands`** **(array<string>)**: An array of brand names associated with the company.  
  - Example: `['Technology', 'Luminova']`

- **`company_name`** **(string)**: The official name of the company.  
  - Example: `'Nanoblock Technology Ltd'`

- **`company_duns`** **(string|null)**: The DUNS number of the company, if applicable.  
  - Example: `null`

- **`company_email`** **(string|null)**: The official contact email of the company.  
  - Example: `null`

- **`company_description`** **(string)**: A description of the company, emphasizing its offerings.  
  - Example: `'Luminova is a PHP web application framework built for speed.'`

- **`title`** **(string)**: The title of the web page, often used for SEO.  
  - Example: `'Luminova - PHP Framework For Developers'`

- **`page_description`** **(string)**: A brief description of the web page's content, used for SEO.  
  - Example: `'Luminova is a PHP web application framework built for speed, with easy-to-use syntax, allowing you to create web applications with ease.'`

- **`site_id`** **(string)**: The unique identifier of the site, usually a URL.  
  - Example: `https://example.com` or `href(null, true)`

- **`start_url`** **(string)**: The URL where the web application starts.  
  - Example: `https://example.com/` or `href('/', true)`

- **`assets`** **(string)**: The base URL for the application's assets.  
  - Example: `https://example.com/assets/` or `asset('/', true)`

- **`image_assets`** **(string)**: The base URL for the application's image assets.  
  - Example: `https://example.com/assets/images/` or `asset('images', true)`

- **`host_name`** **(string)**: The hostname of the application server.  
  - Example: `example.com` or `APP_HOSTNAME`

- **`logo_image_name`** **(string)**: The filename of the application's logo image.  
  - Example: `'app-square-logo.png'`

- **`logo_image_width`** **(int)**: The width of the logo image in pixels.  
  - Example: `512`

- **`logo_image_height`** **(int)**: The height of the logo image in pixels.  
  - Example: `106`

- **`image_name`** **(string)**: The filename of the application's primary icon image.  
  - Example: `'app-square-icon.png'`

- **`image_width`** **(int)**: The width of the primary icon image in pixels.  
  - Example: `512`

- **`image_height`** **(int)**: The height of the primary icon image in pixels.  
  - Example: `512`

- **`image_type`** **(string)**: The MIME type of the primary icon image.  
  - Example: `'image/png'`

- **`published_date`** **(string)**: The publication date of the web page in ISO 8601 format.  
  - Example: `'2022-03-04T06:45:20+00:00'`

- **`modified_date`** **(string)**: The last modified date of the web page in ISO 8601 format.  
  - Example: `'2023-07-15T03:47:59+00:00'`

- **`category`** **(string)**: The category to which the web page belongs.  
  - Example: `'Documentation'`

- **`article_keywords`** **(array<string>)**: An array of keywords relevant to the article or page.  
  - Example: `['PHP', 'JAVA', 'SWIFT', 'JavaScript']`

- **`language`** **(string)**: The language code of the web page content.  
  - Example: `'en-US'`

- **`locale`** **(string)**: The locale of the web page, often used for internationalization.  
  - Example: `en` or `locale()`

- **`article_section`** **(array<string>)**: Sections of the article or page, useful for SEO.  
  - Example: `[]`

- **`twitter_name`** **(string|null)**: The Twitter handle associated with the application or company.  
  - Example: `null`

- **`social_media`** **(array<string>)**: An array of social media URLs related to the application or company.  
  - Example:  
    ```php
    [
        'https://facebook.com/luminovang',
        'https://twitter.com/luminovang',
        'https://linkedin.com/company/luminovang',
        'https://github.com/luminovang/luminova',
        'https://github.com/luminovang/framework',
    ]
    ```

- **`breadcrumbs`** **(array<string>)**: A breadcrumb trail for navigation, often used in structured data.  
  - Example: `[]`

- **`keywords`** **(array<string>)**: An array of SEO keywords associated with the application or web page.  
  - Example:  
    ```php
    [
        'Luminova', 'PHP', 'PHP Framework', 'Luminova Framework', 'Luminova Ng',
        'PHP Composer','PHP MVC','Luminova MVC','NovaKit','CLI Tools', 'Luminova CLI',
        'Routing', 'PHP Class', 'Nanoblock'
    ]
    ```