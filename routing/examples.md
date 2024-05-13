## URI Routing Examples

***

## Overview

Luminova URI routing implementation examples, routing interface where you can register your project routes.

***

### How Luminova Routing Works?

The framework redirects every request URI that doesn't start with your custom routing context name to the main web controller. For instance, URIs starting with `api` or `console` will not be redirected to the main web controller.

Requests to `https://example.com/api/foo` or any URI beginning with `https://example.com/api/` will be treated as API requests, and they will be handled by the routings defined in `routes/api.php`.

Similarly, requests to `https://example.com/console/foo` or any URI starting with `https://example.com/console/` will be treated as console requests, and they will be handled by the routings defined in `routes/console.php`.

> Ensure that you do not have any custom web routes that start with the paths registered in your `public/index.php` file, such as `api` or `console`, to avoid conflicts with the designated API and console routes.

### Registering Routes Context

To register a route context, it can be done in `public/index.php` and also create the handler PHP file in `routes/`.
The handler file name must match with routing context name registered in `bootstrap` in your `public/index.php`, see below examples.

In `public/index.php`, add a route context, to `$app->router->bootstraps()` method for routing. The bootstrap method accepts arguments of the `\Luminova\Routing\Bootstrap` class instance. You can register as many routes as needed, ensuring that each route name is unique. It's important to ensure that the URI start segments are also unique and match your desired route handler to avoid conflicts.

```php 
<?php
new Bootstrap('routing name', 'callback function for error handling');
```

Learn more about [Routing Context](/routing/view-context)

***

### Bootstrap Context

The below context can be accessed in browser by visiting `https://example.com/panel/`, every request that starts with `panel` will be handles by `routes/panel.php`

```php
<?php
$app->router->bootstraps($app, 
     new Bootstrap('panel', [ViewErrors::class, 'myErrorMethodName'])
);
```

> *IMPORTANT*
> 
> When creating custom routes, avoid changing the default web route `Bootstrap::WEB`. Changing this name may lead to unexpected errors in your application.

***

## Routing Capture & Controllers 

Within any of your route files located in `/routes/`, you can register routing controllers with specific patterns and methods depending on your application's requirements.

To register a custom route, follow these steps:

1. Open `/routes/your-context-name.php` to define your URI routing using the global variable `$router` class instance.
2. Create a corresponding handler PHP file in the `routes/` directory if you haven't, in this example we are using `panel` .
3. Ensure that the controller file name matches the routing bootstrap context name specified in `/public/index.php`.

In `/routes/panel.php`, add your routings 

> For APIs, websites, and other non-CLI routes, they use the same HTTP methods.

***

### HTTP Middleware

Middleware are designed to intercept requests before they reach your controllers, allowing you to perform security checks or other pre-processing tasks. If middleware authentication fails, error will be triggered and controllers will not be called. 

To set up global middleware security for websites and API requests in your application route handler file, create middleware that returns an integer value of `STATUS_SUCCESS` to indicate success or `STATUS_ERROR` to indicate failure.

Here is an example of how you can implement your middleware:

```php
<?php 
$router->middleware('GET|POST', '/.*', 'HomeController::middleware');
```

Implementation example in closure.

```php 
<?php
$router->middleware('GET|POST', '/.*', function (): int {
    if(doAuthenticatedPassed()){
        return STATUS_OK;
    }
    return STATUS_ERROR;
});
```

> You can also implement middleware in `bind` method for group capture.

***

### Presenting Views 

To present your application's views, you can define a route in your `routes/panel.php` file that renders a specific view or template when users accessed, this pattern (`/`) will present your default landing page.

Here's an example of how to set up a landing page route:

```php
<?php
$router->get('/', 'HomeController::index');
```

It can also be done using closure.

```php
<?php
$router->get('/', function() use ($app) {
    $app->view("index")->render();
});
```

***

### URL Parameters

To define a route for URL parameter with dynamic value (e.g., `https://example.com/user/peter1`), you can use a regular expression pattern to match the expected value of the value in the URL.

Here's how you can set up the route using a regular expression pattern in your route file (`routes/web.php` or similar):

Define the route using a regular expression pattern for the username after `user/` segment in a url.
 
```php
<?php
    $router->get('/user/([a-zA-Z0-9-]+)', 'UserController::profile');
```

The above allows you to capture dynamic usernames from the URL and route them to the appropriate controller method for processing and displaying user profiles. 

***

### POST Requests

When implementing functionality to update user profiles via POST requests, it's recommended to create a dedicated controller class for handling it rather than using the main web controller. You can create a custom controller class for your  `API` requests by extending `BaseController` or `BaseViewController` for handling the requests.

Here's an example of defining a route for updating user profiles using a POST request:

```php
<?php
$router->post('/user', 'UserController::update');
```

***

### Nested Binding

To bind and access routes defined under `/blog` and its nested group `/blog/id/id7366` using the following setup:

```php
<?php
use \Luminova\Routing\Router;
$router->bind('/blog', function(Router $router) {
	$router->get('/', 'UserController::blogs');
	$router->get('/id/([a-zA-Z0-9]+)', 'UserController::blog');
});
```

***

#### Custom View Directory

You can load template views from a subfolder within the `resources/views` directory by setting a custom view folder using the `setFolder()` method in your application or within specific route bindings. This allows you to organize views into subdirectories for better structure and organization.

**Setting Custom View Folder Globally**

To set a custom view folder globally for all routes or controllers within a specific context (e.g., `panel`), you can use the `setFolder()` method in your view controller initialization or middleware.

```php
<?php
// Set the custom view folder globally
$app->setFolder("panel");
```

Alternatively, you can set the custom view folder within a specific route binding globally or by view using the `setFolder() `method before rendering a view.

```php
<?php
use \Luminova\Routing\Router;
use \App\Controller\Application;

$router->bind('/admin', function(Router $router, Application $app) {
     // Set in global scope
     $app->setFolder('panel');

     $router->get('/', function() use ($app) {
          //Or optionally set for specific view
          $app->setFolder("panel")->view("foo")->render();
     });
});
```

***

## CLI Routing Capture & Controllers

In NovaKit CLI, you can register global before middleware security checks similar to those used for web and APIs, but using a different method named `before`. Your callback or controller method within this middleware must return an integer (`0` or `STATUS_SUCCESS`) to indicate a pass, or (`1` or `STATUS_ERROR`) to indicate a failure.

***

### CLI Middleware 

You can define a global before middleware for command `foo`.

```php
<?php
$router->before('foo', 'CommandController::middleware');
```

Alternatively, you can pass a `Closure` as the first parameter to the `before()` middleware method to use the middleware globally for all CLI commands. This allows you to define a single authentication check or pre-route action that applies to all CLI routes.

```php
<?php
$router->before(function(): int {
    if (doAuthenticationPassed()) {
        return STATUS_SUCCESS; // Authentication passed
    }
    return STATUS_ERROR; // Authentication failed
});
```

***

### CLI Capture & Controllers

To register command controllers within NovaKit CLI, you can use the `command()` method to define command routes. Unlike HTTP methods (`post`, `get`, `put`, etc.), CLI commands are triggered directly from the command line and do not use traditional HTTP routing methods.

**Registering Command Controllers**

You can register command controllers using the `command()` method with the following syntax.
All commands belonging to a group should be wrapped in `group` method closure.

```php
<?php
use \Luminova\Routing\Router;

// Register a command route named 'foo' mapped to 'CommandController::foo'
$router->group("users", function((Router $router){
    $router->command("foo", 'UserCommandController::foo');
});
```

*To execute the registered command route `foo`, use the following command in the terminal:*

 ```bash
 php index.php users foo
 php index.php users foo --bar
 ``` 
 
 ***

### CLI Dynamic Segments

In NovaKit CLI, you can define routes with dynamic segments to capture variable values within command arguments. Dynamic segments are specified using the syntax `(:value)` within the route pattern.

**Defining Routes with Dynamic Segments**

To create a route with dynamic segments in the router, use the following syntax:

```php
<?php
use \Luminova\Routing\Router;
$router->group("users", function((Router $router){
	$router->command('/profile/name/(:value)', 'UserCommandController::profile');
});
```

*To execute the registered command route `user`, use the following command in the terminal:*

```bash 
php index.php users profile name "Peter"
```

Define a command route with multiple dynamic segments for blog details
 
```php
<?php
use \Luminova\Routing\Router;
$router->group("blogs", function((Router $router){
	$router->command('/post/id/(:value)/title/(:value)', 'BlogCommandController::blog');
});
```

*To execute the registered command route `foo`, use the following command in the terminal:*

```bash 
php index.php blogs post id 277363 title "my post title"
```

***

### CLI Closures Command Controllers (Not Recommended)

While defining command controllers in NovaKit CLI, it's possible to use `Closure` functions directly as command handlers. However, it's generally not recommended due to limitations such as lack of access to the command instance and lack of support for dependency injection within the router command.

**Example with Closure Command Controller**

```php
<?php
use \Luminova\Routing\Router;
$router->group("users", function((Router $router){
	$router->command('/user/name/(:value)', function($name): int {
		echo "Hello, $name!";
		return STATUS_SUCCESS;
	});
});
```

Define a command route with multiple dynamic segments for blog details

```php
<?php
use \Luminova\Routing\Router;
$router->group("blogs", function((Router $router){
     $router->command('/blog/id/(:value)/title/(:value)', function($title, $id): int {
          echo "Blog: {$id}, Title: {$title}";
               
          return STATUS_SUCCESS;
     });
});
```

***

## Controller Classes

### HTTP Controller

This UserController class is an example of a web pages or APis controller that extends BaseController from the Luminova framework component. It defines methods to handle specific URI request actions.

```php
<?php
namespace App\Controllers;
use Luminova\Base\BaseController;

class UserController extends BaseController
{
     public function profile(string $username): void
     {
          $this->app->setFolder('profile')->view('foo')->render([
               'username' => $username
          ]);
     }

     public function blog(string $id): void
     {
          $this->view('foo')->render([
               'blog_id' => $id
          ]);
     }

     public function blogs(): void
     {
          $this->view('blogs')->render();
     }

     public function update(): void
     {
          if ($this->request->isPost()) {
               $name = escape($this->request->getPost("name"));
               response(200)->json(['message' => 'okay']);

               return STATUS_SUCCESS;
          }

          response(405)->json(['message' => 'error']);

          return STATUS_ERROR;
     }
}
```

***

### Command Controller

This CommandController class is an example of a command controller that extends BaseCommand from the Luminova Terminal component. It defines methods to handle specific command actions based on command arguments.

```php
<?php 
namespace App\Controllers;
use Luminova\Base\BaseCommand;

class BlogCommandController extends BaseCommand 
{
     protected string $group = 'blogs';

     public function foo(): int
     {
          echo $this->getOption('bar');
          var_export($this->getQueries());
          return STATUS_SUCCESS;
     }

     public function name(string $id, string $title): int
     {
          echo "Blog: {$id}, Title: {$title}";
          return STATUS_SUCCESS;
     }
}
```