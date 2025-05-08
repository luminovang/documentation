# Configurations for File Access and Permission

***

## Overview

File configuration provides flexibility and control over file and directory permissions, ensuring appropriate access levels for different types of files and directories in the application.

***

## Introduction

The `Files Configuration` class provides properties to set the default Unix file and directory permissions for creating new files and directories in your application.

***

* Class namespace: `\App\Config\Files`
* File path: `/app/Config/Files.php`
* This class is marked as **final** and can't be subclassed

## Properties

### filePermissions

Set Unix file permission, to use while creating a new file in your application.

```php
public static int $filePermissions = 0644;
```

***

### dirPermissions

Set Unix directory permission, to use while creating a new directory.

```php
public static int $dirPermissions = 0755;
```

***

### extensions

File extensions based on MIME types, where the key is the MIME type and the value is the extension.

This property allows you to specify more extensive mime types their extensions.

```php
protected static array $extensions = [
    'image/jpeg' => 'jpg',
    'image/png' => 'png',
	// Add more...
];
```

**Usages:**

```php
$file = 'path/to/file/text.txt';
echo Files::getExtension(get_mime($file)); // Outputs: txt
```