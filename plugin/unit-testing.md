---
title: "Plugins: Unit Testing"
description: "Learn how to set up automated unit testing for your plugins."
---
# Unit Testing

Plugins may contain unit tests - automated tests that ensure that features and functionality within the plugin are operating correctly after any changes are made to the plugin. Winter CMS makes it easy to create and run unit tests for your plugin using the [PHPUnit](https://phpunit.de) testing suite.

## Setting up for unit testing

Plugins can be tested by creating a `phpunit.xml` file in the base directory of your plugin.

```treeview
plugins/
`-- acme/
    `-- myplugin/
        |-- Plugin.php
        `-- phpunit.xml  # Create this file
```

The file should contain the following code, at a minimum:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    backupGlobals="false"
    backupStaticAttributes="false"
    colors="true"
    convertErrorsToExceptions="true"
    convertNoticesToExceptions="true"
    convertWarningsToExceptions="true"
    processIsolation="false"
    stopOnFailure="false"
    xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/9.3/phpunit.xsd"
>
    <testsuites>
        <testsuite name="Plugin Unit Test Suite">
            <directory>./tests</directory>
        </testsuite>
    </testsuites>
</phpunit>
```

## Creating test cases and fixtures

Unit testing, in general, involves running test cases. A single test case should test a certain aspect or functionality of the plugin. For example, if you were to create a blog plugin, you may have one test case that tests adding a blog post and then another to add a blog category and so on.

As you want the scenarios that you test to be consistent, test cases are often run with "fixtures" - single bits of static data. Using the example above for the test case involving adding a blog post, you may want to test with a certain blog title and content every time you run the tests. This means that the blog title and content are a "fixture".

We recommend the following structure for organising test cases and fixtures in your plugin, but you are free to choose your own structure.

```treeview
plugins/
`-- acme/
    `-- blog/
        |-- tests/                             # The tests folder
        |   |-- cases/                         # The test cases folder
        |   |   |-- BlogCategoryTest.php         # An example test case
        |   |   `-- BlogPostTest.php             # Another example test case
        |   `-- fixtures/                      # The test fixtures folder
        |       |-- first-blog.md                # An example fixture
        |       `-- second-blog.md               # Another example fixture
        |-- Plugin.php
        `-- phpunit.xml
```

A unit test within a plugin should extend the `System\Tests\Bootstrap\TestCase` class. This base test case class includes several features for properly testing plugins, including running migrations for the plugins and ensuring that the environment is set up to properly test plugins.

In order to be picked up by PHPUnit, test cases must match the following conventions:

- Test cases must be defined within a class ended with the `Test` suffix - generally, the test case should match the path and class name being tested, however only the suffix is required. For example, if you are testing a `Post` model, stored in the `models/Post.php` file in your plugin, it is best to define the test cases for this model within a class called `PostTest` and stored in the `tests/cases/models/PostTest.php` path.
- The test case class must extend the `System\Tests\Bootstrap\TestCase` class.
- Test case methods must be public and must be prefixed with the `test` prefix in the method name. Alternatively, the method must have a docblock that defines the `@test` annotation.
- Test cases must make at least one assertion.

An example test case class is below:

```php
<?php

namespace Acme\Blog\Tests\Cases\Models;

use System\Tests\Bootstrap\TestCase;

class BlogTest extends TestCase
{
    // Test adding a post
    public function testAddPost()
    {
        // ...
    }

    /**
     * This is still a test case, as it contains the following annotation.
     * @test
     */
    public function itCanDeleteAPost()
    {
        // ...
    }

    // This is NOT a test case, as the method is protected.
    protected function testNeverRuns()
    {
        // ...
    }
}
```

## Running unit tests

To run unit tests, it is strongly recommended to use the [`winter:test`](../console/utilities#run-unit-tests) Artisan command as opposed to PHPUnit directly, as this will ensure that the necessary bootstrapping of Winter CMS functionality takes place.

```
php artisan winter:test -p Your.PluginCode
```

This command will run all defined test cases that match the conventions above.

## Automating unit tests

If you wish to use continuous integration tools to run unit tests automatically, such as GitHub Actions or GitLab CI, you will need to ensure that a Winter CMS environment is set up by your continuous integration system *before* running the unit tests.

In general, your automation should do the following:

- Set up an environment with the PHP version you wish to test against, as well as [all required extensions](../setup/installation#minimum-system-requirements).
- Download the Winter CMS version you wish to test against.
- Install all Composer dependencies.
- Run the [`winter:test`](../console/utilities#run-unit-tests) Artisan command for your plugin.

### Example GitHub Actions configuration

This is an example of a configuration that you can use for GitHub Actions to test a Winter plugin whenever a new commit is added to your repository. Feel free to use this and adjust as necessary for your specific requirements.

```yaml
name: Tests

on:
  push:

jobs:
  unitTests:
    runs-on: ubuntu-latest
    name: Unit Tests
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          path: my-plugin

      - name: Setup PHP and extensions
        uses: shivammathur/setup-php@v2
        with:
          php-version: 8.1
          extensions: mbstring, intl, gd, xml, sqlite
          tools: composer:v2

      - name: Install Winter
        run: |
          wget https://github.com/wintercms/winter/archive/develop.zip
          unzip develop.zip
          rm develop.zip

          # Move Winter CMS into place
          shopt -s dotglob
          mv winter-develop/* ./
          rmdir winter-develop
          shopt -u dotglob
          mv config/cms.php config/testing/cms.php

          # Move plugin into place (note that the paths below should be changed to match your plugin path)
          mkdir -p plugins/myauthor
          mv my-plugin plugins/myauthor/myplugin

      - name: Install Composer dependencies
        run: composer install --no-interaction --no-progress

      - name: Run unit tests
        run: php artisan winter:test -p MyAuthor.MyPlugin
