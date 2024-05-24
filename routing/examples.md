# URI Routing Examples

***

## Overview

Luminova URI routing implementation examples, routing interface where you can register your project routes.

***

## Introduction

### How Luminova Routing Works?

The framework redirects every request URI that doesn't start with your custom routing context name to the main web controller. For instance, URIs starting with `api` or `console` will not be redirected to the main web controller.

Requests to `https://example.com/api/foo` or any URI beginning with `https://example.com/api/` will be treated as API requests, and they will be handled by the routings defined in `routes/api.php`.

Similarly, requests to `https://example.com/console/foo` or any URI starting with `https://example.com/console/` will be treated as console requests, and they will be handled by the routings defined in `routes/console.php`.

> Ensure that you do not have any custom web routes that start with the paths registered in your `public/index.php` file, such as `api` or `console`, to avoid conflicts with the designated API and console routes.

### Registering Routes Context

To register a route context, it can be done in `public/index.php` and also create the handler PHP file in `routes/`.
The handler file name must match with routing context name registered in `context` in your `public/index.php`, see below examples.

In `public/index.php`, add a route context, to `$app->router->context()` method for routing. The context method accepts arguments of the `\Luminova\Routing\Context` class instance. You can register as many routes as needed, ensuring that each route name is unique. It's important to ensure that the URI start segments are also unique and match your desired route handler to avoid conflicts.

```php 
<?php
new Context('routing name', 'callback function for error handling');
```

To learn more about routing context  [See Documentation](/routing/view-context.md)

***

### Context

The below context can be accessed in browser by visiting `https://example.com/panel/`, every request that starts with `panel` will be handles by `routes/panel.php`

```php
<?php
$app->router->context(new Context('panel', [ViewErrors::class, 'myErrorMethodName']));
```

> *IMPORTANT*
> 
> When creating custom routes, avoid changing the default web route `Context::WEB`. Changing this name may lead to unexpected errors in your application.

***

## Routing Capture & Controllers 

Within any of your route files located in `/routes/`, you can register routing controllers with specific patterns and methods depending on your application's requirements.

To register a custom route, follow these steps:

1. Open `/routes/your-context-name.php` to define your URI routing using the global variable `$router` class instance.
2. Create a corresponding handler PHP file in the `routes/` directory if you haven't, in this example we are using `panel` .
3. Ensure that the controller file name matches the routing context name specified in `/public/index.php`.

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
$router->get('/', function(Application $app): int {
    return $app->view("index")->render();
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
use \Luminova\Base\BaseApplication;

$router->bind('/blog', function(Router $router, BaseApplication $app) {
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
use \Luminova\Base\BaseApplication;

$router->bind('/admin', function(Router $router, BaseApplication $app) {
     // Set in global scope
     $app->setFolder('panel');

     $router->get('/', function() use ($app) {
          //Or optionally set for specific view
          $app->setFolder("panel")->view("foo")->render();
     });
});
```

***

## CLI Capture & Controllers

You can register global before middleware security checks, similar to those used in `HTTP`, but in `CLI` it uses `before` method to handle middleware authentication. Your callback or controller method for middleware must return an integer (`0` or `STATUS_SUCCESS`) to indicate a pass, or (`1` or `STATUS_ERROR`) to indicate a failure.

***

### CLI Middleware 

You can define a global before middleware for command using `any` as the group name.

```php
<?php
$router->before('any', 'CommandController::middleware');
```

Alternatively, you can pass a `Closure` as the `before` callback method to use. This allows you to define a single authentication check that applies to all `CLI` routes.

```php
<?php
$router->before('any', function(): int {
    if (doAuthenticationPassed()) {
        return STATUS_SUCCESS; // Authentication passed
    }
    return STATUS_ERROR; // Authentication failed
});
```

***

### CLI Implementation

To register command controllers in `CLI` routing, you must use the `command` method only, to define commands. Unlike the HTTP routing that support methods like (`post`, `get`, `put`, etc.), the `CLI` commands are triggered directly from the command line tool and do not use traditional HTTP request methods in handling commands.

**Registering Controllers**

To register command controllers and grouped middleware security, use the `command` and `before` method within the `group` closure callback. All commands belonging to a group should be wrapped in same `group` closure do not define multiple group with same name, it must be unique just like you will do on HTTP routing. 

Bellow example we register a command route with 'foo' name, mapped to 'CommandController::foo' to handle the execution.

```php
<?php
use \Luminova\Routing\Router;
use \Luminova\Base\BaseApplication;
use \Luminova\Command\Terminal;

$router->group("users", function(Router $router, BaseApplication $app, Terminal $term){
    $router->before('users', 'UserCommandController::middleware');
    $router->command("foo", 'UserCommandController::foo');
});
```

*To execute the above example command `foo`, use the following command in the terminal:*

 ```bash
 php index.php users foo
 php index.php users foo --bar
 ``` 
 
 >*Note:* The `before` middleware will first execute before `command` is executed.
 >If middleware fails, then the execution of `command` will be terminated.
 
 ***

### CLI Dynamic Segments

In `CLI` routing, you can define routes with dynamic segments to capture variable values within command arguments. Dynamic segments are specified using the syntax like `(:mixed)`, `(:int)`, `(:optional)` and more within the route pattern.

**Example**

This example shows how you can create a route with dynamic segments.

```php
<?php
use \Luminova\Routing\Router;

$router->group("users", function(Router $router){
	$router->command('/profile/name/(:mixed)', 'UserCommandController::profile');
});
```

*To execute the registered command route `user`, use the following command in the terminal:*

```bash 
php index.php users profile name "Peter"
```

Define a command route with multiple dynamic segments.
 
```php
<?php
use \Luminova\Routing\Router;

$router->group("blogs", function(Router $router){
	$router->command('/post/id/(:int)/title/(:string)', 'BlogCommandController::blog');
});
```

*To execute the registered command route `foo`, use the following command in the terminal:*

```bash 
php index.php blogs post id 277363 title "my post title"
```

***

### CLI Closures Command Controllers (Not Recommended)

While defining command controllers for `CLI`, it's possible to use `Closure` functions directly as command handlers. However, it's generally not recommended.

**Example**

```php
<?php
use \Luminova\Routing\Router;

$router->group("users", function(Router $router){
	$router->command('/user/name/(:string)', function(string $name): int {
		echo "Hello, $name!";
		return STATUS_SUCCESS;
	});
});
```

Define a command route with multiple dynamic segments.

```php
<?php
use \Luminova\Routing\Router;
$router->group("blogs", function((Router $router){
     $router->command('/blog/id/(:int)/title/(:string)', function($title, $id): int {
          echo "Blog: {$id}, Title: {$title}";
               
          return STATUS_SUCCESS;
     });
});
```

***

## Controller Classes

### HTTP Controller

This `UserController` class is an example of HTTP controller class that extends `BaseController` from the Luminova's component. It defines methods to handle specific URI request and actions.

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

This `CommandController` class is an example of a `CLI` controller that extends `BaseCommand` from the Luminova's `Terminal` component. It defines methods to handle specific command and actions based on command arguments.

```php
<?php 
namespace App\Controllers;
use Luminova\Base\BaseCommand;

class BlogCommandController extends BaseCommand 
{
     protected string $group = 'blogs';
		 //...
		 
		  public function middleware():int
		 {
			 return STATUS_SUCCESS;
		 }

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