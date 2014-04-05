#### 4.6 Web Application Implementation

* Web application
	* Search results
		* localStorage of selected items
			* Allows users to select, then go back to search to find more articles & previous selections are remembered
	* Application flow
		* How data is moved around in the application
		* The links that they click to get to permalink
		* _Diagram showing application flow_
		* Differences in flow for AJAX and non-AJAX users
	* Bower
		* Giant pain the rear
		* No capability for moving files
			* Have to use grunt plugin to move to desired location
			* "however Bower offers no options for where the files are placed within the project. This functionality is filled by the grunt-bower-task plugin"