## CLI Example

***

## Overview

Novakit command line too examples

***

This example shows how you can create a simple blog application in CLI using Luminova BaseCommand.

Setting up the command routing for controller handling in `/routes/cli.php`.
```php
<?php
use \Luminova\Routing\Router;
$router->group('blogs', function(Router $router) 
{
    $router->command("list", 'Command::list');
    $router->command('/read/id/(:value)', 'Command::read');
});
```

Creating the controller class for manging blog operations.

```php
<?php 

namespace App\Controllers;

use \Luminova\Base\BaseCommand;
use \Luminova\Command\TextUtils;

class BlogCommand extends BaseCommand 
{
    protected string $group = 'blogs';
    protected string $name  = 'blog-command';
    protected array $usages  = [
        'php index.php blogs list' => 'Get a list of blogs',
        'php index.php blogs read id "foo"' => 'Read a blog by its ID'
    ];
    protected string $description = 'CLI tool for managing blogs on our website.';
    protected array $options = [];

    /**
     * Display help information for the blog command.
     * 
     * @param array $helps Help information for the command.
     * @return int Status code.
     */
    public function help(array $helps): int
    {
        return STATUS_ERROR;
    }

    /**
     * List all available blogs.
     * 
     * @return int Status code.
     */
    public function list(): int
    {  
        // Retrieve blogs from the database
        $blogs = $this->builder->table('blogs')->select(['id', 'title']);
        
        // Display header
        $this->write($this->color('OUR BLOG', 'red', null, TextUtils::ANSI_BOLD));
        $this->newLine();
        
        // Display each blog
        foreach($blogs as $row){
            $this->writeln($this->color('[' . $row->id . ']', 'green'). TextUtils::padStart($row->title, 10));
        }

        return STATUS_SUCCESS;
    }

    /**
     * Display details of a specific blog.
     * 
     * @param string $id Blog ID.
     * @return int Status code.
     */
    public function read(string $id): int
    {  
        // Retrieve blog from the database
        $tbl = $this->builder->table('blogs');
        $tbl->where('id', '=', $id);
        $blog = $tbl->find();
        
        // Display blog details
        $this->write($this->color('[' . $blog->title . ']', 'red', null, TextUtils::ANSI_BOLD));
        $this->newLine();
        $this->writeln($blog->description);

        return STATUS_SUCCESS;
    }
}
```

### List Blogs

```php
php index.php blogs list
```

### Get Blogs

```php
php index.php blogs get id 123456
```

> To execute your controller command, you must `cd` to `/public/` before running any controller related commands.