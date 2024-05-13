# Interface Classes

***

## Overview

Defined clear contracts by implementing the interface, your classes ensure it provides the necessary methods or properties required by the interface, enhancing code maintainability and readability.

***

## Introduction

A clear communication between different components is crucial for building robust and maintainable systems.

These classes serve as the cornerstone for defining clear contracts, ensuring seamless integration, and implementation across various parts of the application. By establishing well-defined interfaces.

***

### ExceptionInterface

`\Luminova\Interface\ExceptionInterface`

This Interface serves as a base interface for custom exception classes. It provides a common contract for exception handling and logging errors throughout the application.

***

### ErrorHandlerInterface

`\Luminova\Interface\ErrorHandlerInterface`

This interface serves to specify the type of controller class used in router, in other to properly handle `error controllers`, all error handling controller class must implement `ErrorHandlerInterface`.

***

### CookieInterface

`\Luminova\Interface\CookieInterface`

Interface for managing HTTP client-side cookies, offering methods to create, read, update, and delete cookies.

***

### DatabaseInterface

`\Luminova\Interface\DatabaseInterface` 

Interface for database drivers, providing methods for establishing and managing database connections, preparing and executing `SQL` statements, and fetching query results. This interface serves as a contract for implementing various database drivers.

***

### ConnInterface

`\Luminova\Interface\ConnInterface`

This interface serves to specify the type of database connection class used, such as `PDO` or `MYSQLI`. Instead of directly using class names, we create a unique interface that both database connection objects extend. This promotes flexibility and abstraction in code design, allowing for interchangeable implementations while adhering to a common interface.

***

### EncryptionInterface

`\Luminova\Interface\EncryptionInterface` 

Interface for encryption and decryption drivers, defining methods for encrypting data with various cryptographic algorithms and keys, as well as decrypting encrypted data.

***

### HttpClientInterface

`\Luminova\Interface\HttpClientInterface` 

Interface for HTTP clients drivers such as Guzzle and Curl, defining methods to send a network request and manipulate response .
***

### NetworkInterface
`\Luminova\Interface\NetworkInterface` 

Interface for HTTP network manager, providing methods for establishing network requests, asynchronous requests, sending and receiving data over the network using different types of HTTP methods, and handling network-related errors.

***

### MailerInterface
`\Luminova\Interface\MailerInterface` 

Interface for mail clients, outlining methods for composing and sending emails, managing email attachments, specifying recipients and sender addresses, and configuring email delivery options.

***

### ServicesInterface
`\Luminova\Interface\ServicesInterface` 

Interface for defining application services, specifying methods for initializing, configuring, and managing various services of your business logics.

***

### SessionManagerInterface
`\Luminova\Interface\SessionManagerInterface` 

Interface for managing user sessions storage drivers, it offers methods for storing and retrieving session data, and managing session expiration and persistence.

***

### ValidationInterface

`\Luminova\Interface\ValidationInterface` 

Interface for validating user submitted input data against predefined rules, providing methods for defining validation rules, applying rules to input data, and retrieving validation results. This interface helps ensure data integrity and enforce data validation constraints in applications.