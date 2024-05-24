# Files Configuration

***

## Overview

File configuration provides flexibility and control over file and directory permissions, ensuring appropriate access levels for different types of files and directories in the application.

***

## Introduction

The `Files Configuration` class provides properties to set the default Unix file and directory permissions for creating new files and directories in your application.

***

* Class namespace: `\App\Controllers\Config\Files`
* File path: `/app/Controllers/Config/Files.php`
* This class is marked as **final** and can't be subclassed

## Properties

### filePermissions

Set Unix file permission, to use while creating a new file in your application.

```php
public int $filePermissions = 0644;
```

***

### dirPermissions

Set Unix directory permission, to use while creating a new directory.

```php
public int $dirPermissions = 0755;
```
