## Database Examples

***

## Overview

The Database Examples provides comprehensive examples and guides for leveraging database functionalities within the Luminova Framework.

***

This resource serves as a valuable reference for developers seeking to understand and implement database operations effectively. Here are some of the Database Examples available.

To work with database depending on your use case, you can extend the `Builder` class or `Connection` class.
Once you have setup the database configuration in your `.env` file.

***

### Connection 

To get started with database connection see available methods : [Database Connection](/database/connection.md) and [Database Drivers](/database/drivers.md).

```php
<?php
use \Luminova\Database\Connection;
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
<?php
use \Luminova\Database\Builder;

$builder = Builder::getInstance();

$tbl = $builder->table('users');
$response = $tbl->select(['*']);
```

***

### Statement

To return a prepared statement, you will need to call `returns` method passing `stmt` as the return type before executing `CRUD` method.

```php
<?php
use \Luminova\Database\QueryBuilder;

$builder = QueryBuilder::getInstance();

$tbl = $builder->table('users');
$tbl->returns('stmt');
if($tbl->select(['*'])){
	$statement = $tbl->stmt()
}
```

***

### Database Caching 

Utilizing database result caching in Luminova framework is straightforward and provides significant performance benefits. By employing caching, you can enhance the speed and efficiency of your application's database operations.

> To leverage database result caching, simply invoke the cache method before executing the method to retrieve the result. 
> 
> This method is applicable whether you're utilizing predefined methods or constructing custom queries using the `query()` and `execute()` methods within the Query Builder class.

```php
<?php
use \Luminova\Database\QueryBuilder;

$builder = QueryBuilder::getInstance();

$tbl = $builder->table('users');
$tbl->cache('my_cache_key', 'optional_storage_users', 3600);
$response = $tbl->select(['*']);
```

***

Building your custom SQL query to execute.

```php
<?php
	$query = $builder->query("
		SELECT * 
		FROM users
		WHERE document_parent_id = :parent
	");
	$result = $query->execute([
		'parent' => 'database'
	]);

	var_export($result);
```

***

To directly access raw database connection object.

**From Connection Instance**
```php 
<?php 
use \Luminova\Database\Connection;

$conn = Connection::getInstance()->database()->raw();
```

**From Query Builder Instance**

```php 
<?php 
use \Luminova\Database\QueryBuilder;

$conn = QueryBuilder::getInstance()->db()->raw();
```

***

### Manager

Manager is available in query builder class if you wish to use it outside query builder class you need to initialize the class  [Database Manager](/database/manager.md).

##### Export

Export database table as `csv` or `json` format.

```php 
<?php
$manager = $builder->manager();
$manager->setTable('users');

if($manager->export('csv',  'my-user-table',  $columns = ['email', 'phone'])){
	echo 'Exported';
}
```

##### Backup

Backup your entire database.

```php 
<?php
$manager = $builder->manager();
$manager->setTable('users');

if($manager->backup('my-database-backup')){
	echo 'Backup completed';
}
```

***

### Extending Connection

You can extend the `Connection` class or initialize the class to grab the connection instance `$conn = new Connection();`

```php 
<?php
use \Luminova\Database\Connection;

class MyConn extends Connection 
{  
    public function update(string $name): mixed 
	{
        $query = "SELECT * FROM users WHERE name = :name";
        
        $this->db->prepare($query);
        $this->db->bind(":name", 'Peter');
        $this->db->execute();
        return $this->db->getNext()     
    }

    public function total(string $id): int 
	{
        $query = "SELECT COUNT(*) FROM users";
        
        $this->db->prepare($query);
        $this->db->execute();
        return $this->db->getCount()     
    }
}
```