#### 5.2 Testing summary

In Chapters 3 and 4, an application was described that is designed to assist researchers examine how altmetrics change over time. In this section, the methods by which this application was tested are described. Three major tests were performed; section 5.2.1 describes the unit tests that were created during the design and implementation phase. Section 5.2.2 describes the usability tests that were performed after the application was completed. Finally, section 5.2.3 discusses the methods and results of further usability tests and acceptance tests from a sample of altmetrics researchers.

##### 5.2.1 Unit Testing

Section 3.2 describes the Test Driven Development (TDD) approach taken throughout the project. This approach focusses development on testing, creating a through design mentality. The approach involves designing and creating a test for each individual feature of the code before the code is implemented. The focus on testing each individual feature of the code is called unit testing. Unit testing, therefore, can be considered a form of functional testing where verification of specific actions is performed. In some extreme cases, each line of production code is tested using a unit test, although this is obviously impractical for most applications.

Once the test is created, the test is run, to ensure that the test will fail. If the test does not fail, then the feature already exists, or alternatively, the test is defective. The feature is then implemented, according to the parameters set by the test. The test is run again, and the feature's implementation must pass the test.

This approach forces the developer to consider the design of the implementation, as well as any edge cases that could break this design before any production code is written. This design-heavy mentality was found to be extremely beneficial when creating production code, as potential problems are considered ahead of time and factored into the implementation.

Another benefit is that once tests are created, refactoring poorly written or inelegant code becomes much easier. Without unit tests, refactoring is dangerous and time-consuming as newly written code has to be tested manually to ensure that it still produces the expected behaviour, and no new bugs were introduced. This often takes time and is prone to error, as it is easy to miss implementation details when performing tests manually. With unit tests however, the "fear factor" of potentially breaking working code is removed, leaving the developer to focus on improving the code during a refactor. For this reason, TDD reduces the imperative that production code must be perfect first time. Revisiting the code later is acceptable as long as tests are available. This is one of the main reasons why TDD has been adopted into the agile development movement.

However, unit testing is not without its problems. The main issue encountered when following the TDD approach is that, while refactoring production code is encouraged through the use of tests, refactoring tests, or refactoring output that the tests are validating against remains potentially dangerous and time-consuming. Making large changes to the underlying concept of the feature or changing the output of a feature still needs to be tested manually, as the tests will need to be rewritten to accommodate the change. For example, the altmetrics data collection API wrapper was modified after tests were written, entirely changing the structure of how the wrapper returned results (see section 4.2.2). This required each test to be converted to the new structure, potentially losing some test coverage. Unfortunately, there is no good solution to this problem. Restructuring a feature entirely should be treated as a rewrite, and thus, tests should also be rewritten. Maintaining support for previous details of the implementation between these modifications remains part of the developer's duties.

For TDD to take place a testing infrastructure must be created. For this project, an assertion library, that will allow assertions about output to be validated within a test; and a test runner, that will run the supplied tests within its environment. Section 3.4.10 discusses the usage of the Mocha and Chai libraries for this purpose.

The Mocha library is a test runner that will run tests within an environment, reporting the results back to the user. These results can be in the form of printing to the command line (by running the `mocha` command) or creating a web page showing the results. A test is considered to have failed if an unexpected error is thrown at any point during the test. Expected errors must be handled by the test for the test to pass. The following code snippet shows a Mocha test used in the altmetrics data collection wrapper.

```js
describe('ALM', function() {
	describe('General fetch', function() {
		it('should take a DOI as the first argument', function() {
			// Test code
		});
	});
});
```

Mocha tests are structured so that tests can be grouped according to what they are testing. The `describe()` method is used to group tests in this manner. Blocks can be nested up to two layers, each layer increasing in specificity. In the above example, the first `describe` block will group all tests for the altmetrics data wrapper. The second block will group all tests that relate to the fetching functionality of the module. Other tests at this same level include tests for accepting options and error handling tests.

Within each of these blocks a series of tests can be run. These are declared as callbacks to the `it()` function. This function takes a human readable description of the test as the first argument, which will shown to the user if a test fails. If set up or tear down is required, Mocha provides `before()`, `beforeEach()`, `after()` and `afterEach()` functions for this purpose.

Some tests require the environment to handle asynchronous behaviour. Both the API wrappers use asynchronous calls, and would fail their tests as Mocha will time-out the tests after 200 milliseconds. Responses from the respective APIs take much longer than this. Tests for these libraries must therefore must use the `done()` callback that is provided by Mocha. Once a test is considered to have been completed asynchronously, the `done()` method should be called, ending the test.

The Chai assertion library provides assertions to the testing environment. Assertions are the core of testing logic.They provide a mechanism for the developer to add predicate (true or false) statements that should always be true. If not true, an error will be raised. Chai's API is very readable, often considered a best practice for testing, as discussed in section 3.4.10. Sentence-like assertions can be created using the `expect()` method and the library's "language chains".

Coverage, a term often used in relation to testing, refers to the amount of production code that is "covered" or tested by test code. It is used as a metric for judging the quality and extensiveness of test code. Coverage of the altmetrics data collection and search wrappers is very good, with a large percentage of production code tested. The code coverage tool Istanbul was used to calculate coverage metrics for both modules.

| Module                     | Statements | Branches | Functions | Lines  |
| -------------------------- | ---------- | -------- | --------- | ------ |
| Altmetrics data collection | 93.85%     | 84.85%   | 100%      | 93.85% |
| Search                     | 96.97%     | 90.48%   | 100%      | 96.97% |

Coverage of the web application part of the code base is poor. Time restrictions meant that few tests were written while developing this part of the application. However, this may not seriously affect the project, as much of the functionality of the web application extends the well tested Express framework, or Mongoose object-model map.

One area that may be affected is the visualisation library, which was not extensively tested. This is because the library developed from experimentation with the D3.js library, rather than as a specific project designed to meet the requirements. Testing after a large amount of production code has been created takes time, and is more prone to errors. However, if the library is to be released for usage by third parties, unit tests should have been created.

An analysis of the results of unit testing is difficult, as the tests were developed to assist during the implementation phase. They are not designed to evaluate the application against the requirements. By following the TDD approach, tests were written before implementation, so production code can be created to pass the tests. Production code is not considered to be complete until the tests pass. Unless tests are incomplete - see above for discussion of code coverage - no unit tests will fail - the TDD approach guarantees this.

##### 5.2.2 Usability Testing

A usability test was performed to analyse how well the application meets requirement 6 - the application must be easy to use (see section 3.3) - and the larger project goal of usability. A small sample of users were given a task to perform using the application. The application was running on a locally installed server, using a development machine. They were then observed during the completion of the task. Task performance was evaluated for speed, "easiness" of the task and comprehension of steps taken to complete the task. After observation was completed, feedback was gathered. This gives a qualitative assessment of the application.

Volunteers were sourced from friends and family, to create a sample of 10 users. This is a fairly small sample size, and the sample was not randomised, meaning that some bias may be introduced. To mitigate this risk, users were shown the application for the first time during the test, and were instructed to give objective feedback. Observers who were familiar with the system were discouraged from interfering with the users. This prevents biased results, caused by a "guided walkthrough" of the system, rather than a true usability test. None of the users in the sample are involved in altmetrics, and therefore the result produced by the visualisation could not be analysed. Instead, usability of the application was assessed.

The task set for each user was to create a visualisation for a given set of articles. This task was chosen because it requires the use of every part of the application, from the use of the web application to use the search form, then the search module to find the articles in question, the data collection module to fetch altmetrics data, the database to store this data, and finally, the AlmChart library to view the visualisation. This task also closely matches an ultimate goal of the project - to create tools for altmetrics visualisation - and therefore provides a useful test. To complete the task users were given a list of article authors and titles that were to be included in the visualisation. Most of these articles could be found in a single search, however, some required the user to make usage of the storage of articles (see section 4.6.2), and create a new search to add articles to the list of selected articles.

After the task users were asked several short questions to determine whether they understood how the application worked. They were asked a range of questions including if they understood that articles were stored when selected and that the generated URL would act as a permalink for the current visualisation. In addition, they were asked whether they understood the temporal aspect of the visualisation and if they were able to compare different altmetric data sources using the selection boxes. Finally, they were asked if they had any suggestions for improving the application.

Results of this test were mixed. All 10 users in the sample were able to complete the task relatively quickly. However, most users were confused by the storage of selected articles.

The average time taken to complete the task was 2 minutes 11 seconds. The fastest time was 1 minute 39 seconds, and the slowest 3 minutes 40 seconds. These results show that the application can be used reasonably quickly to produce a visualisation. Most users found that searching for an article and generating its visualisation was simple. 7 of the sample responded that they understood that the generated URL would act as a permalink for the current visualisation. These results are encouraging, as they suggest that the core usability of the system is good.

However, nearly all users struggled to understand the article storage concept. 2 members of the sample asked the observer for assistance in completing this part of the task. All users undertaking this task took longer to complete than those completing the simpler task. This suggests that the interface for selecting articles must be improved to reduce confusion.

##### 5.2.3 Acceptance Test

In addition to the usability test described in section 5.2.2, an acceptance test was performed on a number of members of the altmetrics research community. This test involved setting up the system as a live web application and letting researchers perform their own tests. Researchers were then contacted by email to discuss the artefact and gather feedback. This approach cannot accurately be termed a "test", as the researcher's testing was unstructured and unsupervised. However, this was unavoidable, due to time and distance constraints.

The sample size for this test is very small, with only 3 researchers participating. There are several reasons for this; the first being the overall size of the altmetrics research community. Altmetrics is a relatively new field of study, and has yet to gain widespread interest from the larger population of researchers. In addition, spreading word to other altmetrics researchers is somewhat difficult, especially from a previously unknown and untrusted source. Despite these difficulties, three respected scholars were able to provide feedback.

Unfortunately, the results of this test were poor. The researchers found that the visualisation was a good method for comparing altmetrics data sources against each other and against traditional citation metrics. However, all three researchers criticised the use of interpolation on the visualisation, stating that the model used to calculate interpolated values is assumed and has little evidence to support it. They pointed out that no research has been done that validates the linear interpolation model used by the application, and thus the visualisation has reduced scientific value. This result means that the application fails to reach its primary goal - to assist in developing the study of altmetrics and how they change over time.

However, it is possible to reach some positive conclusions of this test. The researchers suggested that the visualisation tool may be useful as a starting point for this research. A lack of evidence supporting the interpolation model does not necessarily disprove the model, it only requires more study.

There are other positives to take from this test. The application was commended for its ease of use, and openness. Several comments believe that the system's simplicity will be beneficial to the altmetrics movement, as anybody with an interest in the subject can get an understanding very quickly. They found that it was useful for comparing different data sources, if the interpolation was ignored.

In addition, the open nature of the project was praised, with all three researchers commenting that the approach is well suited to the altmetrics community. The permissive license used for the application means that the problems raised above could potentially be fixed, through research, and patches contributed back to the project. In addition, the modularly of the application means that individual parts, such as the altmetrics data collection and search API wrappers, could be reused in other altmetrics research.

