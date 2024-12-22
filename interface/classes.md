# Adhering to Class Interface Contract Rules

***

## Overview

Luminova Interfaces are fundamental for establishing standardized contracts between modules, enabling seamless integration and consistent functionality across different parts of your application. 

***

## Introduction

Interfaces define clear communication contracts between components in object-oriented programming, playing a critical role in building robust, maintainable systems. They ensure consistency across an application by enforcing specific structures and data types.

All available interfaces in Luminova are located in the `/system/Interface/` directory and share a common namespace prefix: `Luminova\Interface\`. These interfaces provide a unified structure for interaction across various components, ensuring consistency and flexibility throughout the framework.

***

### Interface Implementation

In Luminova, interfaces are used to define contracts for classes that implement them. 
By implementing an interface, a class agrees to provide specific functionality defined by that interface. This allows for better structure and flexibility in your code, allowing extending capabilities.

```php
<?php
use Luminova\Interface\FooInterface;

class MyClass implements FooInterface
{
    // Implement the abstract methods as defined in FooInterface
}
```

> **Note:** You must implement all the methods declared in the interface, ensuring that your class adheres to the contract specified by the interface.

---

### ExceptionInterface

**Namespace:** `Luminova\Interface\ExceptionInterface`

This interface acts as a foundational contract for custom exception classes, standardizing exception handling and error logging throughout the application.

---

### ErrorHandlerInterface

**Namespace:** `Luminova\Interface\ErrorHandlerInterface`

This interface specifies the type of controller class used in the router for managing errors. All error-handling controller classes must implement this interface.

---

### CookieInterface

**Namespace:** `Luminova\Interface\CookieInterface`

Interface for managing HTTP client-side cookies, providing methods to create, read, update, and delete cookies.

---

### CookieJarInterface

**Namespace:** `Luminova\Interface\CookieJarInterface`

This interface defines the contract for the implementing HTTP cookie file jar.

---

### DatabaseInterface

**Namespace:** `Luminova\Interface\DatabaseInterface`

This interface defines methods for establishing and managing database connections, preparing and executing SQL statements, and fetching query results. 
It serves as a contract for implementing various database drivers.

---

### ConnInterface

**Namespace:** `Luminova\Interface\ConnInterface`

This interface specifies the type of database connection class used (e.g., PDO or MySQLi). 
By creating a unique interface that both database connection objects extend, it promotes flexibility and abstraction, allowing for interchangeable implementations while adhering to a common contract.

---

### EncryptionInterface

**Namespace:** `Luminova\Interface\EncryptionInterface`

Interface for encryption and decryption drivers, defining methods for encrypting data with various cryptographic algorithms and keys, as well as decrypting encrypted data.

---

### ClientInterface

**Namespace:** `Luminova\Interface\ClientInterface`

This interface defines methods for HTTP client drivers (e.g., Guzzle and cURL), enabling the sending of network requests and manipulation of responses.

---

### NetworkInterface

**Namespace:** `Luminova\Interface\NetworkInterface`

Interface for an HTTP network manager, providing methods for establishing network requests, handling asynchronous requests, sending and receiving data over various HTTP methods, and managing network-related errors.

---

### RequestInterface

**Namespace:** `Luminova\Interface\RequestInterface`

This interface defines the contract for handling HTTP request data within the Luminova framework. Implementations of this interface are responsible for encapsulating the details of incoming HTTP requests, such as request methods, headers, query parameters, and payloads.

---

### ResponseInterface

**Namespace:** `Luminova\Interface\ResponseInterface`

Defines the contract for handling HTTP client responses in Luminova. This interface supports implementations for both `Luminova\Http\Client\Curl` and `Luminova\Http\Client\Guzzle` clients.

---

### PromiseInterface

**Namespace:** `Luminova\Interface\PromiseInterface`

The contract for promise-based implementations in Luminova, including `Luminova\Utils\Promise\Promise`, `Luminova\Utils\Promise\FulfilledPromise`, and `Luminova\Utils\Promise\RejectedPromise` classes.

---

### ViewResponseInterface

**Namespace:** `Luminova\Interface\ViewResponseInterface`

The interface defines the methods for rendering and managing views in response to HTTP requests. This interface handles the final output that will be sent back to the client, typically as `HTML`, `JSON`, `Media`, `XML`, `TEXT` and more based on the content that needs to be rendered.

---

### MailerInterface

**Namespace:** `Luminova\Interface\MailerInterface`

This interface outlines methods for composing and sending emails, managing attachments, specifying recipient and sender addresses, and configuring email delivery options.

---

### ServicesInterface

**Namespace:** `Luminova\Interface\ServicesInterface`

Interface for defining application services, specifying methods for initializing, configuring, and managing various business logic services.

---

### SessionManagerInterface

**Namespace:** `Luminova\Interface\SessionManagerInterface`

This interface offers methods for managing backend session storage drivers, including storing and retrieving session data, and handling session expiration and persistence.

---

### ValidationInterface

**Namespace:** `Luminova\Interface\ValidationInterface`

Interface for validating user-submitted input data against predefined rules. It provides methods for defining validation rules, applying them to input data, and retrieving validation results, thereby ensuring data integrity and enforcing validation constraints in applications.

---

### AiInterface

**Namespace:** `Luminova\Interface\AiInterface`

This interface defines methods for creating AI models, with the current implementation being for `OpenAI`.

---

### CallableInterface

**Namespace:** `Luminova\Interface\CallableInterface`

This interface serves as a callback signature for methods that require the Luminova callable interface.

---

### RouterInterface

**Namespace:** `Luminova\Interface\RouterInterface`

This interface allows for the implementation of custom routable classes. By default, Luminova routing handles `BaseController`, `BaseViewController`, and `ErrorHandlerInterface`. 
Classes that do not extend these must implement this interface to ensure routability.

---

### LazyInterface

**Namespace:** `Luminova\Interface\LazyInterface`

The LazyInterface is designed to ensure type compatibility when working with lazy-loaded instances. By implementing this interface, you can maintain type safety while leveraging the benefits of lazy loading.

**Implementation**

To use the `LazyInterface`, implement it in your class:

```php
namespace MyUtils;
use Luminova\Interface\LazyInterface;

class Foo implement LazyInterface{}
```

**Usage Example**

Below is an example demonstrating how to use the` LazyInterface` with the `Luminova\Utils\LazyObject` utility for initializing a lazy-loaded instance:

```php
use Luminova\Interface\LazyInterface;
use MyUtils\Foo;

class SomeClass 
{
    /**
     * Lazy-loaded instance of Foo.
     * 
     * @var Foo<LazyInterface> $foo
     */
    private Foo|LazyInterface|null $foo = null;
		
    public __construct(){
		// Initialize the lazy-loaded instance of Foo.
        $this->foo = Luminova\Utils\LazyObject::newObject(Foo::class);
    }
}
```