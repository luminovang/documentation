# Application Debugging and Troubleshooting

***

## Overview

Luminova framework application debugging and profiling. 

***

## Introduction

## Performance Profiling

Luminova includes a built-in performance profiling tool that helps you monitor your application's performance with ease. This feature requires no additional setup or configuration. To enable it, simply set the `debug.show.performance.profiling` variable to `true` in your environment file. 

When activated, a floating profiler will appear at the bottom of each page, displaying key performance metrics. In CLI mode, the profiling information will be printed after a command has been executed, while for `API`, the  profiling information will be logged as JSON using log level `metrics` . 

The profiler provides an overview of the current application's performance, including the total number of files included in the request. It also distinguishes between framework core modules and third-party files, giving you a clear picture of what's contributing to your application's load.

You can expand the profiling details to reveal more information, such as execution time, memory usage, and other critical metrics. This detailed insight helps you identify potential bottlenecks and optimize your application for better performance.

### Web Profiling Example
![Web Performance Profiling](https://luminova.ng/assets/images/debugger.png)

### CLI Profiling Example

![CLI Performance Profiling](https://luminova.ng/assets/images/debugger-cli.png)

> **Tip:** Use the profiling tool during development to continuously monitor and improve the efficiency of your code. By addressing performance issues early, you can ensure a smoother and faster experience for your users in production.

***

## Maintenance Mode

When performing debugging or updates on your application, particularly in a production environment, you may need to display a custom maintenance page to visitors. This informs them that the application is temporarily unavailable due to maintenance. 

To activate maintenance mode, simply set the `app.maintenance.mode` variable to `true` in your environment file. Once enabled, the maintenance page will be displayed to visitors instead of the actual content.

### Customizing Maintenance Mode

You can also specify how often the client's browser should refresh to check if the service has been restored by setting the `app.maintenance.retry` variable to the desired number of seconds. This feature is helpful for ensuring that users can automatically return to your site as soon as maintenance is completed.

For a more tailored experience, you can customize the maintenance page to match your application's design and needs. This can be done by modifying the `/resources/Views/system_errors/maintenance.php` file. 

> **Important:** When customizing the maintenance page, ensure that any modules or resources it depends on are available during maintenance. This prevents errors from being displayed instead of your intended message.

***

## Error Display

![Error Debuging Display](https://luminova.ng/assets/images/debugger-error.png)

Luminova provides robust error handling that logs errors and displays a custom error page if a fatal error causes the application to shut down. This approach is recommended as it offers detailed insights into the error, including stack traces, making it easier to diagnose and resolve issues.

### Custom Error Page

**Production:** The custom error page provides a user-friendly way to inform visitors that something has gone wrong, while also helping you maintain a professional appearance even during critical issues. This feature is especially useful in production environments, where displaying raw error messages could expose sensitive information.

**Development:** The custom error page, provides a detailed error information, including stack traces, which can be crucial for debugging.

### Disabling Custom Error Pages

If you prefer to have errors printed directly on the page, bypassing the custom error page, you can easily disable this feature. To do so, set the `debug.display.errors` variable to `true` in your environment configuration. When enabled, this setting will cause errors to be displayed directly in the browser, which may be useful during development but should be handled with caution in production.

> **Note:** Disabling the custom error page may expose sensitive information to users. It's recommended to keep this feature enabled in production environments for security reasons.