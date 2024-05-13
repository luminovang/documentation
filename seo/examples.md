# SSG SEO Examples

***

## Overview

Schema &amp; Sitemap Examples, Website optimization guides using  Luminova&#039;s Structured Data and Sitemap Generation class.


***

## Introduction

The Schema and Sitemap Generator is a powerful tool within the Luminova framework designed to simplify the creation of structured data for search engine optimization (SEO) purposes. 

This class allows you to easily generate structured data schemas and sitemaps XML for your web pages, enhancing their visibility and relevance to search engines.

***

### Schema Setups

To begin using the Schema Generator, you'll first need to initialize the Schema class instance within your application's controller class. 
Here's how you can do it:

```php 
<?php 
use \Luminova\Seo\Schema;

class Application extends BaseApplication 
{
    /**
     * @var Schema $schema;
    */
    protected ?Schema $schema = null;

    public function __construct()
    {
        $this->schema = new Schema();
        parent::__construct();
                
        $this->schema->setCanonical(APP_URL, $this->getView());
    }
}
```

Alternatively, you can utilize dependency injection or the `onCreate` method:

```php 
<?php 
use \Luminova\Seo\Schema;

class Application extends BaseApplication 
{
    public function onCreate()
    {
        $this->export(Schema::class, 'schema'); 
        $this->schema->setCanonical(APP_URL, $this->getView());
	}
}
```

***

### In View

Once you've completed the setup of your application controller class, you can include it in the views where you want to generate the schema.

```php
<?php 
$this->schema->setConfig([
    "link" => request()->getUri(),
    "page_description" => "This is the page description",
    "title" => 'this page title',
    "article_keywords" => ['Luminova', 'PHP']
]);
?>
<!DOCTYPE html>
<html lang="en"> 
<head>
    <?= $this->schema->getMeta() . $this->schema->getScript();?> 
    <title><?= $this->schema->getTitle();?></title>
</head> 

<body class="docs-page">    
    <div class="container">
        <h1>My awesome website</h1>
    </div>
</body>
</html> 
```