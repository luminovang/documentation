# Mailer Preference and Configurations

***

## Overview

Mailer configuration provides flexibility and customization methods to override the default mailing components with your preferred email client.

***

## Introduction

The `Mailer` Configuration class provides basic email configurations and method to specify preferred mailer class to be used across your application.

***

* Class namespace: `\App\Config\Mailer`
* File path: `/app/Config/Mailer.php`
* This class is marked as **final** and can't be subclassed

***
## Methods

### getMailer

Return instance of your preferred mail client.

```php
public getMailer(): ?\Luminova\Interface\MailerInterface
```

> Your mail client class must implement Luminova MailerInterface.
>
> Optionally you can use any of available mail client `\Luminova\Email\Clients\PHPMailer`, `\Luminova\Email\Clients\SwiftMailer`

**Return Value:**

`\Luminova\Interface\MailerInterface|null` - Return preferred mailer instance or null to use default mailer class.