# Introduction to Winter CMS

- [Meet Winter CMS](#meet-winter-cms)
- [Code Structure](#code-structure)
    - [Themes](#code-structure-themes)
    - [Plugins](#code-structure-plugins)
    - [Modules](#code-structure-modules)
- [Development Features](#development-features)
    - [AJAX Framework](#ajax-framework)
    - [Dynamic Content Parser](#dynamic-content-parser)
    - [Components](#components)
    - [Asset Compiler](#asset-compiler)
    - [Image Resizer](#image-resizer)
    - [Dynamic Class Extension & Behaviors](#dynamic-class-extension-behaviors)
    - [Events](#events)
    - [Backend Skins](#backend-skins)
    - [CRUD Management](#crud-management)
- [Common Questions](#common-questions)
    - [Why Laravel?](#why-laravel)
    - [Why Laravel LTS?](#why-laravel-lts)
    - [Why Twig & not Blade?](#why-twig-not-blade)

<a name="meet-winter-cms"></a>
## Meet Winter CMS

Winter is a Content Management System (CMS) whose sole purpose is to make your development workflow simple again. Winter is built on the [Laravel framework](https://laravel.com/), leveraging the [Long Term Support (LTS)](https://laravel.com/docs/8.x/releases#support-policy) releases to ensure stability for your projects.

> Everything should be made as simple as possible, but not simpler <br>[(Albert Einstein, paraphrased)](https://quoteinvestigator.com/2011/05/13/einstein-simple/)

Projects in Winter CMS generally have a Front-end and a Back-end.

<a name="code-structure"></a>
## Code Structure

The code for your Winter CMS projects can generally exist in one of three locations, as a Theme, Plugin, or Module.

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

[Themes](../cms/themes) contain the front-end code, assets, and functionality as well as static site content. Check out the [Themes page](../cms/themes#directory-structure) to see an example of the directory structure.

#### Languages Used:

- [INI](https://en.wikipedia.org/wiki/INI_file) is used for [template configuration](../cms/themes#configuration-section)
- PHP is used to add more complex logic through the [PHP code sections](../cms/themes#php-section)
- [Twig](https://twig.symfony.com/) is used for [templating functionality and conditionals](../cms/themes#twig-section).

<a name="code-structure-plugins"></a>
### Plugins

<a name="code-structure-modules"></a>
### Modules



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
### Components

[Components](../cms/components) are the main conduit between Back-end functionality and Front-end content, being used in layouts, pages, and partials. They handle interactions and dynamic content generation as structured “objects”, including a [PHP file handling all functionality](../plugin/components), default partials for the front-end content, and any additional JS or CSS assets. They can provide configurable properties to set up aspects of the component, controlled through the Backend.

Themes can [override a component’s partial](../cms/components#customizing-default-markup) to tailor the component output to their own specifications.

<a name="asset-compiler"></a>
### Asset Compiler

Winter CMS includes a server-side [Asset Compiler](../services/asset-compilation) that makes use of the [Assetic Framework](https://github.com/assetic-php/assetic) to compile and combine assets like CSS and JavaScript serverside, through PHP, negating the need for complex build workflows. The Asset Compiler provides on-the-fly server-side compilation of SASS and LESS stylesheets as well as [run-once manual compilation of assets](../services/asset-compilation#compiler-bundles) without requiring additional workflow tools like Node or NPM. It is also able to combine and minify CSS and JS files.

Additionally, you can [define variables in the theme.yaml file](../themes/development#combiner-vars) that can be modified in the Theme Settings area of the Back-end which are then injected into the compiled files, creating flexibility for theming and branding.

<a name="image-resizer"></a>
### Image Resizer



<a name="dynamic-class-extension-behaviors"></a>
### Dynamic Class Extension & Behaviors



<a name="events"></a>
### Events



<a name="backend-skins"></a>
### Backend Skins



<a name="crud-management"></a>
### CRUD Management






<a name="common-questions"></a>
## Common Questions

As Winter is a project designed to serve the needs of developers all over the world working on all sorts of different projects we sometimes make decisions that are not obvious from the point of view of individual projects looking to build on Winter.

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

Additionally, Blade is subject to the same breaking changes that Laravel itself introduces between major versions, and while breaking changes in a template engine may be fine for a web application framework designed for single use projects that can easily update their view files as well as their logic files during upgrades of the base framework version; it does not align at all with Winter's requirement to provide a stable base to build long term projects on. Having an update to your CMS require that you make changes to your front-end is simply not the developer experience that we wish to provide.

While it is theoretically possible to have your Winter CMS projects use Blade to render templates, it isn't a recommended approach as it is important to have a single templating engine used for the front-end to ensure maximum compatibility between all Themes & Plugins.

For historical context as well, when the decision was made Blade did not have many of the more advanced features it does today, and those features (such as Components) have existed in Winter CMS since it was created in 2014.
