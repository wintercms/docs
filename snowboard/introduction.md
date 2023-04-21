# Snowboard.js - Winter JavaScript Framework

![image](https://github.com/wintercms/docs/blob/main/images/header-snowboard.png?raw=true)

## Introduction

Winter includes an optional JavaScript framework called **Snowboard**, which acts as an upgrade to the previous [AJAX Framework](../ajax/introduction.md) and provides many new useful features in an extensible fashion, whilst dropping previous hard dependencies to supercharge your projects even further.

The framework takes advantage of the incredible enhancements made to the JavaScript ecosystem in recent years to provide a unique experience, available only on Winter.

## Features

- Rewritten AJAX and JavaScript framework, built from the ground-up using the latest JavaScript syntax (ES2015+) and functionality.
- No dependency on jQuery, allowing the framework to be used across a wide variety of JavaScript projects.
- Easy, comprehensive extensibility and event handling.
- Small footprint and full control over which core functionalities to include ensures your website loads quick.

## Including the framework

> Before proceeding, please read the [Migration Guide](../snowboard/migration-guide.md), especially if you intend to use this framework on an existing project.

Snowboard can be included in your [CMS theme](../cms/themes) by placing the `{% snowboard %}` tag anywhere inside your [page](../cms/pages.md) or [layout](../cms/layouts.md) where you would like the JavaScript assets to be loaded - generally, this should be at the bottom of the page before the closing `</body>` tag. You must use this tag *before* you load any assets that rely on the framework, such as plugins or event listeners, and it should also be located before the `{% scripts %}` tag to allow third party code (i.e. [Winter Plugins](../plugin/registration.md#Introduction)) to provide [Snowboard plugins](plugin-development.md) if they wish.

By default, only the base Snowboard framework and [necessary utilties](../snowboard/utilities.md) are included by the `{% snowboard %}` token in order to allow for complete control over which additional features (such as the AJAX framework) are desired to be included in your themes.

You can specify further attributes to the tag to include optional additional functionality for the framework:

Attribute | Includes
--------- | --------
`all` | Includes all available plugins
`request` | The base [JavaScript AJAX](../snowboard/request.md) request functionality
`attr` | The [HTML data attribute](../snowboard/data-attributes.md) request functionality
`extras` | [Several useful UI enhancements](../snowboard/extras.md), including flash messages, loading states and transitions.

To add Snowboard to your theme with all of its features enabled, you would use the following:

```twig
{% snowboard all %}
```

To include the framework with just the JavaScript AJAX request functionality:

```twig
{% snowboard request %}
```

Or to include both the JavaScript AJAX and HTML data attribute request functionality:

```twig
{% snowboard request attr %}
```
