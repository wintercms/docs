# Introduction to Winter CMS

- [Meet Winter CMS](#meet-winter-cms)
- [Project Structure](#project-structure)
    - [Directory Structure](#project-directory-structure)
    - [Themes](#code-structure-themes)
    - [Plugins](#code-structure-plugins)
    - [Modules](#code-structure-modules)
- [Development Features](#development-features)
    - [AJAX Framework](#ajax-framework)
    - [Dynamic Content Parser](#dynamic-content-parser)
    - [Frontend Components](#components)
    - [Asset Compiler](#asset-compiler)
    - [Image Resizer](#image-resizer)
    - [Behaviors & Dynamic Class Extension](#behaviors-dynamic-class-extension)
    <!--
        @TODO
        - [Backend Widgets](#widgets)
        - [Events](#events)
        - [Backend Skins](#backend-skins)
        - [CRUD Management](#crud-management)
    -->
- [Common Questions](#common-questions)
    - [Why Laravel?](#why-laravel)
    - [Why Laravel LTS?](#why-laravel-lts)
    - [Why Twig & not Blade?](#why-twig-not-blade)

<a name="meet-winter-cms"></a>
## Meet Winter CMS

Winter is a Content Management System (CMS) whose sole purpose is to make your development workflow simple again. Winter is built on the [Laravel framework](https://laravel.com/), leveraging the [Long Term Support (LTS)](https://laravel.com/docs/8.x/releases#support-policy) releases to ensure stability for your projects.

> Everything should be made as simple as possible, but not simpler <br>[(Albert Einstein, paraphrased)](https://quoteinvestigator.com/2011/05/13/einstein-simple/)

<a name="project-structure"></a>
## Project Structure

The code for your Winter CMS projects can generally exist as one of three different types of extension; as a [Theme](#code-structure-themes), [Plugin](#code-structure-plugins), or [Module](#code-structure-modules). Code can also be included in the form of external dependencies managed by [Composer](../help/using-composer).

<a name="project-directory-structure"></a>
### Directory Structure

```
📂 MyWinterProject
 ┣ 📂 bootstrap         <-- Code used to bootstrap the application
 ┣ 📂 config            <-- Configuration files for the application
 ┣ 📂 modules           <-- Modules
 ┣ 📂 plugins           <-- Plugins
 ┣ 📂 storage           <-- Local storage directory
 ┣ 📂 tests             <-- Automated tests
 ┣ 📂 themes            <-- Themes
 ┣ 📂 vendor            <-- Vendor files managed by Composer
 ┣ 📜 artisan           <-- CLI entrypoint
 ┣ 📜 composer.json     <-- Composer project file (managing dependencies)
 ┣ 📜 composer.lock     <-- Composer lock file (info on currently installed packages)
 ┣ 📜 index.php         <-- Web request entrypoint
 ┣ 📜 phpunit.xml       <-- PHPUnit configuration for unit test
 ┗ 📜 server.php        <-- Embedded server for ease of development
 ```

<a name="code-structure-themes"></a>
### Themes

[Themes](../cms/themes) contain the frontend code, assets, and functionality as well as static site content.

Check out the [Themes page](../cms/themes#directory-structure) to see an example of the directory structure.

Themes are flat file based, but can also exist in the database through the [database templates feature](../cms/themes#database-driven-themes) as well as the [theme logging](../cms/themes#theme-logging) feature.

Themes are managed by the CMS module, the default frontend experience in Winter CMS. It is not a required module however, so it is entirely possible to not include the CMS module in your projects if desired and instead use custom plugins to have Winter act as a headless CMS, or not provide a frontend at all and use Winter solely for its backend functionality (for more complex, data focused applications like internal tools or SaaS offerings).

**Languages Used:**

- [INI](https://en.wikipedia.org/wiki/INI_file) is used for [template configuration](../cms/themes#configuration-section)
- PHP is used to add more complex logic through the [PHP code sections](../cms/themes#php-section)
- [Twig](https://twig.symfony.com/) is used for [templating functionality and conditionals](../cms/themes#twig-section).
- HTML, CSS, JS, SCSS/SASS, LESS, etc; any other frontend language can be used in frontend themes as desired.

<a name="code-structure-plugins"></a>
### Plugins

Plugins are the foundation for adding new features to Winter CMS by extending it. The registration process allows plugins to declare their features such as [components](../plugin/components) or backend menus and pages. Some examples of what a plugin can do:

1. Define [components](../plugin/components).
1. Define [user permissions](../backend/users).
1. Add [settings pages](../plugin/settings#backend-pages), [menu items](../plugin/registration#navigation-menus), [lists](../backend/lists) and [forms](../backend/forms).
1. Create [database table structures and seed data](../plugin/updates).
1. Alter [functionality of the core or other plugins](../services/events).
1. Provide classes, [backend controllers](../backend/controllers-ajax), views, assets, and other files.

Check out the [Plugins page](../plugin/registration#directory-structure) to see an example of the directory structure.

<a name="code-structure-modules"></a>
### Modules

Modules in Winter CMS can be thought of as "core plugins". Winter CMS itself consists of the following three modules:

- **System**, used to bootstrap Winter and provide common services used by all other modules and plugins, required for Winter to function.
- **Backend**, provides the backend for Winter and all of its associated services, controllers, and other features used by plugins.
- **CMS**, provides the default frontend for Winter and all of its associated services used by Themes.

Modules have a ServiceProvider class that handles booting and registration and then can contain any number of other features that would be present in a plugin. While it is possible to develop and use your own custom modules, there really isn't a need to most of the time since plugins are capable of doing anything that a custom module would be able to do while providing a better experience with a version file and plugin management features builtin to the System and Backend modules.

Of the three modules present in Winter CMS, only the System module is required for Winter to function at a basic level. It is entirely possible (and actively done in many Winter projects) to [operate with only the Backend module](../setup/configuration#backend-only-mode); usually for complex, data-heavy use cases, "intranet" type internal tools, and SaaS type offerings. While it is also possible to operate Winter with only the CMS module, that is less useful as the Backend module is where Winter really provides a lot of value.

<a name="development-features"></a>
## Development Features

Winter CMS provides a number of features out of the box to make development easier. A few of them are listed below for easier discoverability.

<a name="ajax-framework"></a>
### AJAX Framework

The [AJAX Framework](../ajax/introduction) is available in both the CMS frontend as well as the backend. Requests are processed by [server-side PHP code](../ajax/handlers) and return a response. Responses can include [rendered partials](../ajax/update-partials) (providing dynamic content), validation information, or any custom data. There is a [`data-attributes` API](../ajax/attributes-api) available for using it as well as a [JavaScript API](../ajax/javascript-api) using jQuery.

The AJAX framework is used within [Components](../cms/components), [Controllers](../backend/controllers-ajax) and [Widgets](../backend/widgets).

<a name="dynamic-content-parser"></a>
### Dynamic Content Parser

The [Dynamic Content Parser](../services/parser#dynamic-syntax-parser) is a templating engine unique to Winter CMS that's used to specify dynamic content fields right within the templates themselves. It has two modes, editor (which generates the fields necessary for the user to provide the custom content for the template) and view (which populates the template with the user provided content).

This template engine can be used on top of other templating engines but is mainly used with Twig. It can be used to generate any type of field that the backend currently supports. The dynamic content parser provides the capability of making fully customisable and intricate content pages that can be client-controlled.

<a name="components"></a>
### Frontend Components

[Components](../cms/components) are the main conduit between backend functionality and Frontend content, being used in layouts, pages, and partials. They handle interactions and dynamic content generation as structured “objects”, including a [PHP file handling all functionality](../plugin/components), default partials for the frontend content, and any additional JS or CSS assets. They can provide configurable properties to set up aspects of the component, controlled through the backend.

Themes can [override a component’s partial](../cms/components#customizing-default-markup) to tailor the component output to their own specifications.

<a name="asset-compiler"></a>
### Asset Compiler

Winter CMS includes a server-side [Asset Compiler](../services/asset-compilation) that makes use of the [Assetic Framework](https://github.com/assetic-php/assetic) to compile and combine assets like CSS and JavaScript serverside, through PHP, negating the need for complex build workflows. The Asset Compiler provides on-the-fly server-side compilation of SASS and LESS stylesheets as well as [run-once manual compilation of assets](../services/asset-compilation#compiler-bundles) without requiring additional workflow tools like Node or NPM. It is also able to combine and minify CSS and JS files.

Additionally, you can [define variables in the theme.yaml file](../themes/development#combiner-vars) that can be modified in the Theme Settings area of the backend which are then injected into the compiled files, creating flexibility for theming and branding.

<a name="image-resizer"></a>
### Image Resizing

The [Image Resizing](../services/image-resizing) service can be used for resizing any image resources accessible to the application.

It works by accepting a variety of image sources and normalizing the pipeline for storing the desired resizing configuration and then deferring the actual resizing of the images until requested by the browser. When the resizer route is hit, the configuration is retrieved from the cache and used to generate the desired image and then redirect to the generated images static path to minimize the load on the server.

Future loads of the image are automatically pointed to the static URL of the resized image without even hitting the resizer route.

<a name="behaviors"></a>
### Behaviors & Dynamic Class Extension

In Winter CMS, it is possible to dynamically extend the constructor of most classes to add new properties and methods. This also allows [binding to local events](../events/introduction#event-emitter-trait) only present on specific object instances instead of globaly.

Dynamic class extension provides the following benefits:

1. Add new properties and methods dynamically.
1. Bind to local events fired in models, widgets, and other locations, even those in the core modules or other plugins.
1. Add additional behaviours (private traits, see below) to classes.
1. Must extend the `Winter\Storm\Extension\Extendable` class or implement the `Winter\Storm\Extension\ExtendableTrait`.
1. Already-defined methods and properties cannot be overridden.
1. Extendable classes are protected against having undefined properties set on first use and must use `$object->addDynamicProperty();` instead.
1. Dynamically added methods cannot override methods defined in code.

See below for a sample of what is possible with dynamic class extension:

```php
// Dynamically extend a model that belongs to a third party plugin
Post::extend(function($model) {
    // Bind to an event that's only fired locally
    $model->bindEvent('model.afterSave', function () use ($model) {
        if (!$model->isValid()) {
            throw new \Exception("Invalid Model!");
        }
    });

    // Add a new property to the class
    $model->addDynamicProperty('tagsCache', null);

    // Add a new method to the class
    $model->addDynamicMethod('getTagsAttribute', function() use ($model) {
        if ($model->tagsCache) {
            return $model->tagsCache;
        } else {
            return $model->tagsCache = $model->tags()->lists('name');
        }
    });
});
```

[Dynamic Class Extension](../services/behaviors) also adds the ability for classes to have *private traits*, also known as [Behaviors](../services/behaviors). These are similar to [native PHP Traits](https://php.net/manual/en/language.oop5.traits.php) except they have some distinct benefits:

1. Behaviors have their own constructor.
1. Behaviors can have private or protected methods.
1. Methods and property names can conflict safely.
1. Provide a safe mechanism for shared functionality across controllers whilst still maintaining their own state.
1. Classes can be extended with behaviors dynamically.

The best of example of the power of behaviors would be the backend [form](../backend/forms), [list](../backend/lists), and [relation](../backend/relations) ControllerBehaviors that provide the majority of CRUD requirements in Winter CMS for any controllers that implement them.

<!--
    @TODO:
    <a name="widgets"></a>
    ### Backend Widgets

    <a name="events"></a>
    ### Events

    <a name="backend-skins"></a>
    ### Backend Skins

    <a name="crud-management"></a>
    ### CRUD Management
-->

<a name="common-questions"></a>
## Common Questions

As Winter is a project designed to serve the needs of developers all over the world working on all sorts of different projects we sometimes make decisions that are not obvious from the point of view of individual projects looking to build on Winter. This section should provide some background information on some of the most common questions that are asked about decisions made over the life of the project.

<a name="why-laravel"></a>
### Why Laravel?

The [Laravel documentation](https://laravel.com/docs/8.x#meet-laravel) has an excellent section on why it should be used; but basically it all boils down to the fact that it is the most developer-friendly PHP framework available, not to mention the largest PHP framework with the largest collection of third party packages available for it.

<a name="why-laravel-lts"></a>
### Why Laravel LTS?

Winter is based of off [Laravel LTS releases](https://laravel.com/docs/8.x/releases#support-policy) to ensure the stability of the platform as a whole for all of our users. Laravel has, in the past, sometimes introduced breaking changes during their intermediary major releases between LTS versions. While this is great for the development of the framework, Winter CMS prides itself on its stability and reliability. This means that whenever the core version of Laravel that Winter CMS targets is upgraded, the Winter CMS development team invests a significant amount of time smoothing over the breaking changes as much as possible as well as putting together detailed, highly targeted migration guides that make the update process a breeze.

As such, to keep maintenance burdens low on both the core Winter CMS develoment team as well as our end users, Winter CMS targets only LTS releases of Laravel. This is usually not a problem as all first-party Laravel packages support LTS releases as well as most of the third party package ecosystem.

<a name="why-twig-not-blade"></a>
### Why Twig & not Blade?

Winter CMS uses [Twig](https://twig.symfony.com/) as the default templating engine, despite [Blade](https://laravel.com/docs/6.x/blade) being the templating engine provided by Laravel.

At a basic level, all templating engines are the same. However, Winter made the decision to go with Twig as it is a robust and mature templating language that is easy to learn, widely used in other project, and very similar to [Liquid](https://shopify.github.io/liquid/), the templating engine used by [Shopify](https://www.shopify.com/). These factors combined mean that Twig is easier to pick up and start using for most developers without requiring any existing knowledge of PHP or Blade specifically which means that a wider developer base can have an easier time working with Winter CMS.

Additionally, Blade is subject to the same breaking changes that Laravel itself introduces between major versions, and while breaking changes in a template engine may be fine for a web application framework designed for single use projects that can easily update their view files as well as their logic files during upgrades of the base framework version; it does not align at all with Winter's requirement to provide a stable base to build long term projects on. Having an update to your CMS require that you make changes to your frontend is simply not the developer experience that we wish to provide.

While it is theoretically possible to have your Winter CMS projects use Blade to render templates, it isn't a recommended approach as it is important to have a single templating engine used for the frontend to ensure maximum compatibility between all themes & plugins.

For historical context as well, when the decision was made Blade did not have many of the more advanced features it does today, and those features (such as Components) have existed in Winter CMS since it was created in 2014.
