## Base Database

***

## Overview

Define your backup database connection server with Luminova Base Server, to automatically reconnect to a backup database when the main database fails to connect.

***

Automatic Backup Database Connectivity to ensure your application is always online when needed.

`BaseDatabase` enables you to specify backup database servers. If the main database specified in the environment file fails to connect, the framework automatically attempts to connect to your backup servers if defined to ensure your application is always ready when users request for it.

* Class namespace: `\Luminova\Base\BaseDatabase`
* This class is an **Abstract class**

***

## Properties

Optional servers to connect to when main server fails, an associative array with each database configuration keys and values.

```php
protected static array<int,mixed> $databaseBackups = [];
```

Here is an example on how you can extend and use the base server.

```php 
<?php 
namespace App\Controllers\Config;

use \Luminova\Base\BaseDatabase;

class Servers extends BaseDatabase
{
     protected $databaseBackups = [
          [
               'port' => 0,
               'host' => 'my-db-host',
               'pdo_driver' => 'mysql',
               'connection' => 'pdo',
               'charset' => 'utf8',
               'persistent' => true,
               'emulate_preparse' => false,
               'sqlite_path' => '', // if sqlite
               'username' => 'root',
               'password' => '',
               'database' => 'db_name',
               'socket' => false,
               'socket_path' => ''
          ],
          //...
     ];
}
```