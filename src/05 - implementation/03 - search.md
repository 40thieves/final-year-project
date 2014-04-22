<div class="page-break-avoid">

#### 4.3 Search Implementation

This section will describe the implementation of the search module, as described in section 3.4.6. A wrapper around the PLOS Search API was created, producing a uniform interface to the API. By encapsulating the API logic in a module, the codebase will become cleaner and less verbose.

</div>

##### 4.3.1 Package Publication

The search package, like the altmetrics API package discussed in the previous section, was also published to npm, under the name plos-search. This enables other altmetrics researchers or developers to download and include in their work. The npm command line client provides an easy way for packages to be published. Valid packages can be published using the `npm publish` command.

To create a valid package, a `package.json` file must be created. This holds basic metadata about the package - package name, version number, author, repository - as well as the package's dependencies. External modules that are required for the package to run are listed, along with the relevant version number in the `dependencies` section of the `package.json` file. Modules that are only required for development - usually test or build tools - are listed in the `devDependencies` section. npm can automatically construct a `package.json` file using the `npm init` command.

##### 4.3.2 Similarity to Altmetrics Module

The altmetrics module discussed in section 4.2 is somewhat similar to the search module described in this section. Both use prototypical inheritance (see section 4.2.1) and an event based structure (see section 4.2.2). This approach makes sense as there is some shared functionality between the modules. Both accept some parameters, use these to construct a request to their respective APIs, send a HTTP request using the request module (see section 4.2.4) before parsing and validating the response and returning the data. Therefore using a similar structure means that, overall, the codebase is cleaner and more readable.

##### 4.3.3 Validation of Search Parameters

In contrast to the altmetrics data collection package, where the input only consists of a list of DOIs, input for the search module is more complex. The PLOS Search API will only accept search parameters from a specified list. Using a request that has parameters outside this list will produce a response with no results. To prevent this possibility, the module will throw an error if such parameters are used. This will reduce the number of unnecessary requests, as those which are known to produce an error will never be sent. To achieve this, the package must hold a "whitelist" of permissible parameters and must check current parameters against this whitelist before sending a request.

Implementing the whitelist is relatively easy. The package has some built in configuration, for holding the root URL to the API, and the API key. This configuration is loaded each time a new instance of the module is created. If this configuration is extended to include an array of permissible terms, the module will gain access to the whitelist.

To test current query parameters against the whitelist, a comparison function was created. The following code snippet shows this function, where the the `config.query_params_whitelist` contains the whitelist array and the `throwError()` function will throw an error from the module.

```js
testKey = function(key) {
	if (config.query_params_whitelist.indexOf(key) === -1)
		return self.throwError('Query param: ' + key + ' not recognised');
};
```

This function will be called from the module's constructor. This means that an error can be thrown before error event listeners are attached, which would never be triggered and the module would silently fail. Therefore, if the module is likely to encounter parameters not on the whitelist, instantiation of the module must be wrapped in a try/catch block. This is shown in the following code snippet:

```js
var Search = require('plos-search');
try {
	var search = new Search({ foo: 'bar' });
}
catch (err) {
	// Handle error
}
```

##### 4.3.4 API HTTP Errors

According to the HTTP protocol, APIs should return relevant HTTP error codes when an error occurs during processing of the request. Following these rules is considered a best practice when developing an API. This is because developers can expect a standardised interface when communicating with an HTTP API.

Unfortunately, the PLOS Search API cannot be considered to correctly implement the HTTP protocol. The API does not return error codes for some errors. For example, if a search returns no results, the API returns a 200 OK response that contains no articles in the response body. According to the protocol, this response should be a 404 Not Found error response. In addition, if a request is malformed, for example invalid search parameters, the same empty 200 OK response is returned. Instead, a 400 Bad Request response should be returned.

This causes problems for the module as responses must be parsed to ensure that no error has occurred. This may have an effect on performance of the module, unfortunately, there is no alternative until the API correctly implements the protocol. The following code snippet shows some of the error checking mechanisms within the module.

```js
Search.prototype.checkForErrors = function(err, res, body) {
	body = body.response.docs;
	if (body.length < 1)
		return this.throwError('No results found');
};
```

