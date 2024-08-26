# Developer Guide

The following details outline our standards and guidelines to producing high-quality code and documentation for Winter. While you are free to choose your own standards and guidelines for your own projects, the maintainer team of Winter recommends that you follow these standards for contributions either to the Marketplace, or to the [core Winter CMS repositories](https://github.com/wintercms).

## PHP coding standards

Winter follows the [PSR-12 Extended Coding Style Guide](https://www.php-fig.org/psr/psr-12/) as set forth by the [PHP Framework Interop Group](https://www.php-fig.org/). This guide provides a great starting point for creating code that is easily understandable and familiar to developers, and ensures consistency with other PHP frameworks and projects.

This coding style will be enforced on all contributions to Winter CMS and first-party plugins through automated code style checks. It is recommended that you use IDE tools to check your code style, such as installing the [phpcs extension](https://marketplace.visualstudio.com/items?itemName=shevaua.phpcs) for Visual Studio Code or [enabled PHP_CodeSniffer support](https://www.jetbrains.com/help/phpstorm/using-php-code-sniffer.html) in PHPStorm. You will be required to correct any incorrectly formatted code before we can accept any contributions to the Winter CMS core or first-party plugins.

You may also use the `php artisan winter:phpcs` command in your project to run the in-built PHP_CodeSniffer installation included with Winter, if you have installed the development dependencies of Winter through Composer.

### Additions and exceptions to the standard

We have included some additional rules and made some exemptions to the PSR-12 standard in Winter code for certain language features and syntax that are more open to interpretation in the PSR-12 standard, or where there is a historical choice or technical limitation that prevents us from following PSR-12 to the letter. We will outline these below.

#### Controller methods can have a single underscore

The PSR-12 guidelines state that methods must be in **camelCase** format. However, in [Backend controllers](../backend/controllers-ajax.md) in Winter, AJAX handlers can be created to be scoped to a particular action by defining a method for the action, and then adding an underscore followed by the AJAX handler name. For example:

```php
public function index()
{
    // This is the index page (index action)
}

public function index_onDoSomething()
{
    // AJAX handler only works on the index action
}

public function onDoSomethingElse()
{
    // AJAX handler works globally for all actions
}
```

While this should mostly be limited to controllers and components, due to historical usage in other areas (such as traits and behaviours), we have decided to grant an exemption from this rule. However, you should avoid underscores in method names unless explicitly required for the reason above.

#### Curly braces and indenting for condition blocks

Historical code in Winter has previously used a format where all closing curly braces are on their own line, such as with the following:

```php
if ($expr1) {
    // if body
}
elseif ($expr2) {
    // elseif body
}
else {
    // else body;
}
```

The PSR-12 guidelines have specific requirements for the placement of curly braces for condition blocks and control structures, as defined in [Section 5. Control Structures](https://www.php-fig.org/psr/psr-12/#5-control-structures). Thus, all new code must follow these requirements, for example:

```php
// SINGLE LINE CONDITION STATEMENTS
if ($expr1) {
    // if body
} elseif ($expr2) {
    // elseif body
} else {
    // else body;
}

// MULTI LINE CONDITION STATEMENTS
if (
    $expr1 &&
    $longExpression &&
    $superLongExpressionTooMuchToHandleOhNoWontSomeoneThinkOfTheColumnCount
) {
    // if body
} elseif (
    $seriously ||
    $thisIsSuperLongAndContrived ||
    $butThisIsBetterThanSuperLongOneLiners
) {
    // elseif body
} else {
    // else body;
}
```

In addition, some historical code opted to not use curly braces at all for single line condition blocks.

```php
if ($condition)
    doSomething();
```

We no longer support this format, and instead require all condition blocks use curly braces, even if they are single line.

```php
if ($condition) {
    doSomething();
}
```

#### Use of trailing commas

Although not specified one way or another in the PSR-12 guidelines, Winter CMS requires the use of a trailing comma for the last item in a multi-line array. Trailing commas make it easier to perform maintenance on multiline array items without causing unnecessary visual clutter in diffs or version control history should an item be added or removed from that array.

Single-line arrays do not require a trailing comma for the last item.

**Valid:**

```php
$items = [
    'apple',
    'banana',
    'orange',
];

$items = ['apple', 'banana', 'orange'];
```

**Invalid**:

```php
$items = [
    'apple',
    'banana',
    'orange'
];

$items = ['apple', 'banana', 'orange',];
```

#### Imports (use cases)

As some classes in Winter CMS can use a large number of imported classes and traits, we have extended our ruleset beyond PSR-12 to add the following additional requirements for importing classes through `use` definitions:

##### - All `use` definitions must be alphabetically ordered, including the namespace

**Valid:**

```php
use Cms\Classes\Theme;
use Exception;
use Illuminate\Support\Facades\Cache;
use Winter\Storm\Database\Model;
```

**Invalid:**

```php
use Exception;
use Illuminate\Support\Facades\Cache;
use Cms\Classes\Theme;
use Winter\Storm\Database\Model;
```

##### - We disallow the use of the [grouped `use` declarations](https://www.php.net/manual/en/language.namespaces.importing.php#language.namespaces.importing.group) as it creates additional cruft in diffs

**Valid:**

```php
use Cms\Classes\Layout;
use Cms\Classes\Partial;
use Cms\Classes\Theme;
```

**Invalid:**

```php
use Cms\Classes\{
    Layout,
    Partial,
    Theme,
};
```

##### - We disallow using a `use` case for a class that exists in the same namespace as the current class

**Valid:**

```php
namespace Acme\Blog\Classes;

class Foo
{
    // ...
    $bar = new Bar();
    // ..
}
```

**Invalid:**

```php
namespace Acme\Blog\Classes;

use Acme\Blog\Classes\Bar;

class Foo
{
    // ...
    $bar = new Bar();
    // ..
}
```

##### - We disallow a starting backslash for `use` cases, unless it is for importing a trait

**Valid:**

```php
namespace Acme\Blog\Classes;

use Cms\Classes\Layout;
use Cms\Classes\Partial;
use Cms\Classes\Theme;

class MyClass
{
    use \Acme\Blog\Traits\MyTrait;
}
```

**Invalid:**

```php
namespace Acme\Blog\Classes;

use \Cms\Classes\Layout;
use \Cms\Classes\Partial;
use \Cms\Classes\Theme;

// ...
```

##### - We disallow `use` cases for classes that are unused

You should strip out any `use` cases if the class does not get used throughout the code, as leaving it may mislead future developers as to the capabilities and requirements of that class.

#### Use of single / double quotes

Winter CMS highly recommends the use of single quotes around strings, instead of double quotes. If the string itself contains a single quote as an apostrophe, you should escape that single quote within the string.

**Recommended:**

```php
$translations = [
    'key1' => 'key1\'s value',
];
```

**Not recommended**:

```php
$translations = [
    'key1' => "key1's value",
];
```

#### Templates

Several of PSR-12's rules are relaxed for templates, including CMS templates, and partials, layouts and views in the Backend. Since coding style for mixed PHP/HTML files is not well defined by PSR-12, we have made some determinations on the best "style" for mixed files and have opted to relax the following rules:

- There is no requirement for a space after the opening of a control block (`if`, `switch`, `when`) and a curly brace or colon, as we generally use the "template" PHP format for templates and it looks neater (ie. `<?php if ($something === true): ?>`)
- Templates may finish with a closing `?>` PHP tag if it is required to close off a control block or output block.
- The body of a `case` block within `switch` blocks does not need to be on the next line.
- The `break` after a `case` block also does not need to be on the next line.
- Spacing between "header" blocks (such as `use` cases) does not need to followed. You will still need to define each `use` case on its own line, but will no longer need a line before or after the block.
- Array indentation is not strictly defined in templates, and can be reformatted to better fit the content shown.

## Developer standards and patterns

### Vendor naming

The vendor or author code in a namespace must begin with an uppercase character and should not contain underscores or dashes. These are examples of valid names:

```
Acme.Blog
WinterStorm.User
Happygilmore.Golf
```

These are examples of names that are **not** valid:

```
acme.blog
winterStorm.user
Happy_gilmore.Golf
```

### Repository naming

When publishing work to a repository, such as Git, use the following naming as a convention. Plugins should be named with a `-plugin` suffix and optional `wn-` prefix.

```
blog-plugin
wn-blog-plugin
```

Themes should be named with the `-theme` suffix and optional `wn-` prefix.

```
happy-theme
wn-happy-theme
```

Projects can be named with a `wn-` prefix and suffix indicating the project type (i.e. `-site`, `-app`, etc), although this is just a suggested convention and isn't enforced anywhere.

```
wn-wintercms.com-site
wn-api.wintercms.com-app
```

### PHP Variable naming

Use **camelCase** everywhere except for the following:

1. Database attributes and relationships should use **snake_case**
1. Postback parameters and HTML elements should use **snake_case**
1. Language keys should use **snake_case**

### PHP Class naming

Use **PascalCase** for all classes. Underscores are to be avoided as they have been known to cause issues with autoloading due to the historical use of underscores in class names for pseudo namespacing before namespacing was officially supported in PHP. Numbers can be used if necessary (generally to denote that a class interacts with a specific version of a service, i.e. `Conman` vs `Conman4`) but should never be used at the start of a class name. Avoid all special characters.

If you find yourself wanting to use underscores in your class names for organizational purposes, don't. Use namespacing instead.

Don't:

- `jet`
- `myJet`
- `my_jet`
- `API_Jet`

Do:

- `Jet`
- `MyJet`
- `API\Jet`

### HTML element naming

Form element names should use snake_case (underscores)

```html
<input name="first_name">
```

Where the name is an array, the array keys can be either StudlyCase or snake_case.

```html
<input name="ForumMember[first_name]">
<input name="forum_member[first_name]">
```

Element IDs should be camel case or hyphen-case (dashes)

```html
<div id="firstNameGroup">
    <input id="firstName">
</div>

<div id="first-name-group">
    <input id="first-name">
</div>
```

Element classes names should use hyphen-case (dashes)

```html
<div class="form-group">
    <input class="form-control">
</div>
```

### View file naming

Partial views should begin with an underscore character. Whereas Controller and Layout views do not begin with an underscore character. Since views are often found in a single folder, the underscore (_) and dash (-) characters can be used to organise the files. A dash is used as a substitute for a space character. An underscore is used as a substitute for a slash character (folder or namespace).

```
index_fancy-layout.php       <== Index\Fancy layout
form-with-sidebar.php        <== Form with sidebar
_field-container.php         <== Field container (partial)
_field_baloon-selector.php   <== Field\Baloon Selector (partial)
```

View files must end with the `.php` file extension.

> **NOTE:** For backwards compatibilty, we still support the `.htm` legacy file extension. It is recommended to use `.php` for any new files.

### Class naming

Classes commonly are placed in the `classes` directory. There is a number of class suffixes and prefixes that we recommend to use.

1. Manager
1. Builder
1. Writer
1. Reader
1. Handler
1. Container
1. Protocol
1. Target
1. Converter
1. Controller
1. View
1. Factory
1. Entity
1. Engine
1. Bag

> Don't get naming paralysis. Yes, names are very important but they're not important enough to waste huge amounts of time on. If you can't think up a good name in five minutes, move on.

### Event naming

When specifying [event names](../events/introduction). The term *after* is not used in Events, only the term *before* is used. For example:

1. **beforeSetAttribute** - this event comes *before* any default logic.
1. **setAttribute** - this event comes *after* any default logic.

> **NOTE:** This is true for the vast majority of cases, however the events present for default model events like `boot`, `create`, `delete`, `fetch`, `restore`, `save`, `update`, & `validate` all have before & after variants to match the model method events.

Where possible events should cover global and local versions. Global events should be prefixed with the module or plugin name. For example:

```php
// For global events, it is prefixed with the module or plugin code
Event::fire('cms.page.end');

// For local events, the prefix is not required
$this->fireEvent('page.end');
```

Avoid using terms such as *onSomething* in event names since the word *bind*/*fire* represent this action word.

It is good practise to always pass the calling object as the first parameter to the global event, the local event should not need this. Local events take priority over global events when halting, or come first when processing.

```php
// Local event
if ($this->fireEvent('beforeAddContent', [$message, $view], true) === false) {
    return;
}

// Global event
if (Event::fire('mailer.beforeAddContent', [$this, $message, $view], true) === false) {
    return;
}
```

When expecting multiple results, it is easy to combine the arrays like so:

```php
// Combine local and global event results
$eventResults = array_merge(
    $this->fireEvent('form.beforeRefresh', [$saveData]),
    Event::fire('backend.form.beforeRefresh', [$this, $saveData])
);
```

### Database table naming

Tables names should be prefixed with the author and plugin name.

```
acme_blog_xxx
```

Boolean column names should be prefixed with `is_`

```
is_activated
is_visible
```

This is because the model attributes can conflict, for example, `public $visible;` in the Model class conflicts with a database column with the same name. Some column names are exceptions, for example `notify_user`.

If your plugin extends tables belonging to other plugins, the added column names should be prefixed with the author and plugin name:

```
acme_blog_category_id
```

The author and plugin name acronym is also acceptable as a prefix:

```
ab_category_id
```

### Component naming

Component classes are commonly place in the `components` directory. The name of a component should represent its primary function.

To display a list of records, use the `List` suffix, eg:

```
ProductList
ProductReviewList
CategoryList
```

To display a single record, use the `Details` suffix, eg:

```
ProductDetails
CategoryDetails
```

Using the suffix helps avoid conflicts with controller and model names. Alternatively you can name components without the suffix, for cases when the name is descriptive and does not conflict:

```
ProductReviews
CustomerCheckout
SeoDirectory
UserProfile
```

### Controller naming

Controllers are commonly are placed in `controllers` directory, for backend controllers. The name of a controller should be in plural form, for example:

```
People
Products
Categories
ProductCategories
```

### Model naming

Models are commonly are placed in `models` directory. The name of a model should be in singular form, for example:

```
Person
Product
Category
ProductCategory
```

When dynamically extending other plugin's models, you should prefix the field with at least the plugin name. This helps to avoid potential future conflicts if that plugin is updated to add new relationships that could conflict with your dynamic relationships.

```php
User::extend(function($model) {
    $model->hasOne['forum_member'] = ['Winter\Forum\Models\Member'];
});
```

The fully qualified plugin name is also acceptable, for example:

```php
$user->winter_forum_member
```

### Model scopes

[Model scopes](../database/model#query-scopes) should always return the scoped `QueryBuilder` instance to support scope chaining. If a scope is not returning a `QueryBuilder` instance then it is not a scope and should be a regular / static method instead.

```php
// Valid scope method
public function scopeWithValidUser(Builder $query, User $user)
{
    return $query->where('user_id', $user->id);
}

// Invalid scope method
public function scopeWithValidUser(Builder $query, User $user)
{
    return $query->where('user_id', $user->id)->get();
}

// Valid standalone method returning a collection
public function getValidUser(User $user)
{
    return $this->withValidUser($user)->get();
}
```

Scope naming can be tricky as it's important to avoid conflicts with other aspects of the model's API, such as relationships & attributes. Generally speaking, scopes that receive additional parameters should be prefixed with `apply` to indicate they are being applied to the query. Defined as:

```php
public function scopeApplyUser(Builder $query, User $user)
{
    return $query->where('user_id', $user->id);
}
```

Then applied to the model as:

```php
$model->applyUser($user);
```

Whilst `apply` is the ideal prefix name for those situations, here are some other prefixes we recommended for chained scopes:

- `is`
- `for`
- `with`
- `without`
- `filter`

### Class guidance

These points are to be considered in a relaxed fashion:

1. In classes, properties and methods should be declared as `protected` in favor of `private` so that all classes can be used as base classes. Similarily, `static::someMethodOrConstantOrProperty` is preferred to `self::someMethodOrConstantOrProperty`, again to make it easier to extend classes.
1. If a property contains a single value (not an array), make the property `public` instead of a get/set approach.
1. If a property contains a collection (is an array), make the property `protected` with get `getProperties`, `getProperty` and `setProperty`.

## Writing documentation

Your contributions to the Winter documentation are very welcome. We ask that you follow these guidelines when committing or proposing changes to the documentation.

### Grammar & language

The Winter documentation is written in [GitHub Flavored Markdown](https://github.github.com/gfm/) and should adhere to this specification.

At the moment, the Winter documentation only supports the English language. Documentation should be provided in US English. Where possible, you should strive to use natural language that would sound like a person verbally speaking to you, however, the maintainer team are more than happy to assist you with suggestions to this.

A number of our users use translation tools such as Google Translate to read our documentation, so where possible, you should avoid using too much jargon that may not be easily translateable.

### Headings

Each page should have a single `# Title` (`<h1>`) at the top of the page that provides the title for the entire page. For all other headings, you should follow a logical heading structure, with main sections using a `## Secondary Heading` (`<h2>`) title and sub-sections using a `### Tertiary Heading` (`<h3>`) title.

You do not need to provide anchors for the headings, as these will be automatically generated.

```md
# The main title

This is some introductory content for the page itself.

## Section heading

The section may have some introductory content here.

### Sub-section heading

This is the content of the first sub-section.

### Sub-section 2 heading

This is the content of the second sub-section.

## Next section heading
```

### Table of contents

The Winter documentation site automatically generates a table of contents based on the heading structure of the page. You do not need to manually provide a table of contents. The table of contents is generated from the section and sub-section headings (`<h2>` and `<h3>`).

### Emphasis

You may use **bold** (`**bold**`) and *italicised* (`*italicised*`) emphasis as required, but you should strive to use it sparingly. Avoid using either emphasis type for variable or path names and opt to use inline code blocks instead, as this will prevent these names from being translated by translation services such as Google Translate and ensure that people who use these tools will still be able to find these paths or variables.

### Linking

It is encouraged to use linking as much as possible throughout the documentation, in order to provide users the most context possible during browsing the documentation. However, we recommend that you do not add links to the same article twice in the same section or sub-section, unless it is to another anchor point on the page.

External links are encouraged, but please confirm that the external resource is useful and operational. External links will always be opened up in a new tab.

### Code

We recommend the use of `inline code` code blocks for all path, variable and single line code references in the documentation. This provides a visual clue that the reference relates to code or the Winter files, and as stated above, will prevent the references from being translated. Inline code blocks can be created by wrapping the reference with a single backtick (<code>`</code>) at the start and end of the reference.

For larger, multiple line code blocks, we recommend the use of the code block format, by adding three backticks (<code>```</code>) to the start and end of the code block. You may also specify the language of the code immediately after the first set of backticks to enable the code highlighting feature of code blocks.

<pre><code>```md
This is a code block for the Markdown language.

**It should highlight parts of the language.**
```</code></pre>

Will be converted into:

```md
This is a code block for the Markdown language.

**It should highlight parts of the language.**
```

### File structures

File structures can be demonstrated in Winter documentation with the `treeview` language inside a code block. The tree view language is a special language which uses a tree diagram to traverse the file and folder hierarchy.

A sample of a file structure can be found below:

<pre><code>```treeview
folder/
|-- subfolder1/
|-- subfolder2/
|   |-- image.jpeg
|   |-- photo.png
|   `-- document.pdf
|-- subfolder3/
|   |-- sub2folder/
|   |    `-- sub3file    # A comment about this file
|   `-- sub2folder2/
|-- index.php
`-- .hidden_file        # This file is hidden and should be slightly transparent
```</code></pre>

Which is then converted to the following code block:

```treeview
folder/
|-- subfolder1/
|-- subfolder2/
|   |-- image.jpeg
|   |-- photo.png
|   `-- document.pdf
|-- subfolder3/
|   |-- sub2folder/
|   |    `-- sub3file    # A comment about this file
|   `-- sub2folder2/
|-- index.php
`-- .hidden_file        # This file is hidden and should be slightly transparent
```

To break down this structure:

- A **folder** is a name followed by a `/` character.
- A **file** is a name with no `/` character suffixed.
- A `|--` tag indicates a child item of the item above. The pipe symbol (`|`) should align with the first character of the parent item. A space should always proceed the tag.
- A <code>`-- </code> tag is the same as the <code>|-- </code> tag but indicates the *last* child of the item.
- A `|` (pipe) should be use for parent folders that are still "open" while their child folders and files are being traversed. You must proceed a pipe with 3 spaces if there is any content to follow.
- A `# comment` can be left at the end of any line. It cannot be used within the tree structure.

This feature also supports the output of the `tree` command-line utility which is available on most OS systems, allowing you to create the file structure in your OS and print a similar diagram to the one above. Use `tree -Fa --dirsfirst <path>` to print the friendly path for item, include hidden files and list directories first.

```bash
> tree -Fa --dirsfirst folder/

# folder/
# ├── .hidden_file
# ├── index.php
# ├── subfolder1/
# ├── subfolder2/
# │   ├── document.pdf
# │   ├── image.jpeg
# │   └── photo.png
# └── subfolder3/
#     └── sub2folder/
#         └── sub3file
```

Which can then be converted to the following:

```treeview
folder/
├── subfolder1/
├── subfolder2/
│   ├── document.pdf
│   ├── image.jpeg
│   └── photo.png
├── subfolder3/
│   └── sub2folder/
│       └── sub3file
├── .hidden_file
└── index.php
```

> **NOTE:** The `tree` command may print out indented lines using a character that looks to be a space character, but is not. If this is the case, you may need to add the `--charset=ascii` option to the command, which will print a diagram similar to the first example.
