# Abstract Base Class for Database Models

***

## Overview

Base Model, allows you to define database models for your application, it also supports database searching using a third-party library provided by the Luminova team.

***

## Introduction

The `BaseModel` serves as the foundation for all models within your application, providing essential functionalities and features to interact with the database in an efficient and secure method.

It allows you to define basic rules for your models, like fields that are allowed to be inserted into the model database table, delete, or update. Additionally, it also supports flagging the model as read-only which limits the model to only perform select statements.

***

### Usage Example

The following example demonstrates how to use the Luminova model to interact with a database table.

---

#### Defining the Model

```php
// File: /app/Models/Customers.php

namespace App\Models;

use Luminova\Base\Model as BaseModel;

/**
 * The `Customers` model represents the `customers` table in the database.
 */
class Customers extends BaseModel
{
    /**
     * The table associated with the model.
     */
    protected string $table = 'customers';

    /**
     * The primary key for the table.
     */
    protected string $primaryKey = 'id';
}
```

---

#### Example Usage

##### Retrieving a Record

```php
use App\Models\Customers;

// Initialize the Customers model
$customers = new Customers();

// Find a person by their ID, retrieving specific columns
$customer = $customers->find(1, ['name', 'email']);

if ($customer) {
    echo "Customer Name: " . $customer->name . "\n";
    echo "Customer Email: " . $customer->email . "\n";
} else {
    echo "No customer found with ID 1.\n";
}
```

**Explanation**:  
- The `find` method retrieves a record by its primary key (`id` in this case) and returns the specified columns (`name` and `email`).

---

##### Updating or Inserting Records

```php
use App\Models\Customers;

// Initialize the Customers model
$customers = new Customers();

// Check if a record with the given ID exists
if ($customers->exists(1)) {
    // Update the record
    $customers->update(1, [
        'name' => 'Peter',
        'email' => 'peter@example.com',
    ]);
    echo "Record updated successfully.\n";
} else {
    // Insert a new record
    $customers->insert([
        [
            'name' => 'Peter',
            'email' => 'peter@example.com',
        ]
    ]);
    echo "New record inserted successfully.\n";
}
```

***

## Class Definition

* Class namespace: `Luminova\Base\Model as BaseModel`
* This class is an **Abstract class**
* This class implements: [Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)

***

## Properties

### table

Model table name name.

```php
protected string $table = '';
```

***

### primaryKey

The default primary key column name for table, this will be used while selecting, updating and more.

```php
protected string $primaryKey = '';
```

***

### searchable

Define your database table searchable columns, the fields that search will have to look for queries.

```php
protected array<int,string> $searchable = []
```

***

### cacheable

Enable or disable database caching for model while calling `select`, `find`, `search`, `total` or `count`.

```php
protected bool $cacheable = true;
```

***

### expiry

Database cache expiration time `TTL` in seconds.

```php
protected DateTimeInterface|int $expiry = 7 * 24 * 60 * 60;
```

***

### readOnly

Specify whether the model's table is `updatable`, deletable, and `insertable`.

```php
protected bool $readOnly = false
```

***

### insertable

Define fields that can be inserted into.

```php
protected array $insertable = []
```

***

### updatable

Define fields that can be updated.

```php
protected array $updatable = []
```

***

### rules

Define your Input validation rules based on `Validator` class rules.

```php
protected array<string,string> $rules = []
```

***

### messages

Define your Input validation rules errors messages.

```php
protected array<string,array> $messages = []
```

***

### builder

Database query builder class instance.

```php
protected ?Luminova\Database\Builder $builder = null;
```

***

### validation

Input validation class instance.

```php
protected static ?Luminova\Security\Validation $validation = null;
```

***

## Methods

### constructor

Initialize the constructor for the Model class.

```php
public __construct(): mixed
```

***

### insert

Insert a new record into the current database.

```php
public insert(array<string,mixed> $values): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$values` | **array<string,mixed>** | nested array of values to insert into table. |

**Return Value:**

`bool` - Return true if records was inserted, otherwise false.

**Throw Exception:**

[Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - Throws if insert columns contains column names that isn't defined in `$insertable`.

***

### update

Update current record in the database.

```php
public update(array<int,mixed>|string|float|int|null $key, array $data, ?int $max = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string\|float\|int\|array<int,mixed>\|null** | The key?s to update its record or null to update all records in table. |
| `$data` | **array** | An associative array of columns and values to update. |
| `$max` | **int\|null** | The maximum number of records to update (default: null). |

**Return Value:**

`bool` - Return true if records was updated, otherwise false.

**Throw Exception:**

[Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - Throws if update columns contains column names that isn't defined in `$updatable`.

***

### find

Fine next or a single record from the database table.

```php
public find(array<int,mixed>|string|float|int $key, array<int,string> $fields = ['*']): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;array<int,mixed>\|float\|int** | The key?s to find its record |
| `$fields` | **array<int,string>** | The fields to retrieve (default is all). |

**Return Value:**

`mixed` - Returns selected records or false on failure.

***

### select

Select records from the database table.

```php
public select(
	string|int|float|array<int,mixed>|null $key = null, 
	array<int,string> $fields = ['*'], 
	int $limit = 100, 
	int $offset = 0
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;array<int,mixed>\|float\|int\|null** | The key?s to select its record, if null all record in table will be selected. |
| `$fields` | **array<int,string>** | The fields to retrieve (default is all). |
| `$limit` | **int** | Select result limit (default: 100). |
| `$offset` | **int** | Select limit offset (default: 0). |

**Return Value:**

`mixed` - Returns selected records or false on failure.

***

### search

Select records from the database.

```php
public search(string $query, array<int,string> $fields = ['*'], int $limit = 100, int $offset = 0): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | Search query string, escape string before passing. |
| `$fields` | **array<int,string>** | The fields to retrieve (default is all). |
| `$limit` | **int** | Search result limit (default: 100). |
| `$offset` | **int** | Search limit offset (default: 0). |

**Return Value:**

`mixed` - Returns found records or false on failure.

***

### delete

Delete a record from the database.

```php
public delete(string|array<int,mixed>|float|int|null $key = null, int $max = 1): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;array<int,mixed>\|float\|int\|null** | The keys to delete, if null all record in table will be deleted. |
| `$max` | **int** | The maximum number of records to delete. |

**Return Value:**

`bool` - Return true if the record was successfully deleted otherwise false.

***

### total

Get total number of records in the database.

```php
public total(): int|bool 
```

**Return Value:**

`int|bool ` - Return the number of records or false if failed.

***

### count

Get total number of records in the database based on the keys.

```php
public count(array<int,mixed>|string|float|int $key): int|bool 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **array<int,mixed>\|string\|float\|int** | The key?s to find total number of matched. |

**Return Value:**

`int|bool ` - Return the number of records or false if failed.

***

### exists

Determine if a record exists in the database.

```php
public exists(string|int|float $key): bool 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string\|float\|int** | The key to check if it exists in the database. |

**Return Value:**

`bool ` - Return true if the record exists otherwise false.

***

### purge

Deletes all cache entries related to the current model.

This method removes all cache files for the model from the cache directory. The path is constructed based on the model's class name and is expected to be within the filesystem cache directory.

```php
public purge(): bool
```

**Return Value:**

`bool` - Returns true if the cache files are successfully deleted, false otherwise.

***

### validation

Initialize and ser validation class object.

```php
protected validation(): Luminova\Security\Validation
```

**Return Value:**

`Luminova\Security\Validation` - Return the number of records.

> After first initialization you can then use `static::$validation` to access the object.

***

### doSearch

Run a search in database table using the `$searchable` to index search columns. This method uses third-party libraries to search database table.

```php
public doSearch(
	string $query, 
	array $fields = ['*'], 
	int $limit = 100, 
	int $offset = 0, 
	string $flag = 'any'
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | search query string, escape string before passing. |
| `$fields` | **array<int,string>** | The fields to retrieve (default is all). |
| `$limit` | **int** | Search result limit (default: 100). |
| `$offset` | **int** | Search limit offset (default: 0). |
| `$flag` | **string** | Search matching flag, default is (any) any matching keyword. |

**Return Value:**

`mixed` - Return found records or false on failure.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If search controller class is not installed.

> To use this method or `searchInstance` you will first have to install a third party library by running composer command `composer require peterujah/php-search-controller` .

***

### searchInstance

Return search controller class instance.

```php
protected searchInstance(string $flag): \Peterujah\NanoBlock\SearchInstance
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flag` | **string** | Search matching flag (e.g `any`, `start`). |

**Return Value:**

`SearchInstance` - Search controller instance.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If the search controller class is not installed.

### Search Flags

The `$flags` keys to predefined search behaviors in the `SearchInstance` class. Here are the available flags and their corresponding behaviors:

- **'start'**: Matches records that start with the query string.
- **'end'**: Matches records that end with the query string.
- **'any'**: Matches records that contain the query string anywhere within the field.
- **'second'**: Matches records that contain the query string in the second position (specific use case).
- **'length2'**: Matches records that start with a query string of length 2.
- **'length3'**: Matches records that start with a query string of length 3.
- **'startend'**: Matches records that start and end with the query string.

***

### getTable

Get the name of the database table associated with this model.

```php
public getTable(): string
```

**Return Value:**

`string` - Return the name of the database table.

***

### getKey

Get the primary key field name for this model.

```php
public getKey(): string
```

**Return Value:**

`string` - Return the primary key field name.

***

### getSearchable

Get the table searchable array of column names.

```php
public getSearchable(): array<int,string>
```

**Return Value:**

`array<int, string>` - Return table searchable column names.

***

### getConn

Get instance of database connection.

```php
public getConn(): Luminova\Interface\DatabaseInterface
```

**Return Value:**

`DatabaseInterface` - Return database driver connection instance.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throws if database connection failed.

***

### getBuilder

Get instance of database builder class.

```php
public getBuilder(): ?Luminova\Database\Builder
```

**Return Value:**

`Builder|null` - Return the instance database builder.

***

### getLastInsertedId

Retrieve last inserted id from database after insert method is called.

```php
public getLastInsertedId(): mixed
```

**Return Value:**

`mixed` - Return last inserted id from database.

***

### setReturn

Change the database result return type (e.g, array or object).

```php
public setReturn(string $returns): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$returns` | **string** | The result returned as (e.g, `array` or `object`). |

**Return Value:**

`self ` - Return instance of model class.

***

### isReadOnly

Check if method is read only

```php
public isReadOnly(): bool
```

**Return Value:**

`bool` - Return true if is read only otherwise false.