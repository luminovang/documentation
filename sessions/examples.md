# User Session Examples

***

## Overview

Learn how to securely utilize Luminova's back-end session storage, to create, a simple user login with input validation.

***

## Introduction

Back-end session storage is a convenient way to temporarily store user data on the server. This data persists even after the user closes the browser window, depending on the session configuration. It is useful for various purposes such as session login management, user preferences, and tracking.

### Examples

Initialize the session class with your proffered storage manager in your application controller class like `SessionManager` or `CookieManager`.

```php
<?php
namespace App\Controllers;

use \Luminova\Base\BaseApplication;
use \Luminova\Sessions\Session;
use \Luminova\Sessions\SessionManager;

class Application extends BaseApplication
{
	protected ?Session $session = null;
    
    public function __construct()
    {
		$this->session = new Session(new SessionManager());
		$this->session->setStorage('my-user-storage');
		$this->session->start();

		parent::__construct();
	}
}
```

Use `add` or `set` method to save data to your session storage.

```php
<?php
$this->session->set('foo', 'my foo');
$this->session->set('bar', [1,3,4]);
```

To set a new data if the key doesn't already exists use `add` method.

```php
<?php
$this->session->add('foo', 'bar');
```

To set to a different storage without switching storage.

```php
<?php
$this->session->setTo('foo', 'bar', 'new-storage-name');
```

To get from a different storage without switching storage.

```php
<?php
$this->session->getFrom('foo', 'old-storage-name');
```

To remove key from session storage.

```php
<?php
$this->session->remove('foo');
```

> Depending on the context you are in accessing your session instance can be either `$this->session` or `$this->app->session`.
>
> > If you are withing the `routes/my-context.php` you can access your session instance by `$app->session`.

***

###  User login Example

This example will walk you through on how you can implement a login using Luminova Session.

```php
<?php
namespace App\Controllers;

use \App\Utils\Func;
use \Luminova\Base\BaseController;

class UserController extends BaseController
{
 	public function userLogin(): int 
	{
		if ($this->request->getMethod() === 'post') {

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

			if($this->validate->validate($this->request->getBody())){
				$password = $this->request->getPost("password");
				$email = escape($this->request->getPost("email"));

				$tbl = $this->builder->table('users');
				$tbl->where('email', '=', $email);
				$user = $tbl->find(['user_name', 'user_password_hash', 'user_id']);

				if($user && Func::verifyPassword($password, $user->user_password_hash)){
					$this->app->session->set('name', $user->user_name);
					$this->app->session->set('email', $email);
					$this->app->session->set('id', $user->user_id);
					$this->app->session->synchronize(ip_address());
					// Render view or redirect to view
					return $this->view('profile');
				}else{
					echo 'Invalid email or password provided';
				}
			}else{
				echo $this->validate->getError();
			}
		}else{
			echo 'Invalid request method';
		}

		return STATUS_ERROR;
	}
}
```

Now you can access user information anywhere around your application.

```php 
<?php 
if($this->app->session->online()){
	// Do anything you want to do here...
}
```
To logout simply call the clear method clear all session data.
If you prefer not to clear all session data, you can pass your storage name to only clear the storage where you store your user session data.

```php 
<?php 
$this->app->session->clear()
```