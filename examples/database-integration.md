# Database Management and Usage Examples

***

## Overview

The Database Examples provides comprehensive examples and guides for leveraging database functionalities within the Luminova Framework.

***

## Introduction

This resource serves as a valuable reference for developers seeking to understand and implement database operations effectively. Here are some of the Database Examples available.

To work with database depending on your use case, you can extend the `Builder` class or `Connection` class.
Once you have setup the database configuration in your `.env` file.

***

### Connection 

To get started with database connection see available methods : [Database Connection](/database/connection.md) and [Database Drivers](/database/drivers.md).

```php
use Luminova\Database\Connection;

$db = Connection::getInstance()->database();

$db->prepare("UPDATE users SET name = :name");
$db->bind(":name", $name);
$db->execute();
$response = $db->rowCount();
```

***

### Query Builder

To get started with database query builder see available methods : [Database Query Builder ](/database/query-builder.md).

```php
use Luminova\Database\Builder;

// Initialize table 
$tbl = Builder::table('users')
    ->select(['*']);

// Executing result

// Get result
$result = $tbl->get();

// Get result from statement
$result = $tbl->stmt()->fetchObject(User::class);

while($row = $tbl->fetch()){
    // handle result
}

// Promise resolver
$tbl->promise()->then(function(mixed $result){
    // handle result
});
```

***

### Statement

To return a prepared statement, you don't need to call any `CRUD` method.

```php
use Luminova\Database\Builder;

$stmt = Builder::table('users')
    ->select(['*'])
    ->where('country', '=', 'NG')
    ->limit(50)
    ->order('join_date', 'DESC')
    ->stmt(['*']);

var_export($stmt->getNext());
```

***

### Database Caching 

Utilizing database result caching in Luminova framework is straightforward and provides significant performance benefits. By employing caching, you can enhance the speed and efficiency of your application's database operations.

> To leverage database result caching, simply invoke the cache method before executing the method to retrieve the result. 
> 
> This method is applicable whether you're utilizing predefined methods or constructing custom queries using the `query()` and `execute()` methods within the Query Builder class.

```php
use Luminova\Database\Builder;

$response = Builder::table('users')
    ->select(['*'])
    ->cache('my_cache_key', 'optional_storage_users', 3600)
    ->get();
```

***

Building your custom SQL query to execute.

```php
$result = Builder::query('
	SELECT * 
	FROM users
	WHERE user_id = :user_id
')->execute([
	'user_id' => 100
]);

var_export($result);
```

***

To directly access raw database connection object.

**From Connection Instance**
```php 
use Luminova\Database\Connection;

$conn = Connection::getInstance()->database()->raw();
```

**From Query Builder Instance**

```php 
use Luminova\Database\Builder;

$conn = Builder::getInstance()->database()->raw();
```

***

### Manager

Manager is available in query builder class if you wish to use it outside query builder class you need to initialize the class  [Database Manager](/database/manager.md).

##### Export

Export database table as `csv` or `json` format.

```php 
$manager = Builder::getInstance()->manager();

$exported = $manager->setTable('users')
	->export('csv', 'my-user-table', ['email', 'phone']);

if($exported){
	echo 'Exported';
}
```

##### Backup

Backup your entire database.

```php 
$manager = Builder::getInstance()->manager();
$manager->setTable('users');

if($manager->backup('my-database-backup')){
	echo 'Backup completed';
}
```

***

### Extending Connection

You can extend the `Connection` class or initialize the class to grab the connection instance `$conn = new Connection();`

```php 
use Luminova\Database\Connection;

class MyConn extends Connection 
{  
	public function __construct(){
		parent::__construct();
	}

    public function get(int $id): mixed 
	{
        $query = "SELECT * FROM users WHERE user_id = :id";
        
        $this->db->prepare($query);
        $this->db->bind(":id", $id);
        $this->db->execute();

        return $this->db->getNext()     
    }

    public function count(string $id): int 
	{
        $query = "SELECT COUNT(*) FROM users";
        
        $this->db->prepare($query);
        $this->db->execute();

        return $this->db->getCount()     
    }
}
```