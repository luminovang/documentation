# Email

***

## Overview

The Mailer class provides methods to facilitate sending emails. This documentation outlines its methods and their usage.

***

## Introduction

The Luminova Mailer class simplifies the process of sending emails. It provides methods to handle various aspects of email composition, such as setting recipients, sender address, message body, attachments, and more.

It also supports third-party PHP mailer libraries like `PHPMailer` and `SwiftMailer`, allowing you to switch between mailer clients without the need to rewrite your code.

***

* Class namespace: `\Luminova\Email\Mailer`

## Methods

By default, the `Mailer` class uses a simple email class `NovaMailer` to send emails. If you wish to switch to a more advanced mailer, you can use `PHPMailer` or `SwiftMailer`, which are already implemented in the Luminova framework.

To use another mailer library, you need to initialize the `Mailer` class with your preferred PHP mailer library like `PHPMailer` or `SwiftMailer`, or set your preferred mailer class in `/app/Controllers/Config/Preference.php`.

Initializing with `PHPMailer`:

```php
<?php
use \Luminova\Email\Clients\PHPMailer;
$mailer = new Mailer(PHPMailer::class);
```

Initializing with `SwiftMailer`:

```php
<?php
use \Luminova\Email\Clients\SwiftMailer;
$mailer = new Mailer(SwiftMailer::class);
```

*Note:*

To use either `PHPMailer` or `SwiftMailer`, you will need to install the package first using Composer:

- For PHPMailer: `composer require phpmailer/phpmailer`
- For SwiftMailer: `composer require "swiftmailer/swiftmailer:^6.0"`

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

- [\Luminova\Exceptions\MailerException](/exceptions/classes.md#mailerexception) - Throws if error occurred while sending email.

***

### getClient

Get the Mailer client instance.

```php
public getClient(): \Luminova\Interface\MailerInterface
```

**Return Value:**

`\Luminova\Interface\MailerInterface` - The Mailer client instance.

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

### replyTo

Add a reply-to address.

```php
public replyTo(string $address, string $name = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$address` | **string** | The email address. |
| `$name` | **string** | The recipient's name (optional). |

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
public body(\Luminova\Base\BaseMailer|string $message): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **\Luminova\Base\BaseMailer&#124;string** | The body content of the email. |

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

### addFile

Add an attachment from a path on the filesystem.

```php
public addFile(string $path, string $name = '', string $encoding = 'base64', string $type = '', string $disposition = 'attachment'): self
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

- [\Luminova\Exceptions\MailerException](/exceptions/classes.md#mailerexception) - Throws if error occurred while sending email.

***

### send

Send the email.

```php
public send(\Luminova\Base\BaseMailer|string|null $message = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$message` | **\Luminova\Base\BaseMailer&#124;string&#124;null** | Optionally pass message body in send method. |

**Return Value:**

`bool` - True if the email was sent successfully, false otherwise.

**Throws:**

- [\Luminova\Exceptions\MailerException](/exceptions/classes.md#mailerexception) - Throws if error occurred while sending email.

***

### Examples

Creating mailer instance to send email.

```php
<?php
namespace App\Controllers;

use \Luminova\Base\BaseController;
use \Luminova\Email\Mailer;

class MailerController extends BaseController
{
    public function sendEmail(): int
    {
        (new Mailer())->from('system@example.com')
            ->address('peter@example.com')
            //->cc('cc@example.com')
            //->bcc('bcc@example.com')
            //->replyTo('admin@example.com')
            //->addFile()
            ->subject('Email Hello!')
            ->body('<p>HTML email message body</p>')
            ->text('Alt text email body!')
            ->send();

        // ...
    }
}
```

***

Creating a mailer [template controller class](/base/mailer.md).

```php
<?php
namespace App\Controllers;

use \Luminova\Base\BaseController;
use \App\Controllers\Utils\OrderTemplate;
use \Luminova\Email\Mailer;

class MailerController extends BaseController
{
    public function sendEmail(OrderTemplate $email): int
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