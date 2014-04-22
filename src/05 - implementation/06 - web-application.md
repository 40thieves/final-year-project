<div class="page-break-avoid">

#### 4.6 Web Application Implementation

This section will discuss the implementation of the web application that was proposed in sections 3.4.1 - 3.4.4. The web application will combine the individual modules described in sections 4.2 - 4.5, providing an easy to use interface for the user. Node.js will provide the infrastructure for the application, running an Express server to serve a search form and results page, and the visualisation once altmetrics data has been collected.

</div>

##### 4.6.1 Application Flow

Figure 4.3 shows a data flow diagram of the application. Users are presented with the home page, containing the search form. When this is submitted, the search parameters are sent to the search API wrapper. Articles returned from the API are then presented to the user in a list. Users can review this list and select articles of interest. This list of selected articles is sent to the altmetrics data collection API wrapper. Data returned from this wrapper is stored in the database. This generates a unique key for the data, which is used to create a permalink for this dataset. Users are then redirected to the permalink where the visualisation is shown.

![Figure 4.3: Flow diagram showing the flow of data between the various parts of the application and the user](../../src/img/figure4-3a.png)

The web site was constructed with a progressive enhancement approach, where pages are constructed in a layered fashion. This means that basic support for all clients is provided by HTML. This is visually enhanced with CSS, and then behaviour is added using JavaScript. These enhancement steps are not required for the core functionality to work, allowing the web site to work on any browser. Each page is constructed to be semantic, giving meaning to a page without using visual design.

For this application, the functionality is slightly different depending on whether JavaScript is enabled on the browser. For example, if turned off, the browser will make a POST request when the selected articles are submitted. When JavaScript is turned on this submit event is captured using an event listener and an XMLHttpRequest will be sent instead. This creates an AJAX (Asynchronous XML and JavaScript) style request to the server. This allows the client-side code to show a "Loading" view while the data is processed, instead of the default loading behaviour of the browser. This improves usability for clients that can use JavaScript while providing the same functionality to all clients.

The slight differences in this behaviour mean that two alternative routes must be created within the application. A key difference between these behaviours is the expected format of the response. An HTML page is expected from a non-AJAX route, while an AJAX route expects a JSON format response, which can be used by client-side JavaScript to modify the current page. To separate these routes, a separation between "regular" routes and API routes is created, where non-AJAX requests should be sent to the former route and AJAX routes should be sent to the latter. Both routes perform similar functionality, however the response is generated differently.

There is an exception to the progressive enhancement approach in this project. JavaScript is required to create the visualisation, as it is created using D3.js. Therefore, no progressive enhancement takes place in this part of the web application, and it is assumed that the user has JavaScript turned on.

##### 4.6.2 Search Results Page

As described in the previous section, users are presented with a list of articles that match their search query. They can then select articles by clicking the checkboxes. This is an acceptable interface for users to designate a list of articles that they wish to view altmetrics data for. However, it became apparent that this user interaction is problematic. If, for example, a user wished to select articles from one author, then go back and select further articles from another author, their original selections were removed and forgotten by the system. Clearly, a mechanism for saving selections was required.

This mechanism is provided by the relatively new JavaScript API, localStorage. localStorage, part of the Web Storage specification ("Web Storage", n.d.), enables developers to store key/value pairs in the browser itself. The named key must be a string, and the value can be of any type, however it must be converted to a string - usually by converting to JSON - before storage. This can then be parsed to retrieve the value when needed. Storage is segmented so that only data stored on a given domain can be retrieved by the same domain. This prevents pollution by other web sites.

Despite its simplicity this storage mechanism is useful for this application. When an article is selected by a user, its key metadata is added to an array of selected articles. This is then converted to a JSON-format string and stored in localStorage, associated with the `articles` key. An "Articles" UI element is updated to reflect the number of stored articles. When the results page is loaded, the articles data is extracted from localStorage, parsed to convert back into an array and iterated through, creating new list elements for each article. This allows the user to select articles, move back to the search form and enter a new search, and retain their previous selections. Clicking the checkbox for a selected article will remove the article from the array, and update localStorage with this new data.

Functionality for localStorage is encapsulated in a class, providing a clean uniform interface for storing data in the browser. The following code snippet shows the Storage class' `get()` function, used for retrieving data with a known key, along with the `parseJson()` call that it depends on.

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

As discussed above, localStorage is part of the Web Storage specification. This is still in the Editor's Draft phase of World Wide Web Consortium (W3C) specification process, a very early stage. Browser support is very good, with all major modern browsers providing full support, with the exception of the Opera Mini mobile browser ("Can I use...", n.d.). Despite this, older browsers may not be able to support localStorage, and will throw errors if used. For this reason, a progressive enhancement approach is taken, where basic support is provided for all browsers, and more complex behaviours are layered on top of this for more up-to-date browsers. This approach has been applied to all client-side JavaScript in the application, but this is most evident when looking at the localStorage support. The following code snippet shows the `isSupported()` method within the Storage class.

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

This method is designed to be called when the Storage class is used for the first time. It will test if localStorage is supported by the user's browser. By wrapping usage of the Storage class in a test for support, a progressive enhancement approach is taken - the more complex behaviour is only used if the browser supports localStorage.

##### 4.6.3 Bower

As discussed in section 3.4.3, the package manager Bower will be used to download important stylesheets from the Twitter Bootstrap project. Bower is an increasingly popular tool for developers creating web sites using third-party libraries and stylesheets, such as Bootstrap and jQuery. Through the use of a command line tool, packages can be more easily downloaded and maintained. By running a `bower update` command, latest versions of the required packages will be downloaded and installed in the project.

Another advantage of Bower is that it removes unnecessary libraries from project repositories, making them somewhat cleaner. Required packages are listed in the `bower.json` file in the root of the project. This allows the project maintainer to remove the libraries from the repository, and require users of the project to run a Bower command that will download libraries and insert them into the project.

There is, however, a major drawback with Bower. Downloaded packages can only be inserted into a single directory. By default this is set to the `bower_components` directory. This prevents developers from having full control over how their project is structured. For example, if Bootstrap and jQuery are required, both are placed in the `bower_components` directory. They cannot be placed in separate directories - a sensible structure that is used by many is the directory `public/css` for stylesheets (such as Bootstrap) and `public/js` for JavaScript libraries (such as jQuery).

These files could be moved manually, however the purpose of Bower is to automate such tasks. Instead, a Grunt plugin, called grunt-bower-task, is used that will run Bower commands then move libraries to their desired directories. This plugin can be configured to layout libraries by type (JavaScript or CSS), copying downloaded libraries from the `bower_components` directory to the relevant directory in the `public` directory. For this application, this plugin will be run when the `grunt build` command is executed. It is frustrating that Bower does not provide this functionality, and an external plugin must be used to perform tasks that seem somewhat basic.

##### 4.6.4 Controller Inheritance

To better structure controllers within the application, a form of inheritance was used. Controllers join the various parts of the application by controlling the flow of data from the HTTP request to the relevant modules, before preparing the relevant response. A base controller class was created that all other controller classes inherit from. This class holds abstracted common functionality for each controller class.

The inheritance is created using a method in the `Base` controller class, called `extend()`. This uses the Underscore.js library's `extend()` function which will copy properties from a source object to a destination object. Overriding properties are resolved by preferentially using the source object's property.

```js
extend: function(child) {
	return _.extend({}, this, child);
},
```

This allows all child controller classes to use properties and methods defined in the parent class. An example is the `run()` method which will find the relevant view class and instantiate using data passed from the controller. The view is then rendered and sent in the response back to the user.

##### 4.6.5 Handlebars Views

In a Model-View-Controller (MVC) architecture views are a concept that represents the interface between the application and the user. In the vast majority of cases views take the form of an HTML page in web applications. For this application, views are created in a two-step process.

Firstly, data generated by the controller is sent to the relevant view generator. The view generator takes this data and uses it to populate the relevant template. As all views within the artefact are based on similar template population, a single Base view generator is used. The view generator selects the desired template, then uses the function `render()` from the Express framework to populate the template using a "view engine". This approach enables developers to use templates of any format, through simple configuration.

For this application the templating engine used is Handlebars. Handlebars templates use a HTML-like format, but with some extra syntax useful for templating. The following code snippet is taken from Handlebars' web site.

```html
<div class="entry">
	<h1>{{title}}</h1>
	<div class="body">
		{{body}}
	</div>
</div>
```

In this example, variables use the double curly brace syntax. These variables will be filled at render-time, with properties of the data object that share the same name. For example, if the data object contains a property called `title`, this will populate the header element in the rendered HTML page. The `render()` function takes this data object as an argument.

Handlebars also uses an iteration concept that is used to populate the search results list in the application. The syntax is shown in the following code snippet.

```html
{{#each articles}}
	<li class="checkbox">
		<input type="checkbox" name="doi" value="{{this.id}}" id="article-{{convert_doi_to_id this.id}}">	
		<h4><a href="http://dx.doi.org/{{id}}">{{this.title_display}}</a></h4>
		<label for="article-{{convert_doi_to_id this.id}}">
			<p class="authors">{{authors this.author_display}}</p>
			<p class="journal">{{this.journal}}</p>
			<p class="publication_date">Published on {{date_format this.publication_date}}</p>
		</label>
	</li>
{{/each}}
```

In this example each item in the `articles` array will be iterated through and a list element created for it. The iteration syntax takes a similar form to variable syntax; `{{#each}}`. The `this` keyword within each iteration refers to the current item in the array. Other similar expressions are used by Handlebars to create rich and flexible templates. For example, Handlebars includes so-called helpers for `if` statements that allow for conditional programming within the template. In addition, custom helpers can be written that allow the creation of arbitrary structures.

