# Base Class for Model Implementation

***

## Overview

Base Model, allows you to define database models for your application, it also supports database searching using a third-party library provided by the Luminova team.

***

## Introduction

The `BaseModel` serves as the foundation for all models within your application, providing essential functionalities and features to interact with the database in an efficient and secure method.

It allows you to define basic rules for your models, like fields that are allowed to be inserted into the model database table, delete, or update. Additionally, it also supports flagging the model as read-only which limits the model to only perform select statements.

***

* Class namespace: `\Luminova\Base\BaseModel`
* This class is an **Abstract class**

***

## Properties

### table

Model table name name.

```php
protected string $table 
```

***

### primaryKey

The default primary key column name for table, this will be used while selecting, updating and more.

```php
protected string $primaryKey
```

***

### searchable

Define your database table searchable columns, the fields that search will have to look for queries.

```php
protected array<int,string> $searchable
```

***

### cacheable

Enable or disable database caching for model while calling `select`, `find`, `search`, `total` or `count`.

```php
protected bool $cacheable
```

***

### expiry

Database cache expiration time `TTL` in seconds.

```php
protected DateTimeInterface|int $expiry
```

***

### readOnly

Specify whether the model's table is `updatable`, deletable, and `insertable`.

```php
protected bool $readOnly
```

***

### insertable

Define fields that can be inserted into.

```php
protected array $insertable
```

***

### updatable

Define fields that can be updated.

```php
protected array $updatable
```

***

### rules

Define your Input validation rules based on `Validator` class rules.

```php
protected array<string,string> $rules
```

***

### messages

Define your Input validation rules errors messages.

```php
protected array<string,array> $messages
```

***

### builder

Database query builder class instance.

```php
protected \Luminova\Database\Builder $builder
```

***

### validation

Input validation class instance.

```php
protected static \Luminova\Security\Validation $validation
```

***

## Methods

### constructor

Constructor for the Model class, if query builder instance is passed null, it will create a new instance.
To use with your configured builder instance pass it.

```php
public __construct(?\Luminova\Database\Builder $builder = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$builder` | **null&#124;\Luminova\Database\Builder** | Query builder class instance.  |

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

[\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throws if insert columns contains column names that isn't defined in `$insertable`.

***

### update

Update current record in the database.

```php
public update(string|array<int,mixed> $key, array $data, int $max = 1): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;array<int,mixed>** | The key?s to update its record. |
| `$data` | **array** | Columns key and value to update. |
| `$max` | **int** | The maximum number of records to update. |

**Return Value:**

`bool` - Return true if records was updated., otherwise false

**Throw Exception:**

[\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - Throws if update columns contains column names that isn't defined in `$updatable`.

***

### find

Fine next or a single record from the database table.

```php
public find(string|array<int,mixed> $key, array<int,string> $fields = ['*']): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;array<int,mixed>** | The key?s to find its record |
| `$fields` | **array<int,string>** | The fields to retrieve (default is all). |

**Return Value:**

`mixed` - Returns selected records or false on failure.

***

### select

Select records from the database table.

```php
public select(string|array<int,mixed> $key = null, array<int,string> $fields = ['*'], int $limit = 100, int $offset = 0): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;array<int,mixed>** | The key?s to select its record, if null all record in table will be selected. |
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
public delete(string|array<int,mixed> $key = null, int $max = 1): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;array<int,mixed>** | The keys to delete, if null all record in table will be deleted. |
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
public count(string|array<int,mixed> $key): int|bool 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;array<int,mixed>** | The key?s to find total number of matched. |

**Return Value:**

`int|bool ` - Return the number of records or false if failed.

***

### purge

Delete all model database caches.

```php
public purge(): bool
```

**Return Value:**

`bool` - Return true if all caches are deleted, false otherwise.

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

Run a database search current model table.

```php
public doSearch(string $query, array<int,string> $fields = ['*'], int $limit = 100, string $offset, string $flag = 'any'): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | search query string, escape string before passing. |
| `$fields` | **array<int,string>** | The fields to retrieve (default is all). |
| `$limit` | **int** | search limit default is 100. |
| `$offset` | **string** | search limit offset default is 0. |
| `$flag` | **string** | Search matching flag, default is (any) any matching keyword. |

**Return Value:**

`mixed` - Return found records or false on failure.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If search controller class is not installed.

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

- [\Luminova\Exceptions\RuntimeException](/running/exceptions.md#runtimeexception) - If the search controller class is not installed.

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

`string` - The name of the database table.

***

### getKey

Get the primary key field name for this model.

```php
public getKey(): string
```

**Return Value:**

`string` - The primary key field name.

***

### getSearchable

Get the table searchable array of column names.

```php
public getSearchable(): array<int,string>
```

**Return Value:**

`array<int, string>` - Return table searchable column names.

***

### isReadOnly

Check if method is read only

```php
public isReadOnly(): bool
```

**Return Value:**

`bool` - Return true if is read only otherwise false.