# Behaviors & Dynamic Class Extension

- [Introduction](#introduction)
- [Comparison to Traits](#compare-traits)
- [Extending Constructors](#constructor-extension)
    - [Dynamically Declaring Properties](#dynamically-declaring-properties)
    - [Retrieving Dynamic Properties](#retrieving-dynamic-properties)
    - [Dynamically Creating Methods](#dynamically-creating-methods)
    - [Checking the Existence of a Method](#check-method-existence)
    - [List Available Methods](#list-available-methods)
    - [Dynamically Implementing Behaviors](#dynamically-implementing-behaviors)
- [Usage Examples](#usage-example)
    - [Behavior / Extension Class](#behavior-extension-class)
    - [Extending a Class](#extending-class)
        - [Using the Extension](#using-extension)
        - [Detecting Utilized Extensions](#detecting-utilized-extensions)
    - [Soft Definitions](#soft-definitions)
- [Using Traits instead of Base Classes](#using-traits)

<a name="introduction"></a>
## Introduction

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

Dynamic Class Extension also adds the ability for classes to have *private traits*, also known as Behaviors. These are similar to [native PHP Traits](https://php.net/manual/en/language.oop5.traits.php) except they have some distinct benefits:

1. Behaviors have their own constructor.
1. Behaviors can have private or protected methods.
1. Methods and property names can conflict safely.
1. Provide a safe mechanism for shared functionality across controllers whilst still maintaining their own state.
1. Classes can be extended with behaviors dynamically.

The best of example of the power of behaviors would be the backend [form](../backend/forms), [list](../backend/lists), and [relation](../backend/relations) ControllerBehaviors that implement the majority of CRUD requirements in Winter CMS for any controllers that implement them.

<a name="compare-traits"></a>
## Comparison to Traits

Where you might use a PHP trait like this:

```php
class MyClass
{
    use \Winter\Storm\UtilityFunctions;
    use \Winter\Storm\DeferredBinding;
}
```

A behavior is used in a similar fashion:

```php
class MyClass extends \Winter\Storm\Extension\Extendable
{
    public $implement = [
        'Winter.Storm.UtilityFunctions',
        'Winter.Storm.DeferredBinding',
    ];
}
```

Where you might define a trait like this:

```php
trait UtilityFunctions
{
    public function sayHello()
    {
        echo "Hello from " . get_class($this);
    }
}
```

A behavior is defined like this:

```php
class UtilityFunctions extends \Winter\Storm\Extension\ExtensionBase
{
    protected $parent;

    public function __construct($parent)
    {
        $this->parent = $parent;
    }

    public function sayHello()
    {
        echo "Hello from " . get_class($this->parent);
    }
}
```

The extended object is always passed as the first parameter to the Behavior's constructor.

To summarize:
- Extend \Winter\Storm\Extension\ExtensionBase to declare your class as a Behaviour
- The class wanting to -implement- the Behaviour needs to extend \Winter\Storm\Extension\Extendable

> **NOTE**: See [Using traits instead of base classes](#using-traits)

<a name="constructor-extension"></a>
## Extending Constructors

Any class that uses the `Extendable` or `ExtendableTrait` can have its constructor extended with the static `extend` method. The argument should pass a closure that will be called as part of the class constructor.

```php
MyNamespace\Controller::extend(function($controller) {
    //
});
```

<a name="dynamically-declaring-properties"></a>
### Dynamically declaring properties

Properties can be declared on an extendable object by calling `addDynamicProperty` and passing a property name and value.

```php
Post::extend(function($model) {
    $model->addDynamicProperty('tagsCache', null);
});
```

> **NOTE**: Attempting to set undeclared properties through normal means (`$this->foo = 'bar';`) on an object that implements the **Winter\Storm\Extension\ExtendableTrait** will not work. It won't throw an exception, but it will not autodeclare the property either. `addDynamicProperty` must be called in order to set previously undeclared properties on extendable objects.

<a name="retrieving-dynamic-properties"></a>
### Retrieving Dynamic Properties

Properties created dynamically can be retrieved with the getDynamicProperties function inherited from
the ExtendableTrait.

So retrieving all dynamic properties would look like this:

```php
$model->getDynamicProperties();
```

This will return an associative array [key => value], with the key being the dynamic property name
and the value being the property value.

If we know what property we want we can simply append the key (property name) to the function:

```php
$model->getDynamicProperties()[$key];
```

<a name="dynamically-creating-methods"></a>
### Dynamically Creating Methods

Methods can be created to an extendable object by calling `addDynamicMethod` and passing a method name and callable object, like a `Closure`.

```php
Post::extend(function($model) {
    $model->addDynamicProperty('tagsCache', null);

    $model->addDynamicMethod('getTagsAttribute', function() use ($model) {
        if ($model->tagsCache) {
            return $model->tagsCache;
        } else {
            return $model->tagsCache = $model->tags()->lists('name');
        }
    });
});
```

<a name="check-method-existence"></a>
### Checking the Existence of a Method

You can check for the existence of a method in an `Extendable` class by using the `methodExists` method - similar to the PHP `method_exists()` function. This will detect both standard methods and dynamic methods that have been added through a `addDynamicMethod` call. `methodExists` accepts one parameter: a string of the method name to check the existence of.

```php
Post::extend(function($model) {
    $model->addDynamicMethod('getTagsAttribute', function () use ($model) {
        return $model->tagsCache;
    });
});

$post = new Post;

$post->methodExists('getTagsAttribute'); // true
$post->methodExists('missingMethod'); // false
```

<a name="list-available-methods"></a>
### List all Available Methods

To retrieve a list of all available methods in an `Extendable` class, you can use the `getClassMethods` method. This method operates similar to the PHP `get_class_methods()` function in that it returns an array of available methods in a class, but in addition to defined methods in the class, it will also list any methods provided by an extension or through an `addDynamicMethod` call.

```php
Post::extend(function($model) {
    $model->addDynamicMethod('getTagsAttribute', function () use ($model) {
        return $model->tagsCache;
    });
});

$post = new Post;

$methods = $post->getClassMethods();

/**
 * $methods = [
 *   0 => '__construct',
 *   1 => 'extend',
 *   2 => 'getTagsAttribute',
 *   ...
 * ];
 */
```

<a name="dynamically-implementing-behaviors"></a>
### Dynamically Implementing Behaviors

This unique ability to extend constructors allows behaviors to be implemented dynamically, for example:

```php
/**
 * Extend the Winter.Users controller to include the RelationController behavior too
 */
Winter\Users\Controllers\Users::extend(function($controller) {
    // Implement the list controller behavior dynamically
    $controller->implement[] = 'Backend.Behaviors.RelationController';

    // Declare the relationConfig property dynamically for the RelationController behavior to use
    $controller->addDynamicProperty('relationConfig', '$/myvendor/myplugin/controllers/users/config_relation.yaml');
});
```

<a name="usage-example"></a>
## Usage Examples

<a name="behavior-extension-class"></a>
### Behavior / Extension class

```php
<?php namespace MyNamespace\Behaviors;

class FormController extends \Winter\Storm\Extension\ExtensionBase
{
    /**
        * @var Reference to the extended object.
        */
    protected $controller;

    /**
        * Constructor
        */
    public function __construct($controller)
    {
        $this->controller = $controller;
    }

    public function someMethod()
    {
        return "I come from the FormController Behavior!";
    }

    public function otherMethod()
    {
        return "You might not see me...";
    }
}
```

<a name="extending-class"></a>
### Extending a class

This `Controller` class will implement the `FormController` behavior and then the methods will become available (mixed in) to the class. We will override the `otherMethod` method.

```php
<?php namespace MyNamespace;

class Controller extends \Winter\Storm\Extension\Extendable
{
    /**
     * Implement the FormController behavior
     */
    public $implement = [
        'MyNamespace.Behaviors.FormController'
    ];

    public function otherMethod()
    {
        return "I come from the main Controller!";
    }
}
```

<a name="using-extension"></a>
#### Using the Extension

```php
$controller = new MyNamespace\Controller;

// Prints: I come from the FormController Behavior!
echo $controller->someMethod();

// Prints: I come from the main Controller!
echo $controller->otherMethod();

// Prints: You might not see me...
echo $controller->asExtension('FormController')->otherMethod();
```

<a name="detecting-utilized-extensions"></a>
#### Detecting utilized extensions

To check if an object has been extended with a behavior, you may use the `isClassExtendedWith` method on the object.

```php
$controller->isClassExtendedWith('Backend.Behaviors.RelationController');
```

Below is an example of dynamically extending a `UsersController` of a third-party plugin utilizing this method to avoid preventing other plugins from also extending the afore-mentioned third-party plugin.

```php
UsersController::extend(function($controller) {
    // Implement behavior if not already implemented
    if (!$controller->isClassExtendedWith('Backend.Behaviors.RelationController')) {
        $controller->implement[] = 'Backend.Behaviors.RelationController';
    }

    // Define property if not already defined
    if (!isset($controller->relationConfig)) {
        $controller->addDynamicProperty('relationConfig');
    }

    // Splice in configuration safely
    $myConfigPath = '$/myvendor/myplugin/controllers/users/config_relation.yaml';

    $controller->relationConfig = $controller->mergeConfig(
        $controller->relationConfig,
        $myConfigPath
    );
}
```

<a name="soft-definitions"></a>
### Soft Definitions

If a behavior class does not exist, like a trait, a *Class not found* error will be thrown. In some cases you may wish to suppress this error, for conditional implementation if a behavior is present in the system. You can do this by placing an `@` symbol at the beginning of the class name.

```php
class User extends \Winter\Storm\Extension\Extendable
{
    public $implement = ['@Winter.Translate.Behaviors.TranslatableModel'];
}
```

If the class name `Winter\Translate\Behaviors\TranslatableModel` does not exist, no error will be thrown. This is the equivalent of the following code:

```php
class User extends \Winter\Storm\Extension\Extendable
{
    public $implement = [];

    public function __construct()
    {
        if (class_exists('Winter\Translate\Behaviors\TranslatableModel')) {
            $this->implement[] = 'Winter.Translate.Behaviors.TranslatableModel';
        }

        parent::__construct();
    }
}
```

<a name="using-traits"></a>
## Using Traits instead of Base Classes

For those cases where you may not wish to extend the `ExtensionBase` or `Extendable` classes, you can use the traits instead. Your classes will have to be implemented as follows:

First let's create the class that will act as a Behaviour, ie. can be -implemented- by other classes.

```php
<?php namespace MyNamespace\Behaviours;

class WaveBehaviour
{
    use \Winter\Storm\Extension\ExtensionTrait;

    /**
        * When using the Extensiontrait, your behaviour also has to implement this method
        * @see \Winter\Storm\Extension\ExtensionBase
        */
    public static function extend(callable $callback)
    {
        self::extensionExtendCallback($callback);
    }

    public function wave()
    {
        echo "*waves*<br>";
    }
}
```

Now let's create the class that is able to -implement- behaviours using the ExtendableTrait.

```php
class AI
{
    use \Winter\Storm\Extension\ExtendableTrait;

    /**
        * @var array Extensions implemented by this class.
        */
    public $implement;
    /**
        * Constructor
        */
    public function __construct()
    {
        $this->extendableConstruct();
    }
    public function __get($name)
    {
        return $this->extendableGet($name);
    }
    public function __set($name, $value)
    {
        $this->extendableSet($name, $value);
    }
    public function __call($name, $params)
    {
        return $this->extendableCall($name, $params);
    }
    public static function __callStatic($name, $params)
    {
        return self::extendableCallStatic($name, $params);
    }
    public static function extend(callable $callback)
    {
        self::extendableExtendCallback($callback);
    }

    public function youGotBrains()
    {
        echo "I've got an AI!<br>";
    }
}
```

The AI class is now able to use behaviours. Let's extend it and have this class implement the WaveBehaviour.

```php
<?php namespace MyNamespace\Classes;

class Robot extends AI
{
    public $implement = [
        'MyNamespace.Behaviours.WaveBehaviour'
    ];

    public function identify()
    {
        echo "I'm a Robot<br>";
        echo $this->youGotBrains();
        echo $this->wave();
    }
}
```

You can now utilize the Robot as follows:

```php
$robot = new Robot();
$robot->identify();
```

Which will output:

```
I'm a Robot
I've got an AI!
*waves*
```

Remember:
- When using the `ExtensionTrait` the methods from `ExtensionBase` should be applied to the class.
- When using the `ExtendableTrait` the methods from `Extendable` should be applied to the class.
