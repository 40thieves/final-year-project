#### 3.4 Proposed Solution

The following section describes the proposed solution to the requirements laid out in section 3.3. This solution is to create a web application called Quo that will assist altmetrics researchers in studying how altmetrics change over time. The application will consist of four parts, that are described in detail in the subsections 3.4.3 - 3.4.6.

A web application will be created on the Node.js platform, written in the Javascript language. This application will consist of a website that allows users to search for articles that they wish to view altmetrics data for, visualise this data, and allow them to save it in a database.

The application will provide a search form, with various fields for article metadata. When the search is requested, the server will access the PLOS article search API, to find matching articles. A wrapper around this API will be created as a standalone module, that will abstract the implementation details of API. The module will return a JSON response containing a list of DOIs.

Using this list of DOIs, a request is made to the PLOS ALM (Article Level Metrics) API, to access the altmetrics data for the selected articles. Another wrapper around this API will be created, again as a standalone module. This module encapsulates the API details, and so can be reused in other projects. The module will return a JSON object containing the altmetrics data.

This data is then returned to the client, where it will be used to create the visualisation. The D3.js library will be used to create a bubble chart where each article is represented by a bubble, the bubble's size corresponds to the total number of citations received, and the x- and y- axis will correspond to altmetrics data sources. A forth "axis" will be represented by the current year, displayed on the background of the chart, and will allow users to control moving back and forward in time. The current values of the altmetric data sources will be calculated using the current year.

When the data is returned to the client, it will also be saved in the application's database. A Node.js module will be used to interact with the MongoDB database. Application data will be stored in a JSON-like format and associated with a unique identifier. Users can access the visualisation and data later using this unique identifier, through the use of a permalink.

##### 3.4.1 Web Application

The project will create a web application to meet the requirements described in 3.3. A web application was chosen as it provides a method for everyone to access the application, quickly, easily and without downloading any large programs.

As discussed in section 2.6, altmetrics are diverse and measure impact from outside science - areas that may not have access to expensive computer equipment. Therefore, a medium that can be accessed by all is suited to this project. No additional programs - other than a web browser, which often come pre-installed on many devices - are required to view a website. In addition, no restrictions are placed on the usage of the web, unlike much of the existing scholarly publishing field where paywalls regularly prevent access.

A website also provides a useful wrapper around the separate parts of the application, as described in sections 3.4.3 - 3.4.6. Without this wrapper, users would have to manually move data between the separate parts, leading to a much more complex and frustrating user experience.

##### 3.4.2 Node.js

Node.js was chosen as the server-side framework for this project, and the logic behind this choice is explained in this subsection. Node.js is designed for server-side applications, providing methods for receiving and responding to HTTP requests. Applications for this platform are written in Javascript, taking advantage of the extremely powerful Google V8 Javascript engine that Node.js is based on.

The fact that applications are written in Javascript is one of Node.js' main advantages, as it is the same language that is used on the client-side for web development. Developers do not lose context when switching between languages, such as with a more traditional set-up with PHP programs on the backend, and Javascript on the frontend. In the past, Javascript server-side frameworks have failed, however Node.js is built on the highly optimised V8 Javascript engine, meaning that programs can be executed much faster ("JavaScript Performance Rundown", 2008).

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

##### 3.4.3 Altmetrics Data Collection

Requirement 4 describes the need for altmetrics data collection from an external API. As discussed in this requirement, gathering new data for the system is out of scope, as it is time- and resource-heavy, and several different altmetrics providers offer APIs. There are several options available, although not all satisfy the requirements. The following table compares the different altmetrics data APIs:

* Table comparing features

From the table, it is clear that for this project, the most appropriate solution is the PLOS ALM (Article Level Metrics) API. Crucially, it provides historical metadata, about when altmetric citations occurred. This is obviously important as the system must be able to show altmetrics changing over time, and that cannot be achieved without this data. The PLOS ALM API also gives a more detailed breakdown of when citation occurred. Citation numbers are given every month (or every year) for each data source, whereas Altmetric.com's API only gives aggregate numbers for all data sources at set points after publication (each day in the first week, 1 month after, 3 months after, 6 months after and 1 year after). The rate limits when compared with the Altmetric.com API are much higher, beyond the greatest expected usage and so they do not need to be considered.

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

##### 3.4.4 Scholarly Article Search

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

Once the form is submitted, a request will be made to the application server with the attached search parameters. This request can take the form of a regular POST request, or can be captured, using Javascript, and sent using an AJAX request, meaning that the form follows the convention of progressive enhancement. The parameters are received by the server and passed to the search API wrapper.

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

* Visualisation
	* Bubble chart showing 2 potential altmetric data sources on the x and y axis, and number of citations as the size of the bubble
	* Bubbles animate as the data changes over time
		* Current year is shown on the background of the chart
		* Year can be controlled using hover states over the year
	* D3.js
		* (Should this be in the Implementation section?)
		* Powerful data analysis/visualisation tool
			* Allows manipulation of DOM elements with data
			* Designed for visualisations on the web
		* Provides an easy way to fetch data, manipulate it and create/manipulate DOM elements based on it
	* Influenced by Hans Rosling's "Health and Wealth of Nations" presentation
		* Shows data changing over time
			* Powerful temporal aspect - what I'm aiming for with this application
	* Javascript library
		* Encapsulated
		* Depends on D3
* Storage
	* Store collected altmetrics data for later
		* Prevents generating again - very complex process
	* Database
		* Data is in JSON format, so sensible to store in JSON format
		* Doesn't need to be complex - just storing the data against a key so it can be retrieved later
		* Nice to have: some level of structure/complexity
			* Data can be analysed later
	* (Again - is this in the Implementation section?)
		* MongoDB
			* JSON store
			* Lots of tools in Node for working with Node
		* Mongoose
			* ORM for Mongo
			* Creates some structure for the data
				* Allowing more structure to queries
	* Provide permalink for data
		* Can be bookmarked/saved/reused later very easily
		* No regeneration of data
* Git
	* Source control
	* Github
		* Issues
		* Hosting/backup
* Grunt
	* Nodemon
	* Bower
	* Sass
	* Mongo
	* Watch
* Sass
	* Bootstrap
* Table comparing altmetrics providers
	* Features of the APIs
		* History
	* How data is collected
		* Twitter
	* Rate limits
		* Altmetric.com rate limits are lower - "significantly higher if using an api key"
	* ImpactStory api (does it still exist?)
