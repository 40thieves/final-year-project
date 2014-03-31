#### 3.3 Requirements

The main purpose of ...

* System requirements
	* Allow users to find scholarly articles to get altmetrics data for
		* Must be able to find articles from a large, representative sample
		* Application has form for input of search parameters
	* Visualises how altmetrics change over time
		* Can show multiple articles at once
		* Easy to control the changes over time
			* Easy to move time back and forward
		* Comparison/validation against citation counts
			* Enables users to see how changes in altmetric data sources affect the overall citation count
		* Can switch between various altmetrics data sources
		* Be able to show article details
	* Altmetrics data collated from various altmetrics data sources
		* Must include historical data
			* When altmetric citations occurred
			* Split at least yearly - the citation numbers are recorded every year, or sooner (i.e. every month/day)
		* Altmetrics data for a representative sample of articles
		* Should be relatively fast to collect data
			* Don't regenerate every time
	* Allow users to save their results for later
		* Can reuse/bookmark
			* Allows users to demonstrate altmetrics impact to assessors
		* Permalink
		* Store altmetrics data in a database
			* Don't have to regenerate every time
			* Can handle JSON data well
	* Easy to use
		* Easily understandable by people not familiar with technical altmetrics terms
	* Open source
		* Methods available under a open, permissive license
		* Allows users to inspect methods of generation/interpretation