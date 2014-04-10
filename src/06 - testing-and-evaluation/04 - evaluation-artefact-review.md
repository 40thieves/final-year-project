##### 5.3.2 Artefact Review

This section will evaluate the application and review the important issues highlighted in during the implementation phase. Each major section from chapter 4 will be evaluated to review whether good decisions were made in this phase.

###### 5.3.2.1 Altmetrics Data Collection API Module

The data collection API wrapper (see section 4.2), was implemented as a package for the Node.js platform. The package acts as a wrapper around the PLOS Article Level Metrics (ALM) API, providing a uniform interface to the API and encapsulating logic within itself.

The best practices for creating a Node.js package were followed when creating the data collection wrapper. This produces a good internal structure for the module, where all related logic is encapsulated within the module. Prototypical inheritance was used to create a class with methods and properties. Instantiating this class would set up the module, and listening to events emitted from the module would return results or errors. Handling the creation and sending of the request, parsing response and returning results is performed within the class. This results in a cleaner, more readable and less verbose codebase, as this logic does not need to be recreated multiple times. This has additional benefits - an update to the API will only affect code within this package. This also means that others can reuse the package without making changes to the core. The package can be found on the Node.js package manager, npm, under the name nodealm. Ultimately, the decision to adopt a prototypical inheritance style structure benefited the project as the code was cleaner and the ability to redistribute the package fits with the goals of the project.

When following these best practices, a lot was learned about creating modules with this structure. This involved completely rewriting the data collection wrapper with a improved implementation, after it was realised that using a constructor to create configuration for the rest of the module was useful, and that a cleaner callback could be constructed. An event based structure was adopted (see section 4.2.2), implementing these solutions. The prototypical inheritance style also meant that deeper understanding of the `this` keyword was acquired, as well as the important `bind()` and `call()` functions.

An area of improvement that could be tackled in future versions of the package would be validation of input. The module accepts a list of Digital Object Identifiers (DOIs) that represent articles for which data is to be found. The application currently does not validate this input to check if the list contains invalid DOIs. If this was implemented, an error would be thrown, to be handled by the developer.

As discussed in section 4.2.3, during the implementation of the wrapper, it was discovered that the API can produce errors in certain circumstances. If querying the API for many articles including their historical metadata, a error response will be returned, however this is sometimes inconsistent. A workaround was found that involves appending a list of required data sources to the request. This would consistently produce the desired results, however it may somewhat inconsiderate to the maintainers of the API. Producing a large amount of data, such as in this request, may tax the servers and increase expenses for the providers. To mitigate this, a bug report was issued to the API maintainers, to make them aware of the situation. In addition, the problem may not be relevant, as it not expected that the application will receive heavy usage, and so few of these requests will be sent. Despite these problems, the PLOS ALM API is the best choice for the application, as was assessed in section 3.4.5. These findings mean that no other provider currently gives historical metadata of the type that is used by the visualisation, and therefore switching based on a inconsistent bug that can be easily worked around is a foolish choice.

Overall, the implementation of this module reaches expectations. Desired functionality is provided in a clean, encapsulated manner. This allows the package to be extracted from the main application for reuse in other projects. The package meets two goals of the project - to be able to collect historical altmetrics data for visualisation, and to provide tools for researchers for examining the temporal aspect of altmetrics.

###### 5.3.2.2 Search API Module

The search module (see section 4.2), was implemented as a Node.js platform, in the same way as the data collection module evaluated above. The module provides an interface to the PLOS Search API, giving a uniform set of methods for searching for articles.

The search wrapper shares many characteristics with the data collection wrapper, as the functionality provided by both is to some extent similar. The search module follows best practices, uses a prototypical inheritance approach to creating a class and uses a event based structure. The major difference is the search module will perform validation on the search parameters that are input to the module. The validation checks to ensure that no unknown parameters are passed in the request to the API. As discussed in section 4.2, this means that errors can be thrown by the constructor, and thus must be wrapped in a try/catch block.

As discussed in section 3.4.6, the PLOS Search API was chosen due to the decision to use the PLOS ALM API for altmetrics data collection. The latter API can only provide altmetrics data for articles in PLOS journals, therefore a search engine that will only provide results from PLOS journals would seem to be a practical choice.

There are some further arguments against this decision. All articles published in PLOS journals are under a open access model - the article is free for anyone to access and read. This may introduce some bias into analysis of altmetrics data, as open access articles often show much higher altmetrics performance than closed models. However, previously, several respected altmetrics studies have solely used open access articles. In addition, as all articles visualised by the system will be open access, this bias can be accounted for in analysis. 

Another argument against the PLOS Search API is that it does not correctly implement the HTTP protocol. A key component of this protocol is that responses are given codes that identify the type of response. For example, successful responses have the status code "200 OK". There are several common error responses, including "404 Not Found" and "400 Bad Request", which indicate that the requested resource cannot be found by the server and that the request is badly constructed respectively. These should be used if an error processing the response is raised. Unfortunately, the Search API does not this, returning a 200 response even if an error is generated. The only way to distinguish successful and error responses is the response body, which contains an error message. This means that message bodies must be parsed by the search module to check for potential errors.

Despite these criticisms, the PLOS Search API still reaches the requirements of the application - PLOS journals publish a large volume of papers, providing a large sample, and search results include article DOIs (see section 5.3.1, requirement 3). For this reason, the decision to use the Search API can be considered the most optimal. This, along with the well structured and efficient wrapper, mean that the implementation of this functionality reaches expectations. Similar to the data collection module, this module meets two goals of the project - to be able to collect altmetrics data for visualisation, and to be able to release tools for researchers to further investigate altmetrics.

###### 5.3.2.3 Visualisation

The visualisation of altmetrics data was implemented as a library that depends on the D3.js framework (see section 4.4). The library creates a bubble chart using SVG elements, with 4 axes, each article is represented by a circle. The x- and y-axes represent selected altmetric data sources that can be controlled by the user. For example, if the Twitter data source is selected, then it's distance along an axis represents how many citations the article has received on Twitter. The size of the circle represents the total number of scholarly citations the article has received. Finally, the fourth axis represents the current year. As this axis advances, the data used for the x- and y-axes is transformed based on the current year. Therefore, as time advances, the bubbles on the graph move based on how much they changed over time.

This part of the codebase was also developed using a prototypical inheritance architecture. An AlmChart class was created that encapsulates the methods required to set up, construct and animate the chart. The library uses the Asynchronous Module Definition (AMD) to define itself as a module. This means that the library can be redistributed as a standalone Javascript library, for reuse in other projects. This approach is similar to that taken by the data collection and search modules, except without the module functionality provided by Node.js, which is instead provided by the AMD structure.

Results of the usability test found that the visualisation is easy to use. This is a key factor in the successful implementation, as the underlying altmetrics data is voluminous and complex. Creating a chart that can draw knowledge from this data is somewhat difficult. Therefore, test results that show that the visualisation is easy to use and understand can be considered a success.

Section 5.2.3 details the acceptance test that was performed on the application. This test found that altmetrics researchers criticised the use of an assumed interpolation model in the visualisation. This allowed for calculation of data values between given years, smoothing the animation. This means that the visualisation has much reduced value to altmetrics researchers. With hindsight, it seems obvious that implementing such a model into scientific software without research was a poor decision. 

However, there are some positives to take from this part of the project. The acceptance test found that the comparison aspect of the visualisation is still useful. Being able to quickly compare altmetric data sources has value to researchers. By removing the smooth animation, it would be possible to retain this aspect of the visualisation. Alternatively, the project could implement a interpolation model that better represents altmetrics research. Unfortunately, this research is not yet complete - of the main goals of this project is to encourage this area of study. When complete, the application could easily be updated to use this model as it is released under an open source license.

Overall, evaluation of the visualisation implementation finds mixed results. The usability test found that there is some underlying value in the visualisation, that must be brought forward. Altmetrics visualisations are still rare, especially one that can be dynamically changed for comparison purposes. As discussed, the interpolation issue drastically reduces the usefulness of the application, however there are potential strategies to mitigate this. The open source nature of the project means that making such changes is relatively easy.

###### 5.3.2.4 Storage

After altmetrics data is retrieved by the data collection module, it is stored in a MongoDB database (see section 4.5). The database stores documents in a JSON-like format, very similar to the format received by the data collection module. The application enforces a lightweight structure for the database, using the Mongoose library. When the data is stored, a unique key is generated, which is used to create a permalink for the data. This can be visited later by the user without having to retrieve data for a second time.

As discussed in section 4.5, the proposed solution for this implementation did not require a structured database. The requirement states that data must simply be stored against a unique key for retrieval later. A defined structure is not required, as data would be treated as a single entity. However, this changed as the implementation progressed. The Mongoose library was found to provide a good interface to MongoDB, while also allowing the creation of a simple database schema. This may prove useful in future development of the project, as aspects of the data can be queried separately, as opposed to key/value store. This implementation choice can be considered a good, forward thinking decision, as it meets the requirements of the system while allowing for further extension.

###### 5.3.2.5 Web Application

The parts of the application described above need to be combined and controlled to provide an interface between them and the user. This is provided by the web application, controlling the flow of data between the individual modules and the web pages that allow the user to interact with the system. The implementation is constructed on the Node.js platform, using the Express web application framework. The application provides a system of routes and controllers that listen for HTTP requests and respond accordingly. Web pages are served on appropriate endpoints, which are constructed from templates so that they contain the correct information.

The web site was constructed with a progressive enhancement approach, where pages are constructed in a layered fashion. The server implementation therefore had to handle requests from "regular" HTTP requests and asynchronous requests (via XMLHttpRequest). These were given separate routes as they could potentially return data in different formats. Requests to the `/api` routes always return data in JSON format. This means that the application can very effectively take a progressive enhancement approach. The Express framework makes this relatively easy. The one exception to this rule is the visualisation page, which requires Javascript and SVG support. Unfortunately, D3.js depends on being able to use these relatively new features of the web, and so a true progressive enhancement approach could not be taken. It is possible that, given more time, an alternative view of the altmetrics data could be created, however this was considered mostly out of scope.


* Web application
	* Async
		* Difficult
		* "Pyramid of doom"
	* Accessibility
	* Search page
		* localStorage can be confusing
			* Especially when going back and searching for different articles once some articles are selected
				* On second search, it would appear that previously searched articles would be returned by the search results, when they're not they are just appended because they are stored
			* Usability test
			* Users not familiar with localStorage concepts - usually have to submit something for storage to take place
			* Should have split stored articles into a separate area - less confusing
		* Could be improved by having visualisation build on side of page, when articles are selected
			* Then change submit button to save button, to save data to db
			* Improves on confusing UI
		* Search results page
			* Search results not paginated
				* What happens if you put in something really generic & get lots of results
	* Styles
		* Bootstrap
	* Overall a reasonable implementation
* More time required
	* Not fully baked

