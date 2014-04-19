#### 3.4 Proposed Solution

The following section describes the proposed solution to the requirements laid out in section 3.3. This solution is to create a web application called Quo that will assist altmetrics researchers in studying how altmetrics change over time. The application will consist of four parts, that are described in detail in the subsections 3.4.5 - 3.4.8.

A web application will be created on the Node.js platform, written in the JavaScript language. This application will consist of a website that allows users to search for articles that they wish to view altmetrics data for, visualise this data, and allow them to save it in a database.

The application will provide a search form, with various fields for article metadata. When the search is requested, the server will access the PLOS article search API, to find matching articles. A wrapper around this API will be created as a standalone module, that will abstract the implementation details of API. The module will return a JSON response containing a list of DOIs.

Using this list of DOIs, a request is made to the PLOS ALM (Article Level Metrics) API, to access the altmetrics data for the selected articles. Another wrapper around this API will be created, again as a standalone module. This module encapsulates the API details, and so can be reused in other projects. The module will return a JSON object containing the altmetrics data.

This data is then returned to the client, where it will be used to create the visualisation. The D3.js library will be used to create a bubble chart where each article is represented by a bubble, the bubble's size corresponds to the total number of citations received, and the x- and y- axis will correspond to altmetrics data sources. A forth "axis" will be represented by the current year, displayed on the background of the chart, and will allow users to control moving back and forward in time. The current values of the altmetric data sources will be calculated using the current year.

When the data is returned to the client, it will also be saved in the application's database. A Node.js module will be used to interact with the MongoDB database. Application data will be stored in a JSON-like format and associated with a unique identifier. Users can access the visualisation and data later using this unique identifier, through the use of a permalink.

##### 3.4.1 Web Application

The project will create a web application to meet the requirements described in 3.3. A web application was chosen as it provides a method for everyone to access the application, quickly, easily and without downloading any large programs.

As discussed in section 2.6, altmetrics are diverse and measure impact from outside science - areas that may not have access to expensive computer equipment. Therefore, a medium that can be accessed by all is suited to this project. No additional programs - other than a web browser, which often come pre-installed on many devices - are required to view a website. In addition, no restrictions are placed on the usage of the web, unlike much of the existing scholarly publishing field where paywalls regularly prevent access.

A website also provides a useful wrapper around the separate parts of the application, as described in sections 3.4.3 - 3.4.6. Without this wrapper, users would have to manually move data between the separate parts, leading to a much more complex and frustrating user experience.

##### 3.4.2 Node.js

Node.js was chosen as the server-side framework for this project, and the logic behind this choice is explained in this subsection. Node.js is designed for server-side applications, providing methods for receiving and responding to HTTP requests. Applications for this platform are written in JavaScript, taking advantage of the extremely powerful Google V8 JavaScript engine that Node.js is based on.

The fact that applications are written in JavaScript is one of Node.js' main advantages, as it is the same language that is used on the client-side for web development. Developers do not lose context when switching between languages, such as with a more traditional set-up with PHP programs on the backend, and JavaScript on the frontend. In the past, JavaScript server-side frameworks have failed, however Node.js is built on the highly optimised V8 JavaScript engine, meaning that programs can be executed much faster ("JavaScript Performance Rundown", 2008).

As a web application is to be constructed for the application, and HTTP server is required. This can be easily achieved using Node.js, creating an application that will listen for HTTP requests and sends responses. The following code snippet is taken from the Node.js website:

```js
var http = require('http');

http.createServer(function (req, res) {
	res.writeHead(200, {'Content-Type': 'text/plain'});
	res.end('Hello World\n');
}).listen(1337, '127.0.0.1');

console.log('Server running at http://127.0.0.1:1337/');
```

This would, when run using the Node.js command line interface, create a server that listens on port 1337 and on the 127.0.0.1 host that responds to requests with a plain text "Hello World".

Modules are core to the Node.js platform, allowing for quick and easy addition and use of code written by yourself or others. This even applies to core parts of Node.js - in the example above, the `http` module is used and so it has to be `require`d first. The very popular npm (Node Package Manager) application comes bundled with Node.js, which enables developers to publish modules for others to use.

The modular philosophy that Node.js holds is very beneficial for this project. As described above, there are several sections of the application that can be split from the main project and published as standalone modules for others to use. As discussed in section 2.6, the altmetrics community encourages transparency and reuse, principles that are reflected in the Node.js developer community. These packages can be reused by any Node.js developer easily, or perhaps by a researcher looking to study altmetrics.

The basic server described above only provides a low-level and overly-verbose approach, that is ill-suited for thie project. For a more powerful server, a third-party module will be used. The Express module is one of the most popular modules available on npm, currently listed as the fifth most depended upon package on npm's package registry. Express offers greater control over common web server tasks, such as routing requests, templating views and generally improving upon the built-in `http` module. Express will be used as the framework for the application, receiving requests, routing them to the correct controllers and generating responses. The following snippet is taken from the Express website: 

```js
var express = require('express');
var app = express();

app.get('/', function(req, res){
	res.send('hello world');
});

app.listen(3000);
```

This shows the Express equivalent to the previous snippet, however in a much cleaner manner. Requests to port 3000, on the index of the current host, will return a plain text "hello world". More routes can be added by registering further callbacks on the `app.get` function.

In the final application, Express will provide the backbone for the server, routing requests to the correct controllers, with any attached request parameters. These controllers will then call methods within the API wrapper modules, before generating responses from templates based on data returned from the modules. 

##### 3.4.3 Grunt

Grunt is a JavaScript task runner that provides automation for many useful functions. There are several uses for this within the project including running the Node.js server, starting the MongoDB database, and process Sass files. Grunt can be configured to run these tasks from the command line, and therefore a useful collection of administration scripts can be created. Many of these tasks are packages published on Node.js' package manager npm (see section 3.4.2), in the form of Grunt plugins. These plugins are designed to work with grunt, and only need to be configured to work with the current project. This configuration can be included in the project repository, to be shared with others so that they can use the administration scripts.

As described in section 3.4.2, Node.js has a web server that must be started from the command line. If changes are made to the application, the server must be restarted for changes to be reflected at runtime. This can become tedious in periods of heavy development. The third-party application, nodemon is design to help with this problem. nodemon will watch application files for changes and automatically restart the server. There is a Grunt plugin (called grunt-nodemon) that will perform this function, but also allow developers to add other Grunt tasks alongside nodemon. This will be used for the project to reduce development times.

Similarly, the database for the application must be started at boot. This is also achieved through Grunt, through the plugin, grunt-shell. This allows Grunt to call command line shell scripts, such as the one used to start the MongoDB database used for this project (see section 3.4.8). The command `mongod` will start the database, so grunt-shell is configured to run this command when the application is started.

The project will use the pre-processor Sass for creating stylesheets (see section 3.4.4). These must be converted to CSS before they are served to a user. This is achieved using Grunt. A plugin is used that will process the Sass files and convert them to CSS, called grunt-contrib-sass, which needs to be configured to set where the source files are and where the destination files will be. However, this plugin will need to be run every time a change is made to the source Sass files. The grunt-watch plugin will be used to watch for changes in the Sass files and automatically run the conversion scripts.

Finally, styles from the Twitter Bootstrap project will be used in this application (see section 3.4.4). These files do not need to be included in the repository, as they are merely included into the main CSS file without modification. Instead, another command line tool is used to download them from the project's host. Bower is "front-end" package manager, created by Twitter employees, for downloading open source JavaScript and CSS projects. This will be configured to download the Bootstrap files, When the Grunt "build" task is run, the plugin will call Bower to download the files and then move them to a configured directory.

##### 3.4.4 Styling

Requirement 6 states that the system must be "easy-to-use" and have "good visual design". To help improve this, the Twitter Bootstrap project will be used to provide a framework for the visual design. The project, created in CSS, allows developers to create stylesheets quickly, by providing a library of pre-constructed components. These components can be created by adding class attributes to HTML documents. In effect, Bootstrap creates a much more appealing default stylesheet for the web. For example, the following code snippet will create a button, that is shown in Figure 3.3.

```html
<button class="btn btn-primary">Primary</button>
```

![Figure 3.3: Button created using Bootstrap](../../src/img/figure3-3.png)

The styles provided by Bootstrap are useful, however, they do require adjustment. This can be a little difficult when using CSS, especially considering the challenges faced in a modern web environment. For example, many browsers use CSS pre-fixes for newer or experimental features. Adding these manually is time-consuming, and so CSS pre-processors have started to fill the gaps left by CSS.

Sass is one such preprocessor, which follows a very similar syntax to CSS but have to passed through a conversion script to be turned into CSS. No browser will interpret a Sass file. Sass offers several advantages over CSS, including variables, a nested syntax, and a function-like syntax called mixins. These allow a developer to spend less time doing functional work to focus on true design work. For example, changing colours in a CSS file can take time to find all instances of the colour, however, with Sass a variable can be used for each of these instances. Changing a colour is as simple as changing that variable.

Another benefit that Sass provides is automatic concatenation, through imports. CSS also uses a `import` syntax, however this is not considered best practice as it adds unnecessary HTTP requests. One Sass file can import another on the server-side, creating a single concatenated CSS file. This will be used in the project to spilt the stylesheet into smaller, more meaningful modules that are combined into a single CSS file when the "build" script is run (see section 3.4.3).

##### 3.4.5 Altmetrics Data Collection

Requirement 4 describes the need for altmetrics data collection from an external API. As discussed in this requirement, gathering new data for the system is out of scope, as it is time- and resource-heavy, and several different altmetrics providers offer APIs. There are several options available, although not all satisfy the requirements. The following table compares the different altmetrics data APIs:

* Table comparing altmetrics providers
	* Features of the APIs
		* History
	* How data is collected
		* Twitter
	* Rate limits
		* Altmetric.com rate limits are lower - "significantly higher if using an api key"
	* ImpactStory api (does it still exist?)
	* PLOS ALM API
		* Github issue about v3 which will include historical metadata for all data sources

From the table it is clear that, for this project, the most appropriate solution is the PLOS Article Level Metrics (ALM) API. Crucially, it provides historical metadata, about when altmetric citations occurred. This is obviously important as the system must be able to show altmetrics changing over time, and that cannot be achieved without this data. The PLOS ALM API also gives a more detailed breakdown of when citation occurred. Citation numbers are given every month (or every year) for each data source, whereas Altmetric.com's API only gives aggregate numbers for all data sources at set points after publication (each day in the first week, 1 month after, 3 months after, 6 months after and 1 year after). The rate limits when compared with the Altmetric.com API are much higher, beyond the greatest expected usage and so they do not need to be considered.

However the PLOS ALM API has a major drawback - only articles that are published with PLOS journals are tracked by the API. Altmetric data for articles published in other journals cannot be calculated since this data is not gathered. Despite this restriction, the system will still offer a very large sample of articles to users as the PLOS library is so large. In 2013, the PLOS One journal published 31500 articles ("Thanking Our Peer Reviewers", 2014).

The PLOS ALM API gives access to altmetrics data that PLOS collects regularly. The program that collects this data has been published under an Apache 2.0 License. Requests to the API will be made with a list of DOIs that represent the articles that the user has selected (see section 3.4.4). For this application, the option for historical metadata will be added to the request. Responses are returned in JSON format, the preferred format for this project, as XML format can be difficult to parse. An example request and response are shown below:

```
http://alm.plos.org/api/v3/articles?api_key={YOUR_API_KEY}&ids=10.1371/journal.pone.0035869&info=history

[
	{
		"doi": "10.1371/journal.pone.0035869",
		"title": "Research Blogs and the Discussion of Scholarly Information",
		"url": "http://www.plosone.org/article/info%3Adoi%2F10.1371%2Fjournal.pone.0035869",
		...
		"publication_date": "2012-05-11T07:00:00Z",
		...
		"views": 21454,
		"shares": 135,
		"bookmarks": 135,
		"citations": 9,
		"sources": [
			{
				"name": "citeulike",
				"display_name": "CiteULike",
				"events_url": "http://www.citeulike.org/doi/10.1371/journal.pone.0035869",
				"metrics": {
					"pdf": null,
					"html": null,
					"shares": 25,
					"groups": null,
					"comments": null,
					"likes": null,
					"citations": null,
					"total": 25
				},
				"update_date": "2014-03-05T07:55:58Z",
				...
				"by_day": [
					{
						"year": 2012,
						"month": 5,
						"day": 12,
						...
						"shares": 5,
						...
						"total": 5
					},
					...
				],
				"by_month": [
					{
						"year": 2012,
						"month": 5,
						...
						"shares": 17,
						...
						"total": 17
					},
					...
				],
				"by_year": [
					{
						"year": 2012,
						...
						"shares": 22,
						...
						"total": 22
					},
					...
				]
			},
		]
	}
]
```

This shows the generated response for the article (with DOI 10.1371/journal.pone.0035869). Firstly, the aggregate altmetrics, organised by category are given - 21454 views, 135 shares and bookmarks and 9 citations since publication. Then an array of altmetric data sources is given, each giving the total metrics for the given source. Finally, each data source reports the number of citations each day, month and year that it changes. So in this example, the article gained 5 CiteULike citations on 12th May 2012, given in the `by_day` array of the CiteULike object. Further documentation of the API is available from the API website ("Public Library of Science (PLOS)", n.d.).

It should also be noted that the request includes an API key, which can be obtained from PLOS ALM API website. This is used to control usage of the API, however there is no published rate limit. It is not expected that this limit will be reached, and so no mitigation is planned.

A wrapper will be created for around this API, encapsulating it's functionality into a single library that has a uniform interface. This abstracts implementation details of the API out of other parts of the codebase that do not need to know these details. The uniform interface will make it easier for queries to be made, as only the list of DOIs will need to be provided to create a response. Another advantage of this modular approach is that if changes are made to the API, then only the wrapper will be affected.

The wrapper will accept a single DOI (as a string) or a list of DOIs (as an array). Optionally, configuration can be set on the wrapper, for example, setting the option to request the historical metadata as described above. These are then validated, before the request string is built using the input DOI list. The request is sent to the API, and the response is parsed to ensure no errors have occurred. The wrapper will then return this parsed response.

In above sections, the ability for standalone Node.js packages to be published was described. The PLOS ALM API wrapper will be published in this manner. The package will be available on the module registry, npm, allowing third-party developers to download and use the package in their own systems, encouraging further altmetrics study. The module will be released under a MIT license, giving permission for others to not only use the package but to make changes and contribute back to the source, improving the project. This shows the great benefit of Node.js' modularity and altmetrics culture of openness.

##### 3.4.6 Scholarly Article Search

Requirement 3 describes the need for a scholarly article search. The proposed solution to this problem is in two parts: the search form, and the PLOS Search API wrapper. The search form will provide the user with a mechanism for interacting with the system to give their search parameters. The search wrapper will execute the search, using these parameters, to the PLOS Search API which will find matching article's in it's archive and return a list of matching articles.

The application will serve the search form as a static web page, containing a form. The form is a relatively simple way for users to interact with the system to find "articles of interest", or articles which they wish to receive altmetrics data for. The form will provide input fields for the following, expanded from requirement 3:

| Field            | Description                                                  |
| ---------------- | ------------------------------------------------------------ |
| Keyword          | Matches all text in an article                               |
| Author           | Matches article's author name                                |
| Journal          | Matches article's journal name                               |
| Subject area     | Matches an article's subject area (taken from PLOS taxonomy) |
| Publication date | Matches article's publication date                           |
| DOI              | Matches an article's Digital Object Identifier               |

Once the form is submitted, a request will be made to the application server with the attached search parameters. This request can take the form of a regular POST request, or can be captured, using JavaScript, and sent using an AJAX request, meaning that the form follows the convention of progressive enhancement. The parameters are received by the server and passed to the search API wrapper.

The search API wrapper will perform a search for articles that match these search parameters. Unfortunately, there are very few reliable sources for searchable published scholarly material. Initially, the CrossRef API was considered as it provides search for a large number of publishers. Search parameters for all of the fields required for the form are provided by this API. However, and as discussed in section 3.4.3, only articles published by PLOS can be processed by the PLOS ALM API wrapper, and therefore the search part of the system cannot return articles published by other journals. This drawback means that the sensible choice for the search API is the PLOS Search API, as this will only search for PLOS articles, and so solves the problem of users searching for non-PLOS articles. For this reason, the PLOS Search API was chosen as the search API for use in the application.

The PLOS Search API allows access to PLOS' internal search engine, based on the Apache Lucene project, Solr. Accepted search parameters meet those required by requirement 3. Responses are returned in JSON format and an example request and response are shown below:

```
GET http://api.plos.org/search?q="altmetrics"&wt=json&api_key={YOUR_API_KEY}

response: {
	numFound: 13,
	start: 0,
	maxScore: 1.8082654,
	docs: [
		{
			id: "10.1371/journal.pone.0064841",
			journal: "PLoS ONE",
			eissn: "1932-6203",
			publication_date: "2013-05-28T00:00:00Z",
			article_type: "Research Article",
			author_display: [
				"Mike Thelwall",
				"Stefanie Haustein",
				"Vincent Larivière",
				"Cassidy R. Sugimoto"
			],
			abstract: [
				...
			],
			title_display: "Do Altmetrics Work? Twitter and Ten Other Social Web Services",
			score: 1.8082654
		},
		...
	]
}
```

This response shows that a simple search for the keyword "altmetrics" will return 13 articles, including each article's DOI. These DOIs can then be used to find their altmetrics data.

Again, it should be noted that the request includes an API key, which can be obtained from the PLOS Search API website. However, unlike the previous API, a rate limit is enforced. If an API key exceeds 7200 requests a day then the API key will be blocked. However, it is not expected that this limit will be reached.

As described in the introduction for this section, a wrapper around the API will be created. This is to abstract details of the API away from other parts of the codebase, encapsulating it's functionality into one module. This will also make querying the API easier, as a uniform interface is provided. Responses will be parsed by the wrapper, making returned data more consistent.

The wrapper will accept search parameters as either a string or an object. A string will indicate that a keyword search is to be performed, while an object will contain key/value pairs indicating which field is to be searched with which value. These parameters are then validated, to ensure that errors made by the user will not produce invalid responses from the API. For example, any search parameter that is not used by the PLOS Search API will raise an error, as this may return an error response from the API. The wrapper will then construct a request string using the validated parameters, and send the response to the API. The response received from the API will be parsed before it is returned to the wider system.

Similarly to the PLOS ALM API wrapper described in section 3.4.3, the wrapper will be published separately as a package. The module will be released under a MIT license, a open source license that satisfies requirement 7. This means that others can download the module from npm, and use within their systems without having to obtain permission. This, again, shows the great benefit of Node.js' modular philosophy.

##### 3.4.7 Visualisation

Requirements 1 and 2 state the need for a visualisation of altmetrics data and how it changes over time. The solution for these requirements will be a D3.js "bubble" chart that will show a visualisation of multiple articles with axes for two altmetrics data sources, an axis for the number of citations and an axis for time.

The chart will show two altmetric data sources on the x- and y-axis, allowing the user to switch between data sources of their choice. Articles are represented by a "bubble" on the chart that is centred according to the values of the selected data sources. The size of the bubble represents the total number of traditional scholarly citations the article has received. An example is shown below:

![Figure 3.1: Example bubble chart](../../src/img/figure3-1.png)

This will meet some of the requirements described in requirement 1, as it allows the user to compare multiple articles against each other. Two altmetric data sources are shown on the x- and y-axis, and the number of scholarly citations is shown in the size of the bubble. Users can see if a large number of views (as in this example) leads to a large number of citations, and thus greater impact. Requirement 1 also states that the altmetric data sources must be able to change to suit the user, and so a system will be created that will allow the user to switch the x- and y-axis between the available data sources. Finally, to meet all of requirement 1, there must be some interface for associated each bubble to the article it represents. As the chart will be built using JavaScript and HTML's SVG specification, it is relatively easy to add a "tooltip" that will appear when the user hovers over the bubble with the mouse. This will show the article's title, and current values for the selected data sources.

To meet requirement 2, the chart must be able to respond to users input and change the data on the chart to represent a different time period. Initially, creating a simple line graph that showed each data source against time was considered. However some tools already exist for this purpose, and comparison between data sources is difficult - a key component in requirement 1. A graph that allows comparison of data sources, while also showing changes over time is required. This area of the design was heavily influenced by Hans Rosling's "Heath & Wealth of Nations" presentation ("Hans Rosling's 200 Countries, 200 Years, 4 Minutes", 2010). In this presentation Rosling shows the life expectancy and income of 200 countries over 200 years. By animating through each year and changing the position of the bubble based on data for that year, changes at certain periods of time become evident that would have gone unseen except under close scrutiny. It is this comparison over changes over time that is missing from other visualisation styles that were considered. The concept will be adopted for the project, by animating the bubbles to move as the time changes. In addition to this animation, the user will also be able to control the passage of time manually using the mouse.

![Figure 3.2: Animated bubble chart](../../src/img/figure3-2.png)

Much like the wrappers described in sections 3.4.3 and 3.4.4, the visualisation code will be encapsulated into a module. A small library will be created that will create the visualisation, once constructed during the system's runtime. Prototypical inheritance will be used to structure the library, giving a object-orientated style "class" that has a constructor, class variables and public or private methods. The library will accept basic options in it's constructor, which will set up where in the DOM the chart is to be added, and where the data can be accessed. The library will then build the chart and append it into the DOM.

The library will be self-contained, following the best practice of not polluting the global namespace in the browser, although it will depend on D3.js. Further, the library will follow the asynchronous module definition (AMD) convention, allowing clients to asynchronously download the library. This is often done using the popular Require.js library.

D3.js is a well known JavaScript library used primarily for creating charts and graphs. It was chosen over other similar libraries for this application for several reasons.

Other potential libraries that were considered include Google Charts, and Chart.js. However, both of these libraries are focussed on instantiating a pre-made chart - line graphs, bar charts, pie charts - with a dataset. This approach is more suited to projects with a more traditional visualisation style, where a simple chart is required, and can be quickly created without overly verbose code. D3.js takes a more flexible approach, stating on it's website "D3 is not a monolithic framework that seeks to provide every conceivable feature" ("D3.js - Data-Driven Documents", n.d.). This is much more appropriate for this project, where a custom style of chart is created specifically for this purpose.

D3.js is based on manipulating Scalable Vector Graphs (SVG) elements. This relatively modern technique is based on XML, and thus does not rely on raster images, meaning that graphics can be scaled without loss of resolution. This is very powerful for usage on the web, where images can be scaled down for smaller screen devices and scaled up for larger screen devices. The World Wide Web Consortium (W3C) standardised a specification for SVG in 2011, and is now supported in a majority of browsers. The D3.js library provides a cleaner, less verbose interface to the SVG specification. This makes manipulation of SVG elements in the browser relatively simple.

As the library is based on transformation of DOM elements, not on an internal representation of the shapes that make the chart, it can be considered more "future friendly". As the web changes, and more standards are added, tools that focus on existing standards will require less change. D3.js is able to adapt to potential additions to the web specification, for example, more advanced CSS properties or new HTML elements.

One of D3.js' most powerful features is it's concept of data "joins". This functionality allows for actions that are to be taken when new data is added to the visualisation, or existing data is removed. This is extremely powerful on the web, which is increasingly dominated by asynchronous JavaScript applications that can fetch new data on the fly. While this functionality is unused for this application there is scope for future work that potentially use it, further validating the choice of D3.js for this system.

Finally, D3.js provides a large amount of useful documentation and example applications that make it easy to learn. As described above, the library does not provide many built-in charts, and leaves much of these decisions to the developer. Therefore, a large gallery of examples helps to inspire as well as learn from.

##### 3.4.8 Storage

Requirement 5 describes the need for storing altmetrics data for faster and more efficient retrieval and for creating a permalink. Data from the altmetrics data collection process will be in JSON format, and so it is sensible to also store this data in JSON format. The storage process does not require a large degree of complexity, as the data only needs to be stored against a unique key so that it can be retrieved later. A key/value pair database therefore is mostly appropriate for this use case. However, future revisions of the project may require a deeper understanding of the data stored in the database. For this reason, it may be useful to have more structure to data stored in the database.

Data will be stored in the database after it is returned from the altmetrics API wrapper described in section 3.4.3. The application flow starts with the user submitting their search, which will call the search wrapper, returning a list of DOIs. This list of DOIs is passed to the altmetrics API wrapper, the results of which will then be stored in the database. The database will generate a unique key for this data, which is used to create a permalink. The user is then redirected to this link, and the visualisation is generated.

MongoDB was chosen for the application as it satisfies these requirements. Documents in MongoDB are stored in a JSON-like format, that is associated to a unique key. This is much less complex than a relational database, where a schema would have to be constructed. There are many tools for Node.js available for use with MongoDB. The most prominent is the `mongodb` library on npm. This provides a simple driver for connecting to MonogoDB, querying and updating the store. However, this library does not offer functionality for creating structure in the database. Another potentially useful library is the popular Mongoose library, that is modelled as a Object Relation Map (ORM) for MongoDB. This would give more structure to the database, while still storing data in the JSON-like format, and will likely give the best results.

##### 3.4.9 Source Control

The source code of the application will be stored in a git repository, a source control system designed to assist developers write code. Code for the project will be hosted on GitHub, a service that offers code hosting for git repositories. By combining these, the project will have powerful tools for controlling, debugging and distributing code.

git allows developers to track and control changes to a codebase, by "committing" updates regularly. This is achieved by a system of cryptographic hashes generated using the source code itself. These hashes can be used as identifiers for stages in the code which can be moved between easily. This has the added benefit of helping to debug problematic code, by identifying when an error was introduced. "Branches" are concept within git that enable changes to be made in a semi-temporary area where developers can experiment with the codebase without fear of permanently breaking anything. Experiments can be merged into the main branch if found to be successful.

GitHub is a service that can accept updates, allowing code to hosted on their servers. This also gives the benefit of creating a backup. GitHub provides a easy-to-use Graphical User Interface (GUI) to git that is helpful under certain circumstances. The service also provides an issue tracker, for saving bugs and new ideas.

Updates can be shared using git's protocol with others who can make further changes. This means that code published under a permissive open source license can be easily edited by others before changes are submitted back to the original author. This structure enables much faster and easier open source development, as described in requirement 7.

##### 3.4.10 Testing

For the application to be considered to be successful, it must contain as few bugs as possible. Section 3.2 describes the project's design methodology of Test Driven Development (TDD). For this to take place, a testing framework must be included in the project. This will provide an environment for tests to be run and tested against. For this project, two libraries will be used; Mocha for running tests; and Chai for providing assertions.

Mocha is a test runner for Node.js, that enables developers to create tests that will be in it's environment. If an error is thrown by the test, then Mocha will report this, either on the command line, or in a browser. The example below shows a basic Mocha test (that will pass):

```js
var expect = require('chai').expect;
describe('Array', function() {
	describe('#indexOf()', function({
		it('should return -1 when the value is not present', function() {
			expect([1,2,3].indexOf(5)).to.equal(-1);
			expect([1,2,3].indexOf(0)).to.equal(-1);
		});
	});
});
```

Chai is an assertion library, again for Node.js, that provides assertions that expressions can be tested against. If the assertion fails an error will be thrown. The examples below show some of the Chai assertions:

```js
expect(foo).to.not.equal('bar');
expect({ foo: 'baz' }).to.have.property('foo').and.not.equal('bar');
expect('test').to.be.a('string');
expect(foo).to.exist;
```

These libraries were chose because of their popularity, flexibility and ease of use. Mocha is widely used among the Node.js developer community. It is popular due to it's rich feature list, and flexibility. Mocha allows developers to choose which assertion library they wish to test with. This is unlike the also popular Jasmine library, which provides a complete all-in-one framework for tests. The Chai library was chosen for it's readable tests. As is shown in the above example, Chai tests can be very similar to sentences, an improvement over the "assert" style of other libraries.

