# Examples of Command Line Tool Implementations

***

## Overview

This example demonstrates how a command line tool can be implemented, using the Base Command class and command routing.

***

## Introduction

### Creating a Simple Blog Application in CLI with Luminova BaseCommand

This example demonstrates how to build a simple blog application using Luminova's `BaseCommand` within a CLI environment.

#### Setting Up Command Routing

Define the command routing for handling blog-related operations:

```php
// /routes/cli.php

<?php
use \Luminova\Routing\Router;

$router->group('blogs', function (Router $router) {
    $router->command("list", 'BlogCommand::list');
    $router->command('/read/id/(:value)', 'BlogCommand::read');
});
```

#### Creating the Controller Class for Blog Operations

The `BlogCommand` controller class handles the various blog operations:

```php
// /app/Controllers/BlogCommand.php

<?php
namespace App\Controllers;

use \Luminova\Base\BaseCommand;
use \Luminova\Command\Utils\Text;

class BlogCommand extends BaseCommand
{
    protected string $group = 'blogs';
    protected string $name  = 'blog-command';
    protected array $usages = [
        'php index.php blogs list' => 'Get a list of blogs',
        'php index.php blogs read id "foo"' => 'Read a blog by its ID',
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
        $this->write($this->color('OUR BLOGS', 'red', null, Text::ANSI_BOLD));
        $this->newLine();
        
        // Display each blog
        foreach ($blogs as $row) {
            $this->writeln($this->color('[' . $row->id . ']', 'green') . ' ' . Text::padStart($row->title, 10));
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
        // Retrieve the blog from the database
        $blog = $this->builder->table('blogs')
                              ->where('id', '=', $id)
                              ->find();
        
        // Display blog details
        $this->write($this->color('[' . $blog->title . ']', 'red', null, Text::ANSI_BOLD));
        $this->newLine();
        $this->writeln($blog->description);

        return STATUS_SUCCESS;
    }
}
```

#### Execution Example

To interact with your blog application via CLI, use the following commands:

- **List Blogs**  
  Retrieve a list of all blogs:

  ```bash
  php index.php blogs list
  ```

- **Read a Specific Blog**  
  Retrieve and display the details of a blog by its ID:

  ```bash
  php index.php blogs read id 123456
  ```

> **Note:** Before running any controller-related commands, ensure you have navigated to the `/public/` directory by using the `cd` command.