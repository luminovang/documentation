# Management of Cron Job Execution with Nova-Kit

***

## Overview

Setting up cron job execution in production using novakit commands, you can automate task execution and ensure your application runs smoothly without manual intervention.

***

## Introduction

To start executing your cron scheduled tasks with Luminova's cron manager, you can use any of the following cron commands. Ensure that the `PHP` execution path is set to `PHP8` or above. If you are using `cPanel`, it has a PHP execution binary for PHP 8, which can be used with the following path: `/usr/local/cpanel/3rdparty/bin/php`.

***

### Example Commands

Replace `/home/path/to/project/` with the actual path to your project and set the actual path to your PHP binary execution file.

### Using PHP Default Path

```bash
* * * * * php /home/path/to/project/novakit cron:run >> /dev/null 2>&1
```

This command runs the `cron:run` command every minute, redirecting output to `/dev/null` to suppress any output.

### Executing in Project Directory

This command navigates to the project directory before running the `cron:run` command. This can be useful if your command depends on being executed from within the project directory.

```bash
* * * * * cd /home/path/to/project/ && php novakit cron:run >> /dev/null 2>&1
```

***

### Using cPanel's 3rdParty PHP Binary

This command specifies the PHP binary provided by cPanel to ensure the correct PHP version is used.

```bash
* * * * * /usr/local/cpanel/3rdparty/bin/php /home/path/to/project/novakit cron:run >> /dev/null 2>&1
```

***

### Using a Default cPanel PHP Binary

This command specifies a custom PHP binary, which is useful if you have multiple PHP versions installed on your server.

```bash
* * * * * /usr/local/bin/php /home/path/to/project/novakit cron:run >> /dev/null 2>&1
```

***

### Logging Output to a File

This command redirects the output of the `cron:run` command to a log file, which is useful for debugging and monitoring your cron jobs for any uncaught errors.

```bash
* * * * * /usr/local/bin/php /home/path/to/project/novakit cron:run >> /home/path/to/project/writeable/log/debug.log 2>&1
```

***

### Force Job Recreation

The `--force` flag in Luminova's cron scheduling allows you to force the creation of services and renew the lock file each time the cron job is executed. This can be useful when you have added new services to your cron configuration and need to ensure they are recognized and executed without manually deleting the lock file or running `novakit cron:create --force` command.

This command runs the `cron:run` command every minute with the `--force` flag to ensures that the services are recreated each time the cron job is executed, and the lock file is renewed. 

```bash
* * * * * /usr/local/bin/php /home/path/to/project/novakit cron:run --force >> /dev/null 2>&1
```

**Benefits**

- If you have added new services to your cron configuration, using the `--force` flag ensures they are immediately recognized and executed.
- The lock file is renewed with each execution, preventing potential issues with stale lock files.
- Forces the recreation of defined services, which can help in scenarios where service definitions might change dynamically.

**Caution**

While the `--force` flag is useful for ensuring new services are recognized and executed immediately, it should be used with caution. Forcing service recreation and lock file renewal on every execution can increase server load, especially if the cron job is set to run frequently. Use it judiciously based on your application's needs.

***

### Note

- The cron time set in your cPanel or server should be every minute to ensure your jobs are executed properly. However, you can adjust this depending on your use case.
- The time set in the `cron service` method within your application does not affect the cPanel cron time. It is only for executing your commands when they are ready according to your defined schedule.