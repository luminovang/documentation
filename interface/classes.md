# Comprehensive List of Framework Interface Classes

***

## Overview

Defined clear contracts by implementing the interface, your classes ensure it provides the necessary methods or properties required by the interface, enhancing code maintainability and readability.

***

## Introduction

A clear communication between different components is crucial for building robust and maintainable systems. 
The following interfaces serve as the cornerstone for defining clear contracts, ensuring seamless integration, 
and implementing functionality across various parts of Luminova application.

---

### ExceptionInterface

**`\Luminova\Interface\ExceptionInterface`**

This interface acts as a foundational contract for custom exception classes, standardizing exception handling and error logging throughout the application.

---

### ErrorHandlerInterface

**`\Luminova\Interface\ErrorHandlerInterface`**

This interface specifies the type of controller class used in the router for managing errors. All error-handling controller classes must implement this interface.

---

### CookieInterface

**`\Luminova\Interface\CookieInterface`**

Interface for managing HTTP client-side cookies, providing methods to create, read, update, and delete cookies.

---

### DatabaseInterface

**`\Luminova\Interface\DatabaseInterface`**

This interface defines methods for establishing and managing database connections, preparing and executing SQL statements, and fetching query results. 
It serves as a contract for implementing various database drivers.

---

### ConnInterface

**`\Luminova\Interface\ConnInterface`**

This interface specifies the type of database connection class used (e.g., PDO or MySQLi). 
By creating a unique interface that both database connection objects extend, it promotes flexibility and abstraction, allowing for interchangeable implementations while adhering to a common contract.

---

### EncryptionInterface

**`\Luminova\Interface\EncryptionInterface`**

Interface for encryption and decryption drivers, defining methods for encrypting data with various cryptographic algorithms and keys, as well as decrypting encrypted data.

---

### NetworkClientInterface

**`\Luminova\Interface\NetworkClientInterface`**

This interface defines methods for HTTP client drivers (e.g., Guzzle and cURL), enabling the sending of network requests and manipulation of responses.

---

### NetworkInterface

**`\Luminova\Interface\NetworkInterface`**

Interface for an HTTP network manager, providing methods for establishing network requests, handling asynchronous requests, sending and receiving data over various HTTP methods, and managing network-related errors.

---

### MailerInterface

**`\Luminova\Interface\MailerInterface`**

This interface outlines methods for composing and sending emails, managing attachments, specifying recipient and sender addresses, and configuring email delivery options.

---

### ServicesInterface

**`\Luminova\Interface\ServicesInterface`**

Interface for defining application services, specifying methods for initializing, configuring, and managing various business logic services.

---

### SessionManagerInterface

**`\Luminova\Interface\SessionManagerInterface`**

This interface offers methods for managing backend session storage drivers, including storing and retrieving session data, and handling session expiration and persistence.

---

### ValidationInterface

**`\Luminova\Interface\ValidationInterface`**

Interface for validating user-submitted input data against predefined rules. It provides methods for defining validation rules, applying them to input data, and retrieving validation results, thereby ensuring data integrity and enforcing validation constraints in applications.

---

### AiInterface

**`\Luminova\Interface\AiInterface`**

This interface defines methods for creating AI models, with the current implementation being for `OpenAI`.

---

### CallableInterface

**`\Luminova\Interface\CallableInterface`**

This interface serves as a callback signature for methods that require the Luminova callable interface.

---

### RouterInterface

**`\Luminova\Interface\RouterInterface`**

This interface allows for the implementation of custom routable classes. By default, Luminova routing handles `BaseController`, `BaseViewController`, and `ErrorHandlerInterface`. 
Classes that do not extend these must implement this interface to ensure routability.