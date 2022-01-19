# Snowboard Plugin Development

- [Introduction](#introduction)
- [Framework Concepts](#concepts)

<a name="introduction"></a>
## Introduction

...

<a name="concepts">
## Framework Concepts

The four cornerstones of this framework are as follows:

### The `Snowboard` class
Represents the global container for the application. This stores all activated plugins and provides instances of these modules as required. It is synonymous with the `Application` class of Laravel, or a `Vue` instance.

### The `PluginLoader` class
The main conduit between a plugin and the Snowboard app. This provides the mechanism for delivering instances of each module (or a single instance of Singleton plugin) and resolves dependencies.

### The `PluginBase` class
A plugin is a specific extension to the Snowboard application. It can contain code that provides new functionality or augments other functionality already registered in the application. A plugin can be reused - in essence, each call to the plugin will create a new instance and will be independent from other instances. This would be used for scenarios such as Flash messages, AJAX requests and the like.

### The `Singleton` class
An extension of the `PluginBase` class. It signifies to the application that this plugin should exist once, and all uses of this plugin should use the same instance. This would be useful for things like event handlers, extending functionality of another plugin or global functionality.

A singleton is initialized automatically when the DOM is ready, if it is called directly or if it listens to an event that is fired. The singleton instance is then used no matter how many times it is called from the Snowboard class.
