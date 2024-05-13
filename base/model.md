# Base Model

***

## Overview

Base Model, allows you to define database models for your application, it also supports database searching using a third-party library provided by the Luminova team.

***

## Introduction

The Base Model serves as the foundation for all models within your application, providing essential functionalities and features to interact with the database.

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

Default primary key column name for table.

```php
protected string $primaryKey
```

***

### searchables

Define your database table searchable column names.

```php
protected array<int,string> $searchables
```

***

### cachable

Enable or disable database caching for query builder.

```php
protected bool $cachable
```

***

### readOnly

Specify whether model table is updatable, deletable and insertable.

```php
protected bool $readOnly
```

***

### insertables

Define fields that allows to be inserted.

```php
protected array $insertables
```

***

### updatables

Define fields that allows to be updated.

```php
protected array $updatables
```

***

### rules

Define your Input validation rules.

```php
protected array $rules
```

***

### messages

Define your Input validation rules errors messages.

```php
protected array $messages
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
protected \Luminova\Security\InputValidator $validation
```

***

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

## Abstract Methods

### insert

Insert a new record into the current database.

```php
abstract protected insert(array<string,mixed> $values): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$values` | **array<string,mixed>** | nested array of values to insert into table. |

**Return Value:**

`int` - Return the number of records inserted.

***

### update

Update current record in the database.

```php
abstract protected update(string|array<int,mixed> $key, array $data): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;array<int,mixed>** | The key?s to update its record |
| `$data` | **array** | Columns key and value to update |

**Return Value:**

`int` - Return the number of records updated.

***

### find

Fine next or a single record from the database table.

```php
abstract protected find(string|array<int,mixed> $key, array<int,string> $fields = ['*']): mixed
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
abstract protected select(string|array<int,mixed> $key = null, array<int,string> $fields = ['*']): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;array<int,mixed>** | The key?s to select its record, if null all record in table will be selected. |
| `$fields` | **array<int,string>** | The fields to retrieve (default is all). |

**Return Value:**

`mixed` - Returns selected records or false on failure.

***

### search

Select records from the database.

```php
abstract protected search(string $query, array<int,string> $fields = ['*']): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | Search query string, escape string before passing. |
| `$fields` | **array<int,string>** | The fields to retrieve (default is all). |

**Return Value:**

`mixed` - Returns found records or false on failure.

***

### delete

Delete a record from the database.

```php
abstract protected delete(string|array<int,mixed> $key = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;array<int,mixed>** | The keys to delete, if null all record in table will be deleted. |

**Return Value:**

`bool` - Return true if the record was successfully deleted otherwise false.

***

### total

Get total number of records in the database.

```php
abstract protected total(): bool
```

**Return Value:**

Return the number of records.

***

### count

Get total number of records in the database based on the keys.

```php
abstract protected count(string|array<int,mixed> $key): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string&#124;array<int,mixed>** | The key?s to find total number of matched. |

**Return Value:**

`int` - Return the number of records.

***

## Methods

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

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#RuntimeException) - If search controller class is not installed.

> To use this method or `searchInstance` you will first have to install a third party library by running composer command `composer require peterujah/php-search-controller` .

***

### searchInstance

Return search controller class instance.

```php
protected searchInstance(string $flag): \Peterujah\NanoBlock\Searchable
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flag` | **string** | Search matching flag. |

**Return Value:**

`Searchable` - Search controller instance.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#RuntimeException) - If the search controller class is not installed.

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

Get the table searchables array of column names.

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