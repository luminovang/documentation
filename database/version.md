# Database Seeding and Migration Tracking

***

## Overview

Database Version Control is an essential tool for managing and cleaning up lock files and old migration or seeder files. By using `db:clear` command, you can ensure a tidy development environment.

***

## Introduction

The Database Version Control uses `db:clear` command to manage and clean up lock files and old migration or seeder files. 
This command helps maintain a clean working environment by removing unnecessary files that can clutter the system or cause conflicts during development and deployment processes.

### Clearing Migration or Seeder Lock Files

`Seeder` or `Migration` lock files are created to track the state of executions. Over time, these lock files can accumulate, become unnecessary and may need to be cleared. The `--lock=[context]` option removes all specific context lock files from the system.

### Clearing Specific Lock Files

If you need to clear lock files for a specific migration or seeder class, use the `--class` option in combination with the `--lock=[context]` option. 
This targets only the lock files associated with the specified class, allowing for more control.

### Removing Old Files

While the primary purpose of `db:clear` is to manage lock files, it also addresses the removal of old migration and seeder files. 
When lock files are cleared, the associated old files are also removed if they are no longer referenced in the lock files. 
This helps to ensure that the file system remains clean and free of obsolete files.

***

### Command Description

The `db:clear` command clears lock files for seeders or migrations.
Additionally, it can remove old migration and seeder files based on the specified options. 
This is particularly useful for cleaning up after testing or when certain migrations or seeders are no longer needed.

***

### Usage Examples

#### Clear Seeder Lock Files

To clear all seeder lock files:

```bash
php novakit db:clear --lock=seeder
```

#### Clear Specific Seeder Lock Files

To clear lock files for a specific seeder class (e.g., `TestSeeder`):
```bash
php novakit db:clear --lock=seeder --class=TestSeeder
```

#### Clear Migration Lock Files

To clear all migration lock files:
```bash
php novakit db:clear --lock=migration

```

#### Clear Specific Migration Lock Files

To clear lock files for a specific migration class (e.g., `TestMigration`):

```bash
php novakit db:clear --lock=migration --class=TestMigration
```

***

### Options

- `-l, --lock`: Specify the context of the lock files to clear. Allowed values are `seeder` and `migration`.
- `-c, --class`: (Optional) Specify the migration or seeder class to clear lock files for.