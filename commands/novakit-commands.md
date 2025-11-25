# Novakit Commands

***

## Overview

Novakit is Luminova’s command-line tool for managing application development tasks, including database operations, sitemap generation, and executing custom console commands via terminal

***

## Introduction

The Luminova **Novakit** command-line tool offers a collection of powerful commands and reserved options for managing application development directly from the terminal. It simplifies tasks like running the development server, generating keys, and handling database operations.

---

## Running Commands

To execute `novakit` commands, run them from your application's **root directory**, for example:

```bash
php novakit command
```

For **controller-related commands**, navigate to the `/public` directory before execution:

```bash
php index.php command
```

> **Note:**
> The `--help` (`-h`) option is reserved for displaying command help messages and **should not** be used for custom arguments when building CLI applications.

---

### Listing Available Commands

To view all available NovaKit commands:

```bash
php novakit list                 # List all novakit and custom commands.
php novakit list --command=db    # List all commands available in specified command group. 
```

---

### View Help Information

NovaKit provides flexible help options for understanding available commands:

**General Help:**

```bash
php novakit --help          # View all novakit commands helps
php novakit --help --all    # View all novakit and custom commands helps
php novakit sitemap --help  # View helps related to a specific command group or command name
```

---

### Output Formatting

Use these flags with commands to modify output formatting.

```bash
php novakit list --help --no-header   # Omits the header from the output
php novakit list --help --no-color    # Disables colored output
php novakit list --help --no-ansi     # Disables color or any ANSI output formatting
```

---

## Basic Generator Commands

NovaKit includes helpful commands to quickly generate boilerplate files for common components in your application—like utility classes, models, controllers, and views.

---

### Create a Utility Class

Generate a class in the `/app/Utils/` directory:

```bash
php novakit create:class "MyClass" \
    --extend "Foo\Bar\BarClassName" \
    --implement "Foo\Interface\FooClassInterface"
```

- `--extend`: (optional) Base class to extend.  
- `--implement`: (optional) Interface to implement.

---

### Creating a Model Class

You can generate a database model class for both MVC and HMVC applications.

**Default Locations:**

* **MVC Application**: Stored in `/app/Models/`
* **HMVC Application**: Stored in `/app/Modules/Models/`
  Use the `--module <ModuleName>` flag to specify a custom module name.

```bash
php novakit create:model "MyModel" \
    --implement "Optional\Interface\FooClassInterface"
```

**Example for a Custom HMVC Module:**

```bash
php novakit create:model "MyModel" --module "Info"
```

---

### Create a Controller Class

Generate a controller in `/app/Controllers/Http/`, or in `/app/Modules/<module>/Controllers/Http/` for HMVC apps:

**For MVC or root HMVC**
```bash
php novakit create:controller "PayStackController" --type "view"
```

**For Custom HMVC Module**

```bash
php novakit create:controller "PayStackController" --type "view" --module="Blog"
```

**Types:**

- `view`: Extends `Luminova\Base\Controller`  
- `command`: Extends `Luminova\Base\Command`  
- `console`: Extends `Luminova\Base\Console`

> **Note:** When using the HMVC design pattern, specify `--module=<moduleName>` to install the controller in the appropriate module directory.  
>
> If the module is not provided, the controller will be installed in the root module controller directory.  
> The module name is case-sensitive and must match the directory name exactly.

---

### Create a View File

Generate a view file in `/resources/Views/`, or in a module path like `/app/Modules/<module>/Views/`:

```bash
php novakit create:view "blog"
```

To create the view inside a subdirectory:

```bash
php novakit create:view "blog" --dir "articles"
```

#### Route URI Prefix Context

To install a new route URI prefix context use the below example command.

```bash
php novakit context "test"
```

> The above command will create a file in `/routes/` directory and update your `/public/index.php` with a new context.
> 
> To disable adding an error handler in `index.php` use flag `--no-error`
> > **Note:** If the error handler is not disabled, you need to manually create the method name in your View errors class in `/app/Errors/Controllers/ErrorController.php`.

---

### Generate Sitemap

Creates a `/public/sitemap.xml` for your website:

```bash
php novakit sitemap
```

> **Configuration:**
> Configure sitemap domain and ignored routes in: `/app/Config/Sitemap.php`

---

### Generate Application Key

Creates a secure encryption/decryption key for your application:

```bash
php novakit generate:key               # Generate application encryption key and write/update to env file, under app.key variable name
php novakit generate:key --no-save     # To generate the key without writing it to your `.env` file
php novakit generate:key --length=36   # To generate the key with a specific length
php novakit generate:key --prefix=sk-  # To generate the key with a key prefix
```

> **Note:**
> 
> When using the **OpenSSL** encryption handler, the key length defaults to the application’s encryption method. 
> Specifying a custom length does **not** apply if the default handler is **Sodium**.

---

### Managing Environment Variable

Add, update or delete an environment variable `.env` file:

```bash
php novakit env:add --key="MY_KEY" --value="Value"            # Add/update variable name `MY_KEY`
php novakit env:add --key="MY_KEY" --value="Value" --no-save  # Add/update for runtime only
php novakit env:remove --key="MY_KEY"                         # Deletes a key from your `.env` file:
```

---

### Build Project for Deployment

Packages your application for production:

```bash
php novakit build:project --type zip
```

> Supported types: `zip`, `tar`, or `none` (default).

---

### Managing Logs

NovaKit allows you to view and manage application log files by specifying the log level you want to interact with (e.g., `debug`, `error`, `info`).

```bash
php novakit log --level=debug                       # Display 5 most recent log entries from `debug`
php novakit log --level=debug --start=5 --end=20    # Display log entries from line `5` to `20`
php novakit log --level=debug --clear               # Clear all log entries from `debug`
```

> **Note:** 
> Replace `debug` with the appropriate log level for your application. Supported levels typically include `info`, `error`, `warning`, `debug`, etc., depending on your configuration.

---

### Global Command Options

```bash
-h, --help        # Displays help information for the Novakit CLI or Routable commands.
-v, --verbose     # Increases output verbosity (use -v multiple times for more detail).
--no-header       # Suppresses Novakit CLI headers.
--no-color        # Disables colored output.
--no-ansi         # Disables all ANSI formatting.
--system-info     # Displays full system and environment information.
```

---

## Available Command Groups

### List Commands

```bash
php novakit list      # Lists all available Novakit commands and their descriptions.
```

### Server Commands

Start the Luminova PHP development server. Optionally bind it to a specific hostname or use the machine's local network address for testing on other devices.

```bash
php novakit server             # Starts the PHP development server with default host and port (localhost:8080).
php novakit server --host      # Bind the server to a specific hostname or address.
php novakit server --port      # Set the port for the development server. Defaults to 8080.
php novakit server --testing   # Bind the server to the machine's local network address for testing on other devices.
```

### App Builder Commands

```bash
php novakit build:project   # Packages or copies required application files for production 
                            # based on 'app.version' configuration.
```

### System Commands

```bash
php novakit generate:key        # Generates a new application encryption key and optionally write key to `.env` file.
php novakit generate:sitemap    # (Deprecated) Use `php novakit sitemap` instead.
php novakit env:add             # Adds or updates an environment variable.
php novakit env:cache           # Generates a cached version of environment variables for production.
php novakit env:setup           # Configures all required variables for a specific environment context.
php novakit env:remove          # Removes an environment variable key from the `.env` file.
```

#### Sitemap Commands

```bash
php novakit sitemap                # Generates an XML sitemap of your website.
php novakit sitemap --broken       # Generates a JSON broken links report.
php novakit sitemap --link-tree    # Generates a TXT link tree report of your website.
```

### Generator Commands

```bash
php novakit create:controller   # Generates a new controller class. Optionally implements an interface, 
                                # creates a template view, or targets a specific module.

php novakit create:view         # Generates a new template view file (".php" or ".tpl") based on the 
                                # template engine settings in `/app/Config/Template.php`.

php novakit create:class        # Generates a new utility class and saves it in `/app/Utils/`.
php novakit create:model        # Generates a new database model extending `Luminova\Base\Model`.
                                # Saved in `/app/Models/` for MVC or `/app/Modules/<Module>/Models/` for HMVC.
```

### Database Commands

```bash
php novakit db:drop         # Drops the migration tracking table.
php novakit db:clear        # Clears migration and seeder lock files.
php novakit db:alter        # Alters database migration tables or columns.
php novakit db:truncate     # Truncates a database table, removing all records.
php novakit db:seed         # Executes registered seeders.
php novakit db:migrate      # Runs database table migrations.
```

### Task Queue Worker Commands

```bash
php novakit task:init        # Initializes the task queue system and creates its database table.
php novakit task:deinit      # Drops the task queue table and removes all tasks.
php novakit task:queue       # Adds a new task to the queue.
php novakit task:list        # Lists tasks with optional filters.
php novakit task:export      # Exports all tasks, optionally filtered by status.
php novakit task:info        # Displays details for a specific task by ID.
php novakit task:delete      # Deletes a specific task from the queue.
php novakit task:purge       # Clears all tasks matching a given status (e.g., completed).
php novakit task:pause       # Pauses a running or pending task.
php novakit task:resume      # Resumes a paused task.
php novakit task:retry       # Retries a failed task by marking it as pending.
php novakit task:sig         # Sends control signals to the task worker (stop or resume).
php novakit task:status      # Updates the status of a specific task.
php novakit task:run         # Executes queued tasks in a worker loop (supports auto-exit after inactivity).
php novakit task:listen      # Monitors real-time task events written to the task log file.
```

### Cache Commands

```bash
php novakit cache      # Manages system caches — clear, delete by key, or list cache items.
```

### Filesystem Commands

```bash
php novakit clear:caches        # Clears cached pages, database cache files, and route caches.
php novakit clear:routes        # Removes all cached route attribute files.
php novakit clear:storage       # Clears private storage files.
php novakit clear:temp          # Removes temporary files.
php novakit clear:writable      # Clears all files and directories in `/writable`.
```

### Logging System Commands

```bash
php novakit log       # Manage and interact with application log files.
```

### CLI Login Authentication Commands

```bash
php novakit auth      # CLI helper for user authentication using a username and key or password.
```

### Cron Worker Commands (Legacy Task Queue)

```bash
php novakit cron:create     # Registers and locks cron tasks in the cron lock file.
php novakit cron:run        # Executes locked cron jobs.
```

### Context Commands

```bash
php novakit context     # Installs application route contexts or generates routes 
                        # from annotation attributes.
```