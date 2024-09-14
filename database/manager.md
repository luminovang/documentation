# Database Backup and Export manager

***

## Overview

Database manager class allows you to create a backup of your database and tables, and export them as a CSV or JSON format.

***

## Introduction

The `Manager` class allows you to create backups of your database, database table or export specific table columns as `CSV` or `JSON` files. All backups and exports are stored in `/writeable/backups/` directory.

***
* Class namespace: `\Luminova\Database\Manager`

***

## Methods

### constructor

Initializes database manager class with `DriversInterface` you are using.

```php
public __construct(\Luminova\Interface\DriversInterface $db, ?string $table = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$db` | **\Luminova\Interface\DriversInterface** | The database driver interface. |
| `$table` | **string&#124;null** | The name of the table to manage. |

**Get database manager instance from `QueryBulder` class.**

```php
$manager = $bilder->manager(): Manager;
```

***

### setTable

Sets the database table to backup or export.

```php
public setTable(string $table): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The database table name. |

***

### export

Exports the specified table and downloads it as a `CSV` or `JSON` file.

```php
public export(string $as = 'csv', ?string $filename = null, array $columns = ['*']): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$as` | **string** | Format to export (e.g, `csv` or `json`). |
| `$filename` | **string&#124;null** | The name of the file to download. |
| `$columns` | **array** | The columns to export (default: `[*] `all columns). |

**Return Value:**

`bool` - Return true on success else false.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If an invalid format is provided or if the export fails.

***

### backup

Creates a backup of the current database or a specific table.

```php
public backup(?string $filename = null, bool $forTable = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string&#124;null** | The name of the backup file. |
| `$forTable` | **bool** | Whether to back up a specific table or the entire database (default: false). |

**Return Value:**

`bool` - Return true on success else false.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If the backup fails or the directory cannot be created.