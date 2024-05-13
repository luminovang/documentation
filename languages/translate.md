# Translation

***

## Overview

With the built-in language helper class and global helper function lang, you can easily integrate language translations into your web applications and APIs.

***

## Introduction

#### Introduction to Luminova's Web Translation

Translation is a crucial aspect of web development, especially for applications that target diverse audiences speaking different languages. By providing translations for various languages, developers can ensure that their applications are accessible and user-friendly for people around the world.

In web development, translation involves converting text content, such as labels, messages, and notifications, from one language to another. This process typically relies on language files that contain key-value pairs, where the keys represent the original text in the default language, and the values represent the corresponding translations in other languages.

One common approach to implementing translation in web applications is through the use of language files organized by locale. Each language file contains translations for a specific locale, allowing the application to dynamically switch between languages based on user preferences or predefined settings.

In this guide, we'll explore how to set up and manage translation locals, integrate translation functionalities in your Luminova web applications, and provide users with the ability to switch between languages. Additionally, we'll discuss best practices for structuring language files, handling placeholders and dynamic content in translations.

> Optionally, you can also use a third-party [Google Translator Class](https://github.com/peterujah/php-google-translator) written in PHP to translate entire webpages.

Here are a couple of steps to follow to create a translation:

### lang

The `lang` helper function allows you to translate between different languages anywhere in your application where translation may be needed.

```php
<?php
function lang(
    string $lookup,
    ?string $default = null,
    ?string $locale = null,
    array $placeholders = []
): string { }
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$lookup` | **lookup** | The key used to look up the translated string. |
| `$default` | **?string** | The default message to return if no translation key was found. (Default: NULL) |
| `$locale` | **?string** | The locale to use for translation. (Default is null drive from env `app.locale`)
| `$placeholders` | **array** | Optional: An associative or int-indexed array of placeholders to replace in the translated string. |

> *Note:*
> The first part of the lookup key must match the language file name before `xx.php`.
> Example: if you have a file named `Blog.fr.php`, then the lookup key must start with `Blog`, followed by the translation array key, using dot annotation for nested lookup.
		
```php 
<?php 
echo lang('Context.foo.bar', 'Default message', 'fr', ['name' => 'Peter', '20']);
```

> This will output the translated string for the key `Context.foo.bar` in French (fr). 
> If the translation is not found, it will output the default message 'Default message'. 
> The placeholders {name} and {0} in the translated string will be replaced with 'Peter' and '20' respectively.

***

### Locals

To begin with, you'll need to create translation locals in `/app/Controllers/Languages/` directory. 

The locale file will serve as translations based on your application's language locale or other languages you may want to add. 

An example of a local file name should look like `Context.Locale.php`, where `Context` represents the application context for the translations, and `Locale` is the language locale code (e.g., `Users.en.php`, `Users.fr.php`).

The `Users.en.php` will contains an array of your application `English` translations as you may require while the `Users.fr.php`, will contain array of `French` translation.

The array `keys` will serve as a lookup identifier to locate each translation in different locals. 

**Users.en.php Example**

```php
<?php
return [
    'invalid_username' => 'Invalid username',
    'notify' => 'Hello, {name}! You have {0} new messages.',
	'password' => [
    	'reset' => 'Enter your email address to reset password'
	]
];
```

**Users.fr.php Example**

```php
<?php
return [
    'invalid_username' => 'Nom d\'utilisateur invalide',
    'notify' => 'Bonjour, {name}! Vous avez {0} nouveaux messages.',
    'password' => [
    	'reset' => 'Entrez votre adresse e-mail pour réinitialiser votre mot de passe'
	]
];
```

In above examples, the array keys and placeholders are left as they are in all translation of same context. While the part of the values are translated depending on the local.

***

### Usages

To translate between different languages, you can set the default locale in the `env` file. Additionally, implement a switcher in your application to allow users to change between locales for other languages translation.

```php
<?php 
echo lang('Users.notify', null, null, ['name' => 'Peter', '20']);
//Hello, Peter! You have 20 new messages.
```

Change locale from `English` to `French`
```php 
<?php 
echo lang('Users.notify', null, 'fr', ['name' => 'Peter', '20']);
//Bonjour, Peter! Vous avez 20 nouveaux messages.
```

****

To temporarily change the locale, you can use the `setenv` function or store the user-selected locale in a session or cookie.

```php
<?php
setenv('app.locale', $locale, false);
```

> **Note:** 
> Avoid using `locale($locale)` or `setenv('app.locale', $locale, true)` for temporary changes, as this will update the locale in your `env` file, affecting all other users and forcing them to use the same locale as the current user.