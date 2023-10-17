# Validation

## Introduction

Validation can be used to cross-examine data and produce error messages against data by supplying it to the `Validator` class. Validation with the `Validator` class can be used for things such as form submissions, query string data and other sources, as well as Model validation (see note below). This section of the documentation explains how to use the `Validator` class to situate, customise and perform validation within your project.

> This section is for detailing validation with the `Validator` class. Validating models is handled slightly differently via a trait. Please see the [Validation Trait](../database/traits#validation) section for more information on validating models.

## Basic usage

The validator class is a simple, convenient facility for validating data and retrieving validation error messages via the `Validator` class.

### Basic Validation Example

```php
$validator = Validator::make(
    ['name' => 'Joe'],
    ['name' => 'required|min:5']
);
```

The first argument passed to the `make` method is the data under validation. The second argument is the validation rules that should be applied to the data.

### Using arrays to specify rules

Multiple rules may be delimited using either a "pipe" character, or as separate elements of an array.

```php
$validator = Validator::make(
    ['name' => 'Joe'],
    ['name' => ['required', 'min:5']]
);
```

### Validating multiple fields

```php
$validator = Validator::make(
    [
        'name' => 'Joe',
        'password' => 'lamepassword',
        'email' => 'email@example.com'
    ],
    [
        'name' => 'required',
        'password' => 'required|min:8',
        'email' => 'required|email|unique:users'
    ]
);
```

Once a `Validator` instance has been created, the `fails` (or `passes`) method may be used to perform the validation.

```php
if ($validator->fails()) {
    // The given data did not pass validation
}
```

If validation has failed, you may retrieve the error messages from the validator.

```php
$messages = $validator->messages();
```

You may also access an array of the failed validation rules, without messages. To do so, use the `failed` method:

```php
$failed = $validator->failed();
```

### Validating files

The `Validator` class provides several rules for validating files, such as `size`, `mimes`, and others. When validating files, you may simply pass them into the validator with your other data.

## Working with error messages

After calling the `messages` method on a `Validator` instance, you will receive a `Illuminate\Support\MessageBag` instance, which has a variety of convenient methods for working with error messages.

### Retrieving the first error message for a field

```php
echo $messages->first('email');
```

### Retrieving all error messages for a field

```php
foreach ($messages->get('email') as $message) {
    //
}
```

### Retrieving all error messages for all fields

```php
foreach ($messages->all() as $message) {
    //
}
```

### Determining if messages exist for a field

```php
if ($messages->has('email')) {
    //
}
```

### Retrieving an error message with a format

```php
echo $messages->first('email', '<p>:message</p>');
```

> **NOTE:** By default, messages are formatted using Bootstrap compatible syntax.

### Retrieving all error messages with a format

```php
foreach ($messages->all('<li>:message</li>') as $message) {
    //
}
```

## Error messages & views

Once you have performed validation, you will need an easy way to get the error messages back to your views. This is conveniently handled by Winter. Consider the following routes as an example:

```php
public function onRegister()
{
    $rules = [];

    $validator = Validator::make(Input::all(), $rules);

    if ($validator->fails()) {
        return Redirect::to('register')->withErrors($validator);
    }
}
```

Note that when validation fails, we pass the `Validator` instance to the Redirect using the `withErrors` method. This method will flash the error messages to the session so that they are available on the next request.

Winter will always check for errors in the session data, and automatically bind them to the view if they are available. **So, it is important to note that an `errors` variable will always be available in all of your pages, on every request**, allowing you to conveniently assume the `errors` variable is always defined and can be safely used. The `errors` variable will be an instance of `MessageBag`.

So, after redirection, you may utilize the automatically bound `errors` variable in your view:

```twig
{{ errors.first('email') }}
```

### Named error bags

If you have multiple forms on a single page, you may wish to name the `MessageBag` of errors. This will allow you to retrieve the error messages for a specific form. Simply pass a name as the second argument to `withErrors`:

```php
return Redirect::to('register')->withErrors($validator, 'login');
```

You may then access the named `MessageBag` instance from the `$errors` variable:

```twig
{{ errors.login.first('email') }}
```

## Available validation rules

Below is a list of all available validation rules and their function:

<div class="columned-list">

- [Accepted](#accepted)
- [Active URL](#active_url)
- [After (Date)](#after_date_)
- [Alpha](#alpha)
- [Alpha Dash](#alpha_dash)
- [Alpha Numeric](#alpha_num)
- [Array](#array)
- [Before (Date)](#before_date_)
- [Between](#between_min__max_)
- [Boolean](#boolean)
- [Confirmed](#confirmed)
- [Date](#date)
- [Date Format](#date_format_format_)
- [Different](#different_field_)
- [Digits](#digits_value_)
- [Digits Between](#digits_between_min__max_)
- [E-Mail](#email)
- [Exists (Database)](#exists_table__column_)
- [Image (File)](#image)
- [In](#in_foo__bar_)
- [Integer](#integer)
- [IP Address](#ip)
- [Max](#max_value_)
- [MIME Types](#mimes_foo__bar_)
- [Min](#min_value_)
- [Not In](#not_in_foo__bar_)
- [Nullable](#nullable)
- [Numeric](#numeric)
- [Regular Expression](#regex_pattern_)
- [Required](#required)
- [Required If](#required_if_field__value_)
- [Required With](#required_with_foo__bar_)
- [Required With All](#required_with_all_foo__bar_)
- [Required Without](#required_without_foo__bar_)
- [Required Without All](#required_without_foo__bar_)
- [Same](#same_field_)
- [Size](#size_value_)
- [String](#string_value_)
- [Timezone](#timezone)
- [Unique (Database)](#unique_table__column__except__idcolumn_)
- [URL](#url)

</div>

### Validation rules list

#### `accepted`

The field under validation must be _yes_, _on_, or _1_. This is useful for validating "Terms of Service" acceptance.

#### `active_url`

The field under validation must be a valid URL according to the `checkdnsrr` PHP function.

#### `after:_date_`

The field under validation must be a value after a given date. The dates will be passed into the PHP `strtotime` function.

#### `alpha`

The field under validation must be entirely alphabetic characters.

#### `alpha_dash`

The field under validation may have alpha-numeric characters, as well as dashes and underscores.

#### `alpha_num`

The field under validation must be entirely alpha-numeric characters.

#### `array`

The field under validation must be of type array.

#### `before:_date_`

The field under validation must be a value preceding the given date. The dates will be passed into the PHP `strtotime` function.

#### `between:_min_,_max_`

The field under validation must have a size between the given _min_ and _max_. Strings, numerics, and files are evaluated in the same fashion as the `size` rule.

#### `boolean`

The field under validation must be able to be cast as a boolean. Accepted input are `true`, `false`, `1`, `0`, `"1"` and `"0"`.

#### `confirmed`

The field under validation must have a matching field of `foo_confirmation`. For example, if the field under validation is `password`, a matching `password_confirmation` field must be present in the input.

#### `date`

The field under validation must be a valid date according to the `strtotime` PHP function.

#### `date_format:_format_`

The field under validation must match the _format_ defined according to the `date_parse_from_format` PHP function.

#### `different:_field_`

The given _field_ must be different than the field under validation.

#### `digits:_value_`

The field under validation must be _numeric_ and must have an exact length of _value_.

#### `digits_between:_min_,_max_`

The field under validation must have a length between the given _min_ and _max_.

#### `email`

The field under validation must be formatted as an e-mail address.

#### `exists:_table_,_column_`

The field under validation must exist on a given database table.

#### `Basic usage of exists rule`

```php
'state' => 'exists:states'
```

#### `Specifying a custom column name`

```php
'state' => 'exists:states,abbreviation'
```

You may also specify more conditions that will be added as "where" clauses to the query:

```php
'email' => 'exists:staff,email,account_id,1'
```

Passing `NULL` as a "where" clause value will add a check for a `NULL` database value:

```php
'email' => 'exists:staff,email,deleted_at,NULL'
```

#### `image`

The file under validation must be an image (jpeg, png, bmp, or gif)

#### `in:_foo_,_bar_,...`

The field under validation must be included in the given list of values.

#### `integer`

The field under validation must have an integer value.

#### `ip`

The field under validation must be formatted as an IP address.

#### `max:_value_`

The field under validation must be less than or equal to a maximum _value_. Strings, numerics, and files are evaluated in the same fashion as the [`size`](#size_value_) rule.

#### `mimes:_foo_,_bar_,...`

The file under validation must have a MIME type corresponding to one of the listed extensions.

#### `Basic usage of MIME rule`

```php
'photo' => 'mimes:jpeg,bmp,png'
```

#### `min:_value_`

The field under validation must have a minimum _value_. Strings, numerics, and files are evaluated in the same fashion as the [`size`](#size_value_) rule.

#### `not_in:_foo_,_bar_,...`

The field under validation must not be included in the given list of values.

#### `nullable`

The field under validation may be `null`. This is particularly useful when validating primitive such as strings and integers that can contain `null` values.

#### `numeric`

The field under validation must have a numeric value.

#### `regex:_pattern_`

The field under validation must match the given regular expression.

**Note:** When using the `regex` pattern, it may be necessary to specify rules in an array instead of using pipe delimiters, especially if the regular expression contains a pipe character.

#### `required`

The field under validation must be present in the input data.

#### `required_if:_field_,_value_,...`

The field under validation must be present if the _field_ field is equal to any _value_.

#### `required_with:_foo_,_bar_,...`

The field under validation must be present _only if_ any of the other specified fields are present.

#### `required_with_all:_foo_,_bar_,...`

The field under validation must be present _only if_ all of the other specified fields are present.

#### `required_without:_foo_,_bar_,...`

The field under validation must be present _only when_ any of the other specified fields are not present.

#### `required_without_all:_foo_,_bar_,...`

The field under validation must be present _only when_ the all of the other specified fields are not present.

#### `same:_field_`

The specified _field_ value must match the field's value under validation.

#### `size:_value_`

The field under validation must have a size matching the given _value_. For string data, _value_ corresponds to the number of characters. For numeric data, _value_ corresponds to a given integer value. For files, _size_ corresponds to the file size in kilobytes.

#### `string:_value_`

The field under validation must be a string type.

#### `timezone`

The field under validation must be a valid timezone identifier according to the `timezone_identifiers_list` PHP function.

#### `unique:_table_,_column_,_except_,_idColumn_`

The field under validation must be unique on a given database table. If the `column` option is not specified, the field name will be used.

#### Basic usage of unique rule

```php
'email' => 'unique:users'
```

#### Specifying a custom column name

```php
'email' => 'unique:users,email_address'
```

#### Forcing a unique rule to ignore a given ID

```php
'email' => 'unique:users,email_address,10'
```

#### Adding additional where clauses

You may also specify more conditions that will be added as "where" clauses to the query:

```php
'email' => 'unique:users,email_address,NULL,id,account_id,1'
```

In the rule above, only rows with an `account_id` of `1` would be included in the unique check.

#### url

The field under validation must be formatted as an URL.

> **NOTE:** This function uses PHP's `filter_var` method.

## Conditionally adding rules

In some situations, you may wish to run validation checks against a field **only** if that field is present in the input array. To quickly accomplish this, add the `sometimes` rule to your rule list:

```php
$v = Validator::make($data, [
    'email' => 'sometimes|required|email',
]);
```

In the example above, the `email` field will only be validated if it is present in the `$data` array.

### Complex conditional validation

Sometimes you may wish to require a given field only if another field has a greater value than 100. Or you may need two fields to have a given value only when another field is present. Adding these validation rules doesn't have to be a pain. First, create a `Validator` instance with your _static rules_ that never change:

```php
$v = Validator::make($data, [
    'email' => 'required|email',
    'games' => 'required|numeric',
]);
```

Let's assume our web application is for game collectors. If a game collector registers with our application and they own more than 100 games, we want them to explain why they own so many games. For example, perhaps they run a game re-sell shop, or maybe they just enjoy collecting. To conditionally add this requirement, we can use the `sometimes` method on the `Validator` instance.

```php
$v->sometimes('reason', 'required|max:500', function($input) {
    return $input->games >= 100;
});
```

The first argument passed to the `sometimes` method is the name of the field we are conditionally validating. The second argument is the rules we want to add. If the `Closure` passed as the third argument returns `true`, the rules will be added. This method makes it a breeze to build complex conditional validations. You may even add conditional validations for several fields at once:

```php
$v->sometimes(['reason', 'cost'], 'required', function($input) {
    return $input->games >= 100;
});
```

> **NOTE:** The `$input` parameter passed to your `Closure` will be an instance of `Illuminate\Support\Fluent` and may be used as an object to access your input and files.

## Validating Arrays

Validating array based form input fields doesn't have to be a pain. You may use "dot notation" to validate attributes within an array. For example, if the incoming HTTP request contains a `photos[profile]` field, you may validate it like so:

```php
$validator = Validator::make(Input::all(), [
    'photos.profile' => 'required|image',
]);
```

You may also validate each element of an array. For example, to validate that each e-mail in a given array input field is unique, you may do the following:

```php
$validator = Validator::make(Input::all(), [
    'person.*.email' => 'email|unique:users',
    'person.*.first_name' => 'required_with:person.*.last_name',
]);
```

Likewise, you may use the `*` character when specifying your validation messages in your language files, making it a breeze to use a single validation message for array based fields:

```php
'custom' => [
    'person.*.email' => [
        'unique' => 'Each person must have a unique e-mail address',
    ]
],
```

You may also use "array notation" in your validation rules if you wish. These rules will be converted to "dot notation" automatically on validation.

```php
$validator = Validator::make(Input::all(), [
    'photos[profile]' => 'required|image',
    'person[][email]' => 'email|unique:users',
]);
```

## Custom error messages

If needed, you may use custom error messages for validation instead of the defaults. There are several ways to specify custom messages.

### Passing custom messages into validator

```php
$messages = [
    'required' => 'The :attribute field is required.',
];

$validator = Validator::make($input, $rules, $messages);
```

> **NOTE:** The `:attribute` place-holder will be replaced by the actual name of the field under validation. You may also utilize other place-holders in validation messages.

### Other validation placeholders

```php
$messages = [
    'same'    => 'The :attribute and :other must match.',
    'size'    => 'The :attribute must be exactly :size.',
    'between' => 'The :attribute must be between :min - :max.',
    'in'      => 'The :attribute must be one of the following types: :values',
];
```

### Specifying a custom message for a given attribute

Sometimes you may wish to specify a custom error messages only for a specific field:

```php
$messages = [
    'email.required' => 'We need to know your e-mail address!',
];
```

### Specifying custom messages in language files

In some cases, you may wish to specify your custom messages in a language file instead of passing them directly to the `Validator`. To do so, add your messages to an array in the `lang/xx/validation.php` language file for your plugin.

```php
return  [
    'required' => 'We need to know your e-mail address!',
    'email.required' => 'We need to know your e-mail address!',
];
```

Then in your call to `Validator::make` use the `Lang:get` to use your custom files.

```php
Validator::make($formValues, $validations, Lang::get('acme.blog::validation'));
```

## Custom validation rules

### Registering a custom validation rule

There are a variety of helpful validation rules; however, you may wish to specify some of your own.

The easiest way to register custom validation rules is by adding the `registerValidationRules() : array` method in the [`Plugin.php` registration file](../plugin/registration#supported-methods) for your plugin. This method should return an array where the key is the validator rule name and the value is either a class that extends `Winter\Storm\Validation\Rule` or a callable function. The callable function receives four arguments, the name of the `$attribute` being validated, the `$value` of the attribute and an array of `$parameters` passed to the rule, and the `$validator` instance.

```php
public function registerValidationRules()
{
    return [
        'be_like_bob' => \Winter\Tester\Rules\BeLikeBobRule::class,
        'uppercase' => function ($attribute, $value, $parameters, $validator) {
            return strtoupper($value) === $value;
        },
    ];
}
```

Another way to register custom validation rules is by extending the Validator instance via the `extend` method. In a Winter CMS plugin, this can be added to the `boot()` callback method inside your `Plugin.php` registration file.

You can extend the Validator instance with your custom validation rule as a `Closure`, or as a `Rule` object.

### Using Closures

If you only need the functionality of a custom rule specified once throughout your plugin or application, you may use a Closure to define the rule. The first parameter defines the name of your rule, and the second parameter provides your Closure.

```php
use Validator;

public function boot()
{
    Validator::extend('foo', function($attribute, $value, $parameters) {
        return $value == 'foo';
    });
}
```

The custom validator Closure receives three arguments: the name of the `$attribute` being validated, the `$value` of the attribute, and an array of `$parameters` passed to the rule.

You may also pass a class and method to the `extend` method instead of a Closure:

```php
Validator::extend('foo', 'FooValidator@validate');
```

Once the Validator has been extended with your custom rule, you will need to add it to your rules definition. For example, you may add it to the `$rules` array of your model.

```php
public $rules = [
    'field' => 'foo'
];
```

### Using Rule objects

A `Rule` object represents a single reusable validation rule for your models that implements the `Illuminate\Contracts\Validation\Rule` contract. Each rule object must provide three methods: a `passes` method which determines if a given value passes validation and a `message` method which defines the default fallback error message. `Rule` objects should extend the `Winter\Storm\Validation\Rule` abstract.

```php
<?php
use Winter\Storm\Validation\Rule;

class Uppercase extends Rule
{
    /**
     * Determine if the validation rule passes.
     *
     * @param  string  $attribute
     * @param  mixed  $value
     * @return bool
     */
    public function passes($attribute, $value)
    {
        return strtoupper($value) === $value;
    }

    /**
     * Get the validation error message.
     *
     * @return string
     */
    public function message()
    {
        return 'The :attribute must be uppercase.';
    }
}
```

To extend the Validator with your rule object, you may provide an instance of the class to the Validator `extend` method:

```php
Validator::extend('uppercase', Uppercase::class);
```

`Rule` objects should be stored in the **/rules** subdirectory inside your plugin directory.

### Defining the Error Message

You will also need to define an error message for your custom rule.

With `Rule` objects, you can set a fallback error message by providing a `message` method that returns a string. This string can be a language string, which will allow the message to be translated as required.

When creating a custom validation rule, you may sometimes need to define custom placeholder replacements for error messages. You may do so by making a call to the `replacer` method on the Validator facade. You may also do this within the `boot` method of your plugin.

```php
public function boot()
{
    Validator::replacer('foo', function ($message, $attribute, $rule, $parameters) {
        // return a message as a string
    });
}
```

The callback receives 4 arguments: `$message` being the message returned by the validator, `$attribute` being the attribute which failed validation, `$rule` being the rule object and `$parameters` being the parameters defined with the validation rule. You may, for example, inject a column name into the message that was defined in the parameters:

```php
public function boot()
{
    Validator::replacer('foo', function ($message, $attribute, $rule, $parameters) {
        return str_replace(':column', $parameters[0], $message);
    });
}
```

### Registering a custom validator resolver

If you wish to provide a large number of custom rules to your application, you can also define a validator resolver. Note that only one resolver may be defined per Validation instance, so it is not recommended to define a resolver in plugins unless you are using your own Validation instance and not the global Validator facade.

To define a resolver, you may provide a Closure to the `resolver` method in the Validator facade.

```php
Validator::resolver(function($translator, $data, $rules, $messages, $customAttributes) {
    return new CustomValidator($translator, $data, $rules, $messages, $customAttributes);
});
```

Each rule supported within a resolver is defined using a `validateXXX` method. For example, the `foo` validation rule would look for a method called `validateFoo`. The `validate` method should return a boolean on whether a given `$value` passes validation.

```php
public function validateFoo($attribute, $value, $parameters)
{
    // return whether the value passes validation
}
```

As with the Validator `replacer` method, you may sometimes need to define custom placeholder replacements for error messages. You may do this in a resolver by defining a `replaceXXX` method.

```php
protected function replaceFoo($message, $attribute, $rule, $parameters)
{
    return str_replace(':foo', $parameters[0], $message);
}
```
