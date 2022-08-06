# Unit Testing

Plugins may contain unit tests - automated tests that ensure that features and functionality within the plugin are operating correctly after any changes are made to the plugin. Winter CMS makes it easy to create and run unit tests for your plugin using the [PHPUnit](https://phpunit.de) testing suite.

### Setting up for unit testing

Plugins can be tested by creating a `phpunit.xml` file in the base directory of your plugin.

```treeview
plugins/
`-- acme/
    `-- myplugin/
        |-- Plugin.php
        `-- phpunit.xml  #  Create this file
```

The file should contain the following code, at a minimum:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    backupGlobals="false"
    backupStaticAttributes="false"
    bootstrap="../../../tests/bootstrap.php"
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
    <php>
        <env name="APP_ENV" value="testing"/>
        <env name="CACHE_DRIVER" value="array"/>
        <env name="SESSION_DRIVER" value="array"/>
    </php>
</phpunit>
```

### Creating test cases and fixtures

Unit testing, in general, involves running test cases. A single test case should test a certain aspect or functionality of the plugin. For example, if you were to create a blog plugin, you may have one test case that tests adding a blog post and then another to add a blog category and so on.

As you want the scenarios that you test to be consistent, test cases are often run with "fixtures" - single bits of static data. Using the example above for the test case involving adding a blog post, you may want to test with a certain blog title and content every time you run the tests. This means that the blog title and content are a "fixture".

We recommend the following structure for organising test cases and fixtures in your plugin, but you are free to choose your own structure.

```treeview
plugins/
`-- acme/
    `-- blog/
        |-- tests/                             # The tests folder
        |   |-- cases/                         # The test cases folder
        |   |   |-- BlogCategoryTestCase.php   # An example test case
        |   |   `-- BlogPostTestCase.php       # Another example test case
        |   `-- fixtures/                      # The test fixtures folder
        |       |-- first-blog.md              # An example fixture
        |       `-- second-blog.md             # Another example fixture
        |-- Plugin.php
        `-- phpunit.xml
```
