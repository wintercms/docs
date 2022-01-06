# Winter JavaScript Framework

- [Introduction](#introduction)
- [Features](#features)
- [Including the framework](#framework-script)

<a name="introduction"></a>
## Introduction

Winter includes a new, *optional*, JavaScript framework, which acts as an upgrade to the previous [AJAX Framework](../ajax/introduction) and provides many new useful features in an extensible fashion, whilst dropping previous hard dependencies to supercharge your projects even further. The framework takes advantage of the incredible enhancements made to the JavaScript ecosystem in recent years to provide a unique experience available only on Winter.

<a name="features"></a>
## Features

- Rewritten AJAX and JavaScript framework, built from the ground-up using the latest JavaScript syntax (ES2015+) and functionality.
- jQuery is no longer a "hard" dependency, allowing the framework to be used across a wide varierty of JavaScript frameworks.
- Easy, comprehensive extensibility and event handling in-built into the framework.

<a name="framework-script"></a>
## Including the framework

> Before proceeding, please read the [Migration Guide](../jsframework/migration-guide), especially if you intend to use this framework on an existing project.

The JavaScript framework is optionally included in your [CMS theme](../cms/themes). To use the framework, you should include it by placing the `{% winterjs %}` tag anywhere inside your [page](../cms/pages) or [layout](../cms/layouts). This instructs Winter to include the framework assets at this point. You must use this tag *before* you load any assets that rely on the framework, such as plugins or event listeners.

By default, this tag just loads the base framework, which *does not* include the AJAX functionality, to allow people to completely customise the AJAX functionality if they wish.

You can specify further attributes to the tag to include optional additional functionality for the framework:

Attribute | Includes
--------- | --------
`request` | The base [JavaScript AJAX](../jsframework/request) request functionality
`attr` | The [HTML data attribute](../jsframework/data-attr-ajax) request functionality
`extras` | [Several useful UI enhancements](../jsframework/extras), including popups, flash messages, loading states and transitions.
`all` | Includes everything above

For example, to include the framework with just the JavaScript AJAX request functionality, the tag would be:

```twig
{% winterjs request %}
```

Or to include both the JavaScript AJAX and HTML data attribute request functionality:

```twig
{% winterjs request attr %}
```