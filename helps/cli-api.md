# How to Implement a Command Line Interface (CLI) API client with CRUD Operations

***

## Overview

The HTTP response object allows easy capture handling of network request responses sent through the cURL client. It provides useful methods to access the response information.

***

## Introduction

This guide demonstrates how to build a simple command-line interface (CLI) that operates like an HTTP REST API within the PHP Luminova framework. This CLI will support full CRUD (Create, Read, Update, Delete) operations similar to HTTP methods: `GET`, `POST`, `PATCH`, and `DELETE`.

![Command API Example](https://luminova.ng/assets/images/cli-api.png)

For working implementation example and source code, visit [GitHub: Luminova HTTP and CLI REST API Examples](https://github.com/luminovang/luminova-rest-api-example).

***

### Requirements

Before you begin, make sure you have:

- **Luminova PHP Framework**: Install the latest version or update your current version.
- **Command-Line Tool**: Any command-line tool (e.g., Terminal, Command Prompt).
- **Database**: A configured database (e.g., MySQL) in your environment.

---

### Step 1: Define Command Routes

In Luminova, routes connect client commands to specific controller actions, just like in an HTTP API.

#### Attribute-Based Routing

Attribute-based routing allows routes to be defined directly on controller methods using PHP attributes. This approach is recommended for its clarity and maintainability compared to traditional method-based routing.

```php
// /app/Controllers/Cli/PostsCommand.php
<?php
namespace App\Controllers\Cli;

use Luminova\Base\BaseCommand;
use Luminova\Command\Utils\Color;
use Luminova\Command\Utils\Text;
use Luminova\Attributes\Route;
use App\Models\Posts;
use JsonException;

class PostsCommand extends BaseCommand
{
    /**
     * Command group associated with posts.
     */
    protected string $group = 'posts';

    /**
     * Command name for identifying this CLI tool.
     */
    protected string $name  = 'post-command';
		
    /**
     * Instance of the Posts model for CRUD operations.
     * 
     * @var Posts|null $posts
     */
    private static ?Posts $posts = null;

    /**
     * Initializes the `Posts` model instance on command creation.
     */
    protected function onCreate(): void 
    {
        self::$posts ??= new Posts();
    }

    /**
     * Displays help information for post command.
     */
    public function help(array $helps): int
    {
        return STATUS_ERROR;
    }

    #[Route('list', group: 'posts')]
    public function list(): int {}

    #[Route('get', group: 'posts')]
    public function show(): int {}

    #[Route('create', group: 'posts')]
    public function create(): int {}

    #[Route('update', group: 'posts')]
    public function update(): int {}

    #[Route('delete', group: 'posts')]
    public function delete(): int {}

    #[Route(middleware: 'after', group: 'posts')]
    public function auth(): int {}
}
```
---

#### Method-Based Routing

If you prefer method-based routing, simply remove the attributes before any method definition in the class example above. In this approach, CLI routes are defined within the `/routes/cli.php` file. Use the `group` method to organize related commands under the same group name, linking them to the appropriate controller methods.

```php
// /routes/cli.php
<?php
$router->group('posts', function (Router $router) {
    $router->before('posts', 'PostsCommand::auth');
    
    // CRUD routes for posts
    $router->command('/list', 'PostsCommand::list');         // Retrieve all posts
    $router->command('/get', 'PostsCommand::show');          // Retrieve a specific post by ID
    $router->command('/create', 'PostsCommand::create');     // Create a new post
    $router->command('/update', 'PostsCommand::update');     // Update an existing post
    $router->command('/delete', 'PostsCommand::delete');     // Delete a post by ID
});
```

> **Middleware**: 
> Middleware functions, like authentication, help secure your routes by executing specific logic before any command within the group runs. 
> For example, `before('posts', 'PostsCommand::auth')` ensures that the `auth` method is executed first, verifying the user's authorization before allowing access to any other `posts` commands. 
> This approach effectively protects all routes within the `posts` group. 

---

### Step 2: Define Post Model

The model represents the `Posts` entity and interacts with the database for CRUD operations.

```php
// app/Models/Posts.php
<?php
namespace App\Models;

use Luminova\Base\BaseModel;

class Posts extends BaseModel
{
    protected string $table = 'posts';  // Database table
    protected array $updatable = ['post_title', 'post_body'];  // Updatable fields
    protected string $primaryKey = 'pid';  // Primary key
}
```

---

### Step 3: Set Up Database Migrations and Seeders

**1. Create the Migration Class:**

Define your migration class to structure the database table.

```php
// app/Database/Migrations/PostsMigration.php
<?php
namespace App\Database\Migrations;

use Luminova\Database\Migration;
use Luminova\Database\Table;
use Luminova\Database\Schema;

class PostsMigration extends Migration
{
    /**
     * Defines the 'posts' table structure.
     */
    public function up(): void
    {
        Schema::create('posts', function (Table $table) {
            $table->prettify = true;
            $table->database = Table::SQLITE;
            
            $table->id('pid');
            $table->uuid('post_uuid')->nullable(false)->unique();
            $table->integer('user_id', 5)->nullable(false);
            $table->string('post_title')->nullable(false);
            $table->string('post_image')->nullable();
            $table->text('post_body')->nullable(false);
            $table->timestamps();

            return $table;
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('posts');
    }

    public function alter(): void {}
}
```

**2. Create the Seeder Class:**

Add seed data to populate your `posts` table.

```php
// app/Database/Seeder/PostsSeeder.php
<?php
namespace App\Database\Seeders;

use Luminova\Database\Seeder;
use Luminova\Database\Builder;

class PostsSeeder extends Seeder
{
    /**
     * Inserts sample data into 'posts' table.
     */
    public function run(Builder $builder): void
    {
        $builder->table('posts')->insert([
            [
                'post_uuid' => func()->uuid(),
                'user_id' => 101,
                'post_title' => 'Getting Started with PHP Luminova',
                'post_body' => 'Learn the basics of setting up and running your first project with PHP Luminova framework.',
            ],
            // Additional seed data
        ]);        
    }
}
```

**3. Configure Database Connection:**

Set up your database configuration in the `.env` file. Here, `SQLITE` is used as an example.

```env
# /.env

database.connection = PDO
database.pdo.engine = sqlite
database.development.sqlite.path = writeable/database/development.sqlite
database.mysql.socket = true
database.mysql.socket.path = /var/mysql/mysql.sock
```

> **Note**: 
> The `socket` configuration is needed for database access on the command line. 
> Adjust the socket path based on your environment.

**4. Run Migrations and Seeders:**

Once configured, run migrations first:

```bash
php novakit db:migrate --class=PostsMigration
```

After a successful migration, seed the database:

```bash
php novakit db:seed --class=PostsSeeder
```

---

### Step 4: Implement Controller Methods for CRUD Operations

In the `PostsCommand` controller, each method performs a specific CRUD operation, responding to CLI commands and interacting with the `Posts` model.

> **Note:** For attribute-based routing, replace only the method content as needed, keeping the route attributes unchanged.

---

### index

**Retrieve All Posts:**

Fetches a list of all posts from the database, with optional arguments `--limit` or short `-l` to specify the number of posts to retrieve, and `--offset` or short `-o` to define the starting point for the result set.

```php
public function list(): int
{
    $limit = strict($this->getAnyOption('limit', 'l', 10), 'int');
    $offset = strict($this->getAnyOption('offset', 'o', 0), 'int');
    $items = self::$posts->select(null, ['*'], (int) $limit, (int) $offset);

    if(!$items){
        $this->error('No post available');
        return STATUS_ERROR;
    }
    
    $this->writeln(Color::apply('LIST POSTS', Text::FONT_BOLD, 'red'));
    $this->newLine();
    
    foreach($items as $item){
        $this->writeln(
            Text::padEnd(Color::style('[' . $item->pid . '] ', 'green'), 40) . $item->post_title
        );
    }

    $this->newLine();
    $this->writeln('To read a post, run: "php index.php posts get --post-id=<n>"');
    return STATUS_SUCCESS;
}
```

This method retrieves and displays a paginated list of posts, allowing users to limit and offset the results using command-line arguments. If no posts are found, it will return an error message.

**CLI Command**: `GET /posts`

```bash
php index.php posts list
```

---

### read

**Retrieve a Single Post:**

Fetches and displays a specific post by its unique identifier, using the `--post-id` or short flag `-p` argument. If the post exists, it shows the title, body, and related metadata.

```php
public function read(): int
{
    $pid = strict($this->getAnyOption('post-id', 'p', 0), 'int');
    if (!$pid) {
        $this->error("Invalid post id: {$pid}.");
        return STATUS_ERROR;
    }

    $item = self::$posts->find($pid);
    
    if ($item) {
        $this->writeln(Color::apply($item->post_title, Text::FONT_BOLD, 'red'));
        $this->writeln($item->post_body);
        $this->newLine();
        $this->writeln(Color::apply('About Post', Text::FONT_BOLD|Text::FONT_UNDERLINE, 'cyan'));
        $this->writeln($this->table( ['User Id', 'Post Id', 'Created', 'Updated'],
        [
            [
                'User Id' => $item->user_id, 
                'Post Id' => $item->pid, 
                'Created' => $item->created_on, 
                'Updated' => $item->updated_on
            ]
        ]));

        return STATUS_SUCCESS;
    }

    $this->error("Post with id: {$pid} not found.");
    return STATUS_ERROR;
}
```

This method retrieves the post based on the `post-id` argument, and if the post is found, it formats and displays the post details with metadata.

**CLI Command**: `GET /posts/{id}`

```bash
php index.php posts get --post-id=2
```

---

### create

**Create a New Post:**

Adds a new post to the database with the specified user ID, title, and body content. Use the `--body` (or shorthand `-b`) argument to pass the post details in JSON format.

```php
public function create(): int 
{
    $item = $this->getAnyOption('body', 'b');
    if (!$item) {
        $this->error('Invalid post body.');
        return STATUS_ERROR;
    }

    setenv('throw.cli.exceptions', 'true');

    try {
        $item = json_decode($item, null, 512, JSON_THROW_ON_ERROR);
        $newItem = [
            'user_id' => strict($item->userId, 'int'),
            'post_title' => escape($item->title),
            'post_body' => escape($item->body),
            'post_uuid' => func()->uuid(),
        ];

        if (self::$posts->insert([$newItem])) {
            $this->success('Post was successfully created.');
            return STATUS_SUCCESS;
        }
    } catch (JsonException $e) {
        $this->error("Invalid JSON body: {$e->getMessage()}.");
    }

    $this->error('Unable to create post.');
    return STATUS_ERROR;
}
```

This method validates and decodes the JSON input, inserts the new post into the database, and provides feedback on the result.

**CLI Command**: `POST /posts`

```bash
php index.php posts create --body='{ "userId": 42, "title": "Testing Luminova CLI API client", "body": "Hello world!" }'
```

---

### update

**Update an Existing Post:**

Updates an existing post's details using the provided `post-id` and content in JSON format. Specify the `--post-id` (or shorthand `-p`) for the post's unique identifier and `--body` (or shorthand `-b`) to pass the new details in JSON.

```php
public function update(): int 
{
    $item = $this->getAnyOption('body', 'b');
    $pid = strict($this->getAnyOption('post-id', 'pid', 0), 'int');
    $uid = strict($this->getAnyOption('user-id', 'u', 0), 'int');

    if(!$pid && $uid){
        $this->error("Invalid post id: {$pid} or User id: {$uid}.");
        return STATUS_ERROR;
    }

    if(!$item){
        $this->error('Invalid post body.');
        return STATUS_ERROR;
    }

    setenv('throw.cli.exceptions', 'true');

    try{
        $item = json_decode($item, true, 512, JSON_THROW_ON_ERROR);
        $count = 0;
        $newItem = [
            'updated_on' => date('Y-m-d H:i:s')
        ];

        if(isset($item['title'])){
            $count++;
            $newItem['post_title'] = escape($item['title']);
        }

        if(isset($item['body'])){
            $count++;
            $newItem['post_body'] = escape($item['body']);
        }

        if($count > 0 && self::$posts->updatePost($pid, $uid, $newItem)){
            $this->success("Post id: {$pid} was successfully updated.");
            return STATUS_SUCCESS;
        }

        if(!$count){
            $this->writeln("Nothing to update on post id: {$pid}.", 'yellow');
            return STATUS_SUCCESS;
        }
    }catch(JsonException $e){
        $this->error("Invalid post json body {$e->getMessage()}.");
    }

    $this->error("Unable to update post id: {$pid}.");
    return STATUS_ERROR;
}
```

This method parses and validates the JSON input, checks for changes, and updates the specified fields. It outputs success or error messages based on the result.

**CLI Command**: `PATCH /posts/{id}`

```bash
php index.php posts update --post-id=6 --body='{"title": "Testing Updated Luminova CLI API client" }'
```

---

### delete

**Delete a Post:**

Deletes a post from the database based on a provided `post-id`. Use the argument `--post-id` (or `-p` for shorthand) to specify the post's unique identifier.

```php
public function delete(): int 
{
    $pid = strict($this->getAnyOption('post-id', 'p', 0), 'int');
    if(!$pid){
        $this->error("Invalid post ID: {$pid}.");
        return STATUS_ERROR;
    }

    if(self::$posts->delete($pid)){
        $this->success("Post ID: {$pid} was successfully deleted.");
        return STATUS_SUCCESS;
    }

    $this->error("Unable to delete Post ID: {$pid}.");
    return STATUS_ERROR;
}
```

This method validates the `post-id`, attempts to delete the post, and outputs a success or error message based on the result.

**CLI Command**: `DELETE /posts/{id}`

```bash
php index.php posts delete --post-id=6
```

---

### Remote Execution

**Using SSH Manually:**

To manually execute commands on a remote server over SSH, use the following commands:

```bash
ssh -i ~/.ssh/your-key username@yourserver.com
cd /path/to/your/public
php index.php posts list
```

**Using the SSH2 Module in PHP**

To execute commands remotely from your PHP script, you can install the `SSH2` PHP extension on your production server and use a remote execution class, like Luminova's `Remote` class, or implement your own solution.

**Example PHP Script**

```php
// /bin/script.php

<?php 
use Luminova\Command\Remote;

$remote = new Remote(
    username: 'your-username',
    password: 'your-password',
);

if(!$remote->connect()){
	die('Authentication Failed');
}

$command = implode(' ', array_slice($argv, 1));
$public = '/path/to/your/public';
$output = $remote->execute($command, "cd {$public} && php index.php ");

if ($output !== false) {
    echo "Command Output: " . $output;
} else {
    echo "Failed to execute command.";
}
```