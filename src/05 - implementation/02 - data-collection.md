#### 4.2 Altmetrics Data Collection Implementation

In section 3.4.5, a solution to the requirement for collection of altmetrics data was proposed. This describes a wrapper around the PLOS ALM API, allowing for cleaner and less verbose usage of the API. The inspiration for this module was taken from Cameron Neylon's pyalm package. However, the approaches taken by the Python and Node.js languages are significantly different, and so this module cannot be considered a port of pyalm.

This wrapper was to be written for the Node.js platform, and published as a standalone package on Node.js' package manager, npm. The package is called nodealm, and can be downloaded by other developers looking to solve similar problems. This section describes the challenges faced when implementing this solution, and how the problems were addressed.

##### 4.2.1 Prototypical Inheritance and Node.js Modules

As the module has been published as a standalone package, JavaScript best practices must be followed. This is to assist other developers using the package, giving them greater understanding of how the package functions. One of these best practices involves structuring the module using prototypical inheritance. Widely popularised by Douglas Crockford, this form of object orientated programming takes advantage of JavaScript's prototype chain to create class-like objects containing class-scoped variables and functions. The prototype chain is made up of internal links between objects, to create a linked chain.

Object inheritance within this module is not required - it lacks the complexity for this. However, method and property inheritance are required. This is achieved in the same way as object inheritance - through the prototype chain. The following example shows a property and a function added to the prototype chain of an object.

```js
var Obj = {};

Obj.prototype.aProperty = 'foo';

Obj.prototype.aFunction = function() {
	...
};
```

If an instance of the `Obj` "class" is created - by calling `obj = new Obj();` - the function `aFunction` can be executed on the instance. The property `aProperty` can be accessed through the instance, by using the `obj.aProperty` statement. This encapsulates these components within the object, preventing them from polluting the global namespace, and affecting other objects within the system.

This can be extended to achieve a "constructor" style class. Functions within JavaScript also have a prototype, meaning that the prototype chain can be extended for functions in the same way as above. The following example demonstrates the declaration of class with a constructor.

```js
var Func = function() {
	// Constructor
};

Func.prototype.aProperty = 'bar';

Func.prototype.aFunction = function() {
	// Method
};
```

This is used within the module to create a class, called `Alm`, that can be easily instantiated by external code and is completely encapsulated.

A constructor is used within the API wrapper module as a mechanism for setting up crucial data within the package. The following code snippet shows the constructor used in the wrapper module.

```js
var Alm = function(ids, options) {
	config = configs(this.options.mode);

	if (options)
		extend(config, options);

	this.ids = ids;
};
```

This constructor accepts a list of DOIs - as described in section 3.4.5 - and an options object. The constructor starts by resolving the default options with input options. The first line imports the default options hash, which is then passed into the `extend()` function along with the input options. This function will return the default options object, unless properties within the object are specifically overwritten by input option. The last line of this constructor sets the list of DOIs as a class property for access by other class methods.

The prototypical inheritance structure is enhanced by Node.js. Scope for a given expression is limited to the current module - in most cases, the current file - and the global scope that is maintained by Node.js. To access anything outside of this scope, it must be imported, using the `require()` method. This can be used to import from an external package (referenced using the name of the package) or from an internal module (referenced using the relative path). To "export" from a module, the `module.exports` property must be used. Only data referenced in this object can be accessed outside of the module.

This system of exporting scope can be used to create an equivalence of "public" and "private" methods found in traditional object-orientated languages. Methods that are public are added to the prototype chain, while methods that are private are only available within the module itself.

This approach does not solve all problems related to inheritance. The most obvious of these is the `this` keyword, which holds a reference to the class within class methods. This is very useful, as it allows access to other properties within the class. The following example shows a demonstration where a class property is set in the constructor, then referenced in a class method.

```js
var Foo = function() {
	this.bar = 'baz';
};

Foo.prototype.aFunc = function() {
	return this.bar;
}
```

However, this can become complicated when using prototyped method and non-prototyped methods. In locally scoped functions the `this` keyword loses its reference to the class - as it is technically not part of the class. Fortunately, JavaScript provides ways of mitigating this. The `bind()` and `call()` functions provide different systems for ensuring that the `this` keyword is bound to the desired reference. These are used in the wrapper module to maintain a consistent reference to the `this` keyword.

Another best practice that is recognised by many Node.js developers is the file structure of the module. Typically a Node.js module is organised in the following manner:

![Figure 4.1: Screenshot of the final visualisation](../../src/img/figure4-1.png)

There sometimes are variations on the naming conventions shown here, however, the `lib` directory will hold files containing the application logic, and the `test` directory contains the module's unit tests. The `app.js` file will bootstrap the module, exporting the necessary files from the module library. This will make the logic contained in the library available to Node.js.

##### 4.2.2 Event Based Structure

For the first iteration of the API wrapper, a procedural structure was adopted. The module would be called procedurally, using a `getAlm()` method, giving a list of DOIs and callback function as arguments. This would then bootstrap the module, call the API, process the results and call the callback when finished. This callback would be populated with the results, or if an error occurred, an error object. The follow example demonstrates this functionality.

```js
alm.getAlm('10.1371/journal.pbio.1000242', function(err, result) {
	console.log(result);
});
```

Using this structure, the callback is handling both an error and successful response. The callback would have to test for an error response - which would be `null` if no error occurred - and throw an error. This could then be handled by the wrapper's callee appropriately. This is an acceptable structure, however an alternative structure was proven to be better.

The wrapper module now follows an event based structure, where a new instance of the `Alm` class is created and event listeners for success and error responses are attached. Finally, the `fetch` method is called which will fire the request to the API. This is shown in the following code snippet.

```js
var alm = new Alm('10.1371/journal.pbio.1000242');

alm.on('success', function(result) {
	console.log(result);
});

alm.on('error', function(err) {
	console.log(err);
});

alm.fetch();
```

The class constructor accepts the list of DOIs and options as parameters, which were previously passed to the `getAlm()` method. This would bootstrap the class, and make these options available to the rest of the class as properties. This is the first key improvement of the initial structure. Key properties, such as the options hash, are immediately available to the entire class. The constructor performs its function and bootstraps the class, instead of delegating this task to another method.

Node.js provides events functionality through its `events` core module. These events perform much the same task as events within the browser. Custom events can be created using the `EventEmitter` class, but only from classes that "inherit" from this emitter class. This is achieved using Node.js' `inherits()` method, part of the `util` module.

```js
var util         = require('util');
var EventEmitter = require('events').EventEmitter;

var Alm = function() {
	...
};

util.inherits(Alm, EventEmitter);

Alm.prototype.foo = function() {
	this.emit('foo', {
		foo: 'bar'
	});
};
```

Immediately after the wrapper class is declared, it is set to inherit from the `EventEmitter` class, giving access to the `emit()` function. This has two parameters; the name of the event, and any data that will be attached to the emitted event.

```js
var alm = new Alm();

alm.on('foo', function() {
	...
});
```

To listen to the event, the `on` method is used, as shown in the example above. This also takes two arguments; the name of the event it listening for, and a callback function. When the event is triggered, the callback will be fired.

These callbacks split the duties performed by the callback for the `getAlm()` method. Instead of having to test for an error every time, the success callback will process successfully returned data, while the error callback will only be fired if an error occurs. This is much cleaner than the previous structure, and removes the need for an error check on every response.

##### 4.2.3 API Problems

The PLOS ALM API is a good base for providing altmetrics data, as discussed in section 3.4.5, however it is not without problems. The main problem that was encountered was querying for many articles - over 20 -  with historical metadata in a single request. This will sometimes produce an error response, although this may be inconsistent. This may be due to the fact that a very large amount of data needs to be processed and so the request times out. Alternatively, there may be a bug with the API that causes this intermittently. The bug has been reported to the API providers.

Fortunately, there does appear to be a work-around - the API allows users to restrict the data sources that are included in the response. If the request includes `source=twitter`, for example, then only altmetric data pertaining to Twitter will be returned. This can be expanded to include multiple data sources, using a comma-separated list. In testing it was found that including a list for all possible data sources would not produce the error described above. This work-around has been implemented in the package as a temporary solution, but will be removed if the problem is resolved.

##### 4.2.4 Sending HTTP Requests

The purpose of this module is to request data from the PLOS ALM API. To achieve this, an HTTP request must be sent to the server that runs the API. Node.js provides functionality for this using the `http` module, as shown in the following code snippet.

```js
var http = require('http');
var client = http.createClient(port, host);
var request = client.request('GET', query);
request.end();
request.on('response', function(response) {
	...
});
```

This code performs the required task, however it is overly-verbose. A module that is available on npm can be used instead. The request library is one of the most widely used packages available on the npm repository, currently listed as the third most depended-upon package in npm. The following code snippet shows a request with the same functionality as above, using the request module.

```js
var request = require('request');
request(host + query, function(error, response, body) {
	...
})
```

The request module is used by the data collection module to send HTTP requests to the API. This allows the module to retrieve altmetrics data from the API over HTTP, in a clean and readable manner.

