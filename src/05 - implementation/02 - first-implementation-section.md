#### Bar

Something, something, implementation.


* Visualisation
	* Data is sparse (lots of null values)
	* PLOS doesn't provide historical metadata for citations
		* Only total numbers
		* Good for validation anyway
	* D3.js
		* Un-minified version didn't work correctly
			* Unicode problems? Had something to do with pi (π) symbols
	* AlmChart library
		* AMD
* Storage
	* MongoDB
		* Lack of schema is good
			* Data is sparse (lots of null values), which could be problematic with relational databases
				* Reduced complexity is good
	* Mongoose
		* ORM for Mongo
		* Creates some structure for the data
			* Allowing more structure to queries
* Application flow
	* How data is moved around in the application
	* The links that they click to get to permalink
	* _Diagram showing application flow_
	* Differences in flow for AJAX and non-AJAX users
* Testing
	* What the different describe blocks in the tests mean