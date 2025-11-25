# Abstract Base Class for Email Templates

***

## Overview

The BaseMailer class provides a convenient way to create reusable email templates and send them when necessary using the Luminova email framework.

***

## Introduction

The **Base Mailer** class is a foundational component of the Luminova email framework, designed to simplify the process of creating and sending email messages. It serves as a template for defining email templates with customizable subjects, HTML content, alternative text bodies, and attachments.

***

## Class Definition

* Class namespace: `Luminova\Base\Mailer`
* This class implements: [Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)

***

## Methods

### getSubject

Get the subject of the email.

```php
public getSubject(): ?string
```

**Return Value:**

`string|null` - The subject of the email.

***

### getHtml

Get the `HTML` body of the email.

```php
public getHtml(): ?string
```

**Return Value:**

`string|null` - Return email template html body.

***

### getText

Get the `TEXT` body of the email.

```php
public getText(): ?string
```

**Return Value:**

`string|null` - Return email template alt text body.

***

### getFiles

Get the attachments of the email.

```php
public getFiles(): ?array
```

**Return Value:**

`array<string,string>|null` - Return arrays of attachments or null if not attachment to send.

**Array keys**

- path: The file path.
- name: Optional file name.
- encoding: File encoding (default: base64).
- type: Optional file type.
- disposition: File disposition (default: attachment)

***

### Usage

To create a custom email template, you can extend the base `Mailer` class and implement the required methods for subject, HTML content, alternative text body, and attachments.

```php
// app/Utils/OrderTemplate.php
namespace App\Utils;

use Luminova\Base\Mailer;

class OrderTemplate extends Mailer
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

In this example, `OrderTemplate` extends base `Mailer` and provides implementations for the `getSubject()`, `getHtml()`, `getText()`, and `getAttachments()` methods.

***

### Sending Email

To send an email using the `OrderTemplate`, you can use the `Mailer` class provided by Luminova framework.

```php
// app/Controllers/Http/MailerController.php

namespace App\Controllers\Http;

use App\Utils\OrderTemplate;
use Luminova\Base\Controller;
use Luminova\Utility\Email\Mailer;

class MailerController extends Controller
{
    public function sendEmail(): int
    {
        if(Mailer::to('peter@example.com')->send(new OrderTemplate())){
            return STATUS_SUCCESS;
        }

         return STATUS_ERROR;
    }
}
```

In the above example, an instance of `Mailer` is created, and the `send()` method is called with the `OrderTemplate` instance as an argument. The email will be sent with the configured subject, HTML content, alternative text body, and attachments defined in the `OrderTemplate` class.