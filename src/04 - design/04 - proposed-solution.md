#### 3.4 Proposed Solution

<!-- ... at a high level, then create subsections that go into detail -->

* Four main parts
	* Search
	* Data collection/generation
	* Visualisation
	* Storage
* Web application
	* Accessible online
		* No downloads
		* Available to all
	* Wraps around search, visualisation & data retrieval parts
* Node.js
	* (Is this part of Implementation section?)
	* An interesting new technology
		* A chance to try something new
	* One language for client & server
	* Modular approach
		* Suited the modular design - 4 parts
* Search
	* Search for articles to generate altmetrics for (target articles?)
	* Form for input
	* Use PLOS Search API
		* Can only search for PLOS articles
			* Large sample
			* Maybe biased?
				* Open access
				* Free - higher metrics
			* However because they're all open access, would be normalised/accounted for
	* Write a wrapper around the API
		* Makes it easy to query
		* Abstracts the details of the implementation
		* Modular approach
* Data collection/generation
	* Collecting data myself is too complex/unnecessary
		* Out of scope
		* Already provided by APIs
		* Time and resource heavy
	* PLOS ALM API
		* Provides altmetrics data
		* Includes historic data
			* When citation occurred
			* Taken every month/year
	* Write a wrapper around the API
		* Makes it easy to query
		* Abstracts the details of the implementation
		* Modular approach
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