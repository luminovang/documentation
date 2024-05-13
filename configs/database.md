## Database Configuration

***

## Overview

This configuration provides a failover mechanism by allowing your application to connect to backup database servers if the main server fails.

***

The `Database Configuration` allows you to specify optional backup database servers to connect to in case the main server in `.env` fails. Each backup server configuration must be provided as a nested associative array, with keys representing the database connection parameters.

***

* Class namespace: `\App\Controllers\Config\Database`
* File path: `/app/Controllers/Config/Database.php`
* Parent class: [\Luminova\Base\BaseDatabase](/base/database)

***

## Properties

### databaseBackups

Optional servers to connect to when main server fails.

```php
protected static array $databaseBackups = [];
```

> Nested associative array with each database configuration keys and values.

Supported array keys

- port (`int`) - Database server port.
- host (`string`) - Database server hostname.
- version (`string`) - Database connection version.
- charset (`string`) - Database connection charset.
- sqlite_path (`string`) - Optional Connection sqlite database path.
- username (`string`) - Database connection username.
- password (`string`) - Database connection password.
- database (`string`) - Database name
- socket (`string`) - Force database connection through socket.
- socket_path (`string`) - Database connection socket path.