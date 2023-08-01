# Backend Lists

## Introduction

The **List behavior** is a controller [behavior](../services/behaviors) used for managing lists of records on a page. The behavior provides the sortable and searchable list with optional links for each of its records. The behavior provides the controller action `index`; however the list can be rendered anywhere and multiple list definitions can be used.

The list behavior depends on list [column definitions](#list-columns) and a [model class](../database/model). In order to use the List behavior you should add the `\Backend\Behaviors\ListController::class` definition to the `$implement` property of the controller class.

```php
namespace Acme\Blog\Controllers;

class Categories extends \Backend\Classes\Controller
{
    /**
     * @var array List of behaviors implemented by this controller
     */
    public $implement = [
        \Backend\Behaviors\ListController::class,
    ];
}
```

> **NOTE:** Very often the list and [form behaviors](../backend/forms) are used together in a same controller.

## Configuring the list behavior

The List behaviour will load its configuration in the YAML format from a `config_list.yaml` file located in the controller's [views directory](controllers-ajax/#introduction) (`plugins/myauthor/myplugin/controllers/mycontroller/config_list.yaml`) by default.

This can be changed by overriding the `$listConfig` property on your controller to reference a different filename or a full configuration array:

```php
public $listConfig = 'my_custom_list_config.yaml';
```

Below is an example of a typical List behavior configuration file:

```yaml
# ===================================
#  List Behavior Config
# ===================================

title: Blog Posts
list: ~/plugins/acme/blog/models/post/columns.yaml
modelClass: Acme\Blog\Models\Post
recordUrl: acme/blog/posts/update/:id
recordsPerPage: 20
```

The following fields are required in the list configuration file:

Field | Description
------------- | -------------
`title` | a title for this list.
`list` | a configuration array or reference to a list column definition file, see [list columns](#list-columns).
`modelClass` | a model class name, the list data is loaded from this model.

The configuration options listed below are optional.

Option | Description
------------- | -------------
`filter` | filter configuration, see [filtering the list](#adding-filters).
`recordUrl` | link each list record to another page. Eg: **users/update:id**. The `:id` part is replaced with the record identifier. This allows you to link the list behavior and the [form behavior](../backend/forms).
`recordOnClick` | custom JavaScript code to execute when clicking on a record.
`noRecordsMessage` | a message to display when no records are found, can refer to a [localization string](../plugin/localization).
`deleteMessage` | a message to display when records are bulk deleted, can refer to a [localization string](../plugin/localization).
`noRecordsDeletedMessage` | a message to display when a bulk delete action is triggered, but no records were deleted, can refer to a [localization string](../plugin/localization).
`recordsPerPage` | records to display per page, use 0 to disable the pagination. Default: 0
`perPageOptions` | options to provide the user when selecting how many records to display per page. Default: `[20, 40, 80, 100, 120]`
`showPageNumbers` | displays page numbers with pagination. Disable this to improve list performance when working with large tables. Default: `true`
`toolbar` | reference to a Toolbar Widget configuration file, or an array with configuration (see below).
`showSorting` | displays the sorting link on each column. Default: `true`
`defaultSort` | sets a default sorting column and direction when user preference is not defined. Supports a string or an array with keys `column` and `direction`.
`showCheckboxes` | displays checkboxes next to each record. Default: `false`.
`showSetup` | displays the list column set up button. Default: `false`.
`showTree` | displays a tree hierarchy for parent/child records. Default: `false`.
`treeExpanded` | if tree nodes should be expanded by default. Default: `false`.
`customViewPath` | specify a custom view path to override partials used by the list, optional.

### Adding a toolbar

To include a toolbar with the list, add the following configuration to the list configuration YAML file:

```yaml
toolbar:
    buttons: list_toolbar
    search:
        prompt: Find records
```

The toolbar configuration allows:

Option | Description
------------- | -------------
`buttons` | a reference to a controller partial file with the toolbar buttons. Eg: **_list_toolbar.htm**
`search` | reference to a Search Widget configuration file, or an array with configuration.

The search configuration supports the following options:


Option | Description
------------- | -------------
`prompt` | a placeholder to display when there is no active search, can refer to a [localization string](../plugin/localization).
`mode` | defines the search strategy to either contain all words, any word or exact phrase. Supported options: all, any, exact. Default: `all`.
`scope` | specifies a [query scope method](../database/model#query-scopes) defined in the **list model** to apply to the search query. The first argument will contain the query object (as per a regular scope method), the second will contain the search term, and the third will be an array of the columns to be searched.
`searchOnEnter` | setting this to true will make the search widget wait for the Enter key to be pressed before it starts searching (the default behavior is that it starts searching automatically after someone enters something into the search field and then pauses for a short moment).  Default: `false`.

The toolbar buttons partial referred above should contain the toolbar control definition with some buttons. The partial could also contain a [scoreboard control](../ui/scoreboard) with charts. Example of a toolbar partial with the **New Post** button referring to the **create** action provided by the [form behavior](../backend/forms):

```php
<div data-control="toolbar">
    <a
        href="<?= Backend::url('acme/blog/posts/create') ?>"
        class="btn btn-primary wn-icon-plus">New Post</a>
</div>
```

### Filtering the list

To filter a list by user defined input, add the following list configuration to the YAML file:

```yaml
filter: config_filter.yaml
```

The **filter** option should make reference to a [filter configuration file](#list-filters) path or supply an array with the configuration.

## Defining list columns

List columns are defined with the YAML file. The column configuration is used by the list behavior for creating the record table and displaying model columns in the table cells. The file is placed to a subdirectory of the **models** directory of a plugin. The subdirectory name matches the model class name written in lowercase. The file name doesn't matter, but the **columns.yaml** and **list_columns.yaml** are common names. Example list columns file location:

```treeview
plugins/
`-- acme/
    `-- blog/
        `-- models/                # Plugin models directory
            |-- post/              # Model configuration directory
            |   `-- columns.yaml   # Model list columns config file
            `-- Post.php           # model class
```

The next example shows the typical contents of a list column definitions file.

```yaml
# ===================================
#  List Column Definitions
# ===================================

columns:
    name: Name
    email: Email
```

### Column options

For each column can specify these options (where applicable):

Option | Description
------------- | -------------
`label` | a name when displaying the list column to the user.
`type` | defines how this column should be rendered (see [Column types](#column-types) below).
`default` | specifies the default value for the column if value is empty.
`searchable` | include this column in the list search results. Default: `false`.
`invisible` | specifies if this column is hidden by default. Default: `false`.
`sortable` | specifies if this column can be sorted. Default: `true`.
`clickable` | if set to false, disables the default click behavior when the column is clicked. Default: `true`.
`select` | defines a custom SQL select statement to use for the value.
`valueFrom` | defines a model attribute to use for the value.
`relation` | defines a model relationship column.
`useRelationCount` | use the count of the defined `relation` as the value for this column. Default: `false`
`cssClass` | assigns a CSS class to the column container (see [Asset Compilation](../services/asset-compilation#injecting-page-assets) for injecting custom CSS files into the backend).
`headCssClass` | assigns a CSS class to the column header container.
`width` | sets the column width, can be specified in percents (10%) or pixels (50px). There could be a single column without width specified, it will be stretched to take the available space.
`align` | specifies the column alignment. Possible values are `left`, `right` and `center`.
`permissions` | the [permissions](users#users-and-permissions) that the current backend user must have in order for the column to be used. Supports either a string for a single permission or an array of permissions of which only one is needed to grant access.

### Custom value selection

It is possible to change the source and display values for each column. If you want to source the column value that is actually displayed from another model attribute (or even a relationship's attribute) you can do so with the `valueFrom` option.

```yaml
other_name:
    label: Something Great
    valueFrom: name
```

### Nested column selection

In some cases it makes sense to retrieve a column value from a nested data structure, such as a [model relationship](../database/relations) column or a [jsonable array](../database/model#standard-properties). The only drawback of doing this is the column cannot be marked as searchable or sortable as those options require the column to actually exist in the database table.

```yaml
content[title]:
    name: Title
    sortable: false
```

The above example would look for the value in PHP equivalent of `$record->content->title` or `$record->content['title']` respectively. If you need to make the column searchable or sortable, you could use [model events](../database/model#events) to locally replicate the nested value onto a real database table column whenever it changes.

## Available column types

There are various column types that can be used for the **type** setting, these control how the list column is displayed. In addition to the native column types specified below, you may also [define custom column types](#custom-column-types).

<div class="content-list collection-method-list">

- [Text](#column-text)
- [Image](#column-image)
- [Number](#column-number)
- [Switch](#column-switch)
- [Date & Time](#column-datetime)
- [Date](#column-date)
- [Time](#column-time)
- [Time since](#column-timesince)
- [Time tense](#column-timetense)
- [Select](#column-select)
- [Relation](#column-relation)
- [Partial](#column-partial)
- [Colorpicker](#column-colorpicker)

</div>

### Text

`text` - displays a text column, aligned left

```yaml
full_name:
    label: Full Name
    type: text
```

You can also specify a custom text format, for example **Admin:Full Name (active)**

```yaml
full_name:
    label: Full Name
    type: text
    format: Admin:%s (active)
```

### Image

`image` - displays an image using the built in [image resizing functionality](../services/image-resizing#resize-sources).

```yaml
avatar:
    label: Avatar
    type: image
    sortable: false
    width: 150
    height: 150
    default: '/modules/backend/assets/images/logo.svg'
    options:
        quality: 80
```

See the [image resizing docs](../services/image-resizing#resize-sources) for more information on what image sources are supported and what [options](../services/image-resizing#resize-parameters) are supported

### Number

`number` - displays a number column, aligned right

```yaml
age:
    label: Age
    type: number
```

You can also specify a custom number format, for example currency **$ 99.00**

```yaml
price:
    label: Price
    type: number
    format: $ %.2f
```

> **NOTE:** Both `text` and `number` columns support the `format` property, this property follows the formatting rules of the [PHP sprintf() function](https://secure.php.net/manual/en/function.sprintf.php). Value must be a string.

### Switch

`switch` - displays a on or off state for boolean columns.

```yaml
enabled:
    label: Enabled
    type: switch
```

### Date & Time

`datetime` - displays the column value as a formatted date and time. The next example displays dates as **Thu, Dec 25, 1975 2:15 PM**.

```yaml
created_at:
    label: Date
    type: datetime
```

You can also specify a custom date format, for example **Thursday 25th of December 1975 02:15:16 PM**:

```yaml
created_at:
    label: Date
    type: datetime
    format: l jS \of F Y h:i:s A
```

You may also wish to set `ignoreTimezone: true` to prevent a timezone conversion between the date that is displayed and the date stored in the database, since by default the backend timezone preference is applied to the display value.

```yaml
created_at:
    label: Date
    type: datetime
    ignoreTimezone: true
```

> **NOTE:** the `ignoreTimezone` option also applies to other date and time related field types, including `date`, `time`, `timesince` and `timetense`.

### Date

`date` - displays the column value as date format **M j, Y**

```yaml
created_at:
    label: Date
    type: date
```

### Time

`time` - displays the column value as time format **g:i A**

```yaml
created_at:
    label: Date
    type: time
```

### Time since

`timesince` - displays a human readable time difference from the value to the current time. Eg: **10 minutes ago**

```yaml
created_at:
    label: Date
    type: timesince
```

### Time tense

`timetense` - displays 24-hour time and the day using the grammatical tense of the current date. Eg: **Today at 12:49**, **Yesterday at 4:00** or **18 Sep 2015 at 14:33**.

```yaml
created_at:
    label: Date
    type: timetense
```

### Select

`select` - allows to create a column using a custom select statement. Any valid SQL SELECT statement works here.

```yaml
full_name:
    label: Full Name
    select: concat(first_name, ' ', last_name)
```

### Relation

`relation` - allows to display related columns, you can provide a relationship option. The value of this option has to be the name of the Active Record [relationship](../database/relations) on your model. In the next example the **name** value will be translated to the name attribute found in the related model (eg: `$model->name`).

```yaml
group:
    label: Group
    relation: groups
    select: name
```

It is possible the apply conditions to filter the returned results of the relation using the `conditions` property. Given the following structure:

```
Entries:
- id
- created_at

EntryData:
- entry_id
- key
- value
```
You can display the individual EntryData records as separate columns in the list widget by using the following configuration:

```yaml
id:
    label: ID
created_at:
    label: Created at
_entry_field_email:
    label: Email
    relation: entry_data
    select: value
    conditions: "`key` = 'email'"
```

To display a column that shows the number of related records, use the `useRelationCount` option.

```yaml
users_count:
    label: Users
    relation: users
    useRelationCount: true
```

> **NOTE:** Using the `relation` option on a column will load the value from the `select`ed column into the attribute specified by this column. It is recommended that you name the column displaying the relation data without conflicting with existing model attributes as demonstrated in the examples below:

**Best Practice:**

```yaml
group_name:
    label: Group
    relation: group
    select: name
```

**Poor Practice:**

```yaml
# This will overwrite the value of $record->group_id which will break accessing relations from the list view
group_id:
    label: Group
    relation: group
    select: name
```

### Partial

`partial` - renders a partial, the `path` value can refer to a partial view file otherwise the column name is used as the partial name. Inside the partial these variables are available: `$value` is the default cell value, `$record` is the model used for the cell and `$column` is the configured class object `Backend\Classes\ListColumn`.

```yaml
content:
    label: Content
    type: partial
    path: ~/plugins/acme/blog/models/comment/_content_column.htm
```

### Color Picker

`colorpicker` - displays a color from colorpicker column

```yaml
color:
    label: Background
    type: colorpicker
```

## Displaying the list

Usually lists are displayed in the index [view](controllers-ajax/#introduction) file. Since lists include the toolbar, the view file will consist solely of the single `listRender` method call.

```php
<?= $this->listRender() ?>
```

## Multiple list definitions

The list behavior can support multiple lists in the same controller using named definitions. The `$listConfig` property can be defined as an array where the key is a definition name and the value is the configuration file.

```php
public $listConfig = [
    'templates' => 'config_templates_list.yaml',
    'layouts' => 'config_layouts_list.yaml'
];
```

Each definition can then be displayed by passing the definition name as the first argument when calling the `listRender` method:

```php
<?= $this->listRender('templates') ?>
```

## Using list filters

Lists can be filtered by [adding a filter definition](#adding-filters) to the list configuration. Similarly filters are driven by their own configuration file that contain filter scopes, each scope is an aspect by which the list can be filtered. The next example shows a typical contents of the filter definition file.

```yaml
# ===================================
# Filter Scope Definitions
# ===================================

scopes:

    category:
        label: Category
        modelClass: Acme\Blog\Models\Category
        conditions: category_id in (:filtered)
        nameFrom: name

    status:
        label: Status
        type: group
        conditions: status in (:filtered)
        options:
            pending: Pending
            active: Active
            closed: Closed

    published:
        label: Hide published
        type: checkbox
        default: 1
        conditions: is_published <> true

    approved:
        label: Approved
        type: switch
        default: 2
        conditions:
            - is_approved <> true
            - is_approved = true

    created_at:
        label: Date
        type: date
        conditions: created_at >= ':filtered'

    published_at:
        label: Date
        type: daterange
        conditions: created_at >= ':after' AND created_at <= ':before'
```

### Scope options

For each scope you can specify these options (where applicable):

Option | Description
------------- | -------------
`label` | a name when displaying the filter scope to the user.
`type` | defines how this scope should be rendered (see [Scope types](#scope-types) below). Default: `group`.
`conditions` | specifies a raw where query statement to apply to the list model query, the `:filtered` parameter represents the filtered value(s).
`scope` | specifies a [query scope method](../database/model#query-scopes) defined in the **list model** to apply to the list query. The first argument will contain the query object (as per a regular scope method) and the second argument will contain the filtered value(s)
`options` | options to use if filtering by multiple items, this option can specify an array or a method name in the `modelClass` model.
`nameFrom` | if filtering by multiple items, the attribute to display for the name, taken from all records of the `modelClass` model.
`default` | can either be integer(switch,checkbox,number) or array(group,date range,number range) or string(date).
`permissions` | the [permissions](users#users-and-permissions) that the current backend user must have in order for the filter scope to be used. Supports either a string for a single permission or an array of permissions of which only one is needed to grant access.
`dependsOn` | a string or an array of other scope names that this scope [depends on](#filter-scope-dependencies). When the other scopes are modified, this scope will update.

### Filter Dependencies

Filter scopes can declare dependencies on other scopes by defining the `dependsOn` [scope option](#filter-scope-options), which provide a server-side solution for updating scopes when their dependencies are modified. When the scopes that are declared as dependencies change, the defining scope will update dynamically. This provides an opportunity to change the available options to be provided to the scope.

```yaml
country:
    label: Country
    type: group
    conditions: country_id in (:filtered)
    modelClass: Winter\Test\Models\Location
    options: getCountryOptions

city:
    label: City
    type: group
    conditions: city_id in (:filtered)
    modelClass: Winter\Test\Models\Location
    options: getCityOptions
    dependsOn: country
```

In the above example, the `city` scope will refresh when the `country` scope has changed. Any scope that defines the `dependsOn` property will be passed all current scope objects for the Filter widget, including their current values, as an array that is keyed by the scope names.

```php
public function getCountryOptions()
{
    return Country::lists('name', 'id');
}

public function getCityOptions($scopes = null)
{
    if (!empty($scopes['country']->value)) {
        return City::whereIn('country_id', array_keys($scopes['country']->value))->lists('name', 'id');
    } else {
        return City::lists('name', 'id');
    }
}
```

> **NOTE:** Only scope dependencies with `type: group` are supported at this point.

### Available scope types

These types can be used to determine how the filter scope should be displayed.

<div class="content-list collection-method-list">

- [Group](#filter-group)
- [Checkbox](#filter-checkbox)
- [Switch](#filter-switch)
- [Date](#filter-date)
- [Date range](#filter-daterange)
- [Number](#filter-number)
- [Number range](#filter-numberrange)
- [Text](#filter-text)

</div>

### Group

`group` - filters the list by a group of items, usually by a related model and requires a `nameFrom` or `options` definition. Eg: Status name as open, closed, etc.

```yaml
status:
    label: Status
    type: group
    conditions: status in (:filtered)
    default:
        pending: Pending
        active: Active
    options:
        pending: Pending
        active: Active
        closed: Closed
```

### Checkbox

`checkbox` - used as a binary checkbox to apply a predefined condition or query to the list, either on or off. Use 0 for off and 1 for on for default value

```yaml
published:
    label: Hide published
    type: checkbox
    default: 1
    conditions: is_published <> true
```

### Switch

`switch` - used as a switch to toggle between two predefined conditions or queries to the list, either indeterminate, on or off. Use 0 for off, 1 for indeterminate and 2 for on for default value

```yaml
approved:
    label: Approved
    type: switch
    default: 1
    conditions:
        - is_approved <> true
        - is_approved = true
```

### Date

`date` - displays a date picker for a single date to be selected. The values available to be used in the conditions property are:

- `:filtered`: The selected date formatted as `Y-m-d`
- `:before`: The selected date formatted as `Y-m-d 00:00:00`, converted from the backend timezone to the app timezone
- `:after`: The selected date formatted as `Y-m-d 23:59:59`, converted from the backend timezone to the app timezone

```yaml
created_at:
    label: Date
    type: date
    minDate: '2001-01-23'
    maxDate: '2030-10-13'
    yearRange: 10
    conditions: created_at >= ':filtered'
```

### Date Range

`daterange` - displays a date picker for two dates to be selected as a date range. The values available to be used in the conditions property are:

- `:before`: The selected "before" date formatted as `Y-m-d H:i:s`
- `:beforeDate`: The selected "before" date formatted as `Y-m-d`
- `:after`: The selected "after" date formatted as `Y-m-d H:i:s`
- `:afterDate`: The selected "after" date formatted as `Y-m-d`

```yaml
published_at:
    label: Date
    type: daterange
    minDate: '2001-01-23'
    maxDate: '2030-10-13'
    yearRange: 10
    conditions: created_at >= ':after' AND created_at <= ':before'
```

To use default value for Date and Date Range

```php
myController::extendListFilterScopes(function($filter)
{
    $filter->addScopes([
        'Date Test' => [
            'label' => 'Date Test',
            'type' => 'daterange',
            'default' => $this->myDefaultTime(),
            'conditions' => "created_at >= ':after' AND created_at <= ':before'"
        ],
    ]);
});

// return value must be instance of carbon
public function myDefaultTime()
{
    return [
        0 => Carbon::parse('2012-02-02'),
        1 => Carbon::parse('2012-04-02'),
    ];
}
```

You may also wish to set `ignoreTimezone: true` to prevent a timezone conversion between the date that is displayed and the date stored in the database, since by default the backend timezone preference is applied to the display value.

```yaml
published_at:
    label: Date
    type: daterange
    minDate: '2001-01-23'
    maxDate: '2030-10-13'
    yearRange: 10
    conditions: created_at >= ':after' AND created_at <= ':before'
    ignoreTimezone: true
```

> **NOTE:** the `ignoreTimezone` option also applies to the `date` filter type as well.

### Number

`number` - displays input for a single number to be entered. The value is available to be used in the conditions property as `:filtered`.

The `min` and `max` options specify the minimum and maximum values that can be entered by the user. The `step` option specifies the stepping interval to use when adjusting the value with the up and down arrows.

```yaml
age:
    label: Age
    type: number
    default: 14
    step: 1
    min: 0
    max: 1000
    conditions: age >= ':filtered'
```

> **NOTE:** the `step`, `min`, and `max` options also apply to the `numberrange` filter type as well.

### Number Range

`numberrange` - displays inputs for two numbers to be entered as a number range. The values available to be used in the conditions property are:

- `:min`: The minimum value, defaults to -2147483647
- `:max`: The maximum value, defaults to 2147483647

You may leave either the minimum value blank to search everything up to the maximum value, and vice versa, you may leave the maximum value blank to search everything at least the minimum value.

```yaml
visitors:
    label: Visitor Count
    type: numberrange
    default:
        0: 10
        1: 20
    conditions: visitors >= ':min' and visitors <= ':max'
```

### Text

`text` - display text input for a string to be entered. You can specify a `size` attribute that will be injected in the input size attribute (default: 10).

```yaml
username:
    label: Username
    type: text
    conditions: username = :value
    size: 2
```

## Extending list behavior

Sometimes you may wish to modify the default list behavior and there are several ways you can do this.

- [Overriding controller action](#overriding-action)
- [Overriding views](#overriding-views)
- [Extending column definitions](#extend-list-columns)
- [Inject CSS row class](#inject-row-class)
- [Extending filter scopes](#extend-filter-scopes)
- [Extending the model query](#extend-model-query)
- [Custom column types](#custom-column-types)

### Overriding controller action

You can use your own logic for the `index` action method in the controller, then optionally call the List behavior `index` parent method.

```php
public function index()
{
    //
    // Do any custom code here
    //

    // Call the ListController behavior index() method
    $this->asExtension('ListController')->index();
}
```

### Overriding views

The `ListController` behavior has a main container view that you may override by creating a special file named `_list_container.htm` in your controller directory. The following example will add a sidebar to the list:

```html
<?php if ($toolbar): ?>
    <?= $toolbar->render() ?>
<?php endif ?>

<?php if ($filter): ?>
    <?= $filter->render() ?>
<?php endif ?>

<div class="row row-flush">
    <div class="col-sm-3">
        [Insert sidebar here]
    </div>
    <div class="col-sm-9 list-with-sidebar">
        <?= $list->render() ?>
    </div>
</div>
```

The behavior will invoke a `Lists` widget that also contains numerous views that you may override. This is possible by specifying a `customViewPath` option as described in the [list configuration options](#configuring-list). The widget will look in this path for a view first, then fall back to the default location.

```yaml
# Custom view path
customViewPath: $/acme/blog/controllers/reviews/list
```

> **NOTE**: It is a good idea to use a sub-directory, for example `list`, to avoid conflicts.

For example, to modify the list body row markup, create a file called `list/_list_body_row.htm` in your controller directory.

```php
<tr>
    <?php foreach ($columns as $key => $column): ?>
        <td><?= $this->getColumnValue($record, $column) ?></td>
    <?php endforeach ?>
</tr>
```

### Extending column definitions

You can extend the columns of another controller from outside by calling the `extendListColumns` static method on the controller class. This method can take two arguments, **$list** will represent the Lists widget object and **$model** represents the model used by the list. Take this controller for example:

```php
class Categories extends \Backend\Classes\Controller
{
    /**
     * @var array List of behaviors implemented by this controller
     */
    public $implement = [
        \Backend\Behaviors\ListController::class,
    ];
}
```

Using the `extendListColumns` method you can add extra columns to any list rendered by this controller. It is a good idea to check the **$model** is of the correct type. Here is an example:

```php
Categories::extendListColumns(function($list, $model)
{
    if (!$model instanceof MyModel) {
        return;
    }

    $list->addColumns([
        'my_column' => [
            'label' => 'My Column'
        ]
    ]);

});
```

You can also extend the list columns internally by overriding the `listExtendColumns` method inside the controller class.

```php
class Categories extends \Backend\Classes\Controller
{
    [...]

    public function listExtendColumns($list)
    {
        $list->addColumns([...]);
    }
}
```

The following methods are available on the $list object.

Method | Description
------------- | -------------
`addColumns` | adds new columns to the list
`removeColumn` | removes a column from the list

Each method takes an array of columns similar to the [list column configuration](#list-columns).

### Inject CSS row class

You can inject a custom css row class by adding a `listInjectRowClass` method on the controller class. This method can take two arguments, **$record** will represent a single model record and **$definition** contains the name of the List widget definition. You can return any string value containing your row classes. These classes will be added to the row's HTML markup.

```php
class Lessons extends \Backend\Classes\Controller
{
    [...]

    public function listInjectRowClass($lesson, $definition)
    {
        // Strike through past lessons
        if ($lesson->lesson_date->lt(Carbon::today())) {
            return 'strike';
        }
    }
}
```

A special CSS class `nolink` is available to force a row to be unclickable, even if the `recordUrl` or `recordOnClick` options are defined for the List widget. Returning this class in an event will allow you to make records unclickable - for example, for soft-deleted rows or for informational rows:

```php
public function listInjectRowClass($record, $value)
{
    if ($record->trashed()) {
        return 'nolink';
    }
}
```

### Extending filter scopes

You can extend the filter scopes of another controller from outside by calling the `extendListFilterScopes` static method on the controller class. This method can take the argument **$filter** which will represent the Filter widget object. Take this controller for example:

```php
Categories::extendListFilterScopes(function($filter) {
    // Add custom CSS classes to the Filter widget itself
    $filter->cssClasses = array_merge($filter->cssClasses, ['my', 'array', 'of', 'classes']);

    $filter->addScopes([
        'my_scope' => [
            'label' => 'My Filter Scope'
        ]
    ]);
});
```

> The array of scopes provided is similar to the [list filters configuration](#list-filters).

You can also extend the filter scopes internally to the controller class, simply override the `listFilterExtendScopes` method.

```php
class Categories extends \Backend\Classes\Controller
{
    [...]

    public function listFilterExtendScopes($filter)
    {
        $filter->addScopes([...]);
    }
}
```

The following methods are available on the $filter object.

Method | Description
------------- | -------------
`addScopes` | adds new scopes to filter widget
`removeScope` | remove scope from filter widget

### Extending the model query

The lookup query for the list [database model](../database/model) can be extended by overriding the `listExtendQuery` method inside the controller class. This example will ensure that soft deleted records are included in the list data, by applying the **withTrashed** scope to the query:

```php
public function listExtendQuery($query)
{
    $query->withTrashed();
}
```

When dealing with multiple lists definitions in a same controller, you can use the second parameter of `listExtendQuery` which contains the name of the definition :

```php
public $listConfig = [
    'inbox' => 'config_inbox_list.yaml',
    'trashed' => 'config_trashed_list.yaml'
];

public function listExtendQuery($query, $definition)
{
    if ($definition === 'trashed') {
        $query->onlyTrashed();
    }
}
```

The [list filter](#list-filters) model query can also be extended by overriding the `listFilterExtendQuery` method:

```php
public function listFilterExtendQuery($query, $scope)
{
    if ($scope->scopeName == 'status') {
        $query->where('status', '<>', 'all');
    }
}
```

>**NOTE:** In order to apply the `limit()` scope to the query you have to disable the default pagination behavior of the `ListController` to prevent it from overriding any changes to the query `LIMIT`. This can be done by setting `recordsPerPage: 0` in your list definition configuration.

### Extending the records collection

The collection of records used by the list can be extended by overriding the `listExtendRecords` method inside the controller class. This example uses the `sort` method on the [record collection](../database/collection) to change the sort order of the records.

```php
public function listExtendRecords($records)
{
    return $records->sort(function ($a, $b) {
        return $a->computedVal() > $b->computedVal();
    });
}
```

### Custom column types

Custom list column types can be registered in the backend with the `registerListColumnTypes` method of the [Plugin registration class](../plugin/registration#registration-methods). The method should return an array where the key is the type name and the value is a callable function. The callable function receives three arguments, the native `$value`, the `$column` definition object and the model `$record` object.

```php
public function registerListColumnTypes()
{
    return [
        // A local method, i.e $this->evalUppercaseListColumn()
        'uppercase' => [$this, 'evalUppercaseListColumn'],

        // Using an inline closure
        'loveit' => function($value) { return 'I love '. $value; }
    ];
}

public function evalUppercaseListColumn($value, $column, $record)
{
    return strtoupper($value);
}
```

Using the custom list column type is as simple as calling it by name using the `type` option.

```yaml
# ===================================
#  List Column Definitions
# ===================================

columns:
    secret_code:
        label: Secret code
        type: uppercase
```
