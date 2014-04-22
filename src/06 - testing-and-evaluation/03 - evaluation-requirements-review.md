<div class="page-break-avoid">

#### 5.3 Evaluation

Chapter 1 introduced the concept of altmetrics, and pointed to the problem of a lack of research into how altmetrics changes over time. Chapter 2 gave a deeper look at why altmetrics are needed, what altmetrics are and potential problems with altmetrics. The chapter concluded that tools are needed to assist researchers in studying how altmetrics change over time. Chapter 3 described the design of such a tool, detailing the methodology and requirements of the project needed to develop this tool. A proposed solution to the problem was also described in this chapter. Chapter 4 described the implementation of this proposed solution, giving development details of how the artefact was created. Problems encountered during the implementation were discussed, and the solutions to these problems described.

</div>

Testing for this project was discussed in section 5.2, which demonstrated that the artefact meets some but not all of the requirements. Testing with altmetrics researchers found that the application fails to meet the main goal of the project, however with more research it could be improved or adapted to reach this goal. The application also has problems with some confusing user interfaces, but is overall effective in creating altmetrics visualisations and is easy to use. Several new technologies were experimented with during the course of this project. Node.js, MongoDB and D3.js are examples of this, which were assessed for suitability throughout the development phases of the project. In this section, the project requirements and artefact implementation will be evaluated and issues in the underlying technologies that the implementation has highlighted will be discussed.

<div class="page-break-avoid">

##### 5.3.1 Requirements Review

In this section, the implementation will be evaluated against the requirements (see section 3.3). Each requirement will be examined to identify the extent to which each has been achieved. In addition, a reflection on each requirement will be made, looking at their relevance for future work. Each of the requirements is reintroduced and discussed in turn.

</div>

<div class="page-break-avoid">

__Requirement 1. Visualisation of altmetrics data sources__

This requirement describes the need for a visualisation that will allow for comparison of altmetrics data sources for a set of scholarly articles. Section 2.6 found that research is often performed by comparing altmetrics data sources against each other and against traditional citation counts. This must be facilitated by the artefact. The visualisation must have some mechanism for associating an article with its visual representation.

</div>

The application achieves all significant details of this requirement. The AlmChart library produces a bubble chart visualisation that enables researchers to compare altmetrics data sources for a number of different articles quickly (see section 4.4). The data sources used to generate the chart can be switched at any time, so that different sources can be compared. The chart can show a large number of articles simultaneously, again aiding comparison. Finally, the tooltips shown when the user hovers their mouse over an article's bubble satisfy the requirement for a mechanism for associating visual representation with the original article.

These features mean that altmetrics data can be compared quickly and easily, meeting the needs of this requirement. This finding is supported by the evidence gathering during the testing phase. Both the usability test and the acceptance test with researchers found that the visualisation meets requirement 1.

<div class="page-break-avoid">

__Requirement 2. Visualisation of altmetrics changing over time__

For this requirement, the application must provide some way for the visualisation described in requirement 1 to additionally analyse how altmetrics data changes over time. This requirement is a significant part of the overall goal of the project - to create tools to aide researchers examining how altmetrics change over time. The requirement specifies that the chart must use a time dimension, for which altmetrics data is transformed to represent different time periods. The user must be able to control the passage of this time dimension.

</div>

For the application these time periods were chosen to be every year, as other time periods were found to be either unreliable or overly complex to visualise (see section 4.4). An animation was created that will start with the altmetrics data shown for the earliest year, and will move the article bubbles between each year, until reaching the latest year. An interpolation algorithm was created to calculate data values between years, smoothing the animation's movement. The user is able to control this animation by hovering over a year label that was added to the chart. Further to the left of the label, the earlier the data, and further to the right, the later the data.

The visualisation library mostly fails to meet this requirement. The interpolation algorithm was criticised by altmetrics researchers in the acceptance test. The approach to this part of the visualisation library was based on an unsupported assumption, and therefore cannot be found to have met the requirement. Researchers believe that more study of how altmetrics changes over time is required before modelling these relationships can be accurately achieved. However, because of the open source licence applied to the visualisation library, it is believed that once this research has been completed, the library can be modified to reflect this new model and the application will meet the requirement. The usability testing supports this, as non-researchers found the temporal aspect of the chart easy to understand, suggesting that the visualisation may be useful in areas outside of science.

<div class="page-break-avoid">

__Requirement 3. Scholarly article search__

The visualisation described in requirements 1 and 2 shows altmetrics data for multiple articles, and this requirement states the user's need to be able to identify which articles are to be visualised. The requirement states that a search form must be implemented that allows users to search for articles by title, author and other common article metadata. The requirement states the need for a mechanism that uniquely identifies articles within the application, suggesting that the DOI system could be used.

</div>

The application provides search functionality through the PLOS Search API, allowing search for any articles in the PLOS library (see section 4.3). This restriction was implemented due to choices made for requirement 4. A uniform interface for the API was provided, through the creation of a search wrapper. This takes search parameters from the search form, and makes a request to the search API. This significantly cleans up the codebase, encapsulating search API logic into one library.

This requirement is fully met by the artefact. The search wrapper allows the application to take search parameters from the user, process them and return a list of matching articles, including their metadata. The system can handle single articles or a set of articles, where each article is uniquely identifiable through its DOI. This evaluation is supported by the test results. Users in the usability and acceptance tests were able to search for and find articles of interest. The search module was released separately and can be used outside of the main artefact, satisfying the requirement. Unit tests for the module were also released, allowing others to verify the application's functionality.

<div class="page-break-avoid">

__Requirement 4. Altmetrics data collection__

Similar to the previous requirement, the visualisation system within the application requires altmetrics data to generate the chart and show how data changes over time. The mechanism for collection of this data is described by this requirement. The requirement states that data collection from the original source is out of scope, and an altmetrics data API provider should be used. The system must take a list of selected article DOIs, request altmetrics data related to these articles from the API, and return the relevant data.

</div>

An altmetrics data collection API wrapper was developed to meet this requirement. This, much like the search wrapper, creates a uniform interface for the API. The PLOS ALM API was chosen for the application for several reasons, discussed in detail in section 3.4.5. Requirement 10 will evaluate this choice. This API can only provide altmetrics data for articles in PLOS journals, and thus only PLOS articles can be used for the application. This is the reason for the restriction on search APIs, discussed above. The wrapper takes a set of article DOIs, creates a request to the API, and processes and parses the response before returning the altmetrics data. Similarities with the search wrapper can be drawn. The module uses best practices to encapsulate data collection logic within itself.

This requirement is also fully met by the application. The data collection wrapper allows the application to perform the tasks laid out in the requirement, creating a cleaner, less verbose codebase. This is supported by the test results. Both the usability and acceptance tests found that users were able to collect altmetrics data for articles of interest. This functionality is easy to use, by altmetrics researchers and non-scholars. The wrapper module was released separately so it can be reused by others, along with unit tests. This satisfies all parts of the requirement.

<div class="page-break-avoid">

__Requirement 5. Storage of results__

This requirement states that the system must provide some way of storing altmetrics data so that users can revisit the generated visualisation later. This is to prevent unnecessary requests to the data collection API to retrieve data for a second time. The requirement states that a unique identifier must be generated when the data is stored that is used to create a permalink to the resulting visualisation.

</div>

The application uses a MongoDB database to store altmetrics data after retrieval from the data collection module. MongoDB stores "documents" in a JSON-like format - well suited to this application as the data collection API returns a JSON format response. The Mongoose library is used to provide some database structure and handle querying and updating functionality. When the data is stored, MongoDB will generate a unique key for the data - similar to a primary key in SQL databases. This is used to create a permalink for the data and its resulting visualisation.

This requirement is fully met by the application. The data is stored after it is retrieved from the data collection API, generating a unique identifier. This is used to create a permalink for the data. This is, again, supported by test results, with both tests finding that the generated permalink was a useful feature for the system to have.

<div class="page-break-avoid">

__Requirement 6. Easy to use__

This requirement states the artefact must be easy to use by users who are not familiar with altmetrics terms as well as those not in the technology industry. Therefore, an understandable user interface must be developed, that does not use obscure altmetrics-related vocabulary. A good visual design will help to differentiate the project from other scientific software.

</div>

The application uses stylesheets from the Twitter Bootstrap project to achieve a simple, well designed visual design. Attempts were made to improve the user interface, for example by storing selected article locally in the browser. This allows users to select articles of interest, then perform another search while maintaining the selection of previous articles.

The application does not meet all of this requirement. Testing found that users were confused by the selected article storage implementation. It took longer for users to complete tasks when using this functionality. It is possible that an improved interface could be created, by providing more affordances - inherent properties - that explain how article storage works. However, some of this requirement was met by the application. Researchers in the acceptance test commented on the clean visual design used for the artefact.

<div class="page-break-avoid">

__Requirement 7. Open source__

For this requirement, the application code must be published under a permissive open source licence. The requirement states the need for transparency in the calculation of altmetrics. The requirement defines an open source licence as one certified by the Open Source Initiative (OSI).

</div>

As described in sections 4.2 and 4.3, the search and altmetrics data collection functionality of the application was developed as separate modules. This means that they can be reused by external developers in other projects, encouraging the development of altmetrics research. For this to be achieved without unnecessary legal difficulties, the modules would be released under an open source licence. The MIT licence was chosen, allowing developers to reuse module code in proprietary and non-proprietary software, provided copies include the text of the licence. This means that original development credit is retained, but permissive reuse is allowed. The same approach was taken for the AlmChart visualisation library, which was released under an MIT licence as a standalone library.

Along with the modules described above, the entire artefact will be published under the MIT licence. This is of less use to altmetrics developers as this represents an entire project. It is inherently harder to reuse a fully complete package than a separated module. However, as is stated by the requirement, transparency is a core feature of altmetrics. Therefore the methods taken to generate the visualisation should be published.

In addition, source for both the modules and the final artefact is hosted on GitHub, meaning that patches can be accepted. This will encourage further development to improve the software.

The project meets all points of the requirement, and some in cases exceeds it. Source code for the application is published under an OSI approved open source licence, and will accept further development patches. By separating the modules from the core project, reuse is further encouraged, as the inherent structure of a module allows it to be "plugged in" anywhere. This is supported by evidence from the acceptance, where altmetrics researchers showed some interest in reusing the data collection module in a future project.

<div class="page-break-avoid">

__Requirement 8. Suitability of Node.js for this project__

For this requirement, the project assessed whether Node.js was the correct choice for this application. Further, this requirement is important to the experimental goals of the project (see section 1.2). Node.js was chosen as the server side platform to control data flow between the modules, execute the modules themselves and serve the web application. The requirement states that the application must be built quickly and easily using this platform. In addition, Node.js must encourage the creation of a well-structured and efficient artefact.

</div>

As described in section 4.6, Node.js was used for the server-side implementation of the web application. This section of the project combines the other sections together, controlling their interactions and providing an interface between these sections and the user. The search and data collection modules were constructed as Node.js packages, and published on the built-in package repository, npm. In addition, Node.js acted as the web server for the application, serving web pages and accepting HTTP requests.

The project meets this requirement fully, finding that Node.js is a suitable platform. Node.js' focus on modularity is crucial to the approach taken by the project. Without the ability to include modules quickly and without difficulty, the project may not have been able to release the search and data collection packages for reuse by altmetrics researchers - a key part of this project (see requirement 7). The server created for this project was able to effectively link the disparate sections that make up the application, although the asynchronous nature of Node.js did lead to some difficulties in creating clean code. It was also found that, unlike frameworks on other languages, Node.js takes an un-opinionated approach to the application structure. This has benefits and drawbacks - it is useful to develop custom structures, however in some circumstances, more boilerplate code is required to create a basic MVC structure. Despite this, the project found that Node.js provides a platform for creating well-structured and efficient servers.

<div class="page-break-avoid">

__Requirement 9. Suitability of D3.js for this project__

This requirement, similar to the previous requirement, will assess whether the D3.js library is suitable for this project. D3.js was used as the framework for creating the visualisation (see section 4.4). It allows manipulation of the browser DOM through the use of data. The requirement states that for this library to be considered successful, it must provide a stable and efficient platform for creating visualisations such as the one described in requirements 1 and 2.

</div>

As discussed in section 4.4, D3.js was used as the framework for creating the visualisation. The AlmChart library depends on D3.js, using it to create the SVG elements that make up the chart. The framework requests data from the server asynchronously, which is then transformed to clean the data, before being used to set up axes and finally draw the bubbles of the visualisation.

This requirement is met fully by the project, finding that D3.js is a suitable framework for creating complex visualisations. D3.js was found to be able to efficiently create the visualisation, even with large amounts of data. The visualisation is a relatively complex custom chart, that was relatively easily created using D3.js.

<div class="page-break-avoid">

__Requirement 10. Assessment of existing altmetrics providers__

For this requirement, the project will compare altmetrics data APIs, to find the most suitable for this project. The requirement states that the API must provide historical metadata to allow the visualisation to show how altmetrics change over time. The API must also be able to provide data for a reasonable number of articles and altmetric data sources. Without these, it would be difficult to generate a visualisation with much relevance and would be difficult to compare data sources.

</div>

The PLOS ALM API and the Altmetric.com API were researched to assess which would be the most suitable. Section 3.4.5 includes a table that breaks down the key features of the two APIs that were considered. As shown in this table, the PLOS ALM API was chosen as it meets all three of the key requirements. Crucially, the historical metadata that is included in the API response is well suited to a series analysis, such as one used in the visualisation. The Altmetric.com API also provides historical data, but it is structured in such a way that is not conducive to a series analysis. All three APIs provide a large number of altmetric data sources, so this was not a factor in the decision. The PLOS API does not perform as well as the other APIs at providing data for a large number of articles - it can only provide data for articles in PLOS journals. However, PLOS journals are very prolific, and so the pool of potential papers remains high.

The project meets all parts of this requirement, finding that the PLOS ALM API is the most suitable API for providing altmetrics data. All three of the major altmetrics providers were considered in this analysis, and so it is likely that the best possible solution was found.

