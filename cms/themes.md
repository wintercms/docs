# CMS Themes

## Introduction

Themes define the appearance of your website or web application built with Winter. Winter themes are completely file-backed and can be managed with any version control system, for example, Git. This page gives you the high-level description of Winter themes. You will find more details about [pages](pages), [partials](partials), [layouts](layouts) and [content files](content) in the corresponding articles.

> **NOTE:** Themes can store templates in the database if `cms.databaseTemplates` is enabled, see the [database driven themes](#database-driven-themes) section for more information.

Themes are directories that reside in the **/themes** directory by default. Themes can contain the following objects:

Object | Description
------------- | -------------
[Pages](pages) | represent the website pages.
[Partials](partials) | contain reusable chunks of HTML markup.
[Layouts](layouts) | define the page scaffold.
[Content files](content) | text, HTML, or [Markdown](http://daringfireball.net/projects/markdown/syntax) blocks that can be edited separately from the page or layout.
**Asset files** | are resource files like images, CSS and JavaScript files.

## Directory structure

Below, you can see an example theme directory structure. Each Winter theme is represented with a separate directory and generally, one active theme is used for displaying the website. This example displays the "example-theme" theme directory.

```treeview
themes/
`-- example-theme/
    |-- assets/                 # Theme assets
    |   |-- css/
    |   |-- fonts/
    |   |-- images/
    |   |-- javascript/
    |   |-- scss/
    |   `-- vendor/
    |-- content/                # Static content files
    |   |-- static-pages/       # Content files from the Winter.Pages plugin
    |   |   `-- index.htm
    |   `-- welcome.htm
    |-- layouts/                # Theme Layouts (Page scaffolds / templates)
    |   |-- default.htm
    |   `-- sidebar.htm
    |-- meta/                   # Menu definitions and other plugin specific YAML files
    |   |-- menus/
    |   |   |-- main-menu.yaml
    |   |   `-- sitemap.yaml    # Meta file describing the sitemap
    |   `-- static-pages.yaml   # Meta file describing the structure of the Winter.Pages static pages
    |-- pages/                  # Theme Pages (Contain the routing for the frontend)
    |   |-- 404.htm             # Page for 404 responses
    |   |-- home.htm
    |   |-- error.htm           # Page for 500 responses
    |   `-- sitemap.htm         # Page for rendering the sitemap response
    |-- partials/               # Theme Partials (Reusable pieces of HTML markup)
    |   |-- html-footer.htm
    |   |-- html-header.htm
    |   `-- navbar.htm
    |-- theme.yaml              # Theme information file
    `-- version.yaml            # Theme updates file
```

> The active theme is set with the `activeTheme` parameter in the `config/cms.php` file or with the Theme Selector on the System > CMS > Frontend Theme backend page. The theme set with the Theme Selector overrides the value in the `config/cms.php` file.

### Subdirectories

Winter supports single level subdirectories for **pages**, **partials**, **layouts** and **content** files, while the **assets** directory can have any structure. This simplifies the organization of large websites. In the example directory structure below, you can see that the **pages** and **partials** directories contain the **blog** subdirectory, and the **content** directory contains the **home** subdirectory.

```treeview
themes/
`-- website/
    |-- pages/
    |   |-- home.htm
    |   `-- blog/                  # Subdirectory
    |       |-- archive.htm
    |       `-- category.htm
    |-- partials/
    |   |-- sidebar.htm
    |   `-- blog/                  # Subdirectory
    |       `-- category-list.htm
    `-- content/
       |-- footer-contacts.txt
       `-- home/                   # Subdirectory
           `-- intro.htm
```

To refer to a partial or a content file from a subdirectory, specify the subdirectory's name before the template's name. Example of rendering a partial from a subdirectory:

```twig
{% partial "blog/category-list" %}
```

> **NOTE:** The template paths are always absolute. If, in a partial, you render another partial from the same subdirectory, you still need to specify the subdirectory's name.

## Template structure

Pages, partials and layout templates can include up to 3 sections: **configuration**, **PHP code**, and **Twig markup**.
Sections are separated with the `==` sequence.
For example:

```twig
url = "/blog"
layout = "default"
==
function onStart()
{
    $this['posts'] = ...;
}
==
<h3>Blog archive</h3>
{% for post in posts %}
    <h4>{{ post.title }}</h4>
    {{ post.content }}
{% endfor %}
```

### Configuration section

The configuration section sets the template parameters. Supported configuration parameters are specific for different CMS templates and described in their corresponding documentation articles. The configuration section uses the simple [INI format](http://en.wikipedia.org/wiki/INI_file), where string parameter values are enclosed within quotes. Example configuration section for a page template:

```ini
url = "/blog"
layout = "default"

[component]
parameter = "value"
```

### PHP code section

The code in the PHP section executes every time before the template is rendered. The PHP section is optional for all CMS templates and its contents depend on the template type where it is defined. The PHP code section can contain optional open and close PHP tags to enable syntax highlighting in text editors. The open and close tags should always be specified on a different line to the section separator `==`.

```twig
url = "/blog"
layout = "default"
==
<?
function onStart()
{
    $this['posts'] = ...;
}
?>
==
<h3>Blog archive</h3>
{% for post in posts %}
    <h4>{{ post.title }}</h4>
    {{ post.content }}
{% endfor %}
```

In the PHP section, you can only define functions and refer to namespaces with the PHP `use` keyword. No other PHP code is allowed in the PHP section. This is because the PHP section is converted to a PHP class when the page is parsed. Example of using a namespace reference:

```php
url = "/blog"
layout = "default"
==
<?
use Acme\Blog\Classes\Post;

function onStart()
{
    $this['posts'] = Post::get();
}
?>
==
```

As a general way of setting variables, you should use the array access method on `$this`, although for simplicity you can use **object access as read-only**, for example:

```php
// Write via array
$this['foo'] = 'bar';

// Read via array
echo $this['foo'];

// Read-only via object
echo $this->foo;
```

### Twig markup section

The Twig section defines the markup to be rendered by the template. In the Twig section, you can use functions, tags, and filters [provided by Winter](../markup), all the [native Twig features](https://twig.symfony.com/doc/2.x/), or those [provided by plugins](../plugin/registration#extending-twig). The content of the Twig section depends on the template type (page, layout, or partial). You can find more information about specific Twig objects further in the documentation.

More information can be found [in the Markup guide](../markup).

## Theme Logging

Winter CMS comes with a very useful feature, disabled by default, called Theme Logging.

Since layouts and pages store most of the data in flat files, it's possible for you or your clients to accidentally lose content. For example, switching the layout of a page will modify the scaffold of the page, and, as such, will result in data loss.

To enable Theme Logging, simply go to **Settings -> Log settings** and enable **Log theme changes**. All changes are now logged.

The theme changelog can be viewed at **Settings -> Theme log**. Each change has an overview of what has been added/removed, along with a copy of the changed file before and after. You can use this information to decide the appropriate action, to aid the reversion of these changes, if necessary.

## Database Driven Themes

Winter CMS comes with another very useful feature, disabled by default, called Database Driven Themes. When this feature is enabled (by setting `cms.databaseTemplates` to `true`, or `null` when `app.debug` is `false`); the database layer stores all modified CMS files in the database. Files that are not modified continue to be loaded from the filesystem. There is a [`theme:sync $themeDir`](../console/theme-management#synchronise-database-templates) console command that can be used to sync changes between the filesystem and database.

Files modified in the database are cached to indicate that they should be loaded from the database.

> **NOTE:** All CMS template objects (ex. `Layout`, `Page`, `Content`, `Partial`, `Meta`, etc) are stored in the database when this feature is enabled and a change is made to the template in question; however theme asset files will **not** be.

## Child Themes

Child themes allow you to create a new theme that inherits templates and assets from a "parent" theme. This is useful when you want to customize an existing theme without modifying the original files, create variations of a base theme, or manage multiple similar themes for different clients or tenants.

When using a child theme, you can override specific pages, partials, layouts, content files, or assets from the parent theme by creating files with the same path in your child theme. Any files not present in the child theme will automatically fall back to the parent theme.

### Configuring a Child Theme

To create a child theme, specify the parent theme's directory name using the `parent` key in your child theme's `theme.yaml` file:

```yaml
name: "My Custom Theme"
description: "A customized version of the Demo theme"
author: "Your Name"
homepage: "https://example.com"
parent: demo
```

The value of `parent` should be the directory name of the parent theme (e.g., if the parent theme is located at `themes/demo`, use `demo` as the parent value).

> **NOTE:** See the [Theme information file](../themes/development#theme-information-file) documentation for more details on configuring the `parent` option.

### Creating a Child Theme

The minimal requirement for a child theme is a `theme.yaml` file that specifies a parent theme. Here's how to create a basic child theme:

**Step 1:** Create a new theme directory:

```treeview
themes/
|-- demo/                   # Parent theme
`-- my-custom-theme/        # Child theme
    `-- theme.yaml
```

**Step 2:** Define the parent in `themes/my-custom-theme/theme.yaml`:

```yaml
name: "My Custom Theme"
parent: demo
```

At this point, your child theme will function identically to the parent theme, as it inherits all templates and assets.

**Step 3:** Override specific files as needed. For example, to customize the homepage:

```treeview
themes/
|-- demo/
|   |-- pages/
|   |   `-- home.htm        # Parent's homepage
|   `-- theme.yaml
`-- my-custom-theme/
    |-- pages/
    |   `-- home.htm        # Child's customized homepage
    `-- theme.yaml
```

When the child theme is active, Winter will use `my-custom-theme/pages/home.htm` instead of `demo/pages/home.htm`, while all other pages from the parent theme remain available.

### Inheritance Behavior

Child themes use a cascading file resolution system. When Winter needs to load a template or asset, it searches in the following order:

1. Child theme database templates (if [database templates](#database-driven-themes) are enabled)
2. Child theme filesystem
3. Parent theme database templates (if database templates are enabled)
4. Parent theme filesystem

This means:

- **Templates** (pages, partials, layouts, content files) in the child theme override those in the parent theme when they have the same path
- **Assets** (CSS, JavaScript, images, fonts) in the child theme override those in the parent theme when they have the same path
- **Localization strings** from the child theme's `lang` directory override parent theme strings
- Any file not present in the child theme falls back to the parent theme

#### Example Directory Structure

Here's an example showing a child theme that overrides specific files:

```treeview
themes/
|-- demo/                           # Parent theme
|   |-- assets/
|   |   |-- css/
|   |   |   `-- theme.css           # Parent CSS
|   |   `-- images/
|   |       `-- logo.png            # Parent logo
|   |-- layouts/
|   |   `-- default.htm             # Parent layout
|   |-- pages/
|   |   |-- home.htm                # Parent homepage
|   |   `-- about.htm               # Parent about page
|   |-- partials/
|   |   |-- header.htm              # Parent header
|   |   `-- footer.htm              # Parent footer
|   `-- theme.yaml
|
`-- my-custom-theme/                # Child theme
    |-- assets/
    |   |-- css/
    |   |   `-- theme.css           # OVERRIDES parent CSS
    |   `-- images/
    |       `-- logo.png            # OVERRIDES parent logo
    |-- partials/
    |   `-- header.htm              # OVERRIDES parent header
    `-- theme.yaml                  # Specifies parent: demo
```

In this example, when `my-custom-theme` is active:
- `theme.css` and `logo.png` load from the child theme
- `header.htm` loads from the child theme
- `footer.htm` loads from the parent theme (inherited)
- `default.htm` layout loads from the parent theme (inherited)
- Both `home.htm` and `about.htm` pages load from the parent theme (inherited)

### Asset Management

Assets in child themes work with the same fallback mechanism as templates. When referencing assets using the `theme.assetUrl()` helper or the `| theme` filter, Winter will check the child theme first, then fall back to the parent theme if the asset doesn't exist.

#### Example: Asset References

In your templates:

```twig
{# This will use child theme's logo if it exists, otherwise parent's logo #}
<img src="{{ 'assets/images/logo.png' | theme }}" alt="Logo">

{# CSS files cascade the same way #}
<link href="{{ 'assets/css/theme.css' | theme }}" rel="stylesheet">
```

#### Asset Compilation

When using the [asset combiner](../markup/filter/theme#combiner-aliases), child theme assets are resolved first:

```twig
{# If custom.css exists in child theme, it will be used; otherwise parent's version #}
<link href="{{ ['assets/css/theme.css', 'assets/css/custom.css'] | theme }}" rel="stylesheet">
```

### Database-Driven Child Themes

Child themes can be purely virtual, meaning they don't require a physical directory on the filesystem. This is particularly useful for multi-tenant applications where each tenant needs a customized theme.

When [database templates](#database-driven-themes) are enabled, you can:

1. Create a database-only record for the child theme with a `theme.yaml` file specifying the parent
2. Store all template customizations in the database
3. Inherit all other files from the parent theme's filesystem

This approach allows you to:
- Manage hundreds of similar themes without duplicating files
- Update the parent theme and have changes cascade to all child themes automatically
- Store tenant-specific customizations in the database while sharing a common codebase

**Example: Creating a virtual child theme**

1. Enable database templates in `config/cms.php`:

```php
'databaseTemplates' => true,
```

2. Create a theme record in the database with just the `theme.yaml` content:

```yaml
name: "Client A Custom Theme"
parent: base-theme
```

3. Customize only the templates that need to differ from the parent by saving them to the database

The child theme will now function without any physical directory, inheriting everything from `themes/base-theme` except for the database-stored customizations.

### Best Practices

**When to use child themes:**
- Customizing a third-party theme while preserving the ability to update it
- Creating multiple branded variations of a base theme
- Building a multi-tenant application where each tenant needs minor customizations
- Developing a theme framework where a base theme provides core functionality

**When to create a new theme instead:**
- Making extensive changes that affect most templates and assets
- Building something significantly different from the original design
- When you need to modify the theme structure itself

**Organization tips:**
- Keep child themes minimal - only override what's necessary
- Document which files are overridden and why
- Use clear, descriptive names for child themes (e.g., `mytheme-client-a`, `mytheme-blue-variant`)
- Consider using [theme customization](../themes/development#theme-customization) for simple configuration changes before creating a child theme

**Performance considerations:**
- Child themes have minimal performance impact - file resolution is cached
- Avoid deep nesting (grandparent/parent/child) - only one level of inheritance is supported
- Database-driven templates are cached, so virtual child themes perform well

### Updating Parent Themes

When updating a parent theme, child themes automatically inherit the changes for any files they haven't overridden. This means:

- Bug fixes and improvements in the parent theme benefit all child themes
- New templates added to the parent theme become available to child themes
- Breaking changes in parent templates that are overridden in the child theme won't affect the child theme

To update a parent theme safely:

1. Test the parent theme update in a development environment
2. Check if any overridden templates in child themes are affected by parent theme changes
3. Update child theme overrides if necessary to maintain compatibility
4. Deploy the parent theme update

> **NOTE:** Child themes only support one level of inheritance. A child theme cannot specify another child theme as its parent.
