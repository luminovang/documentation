# v3.5.6^ Full Changelog

***

## Overview

Provides a detailed record of all changes in Luminova's latest version, updates, and improvements made to the Luminova Framework over time.

***

## Introduction

## Luminova 3.5.6+ Update Overview

Version `3.5.6` introduces significant performance improvements and new features. This update enhances attribute parsing through tokenization when PHP attribute routing is enabled, and introduces an API for implementing CLI console controllers using the `novakit` command-line interface.

---

## New Features

### Introduced in Version 3.5.6

#### [Command Group Attribute](/routing/cli-group-attribute.md)

Added support for the `Group` attribute, which allows CLI controllers to define a command group at the class level.

#### [Incoming HTTP Request](/http/request.md)

New methods added to improve request handling:

* `getMethodOverride()` – Returns the HTTP method override value, if any.
* `getAnyMethod()` – Returns the overridden method if available and the request method is `POST`, otherwise returns the original request method.

#### [Routing System Interface](/routing/url-routing.md)

The routing system now implements `Luminova\Interface\RouterInterface`, allowing full replacement with a custom routing implementation.

#### [Application Base Class](/core/application.md)

New method `getRouterInstance()` added to retrieve router instance for application routing, making it easier to replace the routing system.

#### [Crypter Utility Class](/encryption/crypter.md)

New methods added for validating cryptographic key types and pairs:

* `isPrivateKey()` – Validates whether a given string is a private key.
* `isPublicKey()` – Validates whether a given string is a public key.
* `isKeyMatch()` – Verifies if a private and public key pair matches.

#### HTTP Request Methods

A new class `Luminova\Http\Method` has been added to provide convenient access to HTTP method constants, such as `Method::GET`, `Method::POST`, and others.

#### CLI Controllers Command Limitation

Added support for `$users` property in CLI controllers extending [Luminova\Base\BaseCommand](/base/command.md) or [Luminova\Base\BaseConsole](/base/console.md). This property allow restricting command execution to specific system users.

#### [Command Utility Class (Terminal)](/commands/terminal.md)

The `Terminal` class now includes new methods for retrieving system details and supporting CLI-based authentication:

* `systemInfo()` – Returns structured system and terminal information.
* `getSystemId()` – Generates a consistent, unique system identifier suitable for CLI authentication.
* `getTerminalName()` – Retrieves the name of the current terminal in use.
* `getSystemModel()` – Returns the device or system model (e.g., *MacBook Pro*, *Dell XPS*).
* `getPid()` – Returns the parent process ID (PPID) for the current CLI session.
* `about()` – Displays a formatted table of system and environment information. Also via command `php novakit --system-info`.
* `whoami()` – Returns the current system user executing the CLI script.

---

## Optimizations

### Improved in Version 3.5.6

#### [File Uploader Class](/files/uploader.md)

Improved file upload handling and error validation:

* **Automatic method switching**: When using the `upload()` or `chunk()` methods, files smaller than 5MB with a valid temporary path will automatically use `move()` for better performance.
* **Directory validation**: An exception is thrown if the upload path is not a valid directory.
* **Write permission check**: Uploads fail with a descriptive error if the upload path is not writable.

#### Attribute Parser

Replaced reflection-based parsing with `PHPToken` to enhance performance and reduce overhead.

#### [Route Attributes](/routing/route-attribute.md)

Improved error handling by throwing exceptions when invalid middleware is specified.

#### Routing System

Refined routing behavior to support HTTP method overriding and improve overall routing speed.

#### Boot Autoload

Updated HTTP method spoofing to rely on `X-HTTP-Method-Override` and restrict it to `POST` requests, aligning with [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231).

#### [Response Renderer](/templates/response.md)

Enhanced response rendering for better accuracy and output consistency.

#### [Uploaded File Configuration](/http/file.md)

Replaced the use of `stdClass` with a dedicated `Luminova\Http\FileConfig` configuration object for better structure and clarity.

---

## Renamed Components

### Changes in Naming Since Version 3.5.6

* `Luminova\Application\Foundation` → `Luminova\Luminova`
* `Luminova\Command\Console` → `Luminova\Command\Novakit`
* `Luminova\Command\NovaKit\*` → `Luminova\Command\Consoles\*`
* `App\Controllers\Errors\ViewError` → `App\Errors\Controllers\ErrorController`
* `/resources/Views/server_errors/*` → `/app/Errors/Defaults/*`

---

## Deprecated

### Marked as Deprecated in Version 3.5.6

#### [Command Handler (Terminal)](/commands/novakit.md)

* `explain()` – Deprecated. Use `parse()` instead.

---

## Removed

### Legacy Methods Removed in Version 3.5.6

The following deprecated methods have been removed:

* `Luminova\Http\File::getMimeFromTemp()` – Use `getMimeFromFile()` instead.
* `Luminova\Routing\Router::before()` – Use `guard()` instead.
* `Luminova\Security\Crypter::verify()` – Use `isPassword()` instead.
* `Luminova\Sessions\Session::toExport()` – Use `getResult()` instead.
* `Luminova\Sessions\Session::ssid()` – Use `getToken()` instead.
* `Luminova\Sessions\Session::ssDate()` – Use `getDatetime()` instead.
* `Luminova\Command\Terminal::color()` – Use `Luminova\Command\Utils\Color::apply(...)` or `Color::style(...)` instead.
