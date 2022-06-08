# Developer Guide

- [Writing documentation](#writing-docs)
- [Exceptions to PSR standards](#psr-exceptions)
    - [Controller methods can have a single underscore](#psr-exception-methods)
    - [Subsequent expressions on same line as closing curly brace](#psr-exception-newline-expressions)
    - [Use of trailing commas](#psr-exception-trailing-commas)
- [Developer standards and patterns](#developer-standards)
    - [Vendor naming](#vendor-naming)
    - [Repository naming](#repository-naming)
    - [PHP Variable naming](#variable-naming)
    - [PHP Class naming](#class-naming)
    - [HTML element naming](#element-naming)
    - [View file naming](#view-naming)
    - [Class naming](#class-naming)
    - [Event naming](#event-naming)
    - [Database table naming](#db-table-naming)
    - [Component naming](#component-naming)
    - [Controller naming](#controller-naming)
    - [Model naming](#model-naming)
    - [Model scopes](#model-scopes)
    - [Class guidance](#class-guide)

<a name="writing-docs"></a>
## Writing documentation

Your contributions to the Winter documentation are very welcome. Please follow the Winter CMS documentation guidelines if you would like  to contribute:

1. Each page that has at least one H2 header should have a TOC list. The TOC list should be the first element after the H1 header. The TOC should have links to all H2 headers on the page.
1. There should be an introductory text below the TOC, even if there is the Introduction section. You may want to get rid of the Introduction section if it's not really needed. Don't leave the TOC alone.
1. Try to use only H2 and H3 headers.
1. Each H2 and H3 header should have a link defined as `<a name="page-cycle-handlers"></a>`
1. Only use UL tags for TOC lists.
1. Avoid short, 1 sentence, paragraphs. Merge short paragraphs and try to be a bit more verbose.
1. Avoid short hanging paragraphs below code sections. Merge such paragraphs with the text above the code blocks.
1. Use the inline `code` tags for everything related to code - variable names, function names, syntax examples, etc.
1. Use code blocks (three backticks followed by the language of the block at the start of the codeblock and three backticks at the end) for code examples that are longer than a single line.
1. Use the **strong** tag for everything else.
1. Cross links to other documentation articles is highly encouraged. Adding links to the same article in the same paragraph is not necessary.
1. See the [cms-pages.md](https://github.com/wintercms/docs/blob/main/cms-pages.md) or [cms-themes.md](https://github.com/wintercms/docs/blob/main/cms-themes.md) files for your reference.

<a name="psr-exceptions"></a>
## Exceptions to PSR standards

There are some exceptions to the PSR standard used by Winter.

<a name="psr-exception-methods"></a>
### Controller methods can have a single underscore

PSR-2 states that methods must be in **camelCase**. However, in backend controllers Winter will prefix AJAX handlers with the action name to define a controlled context. For example:

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

An exception must be granted for these scenarios.

<a name="psr-exception-newline-expressions"></a>
### Subsequent expressions on same line as closing curly brace

PSR-2 does not explicitly state that subsequent expressions should be on the same line as the closing curly brace.

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

While some sections of the Winter CMS codebase may be using the above style, it is not recommended and should be changed to the below when encountered.

```php
if ($expr1) {
    // if body
} elseif ($expr2) {
    // elseif body
} else {
    // else body;
}
```

It is entirely acceptable to place multiple conditions on new lines for readability however:

```php
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

This is an acceptable preference based on a technicality, PSR-1 and PSR-2 are not explicit when using SHOULD, MUST, etc. in this case.

Additionally, `if` statements with no curly braces due to only being one line of code are highly discouraged:

```php
if ($condition)
    doSomething();
```

You should always use full curly braces on your expressions:

```php
if ($condition) {
    doSomething();
}
```

<a name="psr-exception-trailing-commas"></a>
### Use of trailing commas

Although not specified one way or another in [PSR-2](https://www.php-fig.org/psr/psr-2/), Winter CMS highly recommends the use of trailing commas in multi-line arrays, especially for localization files. Trailing commas make it easier to perform maintenance on multiline array items without causing unnecessary visual clutter in diffs or version control history.

**Recommended:**

```php
$items = [
    'apple',
    'banana',
    'orange',
];
```

**NOT** recommended:

```php
$items = [
    'apple',
    'banana',
    'orange'
];
```

<a name="developer-standards"></a>
## Developer standards and patterns

This section describes some standards that we highly recommend to follow for everybody, especially if you are going to publish your products on the Marketplace.

<a name="vendor-naming"></a>
### Vendor naming

The vendor or author code in a namespace must begin with an uppercase character and should not contain underscores or dashes. These are examples of valid names:

    Acme.Blog
    WinterStorm.User
    Happygilmore.Golf

These are examples of names that are **not** valid:

    acme.blog
    winterStorm.user
    Happy_gilmore.Golf

<a name="repository-naming"></a>
### Repository naming

When publishing work to a repository, such as Git, use the following naming as a convention. Plugins should be named with a `-plugin` suffix and optional `wn-` prefix.

    blog-plugin
    wn-blog-plugin

Themes should be named with the `-theme` suffix and optional `wn-` prefix.

    happy-theme
    wn-happy-theme

Projects can be named with a `wn-` prefix and suffix indicating the project type (i.e. `-site`, `-app`, etc), although this is just a suggested convention and isn't enforced anywhere.

    wn-wintercms.com-site
    wn-api.wintercms.com-app

<a name="variable-naming"></a>
### PHP Variable naming

Use **camelCase** everywhere except for the following:

1. Database attributes and relationships should use **snake_case**
1. Postback parameters and HTML elements should use **snake_case**
1. Language keys should use **snake_case**

<a name="class-naming"></a>
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

<a name="element-naming"></a>
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

<a name="view-naming"></a>
### View file naming

Partial views should begin with an underscore character. Whereas Controller and Layout views do not begin with an underscore character. Since views are often found in a single folder, the underscore (_) and dash (-) characters can be used to organise the files. A dash is used as a substitute for a space character. An underscore is used as a substitute for a slash character (folder or namespace).

    index_fancy-layout.htm       <== Index\Fancy layout
    form-with-sidebar.htm        <== Form with sidebar
    _field-container.htm         <== Field container (partial)
    _field_baloon-selector.htm   <== Field\Baloon Selector (partial)

View files must end with the `.htm` file extension.

<a name="class-naming"></a>
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

<a name="event-naming"></a>
### Event naming

When specifying [event names](/docs/services/events). The term *after* is not used in Events, only the term *before* is used. For example:

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

<a name="db-table-naming"></a>
### Database table naming

Tables names should be prefixed with the author and plugin name.

    acme_blog_xxx

Boolean column names should be prefixed with `is_`

    is_activated
    is_visible

This is because the model attributes can conflict, for example, `public $visible;` in the Model class conflicts with a database column with the same name. Some column names are exceptions, for example `notify_user`.

If your plugin extends tables belonging to other plugins, the added column names should be prefixed with the author and plugin name:

    acme_blog_category_id

The author and plugin name acronym is also acceptable as a prefix:

    ab_category_id

<a name="component-naming"></a>
### Component naming

Component classes are commonly place in the `components` directory. The name of a component should represent its primary function.

To display a list of records, use the `List` suffix, eg:

    ProductList
    ProductReviewList
    CategoryList

To display a single record, use the `Details` suffix, eg:

    ProductDetails
    CategoryDetails

Using the suffix helps avoid conflicts with controller and model names. Alternatively you can name components without the suffix, for cases when the name is descriptive and does not conflict:

    ProductReviews
    CustomerCheckout
    SeoDirectory
    UserProfile

<a name="controller-naming"></a>
### Controller naming

Controllers are commonly are placed in `controllers` directory, for backend controllers. The name of a controller should be in plural form, for example:

    People
    Products
    Categories
    ProductCategories

<a name="model-naming"></a>
### Model naming

Models are commonly are placed in `models` directory. The name of a model should be in singular form, for example:

    Person
    Product
    Category
    ProductCategory

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

<a name="model-scopes"></a>
### Model scopes

[Model scopes](../../database/model#query-scopes) should always return the scoped `QueryBuilder` instance to support scope chaining. If a scope is not returning a `QueryBuilder` instance then it is not a scope and should be a regular / static method instead.

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

    - is
    - for
    - with
    - without
    - filter

<a name="class-guide"></a>
### Class guidance

These points are to be considered in a relaxed fashion:

1. In classes, properties and methods should be declared as `protected` in favor of `private` so that all classes can be used as base classes. Similarily, `static::someMethodOrConstantOrProperty` is preferred to `self::someMethodOrConstantOrProperty`, again to make it easier to extend classes.
1. If a property contains a single value (not an array), make the property `public` instead of a get/set approach.
1. If a property contains a collection (is an array), make the property `protected` with get `getProperties`, `getProperty` and `setProperty`.
