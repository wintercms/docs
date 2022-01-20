# Config Writer

- [Creating & updating a PHP config file](#create-and-update-php)
  - [Setting properties](#setting-properties)
  - [Multidimensional arrays](#multidimensional-arrays)
  - [Setting env defaults](#setting-env-defaults)
  - [Setting function calls](#setting-function-calls)
  - [Returning rendered PHP](#returning-rendered-php)
  - [Config sorting](#config-sorting)
  - [Writing to a different file than read](#writing-to-a-different-file)
- [Creating & updating a .env file](#create-and-update-env)
    - [Setting properties](#setting-properties-env)
    - [Writing to a different file than read](#writing-to-a-different-file-env)
    - [Adding new lines](#adding-new-lines)

> Notice, the `ConfigWriter` class has been deprecated and replaced by `ConfigFile`

<a name="create-and-update-php"></a>
## Creating & updating a PHP config file

The `ConfigFile` class can be used to update & create a php config file. The `ConfigFile::read()` method will take the
path of an existing file or create the file upon save if missing.

```php
use Winter\Storm\Config\ConfigFile;

$config = ConfigFile::read('/path/to/file.php');
$config->set('foo', 'bar');
$config->write();
```

<a name="setting-properties"></a>
### Setting properties

Setting properties can be chained or multiple properties can be set by passing an array

```php
ConfigFile::read('/path/to/file.php')
    ->set('foo', 'bar')
    ->set('bar', 'foo')
    ->write();

// or

ConfigFile::read('/path/to/file.php')->set([
    'foo' => 'bar',
    'bar' => 'foo'
])->write();
```

<a name="multidimensional-arrays"></a>
### Multidimensional arrays

Multidimensional arrays can be set via dot notation.

```php
ConfigFile::read('/path/to/file.php')->set([
    'foo.bar.a' => 'bar',
    'foo.bar.b' => 'foo'
])->write();
```

Will output:

```php
<?php

return [
    'foo' => [
        'bar' => [
            'a' => 'bar',
            'b' => 'foo',
        ]
    ]
];
```

<a name="setting-env-defaults"></a>
### Setting env defaults

If a config file has a `env()`, then by setting the property of that function will set the default argument.

For example, the following config:

```php
<?php

return [
    'foo' => [
        'bar' => env('EXAMPLE_KEY'),
    ]
];
```

After setting the position of the env function call.

```php
ConfigFile::read('/path/to/file.php')->set([
    'foo.bar' => 'Winter CMS',
])->write();
```

Will result in:

```php
<?php

return [
    'foo' => [
        'bar' => env('EXAMPLE_KEY', 'Winter CMS'),
    ]
];
```

<a name="setting-function-calls"></a>
### Setting function calls

Function calls can be added to your config either via the `ConfigFunction` class or using the `function()` helper method
on the `ConfigFile` object.

```php
use Winter\Storm\Config\ConfigFile;
use Winter\Storm\Config\ConfigFunction;

ConfigFile::read('/path/to/file.php')->set([
    'foo.bar' => new ConfigFunction('env', ['argument1', 'argument1']),
])->write();

// or

$config = ConfigFile::read('/path/to/file.php');
$config->set([
    'foo.bar' => $config->function('env', ['argument1', 'argument1']),
]);
$config->write();
```

<a name="returning-rendered-php"></a>
### Returning rendered PHP

If you require the php config as a string instead of a file, the `render()` method can be used.

```php
$phpConfigString = ConfigFile::read('/path/to/file.php')->set([
    'foo.bar' => 'Winter CMS',
])->render();
```

<a name="config-sorting"></a>
### Config sorting

The `ConfigFile` object supports sorting your config file before rendering.

```php
$config = ConfigFile::read('/path/to/file.php');
$config->set([
    'b' => 'is awesome'
    'a.b' => 'CMS',
    'a.a' => 'Winter',
]);
$config->sort(ConfigFile::SORT_ASC);
$config->write();
```

Will write out:

```php
<?php

return [
    'a' => [
        'a' => 'Winter',
        'b' => 'CMS',
    ],
    'b' => 'is awesome',
];
```

The sort method supports the following options:

- `ConfigFile::SORT_ASC`
- `ConfigFile::SORT_DESC`
- a callable function

By default, `sort()` will use `ConfigFile::SORT_ASC`.

<a name="writing-to-a-different-file"></a>
### Writing to a different file than read

If required, you can read from an existing file, and write out to a different file.

```php
ConfigFile::read('/path/to/file.php')->set([
    'foo.bar' => 'Winter CMS',
])->write('/path/to/another.file.php');
```

<a name="create-and-update-env"></a>
## Creating & updating a .env file

By default, the env file read will be `base_path('.env')`, this can be changed if required by passing the path to
the `read()` method. 

> NOTE: properties are set in order, sorting is not supported.

```php
use Winter\Storm\Config\EnvFile;

$env = EnvFile::read();
$env->set('FOO', 'bar');
$env->write();
```

<a name="setting-properties-env"></a>
### Setting properties

Similar to the `ConfigFile` object, properties can be set either one at a time or by passing an array.

> NOTE: dot notation is not supported by `EnvFile`

```php
$env = EnvFile::read();
$env->set('FOO', 'bar');
$env->set('BAR', 'foo');
$env->write();

// or

EnvFile::read()->set([
    'FOO' => 'bar'
    'BAR' => 'foo'
])->write();
```

<a name="writing-to-a-different-file-env"></a>
### Writing to a different file than read

If required, you can read from an existing file, and write out to a different file.

```php
EnvFile::read()->set([
    'FOO' => 'bar',
])->write('/path/to/.env.alternative');
```

<a name="adding-new-lines"></a>
### Adding new lines

If required, you can add new lines into the env file.

```php
$env = EnvFile::read();
$env->set('FOO', 'bar');
$env->addNewLine();
$env->set('BAR', 'foo');
$env->write();
```

Will output:

```dotenv
FOO="bar"

BAR="foo"
```