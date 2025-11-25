#  HTTP RESTful API with CRUD Operations

***

## Overview

This guide outlines the basic steps to implement a REST API with full CRUD operations using the Luminova Framework.

***

## Introduction

This guide walks you through building a RESTful API using the Luminova PHP framework. It covers full CRUD (Create, Read, Update, Delete) functionality, utilizing HTTP methods such as `GET`, `POST`, `PUT`, and `DELETE`.  

For a working implementation and source code, check out the [Luminova HTTP and CLI REST API Examples on GitHub](https://github.com/luminovang/luminova-rest-api-example).  

***

### Requirements

Before starting, ensure you have:

- **Luminova PHP Framework** installed.
- A working database (e.g., MySQL).

***

### Step 1: Define API Routes

Routes map client requests to specific controller actions. In Luminova, routes can be defined using **method-based** or **attribute-based** routing.

#### Attribute-Based Routing

With attribute-based routing, routes are defined directly on controller methods using PHP attributes.

```php
// /app/Controllers/Http/RestController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Attributes\Route;
use Luminova\Attributes\Prefix;
use App\Errors\Controllers\ErrorController;

#[Prefix(pattern: '/api/v1/(:root)', onError: [ErrorController::class, 'onRestError'])]
class RestController extends Controller
{
  #[Route('/api/v1/posts/(:root)', methods: ['ANY'], middleware: Route::HTTP_BEFORE_MIDDLEWARE)]
  public function auth(): int {}

  #[Route('/api/v1/', methods: ['ANY'])]
  public function index(): int {}

  #[Route('/api/v1/posts', methods: ['GET'])]
  public function list(): int {}

  #[Route('/api/v1/posts/(:int)', methods: ['GET'])]
  public function read(int $post_id): int {}

  #[Route('/api/v1/posts/create', methods: ['POST'])]
  public function create(): int {}

  #[Route('/api/v1/posts/update/(:int)', methods: ['PUT'])]
  public function update(int $post_id): int {}

  #[Route('/api/v1/posts/delete/(:int)', methods: ['DELETE'])]
  public function delete(int $post_id): int {}
}
```

- **Attributes**: Define routes directly in the controller, specifying HTTP methods and additional configurations like middleware.
- **Prefix Handling**: Routes are scoped under `/api/v1/`, with a global error handler for non-existent endpoints.

#### Method-Based Routing

Routes are defined in the `/routes/api.php` file using method chaining, and linked to corresponding controller methods.

```php
// /routes/api.php

use Luminova\Routing\Router;

// Api endpoints index
$router->any('/', 'RestController::index');

$router->bind('/v1/posts', function(Router $router) {
    $router->middleware(Router::ANY_METHOD, '/(:root)', 'RestController::auth');
    
    // CRUD routes for posts
    $router->get('/', 'RestController::list');                   // Retrieve all posts
    $router->get('/(:int)', 'RestController::read');             // Retrieve a specific post by ID
    $router->post('/create', 'RestController::create');          // Create a new post
    $router->put('/update/(:int)', 'RestController::update');    // Update an existing post
    $router->delete('/delete/(:int)', 'RestController::delete'); // Delete a post by ID
});
```

- **Middleware**: Protects the routes with authentication.
- **Index handling**: Ensures that invalid routes are redirected the the `index` method.

***

#### Method-Based Routing Index Controller 

In your index.php `context` method, add routes prefixes to handle API endpoint request as below:

```php
// /public/index.php

Boot::http()->router->context(
  [
    'prefix' => Prefix::API,
    'error' => [ErrorController::class, 'onRestError'] 
  ],
  [
    'prefix' => Prefix::WEB,
    'error' => [ErrorController::class, 'onWebError'] 
  ],
  [
    'prefix' => Prefix::CLI
  ]
)->run();
```

---

### Step 2: Define the Models

**Posts Model:**

The model represents the `Posts` entity and interacts with the database.

```php
// /app/Models/Posts.php

namespace App\Models;

use Luminova\Base\Model;

class Posts extends Model 
{
  protected string $table = 'posts'; 
  protected string $primaryKey = 'pid'; 
  protected bool $cacheable = true;
}
```

**Users Model:**

The model represents the `Users` entity and interacts with the database.

```php
// /app/Models/Users.php

namespace App\Models;

use Luminova\Base\Model;

class Users extends Model 
{
  protected string $table = 'users'; 
  protected string $primaryKey = 'user_id'; 
  protected bool $cacheable = false;
}
```

---

### Step 3: Database Management

**1. Create the Database Migration Class:**

Define your database migration table blueprint.

```php
// /app/Database/Migrations/PostsMigration.php

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
}
```

**2. Create the Table Seeder Class:**

Add seed data to populate your `posts` table.

```php
// /app/Database/Seeder/PostsSeeder.php

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
        'post_uuid' => Luminova\Funcs\uuid(),
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

```bin
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

### Step 4: Implement Controller Methods

Each method in the `RestController` class corresponds to a specific CRUD operation, processing incoming API requests and interacting with the `Post` model.

---

### auth

**Middleware Authentication**  

Handles API authentication and usage quota validation for requests made to the `/api/v1/posts` endpoint.  

```php
public function auth(): int {}
```

- **HTTP Method**: `ANY /api/v1/posts/`
- **Functionality**:  
  - Validates the bearer token and user ID.  
  - Checks the user's quota limit and update used if within limits.  
  - Returns a success status if authentication and quota checks pass.  
  - Responds with a `401 Unauthorized` status and an error message if authentication fails.

---

### index

**Handle Invalid API Endpoints**  

Processes requests to the API root or invalid endpoints.  

```php
public function index(): int {}
```

- **HTTP Method**: `ANY /api/v1/{invalid}`
- **Functionality**:  
  - Renders a `401 Unauthorized` response for invalid or missing endpoints.  

---

### list

**Retrieve All Posts**  

Retrieves a paginated list of posts. Use query parameters for pagination, e.g., `GET /api/v1/posts?limit=10&offset=2`.

```php
public function list(): int {}
```

- **HTTP Method**: `GET /api/v1/posts`
- **Functionality**:  
  - Fetches all posts from the database.  
  - Returns:  
    - `200 OK` with a list of posts if they exist.  
    - `204 No Content` if no posts are available.  
  - Includes a `GET` link in each response item for easier navigation.

---

### read

**Retrieve a Single Post**  

Fetches a specific post by ID from the URL, e.g., `GET /api/v1/posts/2`.

```php
public function read(int $post_id): int {}
```

- **HTTP Method**: `GET /api/v1/posts/{id}`
- **Functionality**:  
  - Retrieves a post by its ID.  
  - Returns:  
    - `200 OK` with the post data if found.  
    - `204 No Content` if the post does not exist.  

---

### create

**Create a New Post**  

Creates a new post using the data provided in the request body.

```php
public function create(): int {}
```

- **HTTP Method**: `POST /api/v1/posts/create`
- **Functionality**:  
  - Accepts `image` and `body` containing JSON keys `title`, `body` and `userId`.  
  - Inserts a new post into the database.  
  - Returns:  
    - `200 OK` if the post is created successfully.  
    - Relevant error codes for validation or server errors.  
- **Validation**:  
  - Ensures `title`, `body`, and `userId` are provided and meet the required criteria.

---

### update

**Update an Existing Post**  

Updates an existing post by ID, using data from the request body.

```php
public function update(int $post_id): int {}
```

- **HTTP Method**: `PUT /api/v1/posts/{post_id}`
- **Functionality**:  
  - Requires request body `body` a JSON with optional `title` and `content` fields.  
  - Updates the specified post in the database.  
  - Returns:  
    - `200 OK` if the update is successful.  
    - An error message if the update fails.  

---

### delete

**Delete a Post**  

Deletes a specific post by its ID.

```php
public function delete(int $post_id): int {}
```

- **HTTP Method**: `DELETE /api/v1/posts/delete/{post_id}`
- **Functionality**:  
  - Removes the post with the given ID from the database.  
  - Returns:  
    - `204 No Content` if the deletion is successful.  
    - An error message if the operation fails.