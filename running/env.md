# Env Configuration

***

## Overview

This file contains configuration options for your application environment variables. to configure the behavior of your application.

***

## Introduction

The environment configuration file is central to managing your application's behavior by defining various configuration options. These options allow you to configure the functionality, settings, and behavior of your application for different environments such as development, staging, or production.

### Managing Variables

Luminova provides simple methods for adding or updating environment variables in your `.env` file.

**Using PHP Code**

Temporarily set or change the value of an environment variable for the current execution scope.

```php
<?php
setenv('MY_FOO', 'foo value');
```

To set and persist an environment variable, pass `true` as the third argument. This will update your environment file, adding the key-value pair if it does not exist, or updating it if it already does.

```php
<?php
setenv('MY_FOO', 'foo value', true);
```

***

**Retrieve an environment variable**

```php
<?php
echo env('MY_FOO');
```

**Retrieve an environment variable with a default value**

```php
<?php
echo env('MY_BAR', 'default value');
```

***

**Using NovaKit Commands**

These command provide an efficient way to manage your environment configurations with ease.

**Add or update an environment variable**

```bash
php novakit env:add --key=MY_FOO --value="foo value"
```

**Remove an environment variable**

```bash
php novakit env:remove --key=MY_FOO
```

***

### Variables Formatting

- **`true` or `enable`**: 
  - **Description**: Represents a boolean value `true`, which typically activates or enables a feature.

- **`false` or `disable`**: 
  - **Description**: Represents a boolean value `false`, which typically deactivates or disables a feature.

- **`null`**: 
  - **Description**: Represents the absence of a value or a `null` state, useful when no value is required or applicable.

- **`blank`**: 
  - **Description**: Represents an empty string `''`, often used when a value needs to be provided but no actual content is required.

***

## Environment Variables

### Application

- **`app.name`** **(string)**: 
  - **Description**: Specifies the name of your application, which is used for identification and display purposes.

- **`app.hostname`** **(string)**: 
  - **Description**: Defines the hostname where your application is hosted, essential for configuring network-related settings (e.g, `example.com`).

- **`app.timezone`** **(string)**: 
  - **Description**: Sets the timezone for your application, affecting date and time operations, logs, and user interactions.

- **`app.locale`** **(string)**: 
  - **Description**: Defines the locale for the application, impacting language settings, date formats, and other locale-specific formats.

- **`app.charset`** **(string)**: 
  - **Description**: Sets the character encoding used in templates and other parts of the application, crucial for proper text rendering.

- **`app.version`** **(string)**: 
  - **Description**: Indicates the version of your application, useful for version tracking and compatibility management.

- **`app.file.version`** **(string)**: 
  - **Description**: Specifies the version of the application file, which can be different from the main application version, often used for asset versioning.

- **`app.key`** **(string)**: 
  - **Description**: The encryption key for the application, used for securing data and encrypting sensitive information.

- **`app.environment.mood`** **(string)**: 
  - **Description**: Indicates the current environment of the application, which can be `[production]` for live applications or `[development]` for testing and debugging.

- **`app.maintenance.mood`** **(bool)**: 
  - **Description**: Enables or disables maintenance mode for the application. When enabled, it indicates that the application is undergoing maintenance and may be unavailable to users.

- **`app.maintenance.retry`** **(int)**: 
  - **Description**: Specifies the number of seconds to wait before retrying a connection during maintenance mode. Default is 3600 seconds (1 hour).

***

### CLI

- **`cli.environment.mood`** **(string)**: 
  - **Description**: Defines the mood of the CLI environment, which can be `[production]`, `[development]`, or `[testing]`. This setting affects how CLI commands are executed and logged based on the environment.

***

### Cache & Optimization

- **`default.cache.control`** **(string)**:
  - **Description**: Configures cache control directives for HTTP headers. Example values include `no-store`, `max-age=0`, or `no-store, max-age=0, no-cache`. This controls how browsers and intermediary caches handle the cached content.

- **`page.caching`** **(bool)**:
  - **Description**: Enables or disables caching of entire pages to improve performance by reducing the need for repeated processing and database queries.

- **`page.cache.expiry`** **(int)**:
  - **Description**: Sets the duration (in seconds) for which cached pages are considered valid before being refreshed or invalidated. For example, a value of `3600` means cached pages are valid for one hour.

- **`page.minification`** **(bool)**:
  - **Description**: Enables or disables minification of page content, including inline CSS, and JavaScript, to reduce file size and improve loading times.

- **`script.execution.limit`** **(int)**:
  - **Description**: Specifies the maximum execution time (in seconds) allowed for scripts. This helps prevent scripts from running indefinitely and affecting server performance.

- **`script.ignore.abort`** **(bool)**:
  - **Description**: Determines whether script execution should continue even if the client disconnects or aborts the request. Useful for long-running scripts where client disconnection should not stop the process.

- **`script.output.handler`** **(string|null)**:
  - **Description**: Defines a custom output compression handler for script output. Can be set to a compression method like `ob_gzhandler` to reduce the size of data sent to clients. Set to `null` to disable compression.

- **`enable.encoding`** **(bool)**:
  - **Description**: Enables or disables content encoding and compression for views. When enabled, it compresses the output to reduce data size and improve transmission efficiency.

- **`compression.encoding`** **(string|bool)**:
  - **Description**: Specifies the type of compression encoding to use, such as `gzip` or `deflate`. Setting it to `false` disables compression. Adjusts how the content is compressed for optimized delivery.

- **`compression.level`** **(int)**:
  - **Description**: Sets the level of compression to apply, typically ranging from `1` (lowest) to `9` (highest). Higher levels result in greater compression but may increase processing time.

***

### Session

- **`session.strict.ip`** **(bool)**:
  - **Description**: Enforces strict session handling based on the user's IP address. If the user's IP address changes, the session will be invalidated, and the user will need to log in again. This enhances security by binding sessions to a specific IP.

***

### Debugging

- **`debug.show.tracer`** **(bool)**:
  - **Description**: Controls whether a debug trace is displayed, providing detailed information about the execution flow and helping with debugging.

- **`debug.display.errors`** **(bool)**:
  - **Description**: Enables or disables the display of error messages. When set to `true`, errors are shown on the screen, which is useful for development and debugging.

- **`debug.show.performance.profiling`** **(bool)**:
  - **Description**: Displays performance profiling information at the bottom of each page, including execution time, memory usage, and files loaded. Only visible in development mode to help identify performance bottlenecks.

- **`debug.catch.inline.errors`** **(bool)**:
  - **Description**: Enables the detection and handling of hidden errors in view content. If set to `true`, it throws an exception when hidden errors are detected, ensuring that all errors are handled properly.

- **`throw.cli.exceptions`** **(bool)**:
  - **Description**: Allows you to catch and handle exceptions thrown in CLI (Command Line Interface) commands outside of command controller classes. When set to `true`, raw exceptions are thrown in CLI environments.

***

### Email SMTP

- **`smtp.use.credentials`** **(bool)**:
  - **Description**: Specifies whether to use SMTP credentials for authentication. When set to `true`, SMTP username and password are required for sending emails.

- **`smtp.use.password`** **(bool)**:
  - **Description**: Indicates whether to use a password for SMTP authentication. Set to `true` if SMTP credentials include a password.

- **`smtp.debug`** **(bool)**:
  - **Description**: Enables or disables debugging information for SMTP communication. Useful for troubleshooting email sending issues by providing detailed logs.

- **`smtp.host`** **(string)**:
  - **Description**: Defines the SMTP server hostname or IP address used for sending emails. This is the address of the SMTP server that will handle email delivery.

- **`smtp.port`** **(int)**:
  - **Description**: Specifies the port number used for SMTP communication. Common ports are `25`, `465`, or `587`, depending on the SMTP server configuration and encryption method.

- **`smtp.username`** **(string)**:
  - **Description**: SMTP username for authentication. Required if `smtp.use.credentials` is enabled. This username is used to log in to the SMTP server.

- **`smtp.password`** **(string)**:
  - **Description**: SMTP password for authentication. Required if `smtp.use.credentials` is enabled. This password is used to log in to the SMTP server.

- **`smtp.email.sender`** **(string)**:
  - **Description**: The email address used as the sender in outgoing emails. This address appears as the "From" address in emails sent via SMTP.

- **`smtp.encryption`** **(string)**:
  - **Description**: Specifies the encryption method for SMTP communication. Common values are `tls` or `ssl`, which secure the connection between the email client and the SMTP server.

- **`smtp.charset`** **(string)**:
  - **Description**: Defines the character set used for encoding email content. Common values include `UTF-8` to ensure proper representation of international characters.

***

### Database Connection

These configuration variables are used to set up the default database connection. Variables with `development` in their names are specific to development environments, helping to keep development and production configurations separate.

- **`database.hostname`** **(string)**:
  - **Description**: The hostname of your database server (e.g `localhost`).

- **`database.port`** **(int)**:
  - **Description**: The port number for your database connection (e.g. `3306`).

- **`database.charset`** **(string)**:
  - **Description**: The character set used for the database connection (e.g. `utf8mb4`).

- **`database.connection.pool`** **(bool)**:
  - **Description**: Enables or disables connection pooling. Connection pooling reuses existing database connections rather than creating a new connection for each request. This can improve performance and reduce the overhead of frequently opening and closing connections.

- **`database.max.connections`** **(int)**:
  - **Description**: The maximum number of simultaneous database connections allowed to be open. Setting this limit helps manage resource usage and prevent overloading the database server. It's important to balance this value based on your application's needs and the capabilities of your database server.

- **`database.connection.retry`** **(int)**:
  - **Description**: The number of retry attempts for database connections if the initial attempt fails. This can be useful for handling transient connection issues, such as network glitches or temporary server unavailability. Configuring retries helps improve resilience and reliability in database connectivity.

- **`database.persistent.connection`** **(bool)**:
  - **Description**: Enables or disables persistent database connections. Persistent connections remain open across multiple requests, which can improve performance by reducing the overhead of establishing new connections. However, they may also consume more resources and need careful management to avoid potential issues with stale connections.

- **`database.emulate.preparse`** **(bool)**:
  - **Description**: Emulation of the preparse feature, which is used to prepare and optimize SQL queries before execution. This can enhance query performance by reducing the parsing and compilation overhead during runtime. It's particularly useful for databases that do not natively support query preparation.

- **`database.connection`** **(string)**:
  - **Description**: Specifies the database connection driver to use. Available drivers (`MYSQLI` or `PDO`).

- **`database.pdo.engine`** **(string)**:
  - **Description**: Specifies the `PDO` database connection engine to use. Available database (`mysql`, `sqlite`, `sqlsrv`, `pgsql`, `cubrid`, `dblib`, or `oci`).

- **`database.mysql.socket`** **(bool)**:
  - **Description**: Set to `true` to use a Unix socket for database connections when using database connection `PDO` with `MySQL` or `MYSQLI` database connection. Setting this to true forces the database connection to use a socket file instead of a `TCP/IP` connection. Using a socket file can be beneficial when connecting to a MySQL server on the same machine, as it often provides better performance and lower latency compared to a network connection. By default, Luminova may use sockets for CLI-based connections, but enabling this setting explicitly ensures consistency across different types of connections.

- **`database.mysql.socket.path`** **(string)**:
  - **Description**: Path to the socket file used for `mysql` and `mysqli` connections.
  Specifies the file path to the Unix socket used for connecting to a `MySQL` or `MySQLi` database. When `database.mysql.socket` is set to true, this path defines the location of the socket file that Luminova will use for the connection. The socket file is typically located in a system-specific directory (e.g., `/var/run/mysqld/mysqld.sock` or `/tmp/mysql.sock`). Correctly configuring this path ensures proper connection to the MySQL server via the socket, which is essential for reliable and efficient communication between PHP and the MySQL server.

#### Production

Configuration variables specific to production environments.

- **`database.username`** **(string)**:
  - **Description**: The username for the production database.

- **`database.name`** **(string)**:
  - **Description**: The name of the production database.

- **`database.password`** **(string)**:
  - **Description**: The password for the production database.

#### Development

Configuration variables specific to development environments.

- **`database.development.username`** **(string)**:
  - **Description**: The username for the development database.

- **`database.development.name`** **(string)**:
  - **Description**: The name of the development database.

- **`database.development.password`** **(string)**:
  - **Description**: The password for the development database.

#### SQLite

SQLite database configuration is essential for connecting your application to an SQLite database. Here are the key configuration parameters:

- **`database.sqlite.path`** **(string)**:
  - **Description**: Specifies the file path to the SQLite database used in the production environment.
  - **Example**: `writeable/database/production.sqlite`

- **`database.development.sqlite.path`** **(string)**:
  - **Description**: Specifies the file path to the SQLite database used in the development environment.
  - **Example**: `writeable/database/development.sqlite`

> **Note:**  
> Ensure that your database file is stored in the `/writeable/database/` directory. While there is no strict naming convention for the database file, it should be named according to your preference.

***

### Features

Luminova offers several optional features that are disabled by default. These features can be enabled by setting their respective environment variables. Expected values for these variables are `true`, `false`, `enable` or `disable`.

- **`feature.route.attributes`** **(bool)**: 
  - **Description**: Enables the use of route attribute definitions for configuring routes directly in your controller methods.

- **`feature.route.cache.attributes`** **(bool)**: 
  - **Description**: Enables or disables the caching of route annotations. When set to true, the framework caches the route annotations to improve performance by reducing the overhead of parsing annotations on every request. This is especially useful in production environments where routes do not change frequently.

- **`feature.app.class.alias`** **(bool)**: 
  - **Description**: Allows you to define custom aliases for application classes, making it easier to refer to them with shorter or more meaningful names.

- **`feature.app.services`** **(bool)**: 
  - **Description**: Enables the application service class loader, which helps in managing and loading service classes.

- **`feature.app.autoload.psr4`** **(bool)**: 
  - **Description**: Activates PSR-4 autoloading for your application, allowing for automatic class loading based on namespace and directory structure.

- **`feature.app.dev.functions`** **(bool)**: 
  - **Description**: Loads and initializes custom procedural functions defined in `app/Utils/Global.php`.

- **`feature.route.dependency.injection`** **(bool)**: 
  - **Description**: Allows dependency injection type hinting for controller methods that are routable, enabling more flexible and testable code.