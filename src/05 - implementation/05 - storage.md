#### 4.5 Storage Implementation

This section discusses the implementation of the database within the application. Section 3.4.8 describes the proposed solution, a MongoDB database to store data returned by the altmetrics data collection module. This data will be stored against a unique key, so that it can be retrieved without having to fetch the data for a second time.

##### 4.5.1 MongoDB

As discussed in section 3.4.8, MongoDB is well suited to this application, due to its JSON-like document format. The application generates JSON data, so it is sensible to storage this in a JSON-like manner. MongoDB is also characterised by its relative lack of structure, similar to other so-called NoSQL databases. This is suited to the application, due to the sparsity of data that was discussed in section 4.4.3. A SQL database would struggle to define an adequate structure for data that can often be missing values. In addition, requirement 5 (see section 3.3) does not describe an overly-complex datastore. Data is merely associated with a unique key for retrieval later. This is very well suited to MongoDB.

MongoDB is easy to install on many operating systems, with several implementations for package managers, such as `apt-get` or `homebrew`. Unlike SQL databases, there are relatively few good tools for administration of MongoDB databases. The best MacOS client that was found is called Robomongo, and unfortunately suffers from some usability issues. To interoperate with Node.js, one of several packages from npm can be used. The most popular is the official driver `mongodb`, which provides fully featured API for accessing MongoDB data.

##### 4.5.2 Mongoose

The previous section discussed the requirement's need for a lack of complexity and the suitability of MongoDB for this task. However, it was discovered some structure could be implemented for the MongoDB database in a non-time-consuming manner. The Mongoose library for Node.js was found to provide a much cleaner interface to MongoDB and also allows the creation of data structures within the database.

Mongoose is an object model map, the equivalent of an object relational map (ORM) implementations of which have been used for many years in other languages such as PHP and Ruby. Mongoose allows the creation of "schemas" within the database that have specified properties, unlike vanilla MongoDB where documents can have any number of properties. These are not unlike a table within a relational database, meaning that Mongoose can act as a "halfway house" between NoSQL and relational databases. These models provide a greater structure for the data, allowing for future work to expand on analysis of stored data.

A nested structure of schemas was created for the application. At the top level is the report schema. The entire data entity that is returned from the data collection API wrapper within an instance of the report schema. The following code snippet shows the report schema.

```js
var ReportSchema = mongoose.Schema({
	articles: [ArticleSchema],
	createdAt: {
		type: Date,
		default: Date.now
	}
});
```

Although not shown in this snippet, Mongoose will automatically generate a `_id` property for each schema that is used as the unique key. This key is what is used later to generate the permalink to the resource. A creation date is associated with each report, that will be automatically filled with the current time when a new instance is created (called a "model" within Mongoose). Finally, an array of articles will be associated with a report. These articles follow an article schema. This schema holds the article metadata as shown in the following code snippet.

```js
var ArticleSchema = mongoose.Schema({
	doi: String,
	title: String,
	url: String,
	mendeley: String,
	pmid: String,
	pmcid: String,
	publication_date: Date,
	update_date: Date,
	views: Number,
	shares: Number,
	bookmarks: Number,
	citations: Number,
	sources: [SourceSchema]
});
```

This shows the structuring available in Mongoose. The article's publication date will be stored as a JavaScript `Date` object, while the total number of views the article receives will be stored as a number (as opposed to a string representation of the number). The sources property holds an array of data sources associated with an article. This source schema is the final layer in the application's database structure. The following code snippet shows the source schema.

```js
var SourceSchema = mongoose.Schema({
	name: String,
	display_name: String,
	events_url: String,
	metrics: Object,
	update_date: Date,
	by_day: Array,
	by_month: Array,
	by_year: Array,
	histories: Array
});
```

This added structure may be useful for future work, as a more detailed analysis of data can be performed. Without this schema, more complex queries would have to be created to provide the same level of analysis.

