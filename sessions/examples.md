# Backend Session and Login Management Examples

***

## Overview

Learn how to securely utilize Luminova's back-end session storage, to create, a simple user login with input validation.

***

## Introduction

Back-end session storage is a convenient way to temporarily store user data on the server. This data persists even after the user closes the browser window, depending on the session configuration. It is useful for various purposes such as session login management, user preferences, and tracking.

### Examples

Initialize the session class with your proffered storage manager in your application controller class like `SessionManager` or `CookieManager`.

```php
// /app/Application.php
namespace App;

use Luminova\Core\CoreApplication;
use Luminova\Sessions\Session;
use Luminova\Sessions\Managers\Session as SessionManager;

class Application extends CoreApplication
{
	protected ?Session $session = null;
    
    protected function onCreate(): void
    {
		$this->session = new Session(new SessionManager());
		$this->session->setStorage('my-user-storage');
		$this->session->start();
	}
}
```

Use `add` or `set` method to save data to your session storage.

```php
$this->session->set('foo', 'my foo');
$this->session->set('bar', [1,3,4]);
```

To set a new data if the key doesn't already exists use `add` method.

```php
$this->session->add('foo', 'bar');
```

To set to a different storage without switching storage.

```php
$this->session->setTo('foo', 'bar', 'new-storage-name');
```

To get from a different storage without switching storage.

```php
$this->session->getFrom('foo', 'old-storage-name');
```

To remove key from session storage.

```php
$this->session->remove('foo');
```

> Depending on the context you are in accessing your session instance can be either `$this->session` or `$this->app->session`.
>
> > If you are withing the `routes/my-context.php` you can access your session instance by `$app->session`.

***

### User Model Example 

User model class example with custom method to retrieve user information by email instead of the default user id.

```php
// /app/Models/User.php
namespace App\Models;

use Luminova\Base\BaseModel;

class User extends BaseModel
{
    /**
     * The table associated with the user model.
     */
    protected string $table = 'users';

    /**
     * The primary key for the table.
     */
    protected string $primaryKey = 'id';

    /**
     * Custom method to find user by email.
     * 
     * @param string $email
     * @param array|null $fields
     * 
     * @return object|null
     */
    public function findByEmail(string $email, ?array $fields = null): ?object
    {
        $fields ??= [
            'user_name', 
            'user_password_hash', 
            'user_id'
        ];

        return $this->builder->table($this->table)
            ->where('email', '=', $email)
            ->find($fields) ?: null;
    }
}
```

---

### User login Controller class example

This example will walk you through on how you can implement a login using Luminova Session.

```php
// /app/Controllers/Http/UserController.php
namespace App\Controllers\Http;

use Luminova\Base\BaseController;
use Luminova\Attributes\Prefix;
use Luminova\Attributes\Route;
use Luminova\Security\Crypter;
use App\Errors\Controllers\ErrorController;
use App\Models\User;

#[Prefix(pattern: '/api/(:root)', onError: [ErrorController::class, 'onApiError'])]
class UserController extends BaseController
{
    #[Route('/api/login', methods: ['POST'])]
 	public function userLogin(): int 
	{
		if ($this->request->getBody() === []) {
            echo 'Invalid request body';
            return STATUS_ERROR;
        }

        if($this->app->session->online()){
            echo "You are already logged in";
            return STATUS_SUCCESS;
        }

        $this->validate->rules = [
            'email' => 'required|email',
            'password' => 'required|min(6)',
        ];

        $this->validate->errors = [
            'email' => [
                'required' => 'Email must not be empty',
                'email' => 'Email address must be a valid email.'
            ],
            'password' => [
                'required' => 'Password must not be empty',
                'min' => 'Password must not be less than 6 characters'
            ],
        ];

        if(!$this->validate->validate($this->request->getBody())){
            echo $this->validate->getError();
		    return STATUS_ERROR;
        }

        $password = $this->request->getPost("password");
        $email = escape($this->request->getPost("email"));
        $user = (new User())->findByEmail($email);
    
        if(!$user){
            echo 'Not matching email found.';
            return STATUS_ERROR;
        }

        if(Crypter::verify($password, $user->user_password_hash)){
            $this->app->session->set('name', $user->user_name);
            $this->app->session->set('email', $email);
            $this->app->session->set('id', $user->user_id);
            $this->app->session->synchronize(ip_address());

            // Render view or redirect to view
            return $this->view('profile');
        }

        echo 'Invalid email or password provided';
        return STATUS_ERROR;
	}
}
```

Now you can access user information anywhere around your application.

```php 
if($this->app->session->online()){
	// Do anything you want to do here...
    echo 'Welcome: ' . $this->app->session->get('name');
}
```
To logout simply call the clear method clear all session data.
If you prefer not to clear all session data, you can pass your storage name to only clear the storage where you store your user session data.

```php 
$this->app->session->clear()
```