#### 4.5 Storage Implementation

* Storage
	* MongoDB
		* Lack of schema is good
			* Data is sparse (lots of null values), which could be problematic with relational databases
				* Reduced complexity is good
	* Mongoose
		* ORM for Mongo
		* Creates some structure for the data
			* Allowing more structure to queries
		* Half-way house to relational database