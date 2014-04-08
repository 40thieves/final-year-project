#### 5.3 Evaluation

Chapter 1 introduced the concept of altmetrics, and pointed the to problem of a lack of research into how altmetrics changes over time. Chapter 2 gave a deeper look at why altmetrics are needed, what altmetrics are and potential problems with altmetrics. The chapter concluded that tools are needed to assist researchers in studying how altmetrics change over time. Chapter 3 described the design of such a tool, detailing the methodology and requirements of the project needed to develop this tool. A proposed solution to the problem was also described in this chapter. Chapter 4 described the implementation of this proposed solution, giving development details of how the artefact was created. Problems encountered during the implementation were discussed, and the solutions to these problems described.

Testing for this project was discussed in section 5.2, which demonstrated that the artefact meets some but not all of the requirements. Testing with altmetrics researchers found that the application fails to meet the main goal of the project, however with more research it could be improved or adapted to reach this goal. The application also has problems with some confusing user interfaces, but is overall effective at creating altmetrics visualisations and is easy to use. In this section, the implementation will be evaluated and issues in the underlying technologies that the implementation has highlighted will be discussed.

##### 5.3.1 Requirements Review

In this section, the implementation will be evaluated against the requirements (see section 3.3). Each requirement will be examined to identify the extend to which each has been achieved. In addition, a reflection on each requirement will be made, looking at their relevance for future work. Each of the requirements is reintroduced and discussed in turn.

###### Requirement 1. Visualisation of altmetrics data sources

This requirement describes the need for a visualisation that will allow for comparison of altmetrics data sources for a set of scholarly articles by altmetrics researchers. Section 2.6 found that research is often performed by comparing altmetrics data sources against each other and against traditional citation counts. This must be facilitated by the artefact. The visualisation must have some mechanism for associating an article with it's visual data.

The application achieves all significant details of this requirement. The AlmChart library produces a visualisation that enables researchers to compare altmetrics data sources for a number of different articles quickly. The data sources used by 

##### 5.3.2 Artefact Review

In this section, we evaluate the implementation of ... and review the prevailing issues that were highlighted by the implementation, which were ...

* Search
	* localStorage can be confusing
		* Especially when going back and searching for different articles once some articles are selected
			* On second search, it would appear that previously searched articles would be returned by the search results, when they're not they are just appended because they are stored
		* Usability test
		* Users not familiar with localStorage concepts - usually have to submit something for storage to take place
		* Should have split stored articles into a separate area - less confusing
	* Could be improved by having visualisation build on side of page, when articles are selected
		* Then change submit button to save button, to save data to db
		* Improves on confusing UI
* Data collection
	* Problems with API
		* Sometimes produces errors if specific data sources are not requested
			* Adding data sources seems to work fine, even if large list is requested
				* _Not good for API, however heavy usage is not expected_
				* May have improved - was there a blog post (or github issue) about how they're going to improve it?
* Use PLOS Search API
	* Can only search for PLOS articles
		* Large sample
		* Maybe biased?
			* Open access
			* Free - higher metrics
		* However because they're all open access, would be normalised/accounted for
	* Don't return HTTP error codes
		* Errors have to be detected in the response (which is 200 OK)
	* Search results not paginated
		* What happens if you put in something really generic & get lots of results
* Async
	* Difficult
	* "Pyramid of doom"
* PLOS ALM API
	* Returned errors when calculating large amount of data
		* Seemed to fix by requesting specific data sources
* Accessibility