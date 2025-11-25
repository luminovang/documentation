# Mailer System

***

## Overview

The Mailer class provides methods to facilitate sending emails. This documentation outlines its methods and their usage.

***

## Introduction

The Luminova **Mailer class** simplifies the process of sending emails. It provides methods to handle various aspects of email composition, such as setting recipients, sender address, message body, attachments, and more.

It also supports third-party PHP mailer libraries like `PHPMailer` and `SwiftMailer`, allowing you to switch between mailer clients without the need to rewrite your code.

***

## Guides 

By default, the `Mailer` class uses a simple email class `NovaMailer` to send emails. If you wish to switch to a more advanced mailer, you can use `PHPMailer` or `SwiftMailer`, which are already implemented in the Luminova framework.

To use another mailer library, you need to initialize the `Mailer` class with your preferred PHP mailer library like `PHPMailer` or `SwiftMailer`, or set your preferred mailer class in `/app/Config/Preference.php`.

Initializing with `PHPMailer`:

```php
use Luminova\Utility\Email\Clients\PHPMailer;

$mailer = new Mailer(PHPMailer::class);
```

Initializing with `SwiftMailer`:

```php
use Luminova\Utility\Email\Clients\SwiftMailer;
$mailer = new Mailer(SwiftMailer::class);
```

> **Note:**
> To use either `PHPMailer` or `SwiftMailer`, you will need to install the package first using Composer:

For PHPMailer: 
```bash 
composer require phpmailer/phpmailer
```

For SwiftMailer: 
```bash 
composer require "swiftmailer/swiftmailer:^6.0"
```

***

### Usage Examples

Creating mailer instance to send email.

```php
// /app/Controllers/Http/MailerController.php
namespace App\Controllers\Http;

use Luminova\Base\Controller;
use Luminova\Utility\Email\Mailer;

class MailerController extends Controller
{
    public function sendEmail(): int
    {
        (new Mailer())->from('system@example.com')
            ->address('peter@example.com')
            //->notification('peter@example.com')
            //->addresses('peter@example.com,john@example.com')
            //->cc('cc@example.com')
            //->bcc('bcc@example.com')
            //->reply('admin@example.com')
            //->isMail()
            //->isHtml()
            //->options()
            //->addHeader(...)
            //->addFile(...)
            //->addFile(...)
            ->subject('Email Hello!')
            ->body('<p>HTML email message body</p>')
            ->text('Alt text email body!')
            ->send();

        // ...
    }
}
```

***

Creating a [Mailer Controller Class](/base/mailer.md).

```php
// /app/Controllers/Http/MailerController.php

namespace App\Controllers\Http;

use Luminova\Base\Controller;
use App\Utils\OrderTemplate;
use Luminova\Utility\Email\Mailer;

class MailerController extends Controller
{
    public function sendEmail(): int
    {
        (new Mailer())
            ->from('system@example.com')
            ->address('peter@example.com')
            //->body(new OrderTemplate()) Optionally set the mail template in body
            ->send(new OrderTemplate());

        // ...
    }
}
```

***

## Class Definition

* Class namespace: `Luminova\Utility\Email\Mailer`
* This class implements:  [Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)

***

## Methods

Learn how to create reusable email template using [Base Email Template Class](/base/mailer.md).

### getInstance

Initialize and retrieve the singleton instance of the Mailer class.

This method ensures that only one instance of the Mailer class is created and provides global access to that instance. If no instance exists, it will  instantiate the Mailer with the provided interface.

```php
public static getInstance(MailerInterface|string|null $interface = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$interface` | **Luminova\Interface\MailerInterface\|string\|null** | The mailer client interface to be used for instantiation. |

**Return Value:**

`Luminova\Utility\Email\Mailer` - Returns the shared singleton instance of the Mailer class.

***

### getClient

Retrieve the Mailer client instance.

This method returns an instance of the mail client in use.

```php
public getClient(): Luminova\Interface\MailerInterface
```

**Return Value:**

`Luminova\Interface\MailerInterface` - Return he Mailer client instance.

**Example:**

```php
$client = $mailer->getClient() // Luminova mailer client interface
    ->getMailer(); // e.g, PHPMailer instance
```

***

### isHtml

Set the email format to HTML or plain text.

This method sets whether the email should be sent as HTML or plain text.
By default, it sends HTML. If you want to send a plain text email, pass `false` to the method.

```php
public isHtml(bool $html = true): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$html` | **bool** | Whether the email should be sent as HTML (default is true). |

**Return Value:**

`self` - Return the Mailer class instance.

***

### isMail

Set the mail transport method to either Mail or SMTP.

This method determines whether to send emails using the PHP `mail()` function or via SMTP. By default, it uses `mail()`. To send emails via SMTP, pass `false` to this method.

```php
public isMail(bool $mail = true): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mail` | **bool** | Whether to use PHP `mail()` (default is true). Set to `false` to use SMTP. |

**Return Value:**

`self` - Return the Mailer class instance.

***

### to

Send email to a single address.

```php
public static to(string $address): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string** | Email address to send email to. |

**Return Value:**

`static` - Return new static mailer class instance.

**Throws:**

- [Luminova\Exceptions\MailerException](/error-handlers/exceptions.md#mailerexception) - Throws if error occurred while sending email.

***

### address

Add an email address to the recipient list.

```php
public address(string $address, string $name = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string** | The email address. |
| `$name` | **string** | The recipient's name (optional). |

**Return Value:**

`self` - Return the Mailer class instance.

***

### addresses

Add one or more email addresses to the recipient list.

This method supports various input formats, including a comma-separated string, a numeric array of addresses, or an associative array of name-email pairs.

```php
public addresses(string|array $address): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string\|array<string\|int,string>** | A single email string, an array of emails, or an array of name => email pairs. |

**Return Value:**

`self` - Return the Mailer class instance.

**Add multiple addresses:**

```php
// as comma-separated string
$mailer->addresses('example@gmail.com,example@yahoo.com');

// as an indexed array
$mailer->addresses([
    'example@gmail.com',
    'example@yahoo.com'
]);

// as associated names
$mailer->addresses([
    'John' => 'john@gmail.com',
    'Deo' => 'deo@yahoo.com'
]);
```

***

### addHeader

Add a custom header to the email.

The header is specified by a key (the header name), and the optional value (the header's value). If no value is provided, the header will be added without a value.

```php
public addHeader(string $name, ?string $value = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The header name. |
| `$value` | **string\|null** | The header value (optional). |

**Return Value:**

`self` - Return the Mailer class instance.

***

### reply

Add a reply-to address.

```php
public reply(string $address, string $name = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string** | The email address. |
| `$name` | **string** | The recipient's name (optional). |

**Return Value:**

`self` - Return the Mailer class instance.

***

### notification

Set the notification address for read and delivery receipts.

This method allows you to specify an email address where notifications should be sent regarding the status of the email, such as delivery or read receipts.

```php
public notification(string $address): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string** | The email address to receive the notification. |

**Return Value:**

`self` - Return the Mailer class instance.

***

### cc

Add an email address to the recipient list.

```php
public cc(string $address, string $name = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string** | The email address. |
| `$name` | **string** | The recipient's name (optional). |

**Return Value:**

`self` - Return the Mailer class instance.

***

### bcc

Add an email address to the recipient list.

```php
public bcc(string $address, string $name = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string** | The email address. |
| `$name` | **string** | The recipient's name (optional). |

**Return Value:**

`self` - Return the Mailer class instance.

***

### from

Set the email sender's address.

```php
public from(string $address, string $name = '', bool $auto = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string** | The email address. |
| `$name` | **string** | The sender's name (optional). |
| `$auto` | **bool** | Whether to automatically add the sender's name (optional). |

**Return Value:**

`self` - Return the Mailer class instance.

***

### body

Sets the body of the email message.

```php
public body(Luminova\Base\Mailer|string $message): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **Luminova\Base\Mailer&#124;string** | The body content of the email. |

**Return Value:**

`self` - Return the Mailer class instance.

***

### text

Sets the text alternative body of the email message.

```php
public text(string $message): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **string** | The alternative body content of the email. |

**Return Value:**

`self` - Return the Mailer class instance.

***

### subject

Sets the subject of the email message.

```php
public subject(string $subject): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$subject` | **string** | The subject of the email. |

**Return Value:**

`self` - Return the Mailer class instance.

***

### options

Set SMTP stream options.

This method allows you to define custom stream context options for the SMTP connection. It can be used to configure SSL/TLS behavior, such as disabling peer verification or allowing self-signed certificates.

```php
public options(array $smtpOptions): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$smtpOptions` | **array** | An associative array of stream context options. |

**Return Value:**

`self` - Return the Mailer class instance.

**Example:**
```php
$mailer->options([
    'ssl' => [
        'verify_peer' => false,
        'verify_peer_name' => false,
        'allow_self_signed' => true,
    ]
]);
```

***

### addFile

Add an attachment from a path on the filesystem.

```php
public addFile(
    string $path, 
    string $name = '', 
    string $encoding = 'base64', 
    string $type = '', 
    string $disposition = 'attachment'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | Path to the attachment |
| `$name` | **string** | Overrides the attachment name |
| `$encoding` | **string** | File encoding (see $Encoding) |
| `$type` | **string** | MIME type, e.g. `image/jpeg`; determined automatically from $path if not specified |
| `$disposition` | **string** | Disposition to use |

**Return Value:**

`self` - Return the Mailer class instance.

**Throws:**

- [Luminova\Exceptions\MailerException](/error-handlers/exceptions.md#mailerexception) - Throws if error occurred while sending email.

***

### send

Send the email.

```php
public send(Luminova\Base\Mailer|string|null $message = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **Luminova\Base\Mailer&#124;string&#124;null** | Optionally pass message body in send method. |

**Return Value:**

`bool` - True if the email was sent successfully, false otherwise.

**Throws:**

- [Luminova\Exceptions\MailerException](/error-handlers/exceptions.md#mailerexception) - Throws if error occurred while sending email.