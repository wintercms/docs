# Winter JavaScript Framework (Snowboard)

- [Introduction](#introduction)
- [Features](#features)
- [Including the framework](#framework-script)

<a name="introduction"></a>
## Introduction

Winter includes a new, *optional*, JavaScript framework called **Snowboard**, which acts as an upgrade to the previous [AJAX Framework](../ajax/introduction) and provides many new useful features in an extensible fashion, whilst dropping previous hard dependencies to supercharge your projects even further.

The framework takes advantage of the incredible enhancements made to the JavaScript ecosystem in recent years to provide a unique experience, available only on Winter.

<a name="features"></a>
## Features

- Rewritten AJAX and JavaScript framework, built from the ground-up using the latest JavaScript syntax (ES2015+) and functionality.
- jQuery is no longer a "hard" dependency, allowing the framework to be used across a wide variety of JavaScript frameworks.
- Easy, comprehensive extensibility and event handling in-built into the framework.
- Small footprint and full control over which core functionalities to include ensures your website loads quick.

<a name="framework-script"></a>
## Including the framework

> Before proceeding, please read the [Migration Guide](../snowboard/migration-guide), especially if you intend to use this framework on an existing project.

Snowboard is optionally included in your [CMS theme](../cms/themes). To use the framework, you should include it by placing the `{% snowboard %}` tag anywhere inside your [page](../cms/pages) or [layout](../cms/layouts) where you would like the JavaScript assets to be loaded - generally, this should be at the bottom of the page before the closing `</body>` tag. You must use this tag *before* you load any assets that rely on the framework, such as plugins or event listeners.

By default, this tag just loads the base framework and does not include any of the additional functionality, such as the AJAX framework, in order to allow usage of Snowboard without any clutter.

You can then specify further attributes to the tag to include optional additional functionality for the framework:

Attribute | Includes
--------- | --------
`request` | The base [JavaScript AJAX](../snowboard/request) request functionality
`attr` | The [HTML data attribute](../snowboard/data-attr) request functionality
`extras` | [Several useful UI enhancements](../snowboard/extras), including flash messages, loading states and transitions.
`all` | Includes everything above

For example, to include the framework with just the JavaScript AJAX request functionality, the tag would be:

```twig
{% snowboard request %}
```

Or to include both the JavaScript AJAX and HTML data attribute request functionality:

```twig
{% snowboard request attr %}
```