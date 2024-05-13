## Database Manager

***

## Overview

Database manager class allows you to create a backup of your database or export table in CSV or JSON format.

***

With database manager class, you can create a backup of your database or export, download database table columns as CSV or JSOn file format. All your backup and backup will be stored in `/luminova.ng/writeable/caches/database/`

* Class namespace: `\Luminova\Database\Manager`

***


### constructor

Initializes database manager class with `DriversInterface` you are using.

```php
new Manager(\Luminova\Interface\DriversInterface $db, null|string $table = null): mixed
```

Get database manager instance from `QueryBulder` class.

```php
$manager = $bilder->manager(): Manager;
```


**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$db` | **\Luminova\Interface\DriversInterface** |  |
| `$table` | **null&#124;string** |  |


***

## Methods


### setTable

Set the databse table to backup.

```php
public setTable(string $table): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | Database table name to export. |


***


### export

Export database table and download it to browser as JSON or CSV format.

```php
public export(string $as = 'csv', string $filename = null, array $columns = ['*']): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$as` | **string** | Expirt as csv or json format. |
| `$filename` | **string** | Filename to download it as. |
| `$columns` | **array** | Table columns to export (default: all) |

**Return Value:**

`bool` - Return true on success else false.

**Throws:**

- [DatabaseException](/exceptions/database) - If invalid format is provided.
- [DatabaseException](/exceptions/database) - If unable to create export temp directory.
- [DatabaseException](/exceptions/database) - If failed to create export.

***

### backup

Create a database backup file.

```php
public backup(string $filename = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | Filename to store backup as. |

**Return Value:**

`bool` - Return true on success else false.

**Throws:**

- [DatabaseException](/exceptions/database) - If unable to create backup directory.
- [DatabaseException](/exceptions/database) - If failed to create backup.