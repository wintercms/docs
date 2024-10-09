# Asset Compilation - NPM Utilities

## Introduction

Winter supports running `npm` commands within the context of your packages, i.e. allowing you to update a specific plugins dependencies or allowing you to execute a themes npm script.

### Requirements

The following assumes you have a compatible version of `npm` installed (>= v7.0), and you have already configured your packages as described in the [Asset Compilation](./asset-compilation.md) guide.

### Install Node dependencies

```bash
php artisan npm:install <?package name> <dependencies[]>
```

The `npm:install` command will install Node dependencies. You are able to target a package to install the dependency into as the first option.

```bash
php artisan npm:install acme.plugin is-odd
```

Multiple packages can be installed in a single command by appending the desired packages to the end of the command.

```bash
php artisan npm:install acme.plugin is-odd is-even
```

If you wish for the dependencies to be installed within devDependencies, you can pass the `--dev` flag.

```bash
php artisan npm:install acme.plugin is-odd is-even --dev
```

Alternatively, if you wish to install dependencies to your projects root, you may omit the `package name` from the command.

```bash
php artisan npm:install is-odd is-even
```

### Update Node dependencies

```bash
php artisan npm:update <?package name> <dependencies[]>
```

The `npm:update` command will update Node dependencies. You are able to target a package to update the dependency into as the first option.

```bash
php artisan npm:update acme.plugin
```

If you want to update a specific dependency, simply provide it at the end of the command.

```bash
php artisan npm:update acme.plugin is-odd
```

To tell `npm` to also save the version update to your package's `package.json` in addition to the lock file, provide the `--save` flag

```bash
php artisan npm:update acme.plugin --save
```

To update dependencies of your projects root `package.json`, omit the package from the command.

```bash
php artisan npm:update is-odd
```

### Run a package script

```bash
php artisan npm:run <package> <script> [-f|--production] [-- <extra script args>]
```

If your packages `package.json` implements `scripts`, you can execute them via the `npm:run` command. The following assumes you already have scripts configured.

```json5
// package.json
{
    // ...
    "scripts": {
        "scriptName": "script to execute"
    }
    // ...
}
```

The following will run the `test` script from within the `acme.plugin` package.

```bash
php artisan npm:run acme.plugin test
```

You are also able to pass custom arguments to the script via adding `--` followed by your arguments.

```bash
php artisan npm:run acme.plugin test -- --custom-arg --example=1
```

This can be useful for running arbitrary scripts that augment the capabilities of your plugin or theme, such as executing unit tests, making customised builds and much more. Note that scripts will run with the working directory set to the root folder of the package, not the root folder of the entire project that the `artisan` command normally executes within.

By default, all package scripts are run in "development" mode. If you wish to run a script in "production" mode, add the `-f` or `--production` flag to the command.
