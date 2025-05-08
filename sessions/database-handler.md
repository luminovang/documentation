# Session Persistence in Database Handler

***

## Overview

The session database handler class allows the use of a database for session storage, while optionally
encrypting session data. It extends PHP SessionHandler to provide fallback behavior for file-based

***

## Introduction

Unlike the `Filesystem` handler, the `Database` session handler class enables storing PHP session data in a database, offering enhanced control, security, and scalability compared to default file-based session handling. This handler is particularly useful for applications requiring session data to persist across distributed systems or when integrating with other database-driven features.

Key features include:  

- **Optional Data Encryption**: Ensures session data is stored securely, protecting it from unauthorized access.  
- **IP Binding**: Ties sessions to client IPs for improved security.  
- **Session ID Entropy**: Controls the length and complexity of generated session IDs.
- **Column Prefixing**: Avoids naming conflicts in shared database tables.  
- **Caching Support**: Boosts performance by caching session data in memory.  
- **Custom Callbacks**: Hooks for session lifecycle events such as validation, creation, and closure.

---

### Usage Example

The following example demonstrates how to configure and use the `Database` with the `Session` class to manage sessions in a database.

```php
use Luminova\Sessions\Session;
use Luminova\Sessions\Handlers\Database;

$session = new Session();
$session->setHandler(new Database('sessions', [
    'encryption'    => true,         // Enable encryption for session data
    'session_ip'    => true,         // Bind sessions to client IPs
    'columnPrefix'  => 'session_',   // Use 'session_' as column prefix
    'cacheable'     => true          // Enable in-memory caching
    'onCreate' => function (string $path, string $name, string $filename): bool {
        return true; // Your logic here...
    }
]));
$session->start();

// Set and retrieve session data
$session->set('foo', 'Hello World');
echo $session->get('foo'); // Outputs: Hello World
```

---

### Database Table Structure

Below is the suggested table structure for storing session data. It is crucial to ensure that the table is optimized for your database engine and application requirements.

```sql
CREATE TABLE `YourSessionsTableName` (
    `id` VARCHAR(255) CHARACTER SET utf8 COLLATE utf8_unicode_ci NOT NULL, -- Unique session identifier
    `data` TEXT NOT NULL,                                                 -- Serialized session data
    `ip` VARBINARY(32) DEFAULT NULL,                                      -- IP address of the client
    `timestamp` INT(11) UNSIGNED NOT NULL,                                -- Last update timestamp
    `lifetime` INT(11) UNSIGNED NOT NULL,                                 -- Session expiration time
    PRIMARY KEY (`id`),
    KEY `timestamp_index` (`timestamp`)                                   -- Index for efficient timestamp queries
) ENGINE=<YourDbEngine> DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

***

### Using Database Migrations

You can create a dedicated migration for your sessions table to fasten database schema management. Below is an example using the Luminova framework's migration blueprint.

```php
// /app/Database/Migrations/SessionsMigration.php

namespace App\Database\Migrations;

use Luminova\Database\Migration;
use Luminova\Database\Table;
use Luminova\Database\Schema;

class SessionsMigration extends Migration
{
    /**
     * Define the schema for the sessions table.
     * 
     * Command to run the migration:
     * php novakit db:migrate --class=SessionsMigration
     */
    public function up(): void
    {
        // Define the sessions table structure
        Schema::create('YourSessionsTableName', function (Table $table) {
            $table->prettify = true;
            $table->collation = 'utf8mb4_unicode_ci'; // Set collation for the table
            $table->charset = 'utf8mb4'; // Set character set

            // Define table columns
            $table->varchar('id', 255)
                ->primary() // Set as primary key
                ->nullable(false) // Column cannot be null
                ->collation('utf8_unicode_ci'); // Set specific collation for the column

            $table->text('data')
                ->nullable(false); // Column cannot be null

            $table->varBinary('ip', 32)
                ->default(null); // IP address column, allows null by default

            $table->integer('timestamp', 11)
                ->unsigned() // Unsigned integer for positive values only
                ->nullable(false) // Column cannot be null
                ->index(Table::INDEX_PRIMARY_KEY); // Add index for efficient lookups

            $table->integer('lifetime', 11)
                ->unsigned() // Unsigned integer
                ->nullable(false); // Column cannot be null

            return $table;
        });
    }

    /**
     * Rollback the migration by dropping the sessions table.
     */
    public function down(): void
    {
        Schema::dropIfExists('YourSessionsTableName');
    }

    public function alter(): void {}
}
```

> **Notes:**  `Table Prefixing`: If a column prefix is specified (e.g., `session_`), ensure to update the column names accordingly (e.g., `session_id`, `session_data`).

***

## Class Definition

* Class namespace: `\Luminova\Sessions\Handlers\Database`
* Parent class: [\Luminova\Base\BaseSessionHandler](/base/session-handler.md)
* Class implements: [\SessionHandlerInterface](https://www.php.net/manual/en/class.sessionhandlerinterface.php), [\SessionIdInterface](https://www.php.net/manual/en/class.sessionidinterface.php)

***

## Methods

### constructor

Constructor to initialize the session handler.

```php
public __construct(string $table, array<string,mixed> $options = []): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The name of the database table for session storage. |
| `$options` | **array<string,mixed>** | Configuration options for session handling. |

**Options**

This section outlines the available options for configuring a session database handler. These options provide flexibility in managing session data storage and behavior for more information visit [Abstract Base Session handler](/base/session-handler#lmv-docs-configuration-options) documentation.

```php
[
    'encryption'    => false, // Enables encryption for session data to enhance security. 
    'session_ip'    => false, // Binds sessions to the client's IP address to prevent session hijacking.  
    'columnPrefix'  => null, // Specifies a prefix for session-related columns to avoid name conflicts in shared tables.  
	'autoLockDatabase' => false // Enable or disable database session locking.
    'cacheable'     => true, // Enables in-memory caching for faster session data retrieval.
    'debugging'     => null, // Indicates whether log error messages.
    'sid_entropy_bits'  => 160, // Session id entropy bits.
    'onValidate'    => null, // A callback executed during session validation, useful for custom checks.
    'onCreate'      => null, // A callback triggered upon session creation.  
    'onClose'       => null  // A callback triggered when a session is closed or destroyed. 
]
```