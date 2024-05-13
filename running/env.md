## Env Configuration

***

## Overview

This file contains configuration options for your application environment variables. to configure the behavior of your application.

***

The environment configuration file serves as a central hub for managing the behavior of your application by defining various configuration options through environment variables. These variables are crucial for customizing the functionality, settings, and behavior of your application to suit different deployment environments, such as development, staging, or production.

***

### Managing

Luminova simplifies the process of adding or updating variables in your `.env` file through the `setenv` global helper function or command line.

```php
<?php
setenv('MY_FOO', 'foo value', true);
```
> By setting the third argument to `true`, the variables are seamlessly added to your environment file or updated if the key already exists. 

Using NovaKit Command to add or update variable.

```bash
php novakit env:add --key MY_FOO --value "foo value"
```

Deleting variable using command line.

```bash
php novakit env:remove --key MY_FOO
```

> These approach allows you to efficiently manage environment configurations with and ease.

***

### Formatting

You can use any of the following values:

- `true` or `enable`: Returns a boolean value `true`.
- `false` or `disable`: Returns a boolean value `false`.
- `null`: Returns `null`.
- `blank`: Returns an empty string `''`.

***

### Application

- `app.name` *(string)*: Name of the application.
- `app.hostname` *(string)*: Hostname of the application.
- `app.timezone` *(string)*: Timezone of the application.
- `app.locale` *(string)*: Locale of the application.
- `app.charset` *(string)*: Set the charset to use in template and other required place.
- `app.version` *(string)*: Version of the application.
- `app.file.version` *(string)*: Version of the application file.
- `app.key` *(string)*: Application encryption key.
- `app.environment.mood` *(string)*: Mood of the application environment `[production or development]`.
- `app.maintenance.mood` *(bool)*: Maintenance mood of the application.
- `app.maintenance.retry` *(int)*: Number of seconds to wait to before retrying connection on maintenance mood (default: 3600).

***

### CLI

- `cli.environment.mood` *(string)*: Mood of the CLI environment `[production, development or testing]`.

***

###  Cache & Optimization

- `default.cache.control` *(string)*: Cache control configuration example: `(no-store, max-age=0, no-cache)`.
- `page.caching` *(bool)*: Page caching configuration.
- `page.cache.expiry` *(int)*: Expiry time for page caching.
- `page.minification` *(bool)*: Minification configuration.
- `script.execution.limit` *(int)*: Script execution limit.
- `script.ignore.abort` *(bool)*: Ignore abort configuration.
- `script.output.handler` *(string|null)*: Output compression handler.
- `enable.encoding` *(bool)*: Enable or disable view content encoding and compression.
- `compression.encoding` *(string|bool)*: Compression encoding `(gzip, deflate or false)`.
- `compression.level` *(int)*: Compression level.

***

### Session

- `session.strict.ip` *(bool)*: Strict IP session configuration.

***

### Debugging

- `debug.show.tracer` *(bool)*: Show debug tracer configuration.
- `debug.display.errors` *(bool)*: Display error reporting.

***

### Email SMTP

- `smtp.use.credentials` *(bool)*: Use SMTP credentials configuration.
- `smtp.use.password` *(bool)*: Use SMTP password configuration.
- `smtp.debug` *(bool)*: SMTP debug configuration.
- `smtp.host` *(string)*: SMTP host configuration.
- `smtp.port` *(int)*: SMTP port configuration.
- `smtp.username` *(string)*: SMTP username configuration.
- `smtp.password` *(string)*: SMTP password configuration.
- `smtp.email.sender` *(string)*: SMTP email sender configuration.
- `smtp.encryption` *(string)*: SMTP encryption configuration.
- `smtp.charset` *(string)*: SMTP charset configuration.

***

### Database Connection

- `database.hostname` *(string)*: Database hostname configuration.
- `database.port` *(int)*: Database port configuration.
- `database.charset` *(string)*: Database charset configuration.
- `database.connection.pool` *(bool)*: Database connection pool configuration.
- `database.max.connections` *(int)*: Maximum database connections configuration.
- `database.connection.retry` *(int)*: Database connection retry configuration.
- `database.persistent.connection` *(bool)*: Persistent database connection configuration.
- `database.emulate.preparse` *(bool)*: Emulate preparse configuration.
- `database.connection` *(string)*: Database connection driver configuration `[MYSQLI or PDO]`.
- `database.pdo.driver` *(string)*: PDO driver configuration `[mysql, sqlite, pgsql, cubrid, dblib or oci]`.
- `database.mysql.socket` *(bool)*: Set to true to force the use of database connection socket for mysqli and mysqli database.
- `database.mysql.socket.path` *(bool)*: Set your database connection socket file path _`By default opening mysqli or mysqli  connection in CLI will require the use of socket`._

***

#### Production

- `database.username` *(string)*: Database username for production.
- `database.name` *(string)*: Database name for production.
- `database.password` *(string)*: Database password for production.

***

#### Development

- `database.development.username` *(string)*: Database username for development.
- `database.development.name` *(string)*: Database name for development.
- `database.development.password` *(string)*: Database password for development.

***

#### Sqlite

- `database.sqlite.path` *(string)*: Path for SQLite database for production, example: `writeable/database/production.sqlite`.
- `database.development.sqlite.path` *(string)*: Path for SQLite database for development, example: `writeable/database/production.sqlite`.

***

### Features

- `feature.app.class.alias` *(bool)*: Feature for application class name alias.
- `feature.app.services` *(bool)*: Feature for application services.
- `feature.app.autoload.psr4` *(bool)*: Feature for application psr-4 and class autoloading.
- `feature.app.dev.functions` *(bool)*: Feature for developer procedural function.
- `feature.route.dependency.injection` *(bool)*. Feature for dependency injection type hint on router.  