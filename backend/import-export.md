# Importing & Exporting

## Introduction

The **Import Export behavior** is a controller [behavior](../services/behaviors) that provides features for importing and exporting data. The behavior provides two pages called Import and Export. The Import page allows a user to upload a CSV file and match the columns to the database. The Export page is the opposite and allows a user to download columns from the database as a CSV file. The behavior provides the controller actions `import()` and `export()`.

The behavior configuration is defined in two parts, each part depends on a special model class along with a list and form field definition file. In order to use the Import Export behavior you should add the `\Backend\Behaviors\ImportExportController::class` definition to the `$implement` property of the controller class.

```php
class Products extends Controller
{
    /**
     * @var array List of behaviors implemented by this controller
     */
    public $implement = [
        \Backend\Behaviors\ImportExportController::class,
    ];
}
```

## Configuring the behavior

The Import Export behaviour will load its configuration in the YAML format from a `config_import_export.yaml` file located in the controller's [views directory](controllers-ajax#introduction) (`plugins/myauthor/myplugin/controllers/mycontroller/config_import_export.yaml`) by default.

This can be changed by overriding the `$importExportConfig` property on your controller to reference a different filename or a full configuration array:

```php
public $importExportConfig = 'my_custom_import_export_config.yaml';
```

Below is an example of a typical Import Export behavior configuration file:

```yaml
# ===================================
#  Import/Export Behavior Config
# ===================================

import:
    title: Import subscribers
    modelClass: Acme\Campaign\Models\SubscriberImport
    list: $/acme/campaign/models/subscriber/columns.yaml

export:
    title: Export subscribers
    modelClass: Acme\Campaign\Models\SubscriberExport
    list: $/acme/campaign/models/subscriber/columns.yaml
```

The configuration options listed below are optional. Define them if you want the behavior to support the [Import](#import-page) or [Export](#export-page), or both.

Option | Description
------------- | -------------
`defaultRedirect` | used as a fallback redirection page when no specific redirect page is defined.
`import` | a configuration array or reference to a config file for the Import page.
`export` | a configuration array or reference to a config file for the Export page.
`defaultFormatOptions` | a configuration array or reference to a config file for the default CSV format options.

### Import page

To support the Import page add the following configuration to the YAML file:

```yaml
import:
    title: Import subscribers
    modelClass: Acme\Campaign\Models\SubscriberImport
    list: $/acme/campaign/models/subscriberimport/columns.yaml
    redirect: acme/campaign/subscribers
```

The following configuration options are supported for the Import page:

Option | Description
------------- | -------------
`title` | a page title, can refer to a [localization string](../plugin/localization).
`list` | defines the list columns available for importing.
`form` | provides additional fields used as import options, optional.
`redirect` | redirection page when the import is complete, optional
`permissions` | user permissions needed to perform the operation, optional

### Export page

To support the Export page add the following configuration to the YAML file:

```yaml
export:
    title: Export subscribers
    modelClass: Acme\Campaign\Models\SubscriberExport
    list: $/acme/campaign/models/subscriberexport/columns.yaml
    redirect: acme/campaign/subscribers
```

The following configuration options are supported for the Export page:

Option | Description
------------- | -------------
`title` | a page title, can refer to a [localization string](../plugin/localization).
`fileName` | the file name to use for the exported file, default **export.csv**.
`list` | defines the list columns available for exporting.
`form` | provides additional fields used as import options, optional.
`redirect` | redirection page when the export is complete, optional.
`useList` | set to true or the value of a list definition to enable [integration with Lists](#integration-with-list-behavior), default: false.
`permissions` | user permissions needed to perform the operation, optional

### Format options

To override the default CSV format options add the following configuration to the YAML file:

```yaml
defaultFormatOptions:
    delimiter: ';'
    enclosure: '"'
    escape: '\'
    encoding: 'utf-8'
```

The following configuration options (all optional) are supported for the format options:

Option | Description
------------- | -------------
`delimiter` | Delimiter character.
`enclosure` | Enclosure character.
`escape` | Escape character.
`encoding` | File encoding (only used for the import).

## Import and export views

For each page feature [Import](#import-page) and [Export](#export-page) you should provide a [view file](controllers-ajax#introduction) with the corresponding name - **import.htm** and **export.htm**.

The import/export behavior adds two methods to the controller class: `importRender` and `exportRender`. These methods render the importing and exporting sections as per the YAML configuration file described above.

### Import view

The **import.htm** view represents the Import page that allows users to import data. A typical Import page contains breadcrumbs, the import section itself, and the submission buttons. The **data-request** attribute should refer to the `onImport` AJAX handler provided by the behavior. Below is a contents of the typical import.htm view file.

```html
<?= Form::open(['class' => 'layout']) ?>

    <div class="layout-row">
        <?= $this->importRender() ?>
    </div>

    <div class="form-buttons">
        <button
            type="submit"
            data-control="popup"
            data-handler="onImportLoadForm"
            data-keyboard="false"
            class="btn btn-primary">
            Import records
        </button>
    </div>

<?= Form::close() ?>
```

### Export view

The **export.htm** view represents the Export page that allows users to export a file from the database. A typical Export page contains breadcrumbs, the export section itself, and the submission buttons. The `data-request` attribute should refer to the `onExport` AJAX handler provided by the behavior. Below is a contents of the typical export.htm form.

```html
<?= Form::open(['class' => 'layout']) ?>

    <div class="layout-row">
        <?= $this->exportRender() ?>
    </div>

    <div class="form-buttons">
        <button
            type="submit"
            data-control="popup"
            data-handler="onExportLoadForm"
            data-keyboard="false"
            class="btn btn-primary">
            Export records
        </button>
    </div>

<?= Form::close() ?>
```

## Defining an import model

For importing data you should create a dedicated model for this process which extends the `Backend\Models\ImportModel` class. Here is an example class definition:

```php
class SubscriberImport extends \Backend\Models\ImportModel
{
    /**
     * @var array The rules to be applied to the data.
     */
    public $rules = [];

    public function importData($results, $sessionKey = null)
    {
        foreach ($results as $row => $data) {

            try {
                $subscriber = new Subscriber;
                $subscriber->fill($data);
                $subscriber->save();

                $this->logCreated();
            }
            catch (\Exception $ex) {
                $this->logError($row, $ex->getMessage());
            }

        }
    }
}
```

The class must define a method called `importData` used for processing the imported data. The first parameter `$results` will contain an array containing the data to import. The second parameter `$sessionKey` will contain the session key used for the request.

Method | Description
------------- | -------------
`logUpdated()` | Called when a record is updated.
`logCreated()` | Called when a record is created.
`logError(rowIndex, message)` | Called when there is a problem with importing the record.
`logWarning(rowIndex, message)` | Used to provide a soft warning, like modifying a value.
`logSkipped(rowIndex, message)` | Used when the entire row of data was not imported (skipped).

## Defining an export model

For exporting data you should create a dedicated model which extends the `Backend\Models\ExportModel` class. Here is an example:

```php
class SubscriberExport extends \Backend\Models\ExportModel
{
    public function exportData($columns, $sessionKey = null)
    {
        foreach (Subscriber::cursor() as $record) {
            $record->addVisible($columns);
            yield $record->toArray();
        }
    }
}
```

The class must define a method called `exportData` used for returning the export data. The first parameter `$columns` is an array of column names to export. The second parameter `$sessionKey` will contain the session key used for the request.

>**NOTE:** The above example uses [PHP Generators](https://www.php.net/manual/en/language.generators.overview.php) to minimize the memory usage of the export process. Note however that Laravel is unable to eager load relationships when using this approach, so you may have to either switch to loading all the records at once with `Subscriber::get()`, or get a bit fancy with your query in order to select the necessary data for your export. An example of the query approach is provided below:

```php
use Illuminate\Support\Facades\DB;

class PostExport extends \Backend\Models\ExportModel
{
    public function exportData($columns, $sessionKey = null)
    {
        $post = new Post();

        // Get the related table names using the relationships
        $postTable = $post->getTable();
        $authorPostTable = $post->authors()->getTable();
        $authorTable = $post->authors()->getRelated()->getTable();
        $postTagTable = $post->tags()->getTable();
        $tagTable = $post->tags()->getRelated()->getTable();

        $records = Post::query()
            ->leftJoin("{$authorPostTable}", "{$postTable}.id", '=', "{$authorPostTable}.post_id")
            ->leftJoin("{$authorTable}", "{$authorPostTable}.author_id", '=', "{$authorTable}.id")
            ->leftJoin("{$postTagTable}", "{$postTable}.id", '=', "{$postTagTable}.post_id")
            ->leftJoin("{$tagTable}", "{$postTagTable}.tag_id", '=', "{$tagTable}.id")
            ->select(
                "{$postTable}.*",
                DB::raw("GROUP_CONCAT(DISTINCT {$authorTable}.id) as author_ids"),
                DB::raw("GROUP_CONCAT(DISTINCT {$tagTable}.id) as tag_ids")
            )
            ->groupBy("{$postTable}.id")
            ->cursor();

        foreach ($records as $record) {
            $record->addVisible($columns);
            yield $record->toArray();
        }
    }
}
```

## Custom options

Both import and export forms support custom options that can be introduced using form fields, defined in the **form** option in the import or export configuration respectively. These values are then passed to the Import / Export model and are available during processing.

```yaml
import:
    [...]
    form: $/acme/campaign/models/subscriberimport/fields.yaml

export:
    [...]
    form: $/acme/campaign/models/subscriberexport/fields.yaml
```

The form fields specified will appear on the import/export page. Here is an example `fields.yaml` file contents:

```yaml
# ===================================
#  Form Field Definitions
# ===================================

fields:
    auto_create_lists:
        label: Automatically create lists
        type: checkbox
        default: true
```

The value of the form field above called **auto_create_lists** can be accessed using `$this->auto_create_lists` inside the `importData` method of the import model. If this were the export model, the value would be available inside the `exportData` method instead.

```php
class SubscriberImport extends \Backend\Models\ImportModel
{
    public function importData($results, $sessionKey = null)
    {
        if ($this->auto_create_lists) {
            // Do something
        }

        [...]
    }
}
```

## Integration with list behavior

There is an alternative approach to exporting data that uses the [list behavior](../backend/lists) to provide the export data. In order to use this feature you should have the `\Backend\Behaviors\ListController::class` definition to the `$implement` field of the controller class. You do not need to use an export view and all the settings will be pulled from the list. Here is the only configuration needed:

```yaml
export:
    useList: true
```

If you are using [multiple list definitions](lists#multiple-list-definitions), then you can supply the list definition:

```yaml
export:
    useList: orders
    fileName: orders.csv
```

The `useList` option also supports extended configuration options.

```yaml
export:
    useList:
        definition: orders
        raw: true
```

The following configuration options are supported:

Option | Description
------------- | -------------
`definition` | the list definition to source records from, optional.
`raw` | output the raw attribute values from the record, default: `false`.
