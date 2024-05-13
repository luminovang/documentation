## Base Mailer

***

## Overview

The BaseMailer class provides a convenient way to create reusable email templates and send them when necessary using the Luminova email framework.

***

The `BaseMailer` class is a foundational component of the Luminova email framework, designed to simplify the process of creating and sending email messages. 
It serves as a template for defining email templates with customizable subjects, HTML content, alternative text bodies, and attachments.

***

* Class namespace: `\Luminova\Base\BaseMailer`
* This class is an **Abstract class**

***

## Methods

### getSubject

Get the subject of the email.

```php
public getSubject(): string|null
```

**Return Value:**

`string|null` - The subject of the email.

***

### getHtml

Get the `HTML` body of the email.

```php
public getHtml(): string|null
```

**Return Value:**

`string|null` - Return email template html body.

***

### getText

Get the `TEXT` body of the email.

```php
public getText(): string|null
```

**Return Value:**

`string|null` - Return email template alt text body.

***

### getFiles

Get the attachments of the email.

```php
public getFiles(): array&lt;string,string&gt;|null
```

**Return Value:**

`array&lt;string,string&gt;|null` - Return arrays of attachments or null if not attachment to send.

**Array keys**

- path: The file path.
- name: Optional file name.
- encoding: File encoding (default: base64).
- type: Optional file type.
- disposition: File disposition (default: attachment)

***
### Usage

To create a custom email template, you can extend the `BaseMailer` class and implement the required methods for subject, HTML content, alternative text body, and attachments.

```php
// app/Controllers/Utils/
<?php
namespace App\Controllers\Utils;

use \Luminova\Base\BaseMailer;

class OrderTemplate extends BaseMailer
{
    public function getSubject(): ?string 
    {
        return 'Subject';
    }

    public function getHtml(): ?string 
    {
        return '<p>Message html</p>';
    }

    public function getText(): ?string
    {
        return 'Message alt body';
    }

    public function getFiles(): ?array
    {
        return null;
    }
}
```

In this example, `OrderTemplate` extends `BaseMailer` and provides implementations for the `getSubject()`, `getHtml()`, `getText()`, and `getAttachments()` methods.

***
### Sending Email

To send an email using the `OrderTemplate`, you can use the `Mailer` class provided by Luminova framework.

```php
// app/Controllers/
<?php
namespace App\Controllers;

use \Luminova\Email\Mailer;
use \App\Controllers\Utils\OrderTemplate;

class MailerController extends BaseController
{
    public function sendEmail(): int
    {
        Mailer::to('peter@example.com')->send(new OrderTemplate());
    }
}
```

In this code, an instance of `Mailer` is created, and the `send()` method is called with the `OrderTemplate` instance as an argument. 
The email will be sent with the configured subject, HTML content, alternative text body, and attachments defined in the `OrderTemplate` class.