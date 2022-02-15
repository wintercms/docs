# Snowboard Plugin Development

- [Introduction](#introduction)
- [Framework Concepts](#concepts)
  - [The Snowboard class](#snowboard-class)
  - [The PluginLoader class](#plugin-loader-class)
  - [The PluginBase and Singleton abstracts](#plugin-base-singleton)
  - [Global events](#global-events)
  - [Mocking](#mocking)

<a name="introduction"></a>
## Introduction

The Snowboard framework has been designed to be extensible and customisable for the needs of your project. To this end, the following documentation details the concepts of the framework, and how to develop your own functionality to extend or replace features within Snowboard.

<a name="concepts"></a>
## Framework Concepts

Snowboard works on the concept of an encompassing application, which acts as the base of functionality and is then extended through plugins. The main method of communication and functionality is through the use of [JavaScript classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) which offer instances and extendability, and global events - a feature in-built into Snowboard.

The following classes and abstracts are included in the Snowboard framework.

<a name="snowboard-class"></a>
### The Snowboard class

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
`globalEvent` | eventName(`String`)<br>*...parameters* | Calls a non-Promise [global event](#global-events). This will trigger event callbacks for all plugins listening to the given event. This method can be provided additional parameters that will be forwarded through to the event callbacks. This method returns `false` if the event was cancelled by a plugin.
`globalPromiseEvent` | eventName(`String`)<br>*...parameters* | Calls a Promise [global event](#global-events). This will trigger event callbacks for all plugins listening to the given event. This method can be provided additional parameters that will be forwarded through to the event callbacks. This method returns a Promise that will either be resolved or rejected depending on the response from the event callbacks of the plugins.
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
### The PluginLoader class

The PluginLoader class is the conduit between your application (ie. the [Snowboard class](#snowboard-class)) and the plugins. It acts similar to a "factory", providing and managing instances of the plugins and allowing the Snowboard application to communicate to those instances. It also provides a basic level of mocking, to allow for testing or overwriting individual methods of the plugin dynamically.

Each PluginLoader instance will be representative of one plugin.

In general, you will not need to interact with this class directly - most developer-facing functionality should be done on the Snowboard class or the plugins themselves. Thus, we will only document the methods that *may* be accessed by developers.

Method | Parameters | Description
------ | ---------- | -----------
`hasMethod` | methodName(`String`) | Returns `true` if the plugin defines a method by the given name.
`getInstance` | *...parameters* | Returns an instance of the plugin. Please see the **Plugin instantiation** section below for more information.
`getInstances` | | Returns all current instances of the plugin.
`getDependencies` | | Returns an array of the names of all plugins that the current plugin depends on, as strings.
`dependenciesFulfilled` | | Returns `true` if the current plugin's dependencies have been fulfilled.
`mock` | methodName(`String`)<br>callback(`Function`) | Defines a mock for the current plugin, replacing the given method with the provided callback. See the [Mocking](#mocking) section for more information.
`unmock` | methodName(`String`) | Restores the original functionality for a previously-mocked method. See the [Mocking](#mocking) section for more information.

<a name="plugin-base-singleton"></a>
### The `PluginBase` and `Singleton` abstracts

These classes are the base of all plugins in Snowboard, and represent the base functionality that each plugin contains. When creating a plugin class, you will almost always extend one of these abstract classes.

There are two key differences between these abstracts, based on the reusability of the plugin and how it is instantiated in the course of the JavaScript functionality of your project:

Detail | `PluginBase` | `Singleton`
------ | ------------ | -----------
Reusability | Each use of the plugin creates a new instance of the plugin | Each use of the plugin uses the same instance.
Instantiation | Must be instantiated manually when it is needed to be used | Instantiated automatically when the page is loaded

The reason for the separation is to provide better definition on how your plugin is intended to be used. For `PluginBase`, you would use this when you want each instance to have its own scope and data. Contrarily, you would use `Singleton` if you want the same data and scope to be shared no matter how many times you use the plugin.

Here are some examples of when you would use one or the other:

- `PluginBase`
  - AJAX requests
  - Flash messages
  - Reusable widgets with their own data
- `Singleton`
  - Event listeners
  - Global utilities
  - Base user-interface handlers

<a name="global-events"></a>
### Global events

Global events are an intrinsic feature of the Snowboard framework, allowing Snowboard plugins to respond to specific events in the course of certain functionality, similar in concept to DOM events or the Event functionality of Winter CMS.

There are two entities that are involved in any global event:

- The **triggering** class, which fires the global event with optional extra context, and,
- The **listening** class, which listens for when a global event is fired, and actions its own functionality.

There are also two types of global events that can be triggered, they are:

- A **standard** event, which fires and executes all listeners in listening classes, and,
- A **Promise** event, which fires and waits for the Promise to be resolved before triggering further functionality.

In practice, you would generally use standard events for events in where you do not necessarily want to wait for a response. In all other cases, you would use a Promise event which allows all listening classes to respond to the event in due course.

Firing either event is done by calling either the `globalEvent` or `globalPromiseEvent` method directly on the main Snowboard class.

```js
// Standard event
snowboard.globalEvent('myEvent', context);

// Promise event
snowboard.globalPromiseEvent('myPromiseEvent').then(
  () => {
    // functionality when the promise is resolved
  },
  () => {
    // functionality when the promise is rejected
  }
);
```

For a plugin to register as a listening class, it must specify a `listens` method in the plugin class that returns an object. Each key should be the global event being listened for, and the value should be the name of a method inside the class that will handle the event when fired. This is the same whether the event is a standard event or a Promise event.

```js
class MyPlugin extends PluginBase {
  listens() {
    return {
      ready: 'ready',
      eventName: 'myHandler',
    };
  }

  ready() {
    // This method is run when the `ready` global event is fired.
  }

  myHandler(context) {
    // This method is run when the `eventName` global event is fired.
  }
}
```

Snowboard only has one in-built global event that is fired - the `ready` event, which is fired when the DOM is loaded and the page is ready. This event is synonymous with jQuery's `ready` event, and is mainly used to instantiate the Singletons that have been registered with Snowboard.

