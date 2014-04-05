#### 4.4 Visualisation Implementation

* Visualisation
	* Data is sparse (lots of null values)
		* Some data sources don't have historical metadata
			* May have improved - was there a blog post (or github issue) about how they're going to improve it?
		* Interpolation of data is required
	* PLOS doesn't provide historical metadata for citations
		* Only total numbers
		* Good for validation anyway
	* D3.js
		* Un-minified version didn't work correctly
			* Unicode problems? Had something to do with pi (π) symbols
	* D3-tip library
		* Tooltips
	* Data filtered to convert data sources array to associative object map
		* [{}, {}] -> {twitter: {}, facebook: {}}
		* Easier to access data source object (just use name), not dependent on array order
	* Switching data sources
		* Selected data sources are passed to constructor
			* Sets up accessor functions that access the data source's data
	* AlmChart library
		* AMD