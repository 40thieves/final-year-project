<div class="page-break-avoid">

##### 5.3.2 Artefact Review

This section will evaluate the application and review the important issues highlighted during the implementation phase. Each major section from chapter 4 will be evaluated to review whether good decisions were made in this phase.

</div>

<div class="page-break-avoid">

__Altmetrics Data Collection API Module__

The data collection API wrapper (see section 4.2), was implemented as a package for the Node.js platform. The package acts as a wrapper around the PLOS ALM API, providing a uniform interface to the API and encapsulating logic within itself.

</div>

The best practices for creating a Node.js package were followed when creating the data collection wrapper. This produces a good internal structure for the module where all related logic is encapsulated within the module. Prototypical inheritance was used to create a class with methods and properties. Instantiating this class would set up the module, and listening to events emitted from the module would return results or errors. Handling the creation and sending of the request, parsing response and returning results is performed within the class. This results in a cleaner, more readable and less verbose codebase, as this logic does not need to be recreated multiple times. This has additional benefits - an update to the API will only affect code within this package. This also means that others can reuse the package without making changes to the core. The package can be found on the Node.js package manager, npm, under the name nodealm. Ultimately, the decision to adopt a prototypical inheritance style structure benefited the project as the code was cleaner and the ability to redistribute the package fits with the goals of the project.

When following these best practices, a lot was learned about creating modules with this structure. This involved completely rewriting the data collection wrapper with an improved implementation, after it was realised that using a constructor to create configuration for the rest of the module was useful, and that a cleaner callback could be constructed. An event based structure was adopted (see section 4.2.2), implementing these solutions. The prototypical inheritance style also meant that deeper understanding of the `this` keyword was acquired, as well as the important `bind()` and `call()` functions.

As discussed in section 4.2.3, during the implementation of the wrapper it was discovered that the API can produce errors in certain circumstances. If querying the API for many articles including their historical metadata, an error response will be returned, however this is sometimes inconsistent. A workaround was found that involves appending a list of required data sources to the request. This would consistently produce the desired result, however it may be somewhat inconsiderate to the maintainers of the API. Producing a large amount of data, such as in this request, may tax the servers and increase expenses for the providers. To mitigate this, a bug report was issued to the API maintainers, to make them aware of the situation. In addition, the problem may not be relevant, as it is not expected that the application will receive heavy usage, and so few of these requests will be sent.

Despite these problems, the PLOS ALM API is the best choice for the application, as was assessed in section 3.4.5. These findings mean that no other provider currently gives historical metadata of the type that is used by the visualisation, and therefore switching based on an inconsistent bug that can be easily worked around is a foolish decision.

Overall, the implementation of this module reaches expectations. Desired functionality is provided in a clean, encapsulated manner. This allows the package to be extracted from the main application for reuse in other projects. The package meets two goals of the project - to be able to collect historical altmetrics data for visualisation, and to provide tools for researchers for examining the temporal aspect of altmetrics.

<div class="page-break-avoid">

__Search API Module__

The search module (see section 4.2), was implemented as a Node.js platform, in the same way as the data collection module evaluated above. The module provides an interface to the PLOS Search API, giving a uniform set of methods for searching for articles.

</div>

The search wrapper shares many characteristics with the data collection wrapper, as the functionality provided by both is to some extent similar. The search module follows best practices, uses a prototypical inheritance approach to creating a class and uses an event based structure. The major difference is the search module will perform validation on the search parameters that are input to the module. The validation checks to ensure that no unknown parameters are passed in the request to the API.

As discussed in section 3.4.6, the PLOS Search API was chosen due to the decision to use the PLOS ALM API for altmetrics data collection. The latter API can only provide altmetrics data for articles in PLOS journals, therefore a search engine that will only provide results from PLOS journals would seem to be a practical choice.

There are some arguments against this decision. All articles published in PLOS journals are under an open access model - the article is free for anyone to access and read. This may introduce some bias into analysis of altmetrics data, as open access articles often show much higher altmetrics performance than closed models. However, previously, several respected altmetrics studies have solely used open access articles. In addition, as all articles visualised by the system will be open access, this bias can be accounted for in analysis.

Another argument against the PLOS Search API is that it does not correctly implement the HTTP protocol. A key component of this protocol is that responses are given codes that identify the type of response. For example, successful responses have the status code "200 OK". There are several common error responses, including "404 Not Found" and "400 Bad Request", which indicate that the requested resource cannot be found by the server and that the request is badly constructed respectively. These should be used if an error processing the response is raised. Unfortunately, the Search API does not do this, returning a 200 response even if an error is generated. The only way to distinguish successful and error responses is the response body, which is left empty. This means that message bodies must be parsed by the search module to check for potential errors.

Despite these criticisms, the PLOS Search API still reaches the requirements of the application - PLOS journals publish a large volume of papers, providing a large sample, and search results include article DOIs (see section 5.3.1, requirement 3). For this reason, the decision to use the Search API can be considered the most optimal. This, along with the well-structured and efficient wrapper, mean that the implementation of this functionality reaches expectations. Similar to the data collection module, this module meets two goals of the project - to be able to collect altmetrics data for visualisation, and to be able to release tools for researchers to further investigate altmetrics.

<div class="page-break-avoid">

__Visualisation__

The visualisation of altmetrics data was implemented as a library that depends on the D3.js framework (see section 4.4). The library creates a bubble chart using SVG elements, with 4 axes, where each article is represented by a circle. The x- and y-axes represent selected altmetric data sources that can be controlled by the user. For example, if the Twitter data source is selected, then its distance along an axis represents how many citations the article has received on Twitter. The size of the circle represents the total number of scholarly citations the article has received. Finally, the fourth axis represents the current year. As this axis advances, the data used for the x- and y-axes is transformed based on the current year. Therefore, as time advances, the bubbles on the graph move based on how much they changed over time.

</div>

This part of the codebase was also developed using a prototypical inheritance architecture. An AlmChart class was created that encapsulates the methods required to set up, construct and animate the chart. The library uses the Asynchronous Module Definition (AMD) to define itself as a module. This means that the library can be redistributed as a standalone JavaScript library, for reuse in other projects. This approach is similar to that taken by the data collection and search modules, except without the module functionality provided by Node.js, which is instead provided by the AMD structure.

Results of the usability test found that the visualisation is easy to use. This is a key factor in the successful implementation, as the underlying altmetrics data is voluminous and complex. Creating a chart that can draw knowledge from this data is somewhat difficult. Therefore, test results show that the visualisation is easy to use and understand and so can be considered a success.

Section 5.2.3 details the acceptance test that was performed on the application. This test found that altmetrics researchers criticised the use of an assumed interpolation model in the visualisation. This allowed for calculation of data values between given years, smoothing the animation. This means that the visualisation has much reduced value to altmetrics researchers. With hindsight, it seems obvious that implementing such a model into scientific software without research was a poor decision.

However, there are some positives to take from this part of the project. The acceptance test found that the comparison aspect of the visualisation is still useful. Being able to quickly compare altmetric data sources has value to researchers. By removing the smooth animation, it would be possible to retain this aspect of the visualisation. Alternatively, the project could implement an interpolation model that better represents altmetrics research. Unfortunately, this research is not yet complete - one of the main goals of this project is to encourage this area of study (see section 1.2). When complete, the application could easily be updated to use this model as it is released under an open source licence.

Overall, an evaluation of the visualisation implementation finds mixed results. The usability test found that there is some underlying value in the visualisation that must be brought forward. Altmetrics visualisations are still rare, especially one that can be dynamically changed for comparison purposes. As discussed, the interpolation issue drastically reduces the usefulness of the application, however there are potential strategies to mitigate this. The open source nature of the project means that making such changes is relatively easy.

<div class="page-break-avoid">

__Storage__

After altmetrics data is retrieved by the data collection module, it is stored in a MongoDB database (see section 4.5). The database stores documents in a JSON-like format, very similar to the format received by the data collection module. The application enforces a lightweight structure for the database, using the Mongoose library. When the data is stored, a unique key is generated, which is used to create a permalink for the data. This can be visited later by the user without having to retrieve data for a second time.

</div>

As discussed in section 4.5, the proposed solution for this implementation did not require a structured database. The requirement states that data must simply be stored against a unique key for retrieval later. A defined structure is not required, as data would be treated as a single entity. However, this changed as the implementation progressed. The Mongoose library was found to provide a good interface to MongoDB, while also allowing the creation of a simple database schema. This may prove useful in future development of the project, as aspects of the data can be queried separately, as opposed to key/value store. This implementation choice can be considered a good, forward thinking decision, as it meets the requirements of the system while allowing for further extension.

This part of the artefact also meets the experimental goals of the project (see section 1.2). MongoDB was an unfamiliar technology that was assessed as it was implemented into the application. It was found that MongoDB provides a flexible, simple and lightweight database for the project.

<div class="page-break-avoid">

__Web Application__

The parts of the application described above need to be combined and controlled to provide an interface between the parts and the user. This is provided by the web application, controlling the flow of data between the individual modules and the web pages that allow the user to interact with the system. The implementation is constructed on the Node.js platform, using the Express web application framework. The application provides a system of routes and controllers that listen for HTTP requests and respond accordingly. Web pages are served on appropriate endpoints, which are constructed from templates so that they contain the relevant information.

</div>

The use of the Node.js platform and the Express framework was a good choice for creating a cohesive application that combines the four other elements of the system. The application is able to handle HTTP requests, process their data and send correct responses. The Express framework allows a clean and understandable architecture for the application, without taking an opinionated approach.

The web site was constructed with a progressive enhancement approach, where pages are constructed in a layered fashion. The main benefit of this approach is that more users can access the application. There are two broad categories of users in this respect; those with older browsers, and those using accessibility tools. For the former group the progressive enhancement approach means that they can still use the web page despite their outdated browser. For the latter group, separating and optimising the content layer (represented using HTML) from the presentation and behaviour layers (represented using CSS and JavaScript respectively), means that accessibility tools can gain a greater semantic understanding of the web page's functionality.

The server implementation therefore had to handle requests from "regular" HTTP requests and asynchronous requests (via XMLHttpRequest). These were given separate routes as they could potentially return data in different formats. Requests to the `/api` routes always return data in JSON format. This means that the application can very effectively take a progressive enhancement approach. Unfortunately, D3.js depends on being able to use these relatively new features of the web, and so a true progressive enhancement approach could not be taken. It is possible that, given more time, an alternative view of the altmetrics data could be created, however this was considered out of scope.

The event based nature of Node.js leads to applications that use callback structures heavily. The asynchronous capabilities of many core Node.js methods mean that callback functions are required for applications to work. For complex systems there is often a need for a series of nested callbacks that some have jokingly termed the "pyramid of doom". An example is shown in the code snippet below.

```js
func1(function(value1) {
	func2(function(value2) {
		func3(function(value3) {
			func4(function(value4) {
				// ...
			});
		});
	});
});
```

This is considered bad practice, due to the reduced readability, especially issues related to scope. This must be refactored to make the internal structure clearer. This is sometimes difficult to achieve, especially as other programming paradigms lack this structure, and execute procedurally. Unfamiliarity with this style made it hard to adapt to the Node.js platform. The application achieves this refactoring to some extent, especially in the search and data collection API wrappers (see section 4.2.2). This is less true for the web application. The "pyramid of doom" is avoided, however it is replaced by code that is only marginally more readable. The following code snippet shows a controller function that is executed when the home page is requested.

```js
index: function(req, res, next) {
	this.run(req, res, next);
}
```

This code shows some of the confusing function calls that are found in the controller logic. The controller that contains this method uses a form of inheritance to include the `run()` method for all controllers (see section 4.6.4). The `run()` method will find the relevant view class, and render it using data from the current controller. However, as is evident, the request and response objects must be passed to the function. This breaks Express framework's concept of "middleware", where responses are processed by a series of asynchronous functions that call the `next()` callback once finished. Under this paradigm, the controller should perform its task and generate necessary request data before calling the `next()` callback. If additional processing is required, this would be handled next, otherwise, control would move to the view class to create the response. This is much cleaner than the current architecture used by the application, and doesn't require confusing function calls such as the one shown above. Each section of the processing is separated and encapsulated within itself. Given time constraints, a refactor that would convert controllers over to this style was abandoned.

As discussed in section 5.2.2, the usability test found that the article search results page is confusing for users. Selecting an article by clicking the checkbox would store this article in the browser's localStorage. However, other than a small UI element that shows the number of stored articles, there is little feedback for the user to know that the article has been stored. In addition, localStorage is a relatively new technology included in browsers. Therefore many users are likely unaware of such functionality, and expect a "Save" or "Submit" button to be clicked for data to be saved.

Users also found the addition of stored articles to the results list confusing. When search results are shown, and articles have been stored in localStorage, the stored articles are appended to the end of the results list. Users did not understand that these articles were appended, and were confused as to why they were returned as search results. To prevent this, stored articles should have been added to a separate, labelled list that makes it clear how they were generated.

Another problem with the search results page is that the list of results is not paginated. This was discovered late in the testing process, as some users were using generic terms in their search query, producing a very large list of results. This list should have paginated, however it was not considered a priority as it is likely that users will use the application for finding articles on specific subjects. For example, a user looking for altmetrics data about her career would only search for articles authored by herself, likely a shorter list.

Finally, it was suggested that the application could be improved by removing the search results step, and integrating it into the visualisation. The visualisation could be adapted to be viewed alongside the search results, and appear empty when first created. Then as articles in the search results are selected, bubbles representing the article are added to the visualisation. Thus, the visualisation would dynamically grow as articles are selected. Users wishing to revisit the visualisation for use later could "save" the current visualisation by clicking a button and submitting data to the server. This would significantly improve the user experience of the application. However, this enhancement had to be dropped due to time constraints.

The application has a good visual style, thanks to the stylesheets sourced from the Twitter Bootstrap project. This was considered an important factor when creating the application as scientific software is not known for its beautiful applications. The addition of class attributes to the application HTML is an extremely painless method for creating a visual design. The base styles provided by Bootstrap were modified slightly for the application, however much of the application's visual design relies on basic HTML elements that are covered by Bootstrap.

Unit test coverage of the web application is poor. There are tests that cover the basic start-up of the application, the base controller and base view classes, however no tests are provided for specific controllers that inherit from this base. Even within these tests, not all functionality is tested. As discussed in section 5.2.1, it may be that fewer tests are required as much of the functionality is provided by the well-tested Express framework, however more unit tests would help develop the application further and refactor existing mechanisms.

Overall, the web application is the weakest part of the codebase that makes up this application. As discussed in this section, the search results page has a number of usability problems and test coverage is low. The controllers used by the application do not exploit the middleware functionality provided by the Express framework. These failings are evident in the test results. However, Node.js and Express were used to create an application that meets requirements. It was able to combine the four other elements of the application into a cohesive system. The experimental nature of the project likely contributed to the problems with the web application. Further development of this area is required to improve these weaknesses, such as changing the search results page to dynamically create the visualisation as articles are selected.

<div class="page-break-avoid">

__Time Constraints__

As discussed in this section, some parts of the application do not fully meet the requirements. This is due to the experimental nature of the project, as described in the project goals (see section 1.2). As evidenced by requirements 8, 9 and 10 (see section 3.3), which describe the need for assessment of the suitability of Node.js, D3.js and existing altmetrics providers respectively, the project investigated relatively new technologies. This lead to a longer than expected implementation phase, as working out the best solutions for the issues raised was time consuming. Finding a clean and readable architecture for the system was a priority, as it was intended for an open source release. To achieve this several test iterations were tried before the final structure was chosen. Additional time to complete the details of the application was therefore short. Thus, given more time, the lessons of this experimentation could be applied to creating a much improved implementation.

</div>

