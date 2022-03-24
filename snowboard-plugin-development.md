# Snowboard Plugin Development

- [Introduction](#introduction)
- [Framework Concepts](#concepts)
  - [The Snowboard class](#snowboard-class)
  - [The PluginLoader class](#plugin-loader-class)
  - [The PluginBase and Singleton abstracts](#plugin-base-singleton)
  - [Global events](#global-events)

<a name="introduction"></a>
## Introduction

The Snowboard framework has been designed to be extensible and customisable for the needs of your project. To this end, the following documentation details the concepts of the framework, and how to develop your own functionality to extend or replace features within Snowboard.

<a name="concepts"></a>
## Framework Concepts

Snowboard works on the concept of an encompassing application, which acts as the base of functionality and is then extended through plugins. The main method of communication and functionality is through the use of [JavaScript classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) which offer instances and extendability, and global events - a feature in-built into Snowboard.

The following classes and abstracts are included in the Snowboard framework.

<a name="snowboard-class"></a>
### The `Snowboard` class

The Snowboard class is the representation of the application. It is the main point of adding, managing and accessing functionality within the framework, synonymous to using `jQuery` or `Vue` in your scripts. It is injected into the global JavaScript scope, and can be accessed through the `Snowboard` variable anywhere within the application after the `{% snowboard %}` tag is used.

In addition, Snowboard is injected into all plugin classes that are used as the entry point for a plugin, and can be accessed via `this.snowboard` inside an entry plugin class.

```js
// All these should work to use Snowboard globally
Snowboard.getPlugins();
snowboard.getPlugins();
window.Snowboard.getPlugins();

// In your plugin, Snowboard can also be accessed as a property.
class MyPlugin extends PluginBase {
  myMethod() {
    this.snowboard.getPlugins();
  }
}
```

The Snowboard class provides the following public API for use in managing plugins and calling global events:

Method | Parameters | Description
------ | ---------- | -----------
`addPlugin` | name(`String`)<br>instance(`PluginBase`) | Adds a plugin to the Snowboard framework. The name should be a unique name, unless you intend to replace a pre-defined plugin. The instance should be either a [PluginBase or Singleton](#plugin-base-singleton) instance that represents the "entry" point to your plugin.
`removePlugin` | name(`String`) | Removes a plugin, if it exists. When a plugin is removed, all active instances of the plugin will be destroyed.
`hasPlugin` | name(`String`) | Returns `true` if a plugin with the given name has been added.
`getPlugin` | name(`String`) | Returns the [PluginLoader](#plugin-loader-class) instance for the given plugin, if it exists. If it does not exist, an error will be thrown.
`getPlugins` | | Returns an object of added plugins as [PluginLoader](#plugin-loader-class) instances, keyed by the name of the plugin.
`getPluginNames` | | Returns all added plugins by name as an array of strings.
`listensToEvent` | eventName(`String`) | Returns an array of plugin names as strings for all plugins that listen to the given event name. This works for both Promise and non-Promise [global events](#global-events).
`globalEvent` | eventName(`String`)<br>*...parameters* | Calls a non-Promise [global event](#global-event). This will trigger event callbacks for all plugins listening to the given event. This method can be provided additional parameters that will be forwarded through to the event callbacks. This method returns `false` if the event was cancelled by a plugin.
`globalPromiseEvent` | eventName(`String`)<br>*...parameters* | Calls a Promise [global event](#global-event). This will trigger event callbacks for all plugins listening to the given event. This method can be provided additional parameters that will be forwarded through to the event callbacks. This method returns a Promise that will either be resolved or rejected depending on the response from the event callbacks of the plugins.
`debug` | *...parameters* | When the application is in debug mode, this method logs debug messages to the console. Each log message can display one or more parameters at the same time, and includes a trace of the entire call stack up to when the debug call was made.

#### Debugging in Snowboard

As indicated just prior, the Snowboard class provides a `debug` method that allows developers to easily debug their Snowboard application and plugins. This method only works if the Winter application is in debug mode, by setting `debug` to `true` in the `config/app.php` file.

Debugging can be called anywhere that the Snowboard class is accessible.

```js
// Globally
Snowboard.debug('This is a debug message');

// Within a plugin
class MyPlugin extends PluginBase {
  myMethod() {
    this.snowboard.debug('Debugging my plugin', this);
  }
}
```

In general, you would use the first parameter of the `debug` method to state the debug message. From there, additional parameters can be added to provide additional context. The method will print a collapsed debug message to your developer console on your browser. You may extend the debug message in your console to view a stack trace, showing the entire call stack up to when the debug message was triggered.

<a name="plugin-loader-class"></a>
### The `PluginLoader` class

The PluginLoader class is the conduit between your application (ie. the [Snowboard class](#snowboard-class)) and the plugins. It acts similar to a "factory", providing and managing instances of the plugins and allowing the Snowboard application to communicate to those instances. It also provides a basic level of mocking, to allow for testing or overwriting individual methods of the plugin dynamically.

Each PluginLoader instance is representative of one plugin. You can retrieve the PluginLoader instance of a particular plugin through Snowboard:

```js
const pluginLoader = Snowboard.getPlugin('myPlugin');
```

In general, you will not need to interact with this class directly - most developer-facing functionality should be done on the Snowboard class or the plugins themselves. Thus, we will only document the methods that *may* be accessed by developers in limited circumstances:

Method | Parameters | Description
------ | ---------- | -----------
`hasMethod` | methodName(`String`) | Returns `true` if the plugin defines a method by the given name.
`getInstance` | *...parameters* | Returns an instance of the plugin, using the given parameters to send through to the plugin's constructor. If the plugin is a singleton, this will always return the *same* instance.
`getInstances` | | Returns all current instances of the plugin as an array. If this is a singleton plugin, this will always return a single instance of the plugin.
`getDependencies` | | Returns an array of the names of all plugins that the current plugin depends on, as strings.
`dependenciesFulfilled` | | Returns `true` if the current plugin's dependencies have been fulfilled.
`mock` | methodName(`String`)<br>callback(`Function`) | Defines a mock for the current plugin, replacing the given method with the provided callback. This allows a developer to replace a function for testing. The mock will only be available for *new* instances of the plugin after the mock is added. It will not be retroactively added. See the **Mocking** section below for more information.
`unmock` | methodName(`String`) | Removes a mock for the current plugin on the given method, if it has been previously mocked.

#### Mocking

The PluginLoader class allows for the mocking of plugin methods, for both normal plugins as well as singleton plugins through the `mock` method. The main usage of this is for testing, but it can also be used to override plugin functionality in an "ad-hoc" basis.

When mocking a method, you should always ensure that your callback has the same number of parameters that the original method had.

Mocking will apply to any *new* instances of the plugin. If you have previously retrieved an instance of the plugin, and saved it locally as a variable, these instances will *not* have the mock applied. You should generally retrieve instances through Snowboard to avoid this scenario.

```js
class MyPlugin extends PluginBase {
  sayHi(name) {
    return `Hi ${name}!`;
  }
}
Snowboard.addPlugin('myPlugin', MyPlugin);

// Save an instance of the plugin as a variable
const myPlugin = Snowboard.myPlugin();

// Returns "Hi Ben!"
console.log(myPlugin.sayHi('Ben'));

// Now let's mock the method
const pluginLoader = Snowboard.getPlugin('myPlugin');
pluginLoader.mock('sayHi', (name) => {
  return `Hello ${name}!`;
});

// Returns "Hello Ben!" as Snowboard always returns a new instance
console.log(Snowboard.myPlugin().sayHi('Ben'));

// Returns "Hi Ben!", as this instance was created before the mock was applied
console.log(myPlugin.sayHi('Ben'));
```

To remove a previously added mock, use the `unmock` method with the same method name. As with mocking, this is not retroactive, so older instances of the plugin with the mock applied will still exhibit the mocked behaviour.

<a name="plugin-base-singleton"></a>
### The `PluginBase` and `Singleton` abstracts

PluginBase and Singleton are two abstract classes that represent the base of all plugins within the Snowboard architecture. When creating a plugin, it is expected that your plugin extends one of these two classes.

While these two classes are functionally the same, they are intepreted slightly differently by Snowboard when providing instances of the plugin - a class extending the `PluginBase` class is reusable, with each call to the plugin providing a new, clean instance of the plugin. By contrast, a class extending the `Singleton` class has one instance that is used throughout the entire application and shares the same data across all uses of the plugin.

Here are some of the scenarios where you would use either of these classes:

- **PluginBase**
  - Once-off tasks that take data or options, run the task and then are discarded (for example, [AJAX Requests](../snowboard/request))
  - User interface elements, such as flash messages or loading indicators.
  - Widgets or utilities that do not share data across instances.
- **Singleton**
  - Plugins that acts as conduits between global events and other reusable plugins.
  - Shared user interface elements or controllers.
  - Data storage.