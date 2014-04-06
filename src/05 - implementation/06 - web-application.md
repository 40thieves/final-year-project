#### 4.6 Web Application Implementation

This section will discuss the implementation of the web application that was described in sections 3.4.1 - 3.4.4. The web application will combine the individual modules described in sections 4.2 - 4.5, providing a easy to use wrapper for the user. Node.js will provide the infrastructure for the application, running an Express server to serve a search form and results page, and the visualisation once altmetrics data has been collected.

##### 4.6.1 Application Flow

Figure 4.2 shows the flow of the application. Users are presented with the home page, containing the search form. When this is submitted, the search parameters are sent to the search API wrapper. Articles returned from the API are then presented to the user in a list. Users can review this list and select articles that they wish to view altmetrics data for. This list of selected articles is sent to the altmetrics data collection API wrapper. Data returned from this wrapper is stored in the database. This generates a unique key for the data, which is used to create a permalink for this dataset. Users are then redirected to the permalink where the visualisation is shown.

* Application flow
	* How data is moved around in the application
	* The links that they click to get to permalink
	* _Diagram showing application flow_
	* Differences in flow for AJAX and non-AJAX users

##### 4.6.2 Search Results Page

As described in the previous section, users are presented with a list of articles that match their search query. They can then select articles by clicking the checkboxes. This is an acceptable interface for users to designate a list of articles that they wish to view altmetrics data for. However, it became apparent that this user interaction is problematic. If, for example, a user wished to select articles from one author, then go back and select further articles from another author, their original selections were removed and forgotten by the system. Clearly, a mechanism for saving selections was required.

This mechanism is provided by the relatively new Javascript API, localStorage. localStorage, part of the Web Storage specification ("Web Storage", n.d.), enables developers to store key/value pairs in the browser itself. The named key must be a string, and the value can be of any type, however it must be converted to a string - usually by converting to JSON - before storage. This can then be parsed to retrieve the value when needed. Storage is segmented so that only data stored on a given domain can be retrieved by the same domain. This prevents pollution by other web sites.

This extremely simple storage mechanism 

* Web application
	* Search results
		* localStorage of selected items
			* Allows users to select, then go back to search to find more articles & previous selections are remembered
	* Bower
		* Giant pain the rear
		* No capability for moving files
			* Have to use grunt plugin to move to desired location
			* "however Bower offers no options for where the files are placed within the project. This functionality is filled by the grunt-bower-task plugin"