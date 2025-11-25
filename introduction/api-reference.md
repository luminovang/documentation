# Framework Components and API Reference

***

## Overview

Throttler class offers a simple and efficient way to control request frequency, helping you prevent abuse and regulate access across your application.

***

## Introduction

Welcome to the Luminova Components and documentation API Reference, a detailed guide to the classes, functions, variables, and constants within the Luminova core libraries and modules.

---

### Core Components

#### Autoload

- [General Bootstrap](/boot/autoload.md): Initializes the framework with essential environment setup and core autoloaders for all application contexts.
- [HTTP Bootstrap](/boot/public.md): Entry point for handling web (HTTP) requests and initializing HTTP-specific services.
- [CLI Bootstrap](/boot/cli.md): Initializes the framework for command-line interface (CLI) usage, loading only CLI-relevant components.
- [Modules Factory](/boot/factory.md): Use factories to manage module creation and dependencies in a modular application architecture.
- [Custom Modules Service](/boot/service.md): Define and manage custom services within the application, facilitating easy module integration.

#### Core Application

- [Abstract Application](/foundation/application.md): Base application class providing lifecycle methods, context handling, and core bindings.
- [Abstract Functions](/foundation/functions.md): Core helper functions for global utilities used throughout the framework.
- [Abstract Database Backup Connections](/foundation/databases.md): Class for managing automated database connection fallbacks.
- [Abstract Service Loader](/foundation/services.md): Service container logic for managing and injecting dependencies across the application.
- [Abstract Cronjob Tasks](/foundation/cron-queue.md): Scheduler foundation for defining and executing recurring tasks.

#### Configuration

- [AI Models Configurations](/configs/ai-models.md): Define settings for AI model providers, access tokens, and default model behavior.
- [API Requests Configurations](/configs/api.md): Set request handling rules, allowed headers, API restrictions and more.
- [Browser Requests Configurations](/configs/browser.md): Configure headers, user-agent detection, and browser-specific behaviors.
- [Client-Side Cookie Configurations](/configs/cookie.md): Manage how cookies are sent, stored, and secured on the client side.
- [Cronjob Tasks Configurations](/configs/cron-tasks.md): Define scheduled task intervals, execution rules, and handlers.
- [CSP Configurations](/configs/csp.md): Set up Content Security Policies for controlling allowed sources and scripts.
- [Cryptography Configuration](/configs/encryption.md): Manage encryption driver, hashing algorithms, and cryptographic behaviors.
- [Filesystem Configurations](/configs/file-permission.md): Configure file storage paths, permissions, and access rules.
- [IP Address Configurations](/configs/ip-filters.md): Control trusted IPs, blacklist/whitelist settings, and proxy detection API.
- [Mailer Configurations](/configs/mailer.md): Set email transport methods, SMTP credentials, and message defaults.
- [Autoload Modules](/configs/module.md): Register and enable modules and name alias that should be loaded at runtime.
- [Logger Configurations](/configs/logger.md): Define log handler, auto-backup limit, default email template, and more.
- [Backend Session Configurations](/configs/session.md): Handle session cookies, storage backends, and expiry rules.
- [Request Security Configurations](/configs/security.md): Apply validation rules, CSRF protection, and request filters.
- [Database Fallback Configurations](/configs/database.md): Set up credentials fallback database connection if default fails.
- [Service Registry Configurations](/configs/service.md): Configure service modules and their initialization rules.
- [Sitemap Generator Configurations](/configs/sitemap.md): Set generation frequency, default URLs, and robot directives.
- [SCO Schema Configurations](/configs/structured-data.md): Define structured data (schema.org) templates and customization settings.
- [Cloud Storages Configurations](/configs/storage.md): Set credentials and settings for S3, Azure, or other cloud storage providers.
- [Template Rendering Configurations](/configs/template.md): Manage view rendering engines, paths, and caching behavior.

#### Variables, Global Functions and Constants

- [Environment Variables](/global/env-variables.md): Defines runtime environment variables for controlling app behavior and deployment modes.
- [Constants Variables](/global/constants.md): Lists built-in framework constants used for system configuration and logic branching.
- [Global Procedural Functions](/global/functions.md): Collection of helper functions accessible globally across the application.

#### Routing

- [System Routing](/routing/system.md): Describes the default URL routing engine and how it maps requests to handlers.
- [Method Route Attribute](/attributes/route.md): Attach specific route paths directly to controller methods using attributes.
- [Class Prefix Route Attribute](/attributes/uri-prefix.md): Limit a shared URI prefix for all routes within a controller class.
- [Class Error Route Attribute](/attributes/cli-group.md): Set a custom error handler for route-specific `404` at the class level.
- [Routing Placeholder Patterns](/routing/dynamic-uri-placeholder.md): Customize route parameters using defined pattern-based placeholders.
- [Route Method-Based URI Prefixing](/routing/uri-prefix.md): Use methods to programmatically define URI limit prefixes.
- [URI Segment Util](/routing/url-paths.md): Utility for accessing parsed URI segments, paths, and base URLs.
- [Routing Method Dependency Injection](/routing/dependency-injection.md): Inject dependencies directly into route method parameters.
- [Routing Examples](/examples/http-routing-integration.md): Practical routing examples for common use cases and advanced routing scenarios.

#### Controllers

- [Abstract Base HTTP Controller](/controllers/http-controller.md): Core controller class for handling web (HTTP) requests with routing and response tools.
- [Abstract Base CLI Controller](/controllers/cli-command-controller.md): Base class for CLI commands, providing argument parsing and execution structure.
- [Abstract Base Database Model](/base/database-model.md): Defines database interaction logic, including query building and model behavior.
- [Abstract Base Configuration](/base/config.md): Allows access and manipulation of configuration data in a structured way.
- [Abstract Base Email Template](/base/mailer.md): Base class for generating and sending templated emails using configured transports.
- [Abstract Base Session Handler](/base/session-handler.md): Custom session management class for handling backend session storage and behavior.

#### Templating

- [View Rendering](/templates/views.md): Manage rendering of view files and passing of data to templates.
- [Response Handling](/templates/response.md): Encapsulate view output, content types, and status codes in response objects.
- [Core PHP Template](/templates/layout.md): Use native PHP for layout rendering with basic inheritance and sectioning.
- [Smarty Template](/templates/smarty.md): Integrate and use the Smarty templating engine for layout features.
- [Twig Template](/templates/twig.md): Render templates using the Twig engine with logic separation.
- [Template Examples](/examples/template-view-integration.md): Examples showcasing how to structure and render views with different engines.

---

### Data Management

#### Database

- [Database Drivers](/database/drivers.md): Overview of supported database engines like MySQL, SQLite, SQL Server, and Oracle.
- [Database Connection](/database/connection.md): Class for establishing and managing database connections.
- [Database Builder ORM](/database/query-builder.md): Fluent ORM for building and executing SQL queries.
- [Database Backup and Export](/database/manager.md): Tools for exporting and backing up databases.
- [Database Migration](/database/migration.md): Define and run versioned schema changes with `up/down` methods.
- [Database Seeding](/database/seeding.md): Seed your database with initial or test data for development and testing.
- [Database Schema (Blueprint)](/database/schema.md): Blueprint system for defining and modifying table structures.
- [Database Version Control](/database/version.md): Track and manage database versioning and change history.
- [Database Examples](/examples/database-integration.md): Practical usage examples for queries, models, migrations, and more.

#### Caching

- [Filesystem Cache](/cache/file.md): Store cache data on disk for persistence across requests.
- [Memory Cache](/cache/memory.md): Use in-memory storage (e.g., Memcached) for fast, volatile caching.
- [PSR Cache Pool](/cache/psr-cache-pool.md): PSR-6 compatible cache pool for flexible and interoperable caching.
- [PSR Simple Cache](/cache/psr-simple-cache.md): PSR-16 simple cache interface for lightweight, standardized caching.
- [Static View Content](/cache/static-html-cache.md): Cache rendered views and content fragments to reduce rendering time.
- [Cache Examples](/examples/cache-integration.md): Example scenarios for using different caching strategies effectively.

#### Storage and Filesystem Management

- [Cloud Storages](/files/cloud-storages.md): Manage cloud-based storage providers like S3 and Azure Blob.
- [File Management Helpers](/files/file-manager.md): Utilities for handling files, paths, permissions, and metadata.
- [Private File Delivery](/files/file-delivery.md): Secure delivery of private or protected files using File Delivery Manager (FDM).
- [File Uploader](/files/file-uploader.md): Handle secure and efficient file uploads with validation and destination rules.
- [PSR Data Stream](/files/data-stream.md): Stream data using PSR-7/PSR-17 compliant interfaces for file and network IO.

---

### Web & HTTP

#### Incoming HTTP Requests

- [Incoming Request](/http/request.md): Handles and parses incoming HTTP request data including methods, inputs, and URI.
- [File Upload Object](/http/files.md): Encapsulates uploaded files, including validation, size, and MIME type handling.
- [Request Headers](/http/headers.md): Access and manage HTTP headers sent with client requests.
- [Request User-Agent](/http/user-agent.md): Extract and analyze the client's browser or device user-agent string.
- [Server Properties](/http/server.md): Read server-level information like host, port, protocol, and environment.
- [HTTP Status Codes](/http/status.md): Reference for standard HTTP status codes used across requests and responses.
- [PSR URIInterface Class](/http-client/uri-object.md): PSR-7 compliant URI class for parsing and manipulating request URIs.

### Outgoing Network Request

- [Outgoing Request](/http-client/network.md): Make HTTP requests to external APIs or services with customizable options.
- [Request Clients](/http-client/novio.md): Reusable client class for handling external API calls (e.g, `Novio`, `Guzzle`).
- [Request Client Config Options](/http-client/options.md): Configuration options for fine-tuning outgoing request behavior.
- [PSR Response Message](/http-client/response.md): Class representing HTTP responses from remote servers, with status, headers, and body.

#### Cookies and Sessions

- [Client-Side Cookie Class](/cookies/cookie.md): Create and manage client-side cookies with options for security and expiration.
- [Client Cookie Examples](/examples/cookie-integration.md): Real-world examples of how to use and structure cookie data.
- [Cookie File Jar](/cookies/cookie-file-jar.md): Store cookies persistently in a file-based "jar" across HTTP requests.
- [Backend Session Class](/sessions/session.md): Handle server-side session management with advanced control.
- [Database Session Handler](/sessions/database-handler.md): Store session data securely in a database backend.
- [Filesystem Session Handler](/sessions/file-handler.md): Persist sessions to the filesystem with configurable paths.
- [Backend Session Examples](/examples/session-integration.md): Practical examples demonstrating backend session operations and configurations.

#### Security and Encryption

- [CSRF Protection](/security/csrf.md): Prevent cross-site request forgery attacks by generating and validating CSRF tokens.
- [TOTP Authenticator](/security/totp.md): Time-based One-Time Password (TOTP) system for two-factor authentication.
- [TOTP Google Client](/security/totp-client.md): Google authenticator client integration for verifying and submitting TOTP codes.
- [Input Validation (Rule-Based)](/security/validation.md): Rule-based system for validating incoming data with custom rules.
- [JWT Authentication](/security/jwt.md): Secure user authentication using JSON Web Tokens with expiration and claim support.
- [Rate Limiter](/security/rate-limiter.md): Throttle incoming requests to protect endpoints from abuse and overload.
- [Security Examples](/examples/security-integration.md): Code examples demonstrating various security features and use cases.
- [Crypter Integration](/encryption/crypter.md): Simple encryption/decryption layer for strings and sensitive data.
- [Encryption Drivers](/encryption/driver.md): Driver-based encryption using OpenSSL, Sodium, or custom implementations.
- [Encryption Examples](/examples/encryptions-integration.md): Sample usage of cryptographic functions for secure data handling.

#### Languages

- [Translation Integration](/languages/translate.md): Manage and load multilingual content using key-based language files.

#### SEO Optimization

- [Schema Object Integration](/website-optimizations/seo-structured-data.md): Enhance webpage SEO with structured data using schema.org vocabulary.
- [Sitemap Generator](/website-optimizations/sitemap-xml-generator.md): Automatically generate XML sitemaps to improve site indexing and discoverability.
- [SEO Examples](/examples/seo-integrations.md): Sample implementations for structured data, meta tags, and sitemaps.

---

### Utilities & Helpers

#### Utility Classes

- [Event Emitter](/utilities/events.md): Manage and dispatch custom events with observers for asynchronous or synchronous operations.
- [Async & Task Queue](/utilities/async-tasks.md): Handle asynchronous tasks in a queued system for better flow control and prioritization.
- [Fiber Async & Await](/utilities/async-await-fiber.md): Leverage PHP fibers for async programming with `await` and `async` feature.
- [Promise Object](/utilities/promise-object.md): Work with promises for deferred execution and handling asynchronous results.
- [Pipeline Chain Execution](/utilities/pipeline.md): Chain a series of operations or tasks into a single flow for cleaner code.
- [Async Timeout Intervals](/utilities/async-timeout-interval.md): Schedule and manage timed asynchronous operations with delay and interval handling.
- [Command Process Executor](/utilities/process.md): Execute and manage system commands or processes.
- [Array Util](/utilities/array-helper.md): Helper functions for common array operations like filtering, mapping, and merging.
- [Lazy Load Object](/utilities/lazy-object.md): Implement lazy-loaded objects that only initialize when accessed, improving performance.
- [String Listifier](/utilities/string-listification.md): Manipulating strings for searching, storing or converting to array.
- [IP Address](/utilities/ip-addresses.md): Utilities for handling IP addresses, including validation and conversion.
- [Basic Math](/utilities/math-helpers.md): Simple mathematical functions such as addition, subtraction, and rounding.
- [Basic Helper Methods](/utilities/common-functions.md): A collection of basic utility functions for common operations.
- [Input Escaper](/utilities/input-escaper.md): Safely escape user inputs to prevent injection attacks and ensure proper encoding.
- [SMTP & Email Template](/mailer/email.md): Handle SMTP connections and email template rendering for sending emails.
- [OpenAI Model Integration](/ais/openai.md): Integrate OpenAI's models for natural language processing tasks.
- [AI Model Helper](/ais/model.md): Utilities to facilitate the integration and usage of custom AI models.

#### Date and Time

- [Date & Time Immutable](/datetime/time.md): Immutable date and time object with full support for time zone manipulation and formatting.
- [Time-Based Tasks](/datetime/task.md): Utilities for checking time-based tasks (e,g, `isOpen(data-time)`, `expired(...)`) etc.

#### HTML and XHTML Builder

- [Base Document Builder](/documents/base-builder.md): Build the structure of a basic HTML or XHTML document.
- [XML/XHTML Builder](/documents/xhtml.md): Create XML or XHTML documents with full support for namespaces, attributes, and elements.
- [XML/XHTML Builder Examples](/examples/xhtml-builder.md): Practical examples of how to use the XML/XHTML builder for common tasks.
- [Input Form Builder](/documents/form-inputs.md): Generate form input fields, such as text, radio, and select elements, with custom attributes.
- [Input Form Builder Examples](/examples/html-form-inputs-builder.md): Example code demonstrating how to build input forms using the form builder.

#### Logging

- [Static Logger Class](/logging/logger.md): A logging class that offers static methods for writing logs to various destination, including `telegram` and `email`.
- [Aware Logger](/logging/aware-logger.md): A logger that integrates with different parts of the system, capturing detailed information about context and behavior.
- [NovaLogger Class](/logging/nova-logger.md): A specialized logger class designed to provide advanced features such as backup, performance login and more.
- [Log Levels](/logging/levels.md): Defines the different levels of logging.

#### Tasks

- [CronJob Tasks Scheduling](/task-queue-system/cron-scheduling.md): Schedule tasks to run at specified intervals, using cron-like syntax for flexibility.
- [CronJob Tasks Execution](/task-queue-system/cron-task-execution.md): Manage the execution of scheduled cron jobs, including handling errors and retries.

#### Notifications

- [Firebase Cloud Messaging](/notifications/firebase.md): Integrate Firebase Cloud Messaging to send push notifications to web and mobile clients.
- [Cloud Message Model](/notifications/message.md): A model for structuring messages to be sent through various notification services.
- [Notification Examples](/examples/push-notification-integration.md): Example use cases for different notification types and sending methods.

---

### Architecture & Interfaces

#### HMVC

- [HMVC Design Pattern](/introduction/hmvc-design.md): Understand the Hierarchical Model-View-Controller (HMVC) design pattern for building modular and scalable applications.

#### Interface and Exception Classes

- [Interface Classes](/interface/classes.md): A guide to the use of interface classes for defining contracts and ensuring consistent implementation.
- [Exception Handling & Classes](/error-handlers/exceptions.md): Learn how exceptions are handled in Luminova, with a focus on custom exception classes and error management.

#### Error Handling and Debugging

- [Exception & Error Codes](/error-handlers/error-codes.md): A reference for understanding and utilizing error codes within the Luminova framework to handle issues effectively.
- [Global Error Controller](/error-handlers/error-view-controller.md): Centralized error management for catching and displaying errors throughout your application.
- [Application Debugging](/running/debugging.md): Tools and strategies for debugging your application, including enabling debug mode and inspecting logs.

---

### Developer Tools

#### Command Line Tools

- [Novakit Commands](/commands/novakit.md): Access a collection of commands in the Novakit toolset for enhancing command-line development.
- [PHP Development Server](/running/local-php-server.md): Running a built-in PHP development server for local application testing and debugging.
- [Command Helper Class (Terminal)](/commands/terminal.md): A class providing utility methods for working with the terminal, including handling input/output.
- [Command Text Util](/commands/text-util.md): Utilize text utilities for formatting and managing output within command-line applications.
- [Command Color Util](/commands/color-util.md): Enhance command-line output with color formatting for better readability and user interaction.
- [Command Ascii Image Art](/commands/image-util.md): Generate ASCII art images for visual representation within command-line applications.
- [Command Examples](/examples/novakit-command-usages.md): Practical examples showcasing how to use the available command-line tools and utilities effectively.

#### Real-Time Communication

- [HTTP Socket Server](/http-socket/server.md): Set up and manage an HTTP socket server for real-time communication within web applications.
- [HTTP Socket Server Examples](/examples/http-socket-server-integration.md): Example implementations and use cases for the HTTP socket server in real-time applications.