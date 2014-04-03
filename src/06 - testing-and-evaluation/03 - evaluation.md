#### 5.3 Evaluation

Chapter 1 highlighted the problem of ... . Chapter 2 reviewed the state-of-the-art in ... . Chapter 3 identified a set of technical requirements underpinning the development of ..., and the implementaton of a ... was described in Chapter 4.

The testing was described in Section x demonstrates that ... . In this section therefore, we evaluate the implementation and discuss issues in the underlying technologies that the implementation has highlighted.

##### 5.3.1 Requirements Review

This section reviews the implemented platform, referring back to the requirements to identify the extent to which each has been fulfilled, and reflecting on their relevance for future work. Each of the requirements is reintroduced and discussed in turn. 

<!-- Refer back to each specified requirement and discuss -->

##### 5.3.2 Artefact Review

In this section, we evaluate the implementation of ... and review the prevailing issues that were highlighted by the implementation, which were ...

* Use PLOS Search API
	* Can only search for PLOS articles
		* Large sample
		* Maybe biased?
			* Open access
			* Free - higher metrics
		* However because they're all open access, would be normalised/accounted for
* Async
	* Difficult
	* "Pyramid of doom"
* PLOS ALM API
	* Returned errors when calculating large amount of data
		* Seemed to fix by requesting specific data sources