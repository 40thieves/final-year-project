<div class="page-break-avoid">

#### 6.3 Conclusions

The aim of this project was to provide tools to altmetrics researchers to assist study of how altmetrics change over time. These tools must be usable and comprehensible to those outside of altmetrics research and computing. The final goal of the project was to experiment with new technologies and assess their usefulness in this context (see section 1.2).

</div>

The project focussed on providing visualisation tools that allow for altmetric data source comparison over a period of time. A visualisation tool was chosen as it provides a simple mechanism for understanding the voluminous and complex data that characterises altmetrics.

A system was then designed and implemented that could:

* Allow a user to search for articles of interest
* Collect altmetrics data for these selected articles from an altmetrics data API
* Store this data in a database for faster retrieval later
* Use the data to generate a visualisation that shows altmetric data sources changing over time

These were then combined to create a web application with a friendly, easy-to-use interface. The Node.js platform was used as a server, with modules for each of the above capabilities. JavaScript best practices were followed in the implementation of this application, allowing significant parts of the project to be released as standalone packages. It is hoped that these can be used in the development of further research into altmetrics.

In Chapter 1 a general hypothesis is stated that by providing tools for altmetrics researchers studying the temporal aspect of altmetrics, more research into this currently under-valued topic will be encouraged. Furthermore, by improving usability of the tool - an area that is traditionally lacking in scientific software - those not directly involved in altmetrics research will find value in the project.

The first part of this hypothesis was tested by performing an acceptance test on several altmetrics researchers (see section 5.2.3). The results of this test were mixed. Participants in this test found that the current iteration was not useful for research, however several commended individual parts of the application. It is hoped that by splitting the application into multiple reusable packages, some value will be found in the project.

The results of the usability test (see section 5.2.2) were more encouraging, with users finding that the application was on the whole, easy to use and understandable. They were quickly able to navigate through the application to generate the visualisation, and thus complete their goals. There were some problems with specific parts of the application, especially the search results where users were confused by temporary storage of selected articles.

<div class="page-break-avoid">

##### 6.3.1 Key Points

__Altmetrics are a Powerful New Tool for Examining Scholarly Impact__

As discussed at length in sections 2.4 and 2.6, altmetrics provide a faster, more diverse and transparent mechanism for measuring scholarly impact.

</div>

The altmetrics literature finds a correlation between the traditional citation counts that have been relied upon for years and various altmetric data sources, supporting the argument that altmetrics can be used to discover academic influence. Although most find a weak correlation, it is suggested that altmetrics find novel forms of impact, not yet covered by the impact factor. It is in this diversity that altmetrics finds true value, providing new perspectives on impact, by "telling the story" of reuse on the web.

By using online sources for data, altmetrics can be orders of magnitude faster at providing useful altmetrics data than citation counting methods. While traditional mechanisms must wait at least a year for citations to build up, many altmetric data sources can produce reliable data within hours of publication.

The impact factor suffers from a lack of transparency, calculated in a somewhat unscientific manner by a single commercial entity. Considering that this value can have a significant effect on a researcher's career, this practice should be stopped and more open alternatives considered. The Altmetrics manifesto (Priem, Taraborelli, Groth & Neylon, 2010) encourages the use of open source software, allowing other scholars to examine the methods by which altmetrics were calculated.

Altmetrics cannot be considered as a panacea for all bibliometric problems. It is vulnerable to gaming, often relies on the scientific judgement of the general public, and some data sources are weak indicators of impact. However, altmetrics cannot be considered more than a filter for the growing flood of scholarly research. This puts altmetrics alongside traditional tools like peer review and the impact factor. No single filter can be used to describe the complex nature of scholarly impact. In fact, many advocate usage of all of these tools together, acknowledging their individual strengths and blindspots.

<div class="page-break-avoid">

__Further Altmetrics Research is Required__

As is evident from the aims of this project, more study of altmetrics is required. The literature review found several potential topics for this research (see section 2.6.1). This includes data source categorisation, network effects, alternative scholarly output and the temporal aspect of altmetrics. In addition, many of the criticisms levelled at altmetrics (see section 2.5) can be answered through more research. For example, gathering more evidence of correlation with citation counts or investigating methods for reducing gaming.

</div>

As discussed above, altmetrics gives us the opportunity to look at the "story" behind impact, where, and from whom it was generated. One area of this "story" that has not been looked at detail is how it changes over a long period of time. Multiple studies have looked at altmetrics at different times after publication - either a few hours or days after; or several years after. However, no studies have looked at this entire time period from publication up to a few years after publication. The review found that tools are required to study the large amount of complex data that is generated by altmetrics.

<div class="page-break-avoid">

__Examination of Altmetrics Through Visualisation__

The above conclusion finds that more research is required looking into how altmetrics change over time. The project found that a visualisation is likely the most suitable method of presentation for investigating this subject.

</div>

Altmetrics data is often complex and voluminous, as many data sources must be analysed simultaneously to retain the context that is described above. This is especially true when studying historical altmetrics data, as each data source holds a series of values for each time period. This presents a problem to altmetrics researchers attempting to understand and analyse this data.

At first it was difficult to conceive of an effective method for visually displaying how the data changes over time. However, the chart was inspired by Hans Rosling's presentation on the state of countries between 1800 and 2009, which very effectively shows the changing data over time. Trends become clear that would be obfuscated by large amounts of data.

By visualising the data as a bubble chart and allowing these bubbles to change as a temporal dimension advances, an intuitive understanding of the rate of change in altmetrics data can be gained. A comparison between altmetrics data sources and traditional citation counts is also viable. By using this presentation, the application goes a significant way to meeting the goals of the project.

<div class="page-break-avoid">

__Modular and Open Source Approach__

The design of the application called for the development of a set of modules that could be separated from the main application for later reuse. This approach means that logic can be abstracted and encapsulated into modules, giving a clean and verbose interface to the logic. Modules (or module-like plugins) for the search API, the data collection API and the visualisation were developed. These, along with the main application, were released under permissive open source licence.

</div>

Section 2.6 found that altmetrics benefit from a culture of transparency, allowing others to examine calculation methods. An open source licence enables this transparency, giving third-parties legal permission to investigate the mechanisms the application uses to present altmetrics impact. This is important, as significant decisions about a researcher's career can be based on this impact.

The major reasoning behind the modular approach is that modules can be split from the application and distributed as standalone packages. These help contribute to a goal of the project - namely to assist researchers study altmetrics (see section 1.2). The application as a whole aims to achieve this goal, however in doing so, other useful parts of the system need to be developed. If these parts have value outside of the application, it makes sense to release them separately, encouraging novel reuse. As discussed in the acceptance test results (see section 5.2.3), this was found to be the case for some parts of the application, with altmetrics scholars commenting that the API wrappers in particular would be useful.

As discussed in the evaluation of the artefact (see section 5.3.2), the visualisation fails to meet some of the requirements for this project. The interpolation model used for the graph is currently unsupported. However, thanks to the open source licence that the AlmChart library was released under, once a model is developed by research the library can be easily updated and redistributed with this model.

The advantage of the modular, open source approach is that, even if the project has moved on or development has stopped, others can take the code and update it themselves. As the code repository is hosted on GitHub, these changes can be easily patched on the original, through a pull request.

<div class="page-break-avoid">

__JavaScript Best Practices__

As discussed in chapter 4 (see sections 4.2.1 and 4.4.1), JavaScript best practices were followed when developing the architecture of the modules described above. Due to the public nature of the modules, the packages must behave in ways that other developers expect. These best practices mean that the modules can follow what others have found to produce the best results. These lessons were also applied to the development of the web application, which combines the modules together, so that clean and efficient code is created.

</div>

Throughout the application, prototypical inheritance is used to create "class-like" structures, which contain properties and methods. This enables encapsulation of logic into the class, much like more traditional object-orientated languages. A uniform interface can be provided for interaction with the class that increases readability and testability. This is the basis of the modular architecture used in all parts of the application.

For the server-side packages, these classes were then wrapped in Node.js' module logic, and packaged for distribution. Similarly for the browser code, the classes were wrapped in Asynchronous Module Definition (AMD) logic. These enforce a structure that is standardised, so that third-party developers do not have to read and understand a custom structure. The interface becomes somewhat "guessable".

An MVC architecture was developed for the web application, an architecture that is common for web servers as it provides clean separation of concerns and is considered a best practice. It was found that, by separating data flow logic from user interaction, a well-structured and readable web application was created.

<div class="page-break-avoid">

__API Wrappers__

The application contains two API wrappers - one for the PLOS Search API, and one for the PLOS ALM API. These provide a cleaner interface between the codebase and their respective APIs. Logic related to constructing the request and parsing the response is encapsulated within the modules.

</div>

Both modules provide an effective mechanism for retrieving data, without cluttering the code. They both use a well designed and constructed architecture. The event-based structure allows for flexibility when returning data and handling data. This was achieved after iteration and refactoring, improving the structure to provide sensible defaults. Functionality for both wrappers is well tested, with high unit test coverage.

As discussed above, and in section 5.2.3, both wrappers were found to have value outside of the main application. Results from the acceptance test suggested that altmetrics researchers may use the modules for use in external applications. The search API wrapper has received 1 "star" from a known altmetrics researcher on GitHub, showing its potential value. However, one must consider that Node.js is not a very popular platform for researchers, with most preferring Python.

An area of improvement that could be tackled in future versions of the package would be validation of input in the data collection module. The module accepts a list of DOIs that represent articles for which data is to be found which is currently not validated to check if the list contains invalid DOIs. If this was implemented, an error would be thrown, to be handled by the developer.

Aside from this and other incremental improvements to the modules, there is little opportunity for further work on this part of the application. The modules meet their requirements well, and so there is no need to change them further.

<div class="page-break-avoid">

__Visualisation__

The visualisation library, AlmChart, creates a bubble chart using the data gathered from the data collection API. The visualisation shows altmetric data sources on the x- and y-axes of the chart, which can be changed by the user for comparison purposes. Another axis represents the current time in the visualisation. As this axis advances, data on the x- and y-axes is transformed to match the current time. An interpolation model is used to calculate the values between discrete years. This allows for a comparison as the data changes over time, meeting the goal of this project. In addition to this, comparison between the altmetric data sources and traditional citation counts is achieved by another axis. The size of the bubble is determined by the total number of citations accumulated to the present.

</div>

However, as discussed in section 5.2.3, the visualisation was criticised for using an interpolation model that has not been studied in detail. There is no evidence to support this model currently, and therefore it has reduced value as a tool for scientific analysis. This is an obvious target for future work, as the interpolation model could be updated when research into this topic is completed.

Another area for future work is the development of a trace line - a line that is drawn as the article bubble progresses through time. Time constraints led to development of this feature to be stopped, however it would add significantly to the usability of the graph.

<div class="page-break-avoid">

__Search Results Page__

Another area that has potential for future improvements is the development of the search results page. This part of the application shows the list of articles that match the search parameters. Users can click checkboxes next to the articles to select them for altmetrics data collection. When selected, article metadata is stored in the user's browser. This storage is accessed when the search results page is generated, and previously selected articles appended to the list.

</div>

This was found to have several usability issues (see section 5.2.2) - users had no affordance to tell them that selecting an article would save an article. This may be an inherent problem when using localStorage, when implemented incorrectly. Developers must be aware of this problem when designing future systems. In addition to this, no separation between saved articles and search results was made, confusing users as to how some results were generated.

There are some obvious improvements to be made to this implementation that could be completed in future work. Firstly, a separation of saved articles from search results could be made, significantly improving usability. Alternatively, a better user interface for saving articles could be developed.

The search results page has another usability issue - the results list is not paginated. If a search is overly generalised, it produces a large list of results. This is another obvious target for future work.

Another suggestion for future work on the search results page is the use of a dynamically generated visualisation. As results in the current search are selected, their data is retrieved from the data collection API and added to the graph. This would build the visualisation dynamically, removing an extra step in the flow of interaction. Instead of a "Generate" button, a "Save" button could be used that would save the current data to the database. This is a significant change to the data flow of the application, and would therefore require a large amount of work to complete.

<div class="page-break-avoid">

__Node.js__

One of the requirements for the project (see section 3.3), was to assess the suitability of the Node.js platform for this project. As discussed in the requirements review (see section 5.3.1), Node.js was found to be a capable platform for the application, providing a stable, efficient web server. Node.js was used as the skeleton, upon which all other elements of the application were hung.

</div>

The modular philosophy that is inherent to Node.js is its greatest advantage for this project. The close association with the package manager, npm, encourages developers to encapsulate logic into modules. The creation and inclusion of modules into an application is much easier than in other languages, such as PHP. This obviously fits well with the modular approach taken by the project. However, it may be unfair to directly compare languages in this manner, as the project was only developed in Node.js, and not in other languages.

The Express web application framework was used to extend the native capabilities of Node.js, which was useful for creating the clean architecture. However, it was sometimes frustrating to custom build this infrastructure, especially when compared to other web frameworks such as the Laravel PHP framework. The unopinionated approach of Express, and the Node.js community in general, was at times difficult to work with, especially as it was decided early on that an MVC architecture was required. In other more opinionated frameworks, this architecture is enforced, and no custom code is required to create it.

<div class="page-break-avoid">

__Grunt and Bower__

As discussed in sections 3.4.3 and 4.6.3, the command line tools Grunt and Bower were used to create administration tools for the application. Grunt is a "task runner" that provides automation tools, through the use of so-called plugins. Bower is a similar tool, aimed at "front end" web development, for downloading third-party stylesheets and JavaScript libraries.

</div>

The usage of Grunt to create administration tools was found to be extremely useful. The entire web application can be bootstrapped simply by running a `grunt` command in the application's root directory. This will start the database, start the Express web server and compile the Sass pre-processor files into CSS. Without Grunt, these tasks would become tedious. In addition the `grunt build` command was configured to download the necessary stylesheet from the Twitter Bootstrap project and include them into the application.

Bower was found to be much less useful. It was configured to download the Bootstrap stylesheets, as described above, however these files would be placed in the wrong directory in the project (see section 4.6.3). A Grunt plugin had to be used to fill this rather basic functionality, a frustrating problem when using Bower.

<div class="page-break-avoid">

__MongoDB__

The MongoDB database was used to store altmetrics data after it had been collected from the data collection API. This allowed the creation of a permalink for a given set of data, without having to return to the API and re-fetch it, significantly increasing speed.

</div>

MongoDB was found to be a useful database, due to its relative simplicity, and JSON-like storage format. The application did not require an overly complex database schema, as the data could be stored as a single entity, attached to a unique key. The data was also returned from the API in a JSON format, and so it seemed sensible to store this in a JSON-like format. MongoDB performed both of these tasks well.

There is potential for future work to expand on the simple schema implemented in the application, to provide a deeper analysis of the data stored. As described above, the data is treated as a single entity, although a basic schema was created using the Mongoose library. If this schema were to be expanded, analysis of individual parts of the data would be possible.

<div class="page-break-avoid">

__D3.js__

Similar to Node.js, a requirement for the project was to assess the suitability of the D3.js library for the project. This was used to create the visualisation (see section 4.4), by manipulating SVG DOM elements based on data values.

</div>

The library was found to be a powerful and flexible tool for producing interactive, customised visualisations. As discussed in section 5.3.1, other chart creation libraries were tested, however, these could not be manipulated to create the specific visualisation required for the application. This was especially true when looking at interactive graphs, not just static, image-like graphs.

<div class="page-break-avoid">

__Unit Tests__

As discussed in section 5.2.1, unit testing was found to be extremely useful throughout the project. Unit tests were created in a TDD style approach, where tests were created before production code was written. This approach forces the developer to design each individual part of the codebase before it is written. Often potential bugs were thought through before they were created.

</div>

In addition, unit tests are very useful when refactoring code. This process of rewriting to produce cleaner, less buggy code could be considered "dangerous" in that it may introduce unexpected new bugs. However, by producing unit tests, this "fear factor" is much reduced, as a test that covers these bugs is extremely easy to run. This is unlike manual testing, which can be vulnerable to errors or incompleteness.

Future work in this area would include increasing coverage of several areas of the codebase. The search and data collection APIs have good coverage, however the visualisation and web application have poor coverage (see section 5.2.1).

<div class="page-break-avoid">

__PLOS Article Level Metrics API__

The PLOS ALM API is used to collect altmetrics data for a given set of articles. This API was chosen for the application for a number of reasons, discussed in detail in section 3.4.5. The main reason behind the decision is the historical metadata that is provided by this API. Other APIs do not give a full yearly breakdown that is key to the methods used to generate the visualisation.

</div>

However, there are some problems that had to be worked around for the application. At times, the API would produce error responses, especially if a large amount of articles were requested. This bug was reported to the API's issue tracker. It was found that adjusting the request would prevent this error response on most occasions, and so this was temporarily implemented into the application. It may be that these problems are related to scaling the API to provide large amounts of data. It is expected that as these problems are solved, the application will improve.

<div class="page-break-avoid">

__Experimental Nature of the Project__

As discussed in section 5.3.2, the project was under significant time constraints towards the end of the project, which lead to cutting some improvements from the application. This is due to the approach taken throughout the implementation phase. New technologies and techniques were tested to assess whether they were applicable for the project. This consumed more time than expected, and led to delays in the schedule.

</div>

It is expected that future work can build on this experimentation, and significantly improve the work. Improvements listed throughout this section can be applied, which are essentially ready for implementation, as they do not require assessment for suitability first.

