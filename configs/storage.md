# Configurations for Default Local or Cloud Storage Location 

***

## Overview

The Storage Configuration allows you to set up multiple storage contexts in your application, each with its own settings such as visibility and client configuration.

***

## Introduction

To set up multiple storage contexts in your configuration file located at `/app/Config/Storage.php`, you can define an array containing configurations for each storage context. Each context can have its own settings, such as the type of storage which is the `context`, (visibility, client configuration base, etc.).

***

* File path: `/app/Config/Storage.php`

***

### Storage Structure

Here's an example of how you can set up multiple storage contexts.

You can define an array containing configurations for each storage context. Each context can have its own settings, such as the type of storage (context), visibility, directory visibility, client configuration, etc.

```php
<?php
return [
    'default' => [
        'visibility' => 'public',
        'directory_visibility' => 'public'
    ],

    'local' => [...],
    'ftp' => [...],
    'memory' => [...],
    'aws-s3' => [...],
    'aws-async-s3' => [...],
    'azure-blob' => [...],
    'google-cloud' => [...],
    'sftp-v3' => [...],
    'web-dev' => [...],
    'zip-archive' => [...]
];
```

***

### Local

Initialization for `LOCAL` filesystem storage.

```php 
<?php 
Storage::context('local')
```

Configuration for local filesystem storage.

```php
<?php
return [
	'local' => [
        'base' => root('writeable/storages/'), // Path to private storage directory 
        'assets' => root('public/assets/'), // Path to public asset directory to create a symlink if need to
        'urls' => [
            'public_url' => start_url('assets/'), // URL to asset path https://example.ng/assets/'
        ],
        'lock_flags' => LOCK_EX,
        'disallow_links' => true,
        'visibility' => [
            'file' => [
                    'public' => 0644,
                    'private' => 0644,
            ],
            'dir' => [
                    'public' => 0740,
                    'private' => 7604,
            ],
        ]
	]
]
```

***

### FTP

Initialization for `FTP` filesystem storage.

```php 
<?php 
Storage::context('ftp')
```

Configuration for FTP filesystem storage.

```php
<?php 
return [
    //...
    'ftp' => [
        'host' => 'hostname', // required
        'root' => '/root/path/', // required
        'username' => 'username', // required
        'password' => 'password', // required
        'port' => 21,
        'ssl' => false,
        'timeout' => 10,
        'utf8' => false,
        'passive' => true,
        'transferMode' => FTP_BINARY,
        'systemType' => null, // 'windows' or 'unix'
        'ignorePassiveAddress' => null, // true or false
        'timestampsOnUnixListingsEnabled' => false, // true or false
        'recurseManually' => true // true
    ]
]
```

***

### SFTP-v3

Initialization for `SFTP-v3` filesystem storage.

```php 
<?php 
Storage::context('ftp-v3')
```

Configuration for SFTP version 3 filesystem storage.

```php
<?php 
return [
    //...

    'sftp-v3' => [
        'host' => 'localhost', // required
        'username' => 'your-username', // required
        'password' => 'your-password', // required
        'root' => '/root/path/', // required
        'port' => 22,
        'timeout' => 10, // timeout
        'retry' => 4, // max tries
        'private_key_path' => null, // private key path, set to null if password is set
        'passphrase' => null, // passphrase, set to null if privateKey is not used or has no passphrase
        'agent' => false, // use agent 
        'fingerprint' => null,
        'visibility' => [ // Visibility set to null or empty array if you don't require visibility.
            'file' => [
                'public' => 0640,
                'private' => 0604,
            ],
            'dir' => [
                'public' => 0740,
                'private' => 7604,
            ],
        ]
    ]
]
```

***

### Memory

Initialization for `MEMORY` filesystem storage.

```php 
<?php 
Storage::context('memory')
```

Configuration for in-memory filesystem storage.

```php
<?php 
return [
    //...

    'memory' => [
        'readonly' => false
    ]
]
```

***

### AWS S3

Initialization for `AWS-S3` filesystem storage.

```php 
<?php 
Storage::context('aws-s3')
```

Configuration for Amazon S3 filesystem storage.

```php
<?php 
return [
    //...

    'aws-s3' => [
        'configuration' => [
            'credentials' => [
                'key'    => 'your-key',
                'secret' => 'your-secret',
            ],
            'region' => 'your-region',
            'version' => 'latest',
        ],
        'bucket' => 'bucket-name',
        'base' => 'path/prefix',
        'visibility' => 'public', // set [public or private]
    ]
]
```

### AWS Async S3

Initialization for `AWS-ASYNC-S3` filesystem storage.

```php 
<?php 
Storage::context('aws-async-s3')
```

Configuration for Amazon Async S3 filesystem storage.

```php
<?php 
return [
    //...

    'aws-async-s3' => [
        'configuration' => [
            'credentials' => [
                'key'    => 'your-key',
                'secret' => 'your-secret',
            ],
            'region' => 'your-region',
            'version' => 'latest',
        ],
        'bucket' => 'your-bucket-name',
        'base' => 'path/prefix',
        'visibility' => 'public', // set [public or private]
    ]
]
```

### IAM Permissions

The required IAM permissions for AWS storages.

```js
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetObject",
                "s3:DeleteObject",
                "s3:GetObjectAcl",
                "s3:PutObjectAcl",
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::your-bucket-name",
                "arn:aws:s3:::your-bucket-name/*"
            ]
        }
    ]
}
```

***

### Azure Blob

Initialization for `AZURE-BLOB` filesystem storage.

```php 
<?php 
Storage::context('azure-blob')
```

Configuration for Azure Blob filesystem storage.

```php
<?php 
return [
    //...

    'azure-blob' => [
        'dns' => 'DSN-STRING-HERE',
        'container' => 'container-name',
        'base' => 'path/prefix'
    ]
]
```

***

### Google Cloud

Initialization for `GOOGLE-CLOD` filesystem storage.

```php 
<?php 
Storage::context('google-cloud')
```

Configuration for Google Cloud filesystem storage.

```php
<?php 
return [
    //...

    'google-cloud' => [
        'configuration' => [ // Google cloud storage client configuration
            'keyFile' => [], // key file array
            'keyFilePath' => '/path/to/keyfile.json', // Path to key file, if you already set keyFile then comment this and authCache.
			'authCache' => \Luminova\Psr\Cache\CachePool::class, //Caching Access Tokens, if you already set keyFile or keyFilePath then comment this.
        ],
        'bucket' => 'your-bucket-name',
        'base' => 'path/prefix',
    ]
]
```

***

### WebDAV

Initialization for `WEB-DEV` filesystem storage.

```php 
<?php 
Storage::context('web-dev')
```

Configuration for WebDAV filesystem storage.

```php
<?php 
return [
    //...

    'web-dev' => [
        'baseurl' => 'http://your-webdav-server.org/',
        'username' => 'your-bucket-name',
        'password' => 'path/prefix'
    ]
]
```

***

### Zip Archive

Initialization for `ZIP-ARCHIVE` filesystem storage.

```php 
<?php 
Storage::context('zip-archive')
```

Configuration for Zip Archive filesystem storage.

```php
<?php 
return [
    //...

    'zip-archive' => [
        'path' => '/path/to/zip/',
    ]
]
```

> This configuration allows you to easily switch between different storage providers and customize settings for each context.