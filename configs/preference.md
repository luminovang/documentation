# Preference Configuration

***

## Overview

Preference configuration provides flexibility and customization methods to override the default components with your preferred components like logger and mailer client.

***

## Introduction

The `Preference Configuration` class provides methods to specify preferred components, such as logging and mailing, to be used in a Luminova application instead of relying only on the default components.

***

* Class namespace: `\App\Config\Preference`
* File path: `/app/Config/Preference.php`
* This class is marked as **final** and can't be subclassed

***
## Methods

### getLogger

Return instance of your preferred logger class.

```php
public getLogger(): \Psr\Log\LoggerInterface|null
```

> Your logger class must implement psr LoggerInterface.

**Return Value:**

`\Psr\Log\LoggerInterface|null` - Return preferred logger instance or null to use default logger.

***

### getMailer

Return instance of your preferred mail client.

```php
public getMailer(): \Luminova\Interface\MailerInterface|null
```

> Your mail client class must implement Luminova MailerInterface.
>
> Optionally you can use any of available mail client `\Luminova\Email\Clients\PHPMailer`, `\Luminova\Email\Clients\SwiftMailer`

**Return Value:**

`\Luminova\Interface\MailerInterface|null` - Return preferred logger instance or null to use default logger.
