# Push Notification Service Examples

***

## Overview

Comprehensive Guide to Using Luminova's Firebase Cloud Messaging for Push Notifications

***

## Introduction

This section provides code snippets illustrating how to use the `Notification` class to send notifications via Firebase.

### Requirements

1. **Firebase Account**: Create a [Firebase account](https://firebase.google.com/) and a project.
2. **Firebase SDK**: Install the Firebase SDK in your project.

#### 1. Create a Firebase Project

- Go to the [Firebase Console](https://console.firebase.google.com/).
- Click "Add Project" and follow the setup wizard.

#### 2. Add Firebase to Your App

- Setup your application or website to receive push notification.
- Download your service account `JSON` file and place it in `/writeable/credentials/` directory.

***
### Initialization

Initialize Luminova Firebase Notification module, if your service account name is difference from `ServiceAccount.json`, then you must specify the name with extension type in constructor Initialization.

```php 
<?php 
use \Luminova\Notifications\Firebase\Notification;

// Instantiate the Notification class
$notification = new Notification();
```

***

### Simple Configuration

The following examples demonstrate how to send notifications using a simple configuration array. 
For more advanced use cases, consider using the `Message` model or `Full Array` configuration to create a notification payload object.

#### Example Configuration

```php 
<?php 
$simpleConfig = [
    'title' => "New Notification Title",
    'body' => "This is a new notification message",
    'image' => 'https://example.com/assets/images/foo.png'
];
```

### Sending Notifications

#### To a Single Device Token

Send a notification to a single device token using the simple configuration array.

```php
<?php 
$simpleConfig['token'] = 'DEVICE_TOKEN';
$notify = $notification->send($simpleConfig);

if ($notify->isDone()) {
    echo 'Notification was successfully sent.';
} else {
    var_dump($notify->getReport());
}
```

#### To Multiple Device Tokens

Send a notification to multiple device tokens using the simple configuration array.

```php
<?php 
$simpleConfig['tokens'] = [
    'DEVICE_TOKEN_1', 'DEVICE_TOKEN_2', // Add more tokens as needed
];
$notify = $notification->broadcast($simpleConfig);

if ($notify->isDone()) {
    echo 'Notification was successfully sent.';
} else {
    var_dump($notify->getReport());
}
```

#### To a Topic

Send a notification to all devices subscribed to a specific topic using the simple configuration array.

```php
<?php 
$simpleConfig['topic'] = 'NEW_PRODUCT_AVAILABLE';
$notify = $notification->channel($simpleConfig);

if ($notify->isDone()) {
    echo 'Notification was successfully sent.';
} else {
    var_dump($notify->getReport());
}
```

#### To a Conditional Topic

Send a notification to devices that match certain conditions based on topic subscriptions.

```php
<?php 
$simpleConfig['conditions'] = "'ADMINS' in topics && !('USERS' in topics)";
$notify = $notification->condition($simpleConfig);

if ($notify->isDone()) {
    echo 'Notification was successfully sent.';
} else {
    var_dump($notify->getReport());
}
```

***

## Configuration Model

The `Message` model allows you to configure notifications with various options to suit different platforms and use cases. 
Below is an example of how to create and configure a `Message` object. Refer to the  [official documentation](/notifications/message.md) for all available methods.

### Basic Configuration

```php 
<?php 
use \Luminova\Notifications\Models\Message;

$message = (new Message())->setImageUrl('https://example.com/assets/images/foo.png')
    ->setTitle('Notification Title')
    ->setBody('Notification Body')
    ->setClickAction('MainActivity')
    ->setBadgeCount(1)
    ->setColor('#eeee')
    ->setVibration([200, 100, 200])
    ->setSound('dingbong.aiff')
    ->setPlatform(Message::ANDROID)
    ->setPackage('com.example.foo.bar')
    ->setIcon('large')
    ->setLink('https://example.com/')
    ->setPriority('normal')
    ->setTag('tag-name')
    ->setTtl('3s')
    ->setAnalytic('analytic-id')
    ->setRaw(true); // If you want to send a custom notification payload without building the payload 
```

In this example, the `Message` object is configured with properties such as `title`, `body`, `image`, `clickAction`, and platform-specific settings.

### Adding More Configuration

You can add custom data and platform-specific configurations to the `Message` object using various methods.

#### Adding Custom Data

To add key-value pairs to the data payload of the notification.

```php 
<?php 
$message->addData('data-key', 'data-key-value');
```

This will add a custom data field to the notification.

```json
{
    "data": {
        "data-key": "data-key-value"
    }
}
```

#### Adding Android-Specific Configuration

To add Android-specific configuration options:

```php 
<?php 
$message->addAndroid('foo-key', 'foo-key-value');
```

This will add the specified key-value pair under the Android configuration.

```json
{
    "android": {
        "foo-key": "foo-key-value"
    }
}
```

#### Adding APNs-Specific Configuration

To add APNs (Apple Push Notification service) specific configuration.

```php 
<?php 
$message->addApns('foo-key', 'foo-key-value');
```

This will add the specified key-value pair under the APNs configuration:

```json
{
    "apns": {
        "foo-key": "foo-key-value"
    }
}
```

#### Adding WebPush-Specific Configuration

To add WebPush specific configuration options:

```php 
<?php 
$message->addWebpush('foo-key', 'foo-key-value');
```

This will add the specified key-value pair under the WebPush configuration:

```json
{
    "webpush": {
        "foo-key": "foo-key-value"
    }
}
```

#### Adding Notification-Specific Configuration

To add general notification-specific configuration options:

```php 
<?php 
$message->addNotification('foo-key', 'foo-key-value');
```

This will add the specified key-value pair under the notification configuration.

```json
{
    "notification": {
        "foo-key": "foo-key-value"
    }
}
```

***

### Advanced Usages

You can also add nested configuration options to target specific sub-properties within the configuration objects.

#### Adding Nested Configuration for General Use

```php 
<?php 
$message->addNested('foo.bar.baz.boz.bra', 'bra value');
```

This will add the nested configuration as follows:

```json
{
    "foo": {
        "bar": {
            "baz": {
                "boz": {
                    "bra": "bra value"
                }
            }
        }
    }
}
```

#### Adding Nested Configuration for Android

```php 
<?php 
$message->addNested('android.light_settings.light_on_duration', '3.5s');
```

This will add the nested configuration to the Android settings:

```json
{
    "android": {
        "light_settings": {
            "light_on_duration": "3.5s"
        }
    }
}
```

#### Adding Nested Configuration for APNs

```php 
<?php 
$message->addNested('apns.payload.aps.category', 'GAME_INVITATION');
```

This will add the nested configuration to the APNs payload:

```json
{
    "apns": {
        "payload": {
            "aps": {
                "category": "GAME_INVITATION"
            }
        }
    }
}
```

These examples demonstrate how to add various configurations to your `Message` object, enabling you to send platform specific notifications.

***

### Sending Notifications from the Model

The following examples demonstrate how to send notifications using the `Message` model. 
This approach provides a more structured and customizable way to configure and send notifications compared to using simple configuration arrays.

#### Sending Notification to a Single Device Token

To send a notification to a single device token, set the token on the `Message` object and use the `send` method.

```php 
<?php 
$message->setToken('DEVICE_TOKEN');

$notify = $notification->send($message);

if ($notify->isDone()) {
    echo 'Notification was successfully sent.';
} else {
    var_dump($notify->getReport());
}
```

### Sending Notification to a Topic

To send a notification to all devices subscribed to a specific topic, set the topic on the `Message` object and use the `channel` method.

```php 
<?php 
$message->setTopic('NEW_PRODUCT_AVAILABLE');
$notify = $notification->channel($message);

if ($notify->isDone()) {
    echo 'Notification was successfully sent.';
} else {
    var_dump($notify->getReport());
}
```

### Sending Notification to Multiple Device Tokens

To broadcast a notification to multiple device tokens, set the tokens on the `Message` object and use the `broadcast` method.

```php 
<?php 
$message->setTokens([
    'Token1', 'Token2', // Add more tokens as needed
]);
$notify = $notification->broadcast($message);

if ($notify->isDone()) {
    echo 'Notification was successfully sent.';
} else {
    var_dump($notify->getReport());
}
```

### Sending Notification with Conditional Topic

To send a notification based on conditions that match specific topics, set the conditions on the `Message` object and use the `condition` method.

```php 
<?php 
$message->setConditions("'ADMINS' in topics && !('USERS' in topics)");
$notify = $notification->condition($message);

if ($notify->isDone()) {
    echo 'Notification was successfully sent.';
} else {
    var_dump($notify->getReport());
}
```

***

### Full Array Configuration

This section provides a comprehensive structure for configuring notification payloads using an array. 
This method allows for detailed customization of the notification for different platforms and scenarios.

#### Platform Key Values

- `1` - Default notification payload.
- `2` - Android notification payload.
- `3` - APNs (Apple Push Notification service) notification payload.
- `4` - WebPush notification payload.

### Example Configuration Array

```php 
<?php 
$config = [
    'platform'   => 1, // Default platform
    'raw'        => false, // Send raw notification payload
    'topic'      => 'Name of the topic', // Topic name for broadcasting
    'conditions' => 'Topic condition expressions', // Conditions for topic targeting
    'token'      => 'Single token', // Single device token
    'tokens'     => [
        'Token1',  'Token2', // Multiple device tokens
    ],
    'data'       => [
        'key1' => 'data 1',
        // Additional data fields
    ],
    'notification'  => [
        'title' => "Notification title", // Title of the notification
        'body'  => "Notification message", // Body text of the notification
        'image' => 'Optional image url for notification' // URL for the notification image
    ],
    'apns'          => [], // APNs-specific configurations
    'android'       => [], // Android-specific configurations
    'webpush'       => [], // WebPush-specific configurations
    'fcm_options'   => [] // Additional FCM options
];
```

> **Important Notes**
> *Mutually Exclusive Keys:* You cannot use `topic`, `conditions`, `token`, and `tokens` keys together in one configuration. 
> Select the key based on the target recipient(s) of the notification. 
> > Additionally if `raw` is enabled, you don't need to include `platform` key in your array.

- **topic**: For sending notifications to all subscribers of a topic.
- **conditions**: For sending notifications based on complex conditions involving multiple topics.
- **token**: For sending a notification to a single device.
- **tokens**: For broadcasting a notification to multiple devices.

### Summary

Using the full array configuration allows for control over your notifications, ensuring they are targeted to the appropriate platform and recipients. 
Adjust the array to fit your specific needs, making sure to adhere to the mutually exclusive key restrictions for targeting your audience accurately. 