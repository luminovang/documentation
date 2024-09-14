# Integration Examples for Sitemap and Schema Objects

***

## Overview

Schema &amp; Sitemap Examples, Website optimization guides using  Luminova's Structured Data and Sitemap Generation class.

***

## Introduction

In this guide, we'll explore both the `Sitemap` and `Schema` Generators, two powerful modules in Luminova designed to enhance your website's visibility and ranking in search engines.

The `Schema` Generator helps you create structured data for SEO purposes, while the `Sitemap` Generator allows you to produce an XML sitemap containing all the links on your website.

***

## Sitemap Example

Sitemaps are typically generated using the `NovaKit` command-line tool. Before generating your sitemap, you'll need to configure basic settings such as defining URL patterns to ignore, setting the application's start URL, 
specifying the maximum scan depth, and optionally configuring route patterns to capture the last modified date of pages.

### Sitemap Configuration

To set up your sitemap, you'll need to edit the configuration file located at `/app/Config/Sitemap.php`. For more detailed guidance, please refer to the [Sitemap Configuration Documentation](/configs/sitemap.md).

```php
// /app/Config/Sitemap.php
<?php 
final class Sitemap extends BaseConfig
{ 
    /**
     * Maximum depth for scanning URLs.
     * Set to 0 for unlimited scanning.
     */
    public int $maxScan = 0;
		
		/**
     * The start URI prefix set to blank string to scan from start url.
     */
    public string $startPrefix = '';

    /**
     * Patterns of URLs to ignore during the scan.
     */
    public array $ignoreUrls = [
        '*/blog/edit/*', 
        '*/admin/*',
        '*/foo/*'
    ];

    /**
     * Patterns to identify pages and determine their last modified timestamps.
     *
     * @var array<string,string> $viewUrlPatterns
     */
    public array $viewUrlPatterns = [
        'index' => '/',
        'about' => '/about',
        'blogs' => '/blog',
        'blog' => '/blog/([a-zA-Z0-9-]+)'
    ];
}
```

**Start URL Configuration**

To set your application's start URL, edit the environment variables file. Locate or add the `dev.app.start.url` entry, uncomment it if necessary, and set it to the correct URL for your application.

For production environments, use your live domain:

```env
dev.app.start.url = https://example.com/
```

If you're working in a development environment, the URL will depend on where your project is located:

1. **Project within a subdirectory**: If your project is in a subdirectory within the server root (e.g., `htdocs` or `www` on servers like XAMPP or WAMPP):

    ```env
    dev.app.start.url = https://localhost/example.com/public/
    ```

2. **Project in the document root**: If your project is directly in the document root:

    ```env
    dev.app.start.url = https://localhost/public/
    ```

3. **Using Luminova's built-in server**: If you're using the built-in PHP and Luminova development server running on `localhost` with port `8080`:

    ```env
    dev.app.start.url = https://localhost:8080/
    ```

4. **Custom hostname and port**: If you're running the Luminova development server with a custom hostname and port, specify it as follows:

    ```env
    dev.app.start.url = https://your-host-name:port/
    ```

***

Once your setup is complete, navigate to your application's root directory where the `novakit` file is located and run the following command:

```bash
php novakit generate:sitemap
```

***

## Schema Example

The Schema Generator allows you to define default settings that can be overridden on a per-page basis. 
To configure the default options, edit the file located at `/app/Config/Schema.php`. 
For a deeper understanding, refer to the [Schema Configuration Documentation](/configs/schema.md).

### Schema Setup

To start using the Schema implementation in your web pages, initialize the `Schema` class instance within your application's controller class.

Here's an example using the application's `__construct` method:

```php
// /app/Application.php
<?php 
namespace App;
use \Luminova\Seo\Schema;

class Application extends CoreApplication 
{
    /**
     * @var Schema $schema
     */
    protected ?Schema $schema = null;

    public function __construct()
    {
        $this->schema = new Schema();
        parent::__construct();
        
        // Set your preferred canonical version
        //$this->schema->setCanonical(APP_WWW_URL, $this->getView()); // Using www version 
        $this->schema->setCanonical(APP_URL, $this->getView());
    }
}
```

Alternatively, you can use the `onCreate` method:

```php
// /app/Application.php
<?php 
namespace App;
use \Luminova\Seo\Schema;

class Application extends CoreApplication 
{
    /**
     * @var Schema $schema
     */
    protected ?Schema $schema = null;

    public function onCreate()
    {
        $this->schema = new Schema();
        $this->schema->setCanonical(APP_URL, $this->getView());
    }
}
```

**Using Template Isolation Rendering**

When using template isolation rendering, by default access to protected or public properties within the view template file is not allowed.
In this case, you should use the `export` method to export the schema object after application initialization.

```php
// /app/Application.php
<?php 
namespace App;
use \Luminova\Seo\Schema;

class Application extends CoreApplication 
{
    /**
     * @var Schema $schema
     */
    protected ?Schema $schema = null;

    public function onCreate()
    {
        // Optionally you can pass pass the class string.
        //$this->export(Schema::class, 'schema'); 

        $this->schema = new Schema();
        $this->export(Schema::class, 'schema'); 
        $this->schema->setCanonical(APP_URL, $this->getView());
    }
}
```

***

### Using Schema in Views

After setting up the `Schema` in your application controller, you can include it in your views where you need the schema object.

**Overriding Default Configuration**

```php
// /app/Controllers/PageController.php
<?php 
namespace App\Controllers;
use \Luminova\Base\BaseViewController;
use \App\Models\BlogModel;

class PageController extends BaseViewController
{
    public function blog(string $id, BlogModel $model): int 
    {
        $this->app->schema->setConfig([
            'isArticle' => true,
            'link' => request()->getUri(),
            'headline' => 'This is the page description',
            'title' => 'This page title',
            'article_keywords' => ['Luminova', 'PHP'],
            'word_count' => 28,
            'author' => 'Ujah Peter',
            // more options...
        ]);

        return $this->view('blog', [
            'content' => $model->find($id)
        ]);
    }
}
```

***

### Finalize Schema Setup

Now within your `template` view file in `/resources/views/`, call the schema methods `getMeta`, `getScript` and `getTitle` to finalize the setup.

```html
<!-- /resources/views/blog.php -->
<!DOCTYPE html>
<html lang="en"> 
<head>
    <?= $this->schema->getMeta() . $this->schema->getScript();?> 
    <title><?= $this->schema->getTitle();?></title>
</head> 

<body class="blog-page">    
    <div class="container">
        <h1>Your Blog Title</h1>
		<p>Your Blog Body</p>
    </div>
</body>
</html> 
```