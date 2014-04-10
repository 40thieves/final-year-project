#### 4.6 Web Application Implementation

This section will discuss the implementation of the web application that was described in sections 3.4.1 - 3.4.4. The web application will combine the individual modules described in sections 4.2 - 4.5, providing a easy to use wrapper for the user. Node.js will provide the infrastructure for the application, running an Express server to serve a search form and results page, and the visualisation once altmetrics data has been collected.

##### 4.6.1 Application Flow

Figure 4.2 shows the flow of the application. Users are presented with the home page, containing the search form. When this is submitted, the search parameters are sent to the search API wrapper. Articles returned from the API are then presented to the user in a list. Users can review this list and select articles that they wish to view altmetrics data for. This list of selected articles is sent to the altmetrics data collection API wrapper. Data returned from this wrapper is stored in the database. This generates a unique key for the data, which is used to create a permalink for this dataset. Users are then redirected to the permalink where the visualisation is shown.

* Application flow diagram
	* How data is moved around in the application
	* The links that they click to get to permalink
	* _Diagram showing application flow_
	* Differences in flow for AJAX and non-AJAX users
* Other notes
	* Express
		* Routes
		* Controllers

The web site was constructed with a progressive enhancement approach, where pages are constructed in a layered fashion. This means that basic support for all clients is provided by HTML. This is visually enhanced with CSS, and then behaviour is added using Javascript. These enhancement steps are not required for the core functionality to work, allowing the web site to work on any browser. Each page is constructed to be semantic, giving meaning to a page without using visual design.

For this application, the functionality is slightly different depending on whether Javascript is enabled on the browser. For example, if turned off, the browser will make a POST request when the selected articles are submitted. When Javascript is turned on, this submit event is captured using an event listener and an XMLHttpRequest will be sent instead. This allows the client-side code to show a "Loading" view while the data is processed, instead of the default loading behaviour of the browser. This improved usability for clients that can use Javascript while providing the functionality to all clients.

* Progressive enhancement routes
	* API/non-API
* Exception
	* D3
		* Wouldn't work without JS turned on
		* Couldn't think of a way around this

##### 4.6.2 Search Results Page

As described in the previous section, users are presented with a list of articles that match their search query. They can then select articles by clicking the checkboxes. This is an acceptable interface for users to designate a list of articles that they wish to view altmetrics data for. However, it became apparent that this user interaction is problematic. If, for example, a user wished to select articles from one author, then go back and select further articles from another author, their original selections were removed and forgotten by the system. Clearly, a mechanism for saving selections was required.

This mechanism is provided by the relatively new Javascript API, localStorage. localStorage, part of the Web Storage specification ("Web Storage", n.d.), enables developers to store key/value pairs in the browser itself. The named key must be a string, and the value can be of any type, however it must be converted to a string - usually by converting to JSON - before storage. This can then be parsed to retrieve the value when needed. Storage is segmented so that only data stored on a given domain can be retrieved by the same domain. This prevents pollution by other web sites.

Despite it's simplicity this storage mechanism is useful for this application. When an article is selected by a user, it's key metadata is added to an array of selected articles. This is then converted to a JSON-format string and stored in localStorage, associated with the `articles` key. When the results page is loaded, the articles data is extracted from localStorage, parsed to convert back into an array and iterated through, creating new list elements for each article. This allows the user to select articles, move back to the search form and enter a new search, and retain their previous selections. Clicking the checkbox for a selected article will remove the article from the array, and update localStorage with this new data.

Functionality for localStorage is encapsulated in a class, providing a clean uniform interface for storing data in the browser. The following code snippet shows the Storage class' `get()` function, used for retrieving data with a known key, along with the `parseJson()` call that it uses. 

```js
var parseJson = function(json) {
	try {
		return JSON.parse(json);
	}
	catch (e) {
		return false;
	}
};

var Storage = {
	get: function(key) {
		return parseJson(localStorage.getItem(key));
	}
}
```

As is shown in the example, encapsulating functionality in this way reduces complexity and improves readability by providing a uniform interface.

As discussed above, localStorage is part of the Web Storage specification. This is still in the Editor's Draft phase of World Wide Web Consortium (W3C) specification process, a very early stage. Browser support is very good, with all major modern browsers providing full support, with the exception of the Opera Mini mobile browser ("Can I use...", n.d.). Despite this, older browsers may not be able to support localStorage, and will throw errors if used. For this reason, a progressive enhancement approach is taken, where basic support is provided for all browsers, and more complex behaviours are layered on top of this for more up-to-date browsers. This approach has been applied to all client-side Javascript in the application, but this is most evident when looking at the localStorage support. The following code snippet shows the `isSupported()` method within the Storage class.

```js
var Storage = {
	isSupported: function() {
		try {
			localStorage.setItem('supported-test', 'yes');
			localStorage.removeItem('supported-test');
			return true;
		}
		catch (e) {
			return false;
		}
	},
};
```

This method is designed to be called when the Storage class is used for the first time. It will test to if localStorage is supported by the user's browser. By wrapping usage of the Storage class in a test for support, a progressive enhancement approach is taken - the more complex behaviour is only used if the browser supports localStorage. 

##### 4.6.3 Bower

As discussed in section 3.4.3, the package manager Bower will be used to download important stylesheets from the Twitter Bootstrap project. Bower is an increasingly popular tool for developers creating web sites using third-party libraries and stylesheets, such as Bootstrap and jQuery. Through the use of a command line tool, packages can be more easily downloaded and maintained. By running a `bower update` command, latest versions of the required packages will be downloaded and installed in the project.

Another advantage of Bower is that it removes unnecessary libraries from project repositories, making them somewhat cleaner. Required packages are listed in the `bower.json` file in the root of the project. This allows the project maintainer to remove the libraries from the repository, and require users of the project to run a Bower command that will download libraries and insert them into the project.

There is, however, a major drawback with Bower. Downloaded packages can only be inserted into a single directory. By default this is set to the `bower_components` directory, although this can be changed in the `.bowerrc` configuration file. This prevents developers from having full control over how their project is structured. For example, if Bootstrap and jQuery are required, both a placed in the `bower_components` directory. They cannot be placed in separate directories - a sensible structure that is used by many is `public/css` for stylesheets (such as Bootstrap) and `public/js` for Javascript libraries (such as jQuery). 

These files could be moved manually, however the purpose of Bower is to automate such tasks. Instead, a Grunt plugin, called grunt-bower-task, is used that will run Bower commands then move libraries to their desired directories. This plugin can be configured to layout libraries by type (Javascript or CSS), copying downloaded libraries from the `bower_components` directory to the relevant directory in `public`. For this application, this plugin will be run when the `grunt build` command is executed. It is frustrating that Bower does not provide this functionality, and an external plugin must be used to perform tasks that seem somewhat basic.

* Handlebars

