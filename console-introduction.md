# Command Line Interface

- [Introduction](#introduction)
- [List of available commands](#command-list)
- [Building a command](#building-a-command)
    - [Defining arguments](#defining-arguments)
    - [Defining options](#defining-options)
    - [Writing output](#writing-output)
    - [Retrieving input](#retrieving-input)
- [Registering commands](#registering-commands)
- [Calling other commands](#calling-other-commands)

<a name="introduction"></a>
## Introduction

Winter includes several command-line interface (CLI) commands and utilities that allow to install and manage Winter and its plugins and themes, perform site maintenance and speed up the development process. The console commands are executed through Laravel's [Artisan](http://laravel.com/docs/artisan) command-line tool.

Commands are executed by using your terminal or shell and running the following command in the root folder of your project:

```bash
php artisan [command]
```

You can get a list of available commands by not providing any command to the Artisan tool:

```bash
php artisan
```

If you require help or a list of available arguments and options for each command, simply suffix the command with the `--help` flag:

```bash
php artisan [command] --help
```

<a name="command-list"></a>
## List of available commands

The following commands are made available to every Winter installation. Click the name of a command to view more information about the usage of that command.

Command | Description
------- | -----------
**Setup & Maintenance** |
[`winter:install`](../console/setup-maintenance#winter-install) | Install Winter via command line.
[`winter:update`](../console/setup-maintenance#winter-update) | Update Winter and its plugins using the [Marketplace](https://wintercms.com/marketplace) via the command line.
[`winter:up`](../console/setup-maintenance#winter-up) | Run database migrations.
[`winter:passwd`](../console/setup-maintenance#winter-passwd) | Change the password of an administrator.
[`winter:env`](../console/setup-maintenance#winter-env) | Use environment files and configuration for Winter.
[`winter:version`](../console/setup-maintenance#winter-version) | Display the version of Winter in use.
[`winter:fresh`](../console/setup-maintenance#winter-fresh) | Remove the demo plugin and theme.
[`winter:mirror`](../console/setup-maintenance#winter-mirror) | Mirror publicly accessible files in another directory.
**Plugin management** |
[`plugin:install`](../console/plugin-management#plugin-install) | Download and install a plugin for Winter.
[`plugin:list`](../console/plugin-management#plugin-list) | List installed plugins.
[`plugin:rollback`](../console/plugin-management#plugin-rollback) | Rolls back a plugin and its database tables.
[`plugin:refresh`](../console/plugin-management#plugin-refresh) | Rolls back a plugin and its database tables, and re-runs all updates.
[`plugin:disable`](../console/plugin-management#plugin-disable) | Disables a plugin.
[`plugin:enable`](../console/plugin-management#plugin-enable) | Enables a plugin.
[`plugin:remove`](../console/plugin-management#plugin-install) | Removes a plugin.
**Theme management** |
[`theme:install`](../console/theme-management#theme-install) | Download and install a theme for Winter.
[`theme:list`](../console/theme-management#theme-list) | List available themes.
[`theme:use`](../console/theme-management#theme-use) | Switches Winter to the given theme.
[`theme:remove`](../console/theme-management#theme-install) | Removes a theme.
[`theme:sync`](../console/theme-management#theme-sync) | Synchronises a theme between the filesystem and the database, if you use the [Database Templates](../cms/themes#database-driven-themes) feature.
**Asset compilation (Mix)** |
[`mix:install`](../console/asset-compilation#mix-install) | Install Node dependencies for registered Mix packages.
[`mix:update`](../console/asset-compilation#mix-update) | Update Node dependencies for registered Mix packages.
[`mix:list`](../console/asset-compilation#mix-list) | Lists all registered Mix packages.
[`mix:compile`](../console/asset-compilation#mix-compile) | Compiles one or more Mix packages.
[`mix:watch`](../console/asset-compilation#mix-watch) | Watches changes within a Mix package and automatically compiles the package on any change.
**Scaffolding** |
[`create:theme`](../console/scaffolding#create-theme) | Create a theme.
[`create:plugin`](../console/scaffolding#create-plugin) | Create a plugin.
[`create:component`](../console/scaffolding#create-component) | Create a component in a plugin.
[`create:model`](../console/scaffolding#create-model) | Create a model in a plugin.
[`create:settings`](../console/scaffolding#create-settings) | Create a settings model in a plugin.
[`create:controller`](../console/scaffolding#create-controller) | Create a controller in a plugin.
[`create:formwidget`](../console/scaffolding#create-formwidget) | Create a form widget in a plugin.
[`create:reportwidget`](../console/scaffolding#create-reportwidget) | Create a report widget in a plugin.
[`create:command`](../console/scaffolding#create-command) | Create a console command in a plugin.
**Utilities** |
[`cache:clear`](../console/utilities#cache-clear) | Clear the application cache.
[`winter:test`](../console/utilities#winter-test) | Run unit tests on Winter and plugins.
[`winter:util`](../console/utilities#winter-util) | A collection of utilities for Winter development.


<a name="building-a-command"></a>
## Building a command

Plugins can also provide additional commands to augment additional functionality to Winter.

If you wanted to create a console command called `myauthor:mycommand`, you can run the `php artisan create:command MyAuthor.MyPlugin MyCommand` [scaffolding command](../console/scaffolding#create-command) which would create the  associated class for that command in a file called `plugins/myauthor/myplugin/console/MyCommand.php` with the following contents:

```php
<?php namespace MyAuthor\MyPlugin\Console;

use Illuminate\Console\Command;
use Symfony\Component\Console\Input\InputOption;
use Symfony\Component\Console\Input\InputArgument;

class MyCommand extends Command
{
    /**
     * @var string The console command name.
     */
    protected $name = 'myauthor:mycommand';

    /**
     * @var string The console command description.
     */
    protected $description = 'Does something cool.';

    /**
     * Execute the console command.
     * @return void
     */
    public function handle()
    {
        $this->output->writeln('Hello world!');
    }

    /**
     * Get the console command arguments.
     * @return array
     */
    protected function getArguments()
    {
        return [];
    }

    /**
     * Get the console command options.
     * @return array
     */
    protected function getOptions()
    {
        return [];
    }
}
```

Once your class is created you should fill out the `name` and `description` properties of the class, which will be used when displaying your command on the command `list` screen.

The `handle` method will be called when your command is executed. You may place any command logic in this method.

<a name="defining-arguments"></a>
### Defining arguments

Arguments are defined by returning an array value from the `getArguments` method are where you may define any arguments your command receives. For example:

```php
/**
 * Get the console command arguments.
 * @return array
 */
protected function getArguments()
{
    return [
        ['example', InputArgument::REQUIRED, 'An example argument.'],
    ];
}
```

When defining `arguments`, the array definition values represent the following:

```php
array($name, $mode, $description, $defaultValue)
```

The argument `mode` may be any of the following: `InputArgument::REQUIRED` or `InputArgument::OPTIONAL`.

<a name="defining-options"></a>
### Defining options

Options are defined by returning an array value from the `getOptions` method. Like arguments this method should return an array of commands, which are described by a list of array options. For example:

```php
/**
 * Get the console command options.
 * @return array
 */
protected function getOptions()
{
    return [
        ['example', null, InputOption::VALUE_OPTIONAL, 'An example option.', null],
    ];
}
```

When defining `options`, the array definition values represent the following:

```php
array($name, $shortcut, $mode, $description, $defaultValue)
```

For options, the argument `mode` may be: `InputOption::VALUE_REQUIRED`, `InputOption::VALUE_OPTIONAL`, `InputOption::VALUE_IS_ARRAY`, `InputOption::VALUE_NONE`.

The `VALUE_IS_ARRAY` mode indicates that the switch may be used multiple times when calling the command:

```bash
php artisan foo --option=bar --option=baz
```

The `VALUE_NONE` option indicates that the option is simply used as a "switch":

```bash
php artisan foo --option
```

<a name="retrieving-input"></a>
### Retrieving input

While your command is executing, you will obviously need to access the values for the arguments and options accepted by your application. To do so, you may use the `argument` and `option` methods:

#### Retrieving the value of a command argument

```php
$value = $this->argument('name');
```

#### Retrieving all arguments

```php
$arguments = $this->argument();
```

#### Retrieving the value of a command option

```php
$value = $this->option('name');
```

#### Retrieving all options

```php
$options = $this->option();
```

<a name="writing-output"></a>
### Writing output

To send output to the console, you may use the `info`, `comment`, `question` and `error` methods. Each of these methods will use the appropriate ANSI colors for their purpose.

#### Sending information

```php
$this->info('Display this on the screen');
```

#### Sending an error message

```php
$this->error('Something went wrong!');
```

#### Asking the user for input

You may also use the `ask` and `confirm` methods to prompt the user for input:

```php
$name = $this->ask('What is your name?');
```

#### Asking the user for secret input

```php
$password = $this->secret('What is the password?');
```

#### Asking the user for confirmation

```php
if ($this->confirm('Do you wish to continue? [yes|no]'))
{
    //
}
```

You may also specify a default value to the `confirm` method, which should be `true` or `false`:

```php
$this->confirm($question, true);
```

#### Progress Bars

For long running tasks, it could be helpful to show a progress indicator. Using the output object, we can start, advance and stop the Progress Bar. First, define the total number of steps the process will iterate through. Then, advance the Progress Bar after processing each item:

```php
$users = App\User::all();

$bar = $this->output->createProgressBar(count($users));

foreach ($users as $user) {
    $this->performTask($user);

    $bar->advance();
}

$bar->finish();
```

For more advanced options, check out the [Symfony Progress Bar component documentation](https://symfony.com/doc/2.7/components/console/helpers/progressbar.html).

<a name="registering-commands"></a>
## Registering commands

#### Registering a console command

Once your command class is finished, you need to register it so it will be available for use. This is typically done in the `register` method of a [Plugin registration file](../plugin/registration#registration-methods) using  the `registerConsoleCommand` helper method.

```php
class MyPlugin extends PluginBase
{
    public function pluginDetails()
    {
        [...]
    }

    public function register()
    {
        $this->registerConsoleCommand('myauthor.mycommand', \MyAuthor\MyPlugin\Console\MyCommand::class);
    }
}
```

Alternatively, plugins can supply a file named **init.php** in the plugin directory that you can use to place command registration logic. Within this file, you could use the `Artisan::add` method to register the command:

```php
Artisan::add(new MyAuthor\MyPlugin\Console\MyCommand);
```

#### Registering a command in the application container

If your command is registered in the [application container](../services/application#app-container), you may use the `Artisan::resolve` method to make it available to Artisan:

```php
Artisan::resolve('myauthor.mycommand');
```

#### Registering commands in a service provider

If you need to register commands from within a [service provider](../services/application#service-providers), you should call the `commands` method from the provider's `boot` method, passing the [container](../services/application#app-container) binding for the command:

```php
public function boot()
{
    $this->app->singleton('myauthor.mycommand', function() {
        return new \MyAuthor\MyCommand\Console\MyCommand;
    });

    $this->commands('myauthor.mycommand');
}
```

<a name="calling-other-commands"></a>
## Calling other commands

Sometimes you may wish to call other commands from your command. You may do so using the `call` method:

```php
$this->call('winter:up');
```

You can also pass arguments as an array:

```php
$this->call('plugin:refresh', ['name' => 'Winter.Demo']);
```

As well as options:

```php
$this->call('winter:update', ['--force' => true]);
```
