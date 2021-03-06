<div class="page-break-avoid">

#### 3.3 Requirements

A major goal of the system is to provide tools for those investigating altmetrics changes over a period of time. The tool(s) must be usable by members of the general public, not just by altmetrics researchers. Finally, the project will assess the suitability of new technologies for use in the artefact (see section 1.2). To achieve this, there are many requirements that are described in this section. Each requirement is numbered, for referencing later.

</div>

<div class="page-break-avoid">

__Requirement 1. Visualisation of altmetrics data sources__

It was found in section 2.6 that more tools for analysing and visualising altmetrics data are required. In addition, it was found that validation of altmetrics is required for analysis to be accepted more widely. The system, therefore, must provide a mechanism for showing a visualisation of altmetrics data, with some validation of the data. The visualisation must allow for some comparison of article altmetrics.

</div>

As discussed in section 2.2, altmetrics assessment is often performed on a body of work, such as an author's career output. Therefore, the system must be able to provide a visualisation for multiple articles of interest. The system will generate a graph using altmetrics data from these selected articles. This allows the user to compare altmetrics data between these articles.

In addition, altmetrics researchers may only wish to focus on a subset of altmetric data sources (see section 2.6), or they may want to directly compare altmetric data sources. The system must provide a mechanism for this, by allowing the user to switch between altmetric data sources.

The visualisation must also show the total number of scholarly citations for the article, so that the altmetric data sources can be compared to a more traditional measure of impact. As discussed in section 2.6, citation counts are often used as a validation measure to compare against altmetrics data.

Finally, the visualisation must have a method for associating with the original article set, giving each article's title and final metric values. This allows the user to associate a paper with its visualisation.

<div class="page-break-avoid">

__Requirement 2. Visualisation of altmetrics changing over time__

As discussed in section 2.6, there has been little or no study of the temporal aspect of altmetrics. The application must provide a mechanism for showing potential changes in altmetrics data for a given article over a period of time. The previous requirement discussed the need for a visualisation of altmetrics data, and this visualisation can be extended to meet this requirement.

</div>

The system will provide an axis for the visualisation, representing the current time. The data values shown on the visualisation will be transformed according to this axis, so that the visualisation represents the current time period shown on this axis. For example, if this time axis shows the year 2009, then the altmetric data for 2009 will be shown on the graph.

The time-based axis will be interactive. The user will be able to easily change the axis, moving back and forward in time. This allows them to perform a comparison as the data changes over time. In this way, the recommendations in section 2.6 are achieved.

<div class="page-break-avoid">

__Requirement 3. Scholarly article search__

Users of the system must be able to find articles that they wish to visualise altmetrics for, therefore a search for scholarly papers is required. A search form must be provided for the user, to input parameters for their query. The form must contain fields including article title, author, journal and DOI. These parameters must then be sent to a database of scholarly articles, and matching papers found and returned as results. Search results must include article metadata, so that users can correctly identify articles of interest.

</div>

As discussed in section 2.2, altmetrics assessment is often performed on a body of work, such as an author's career output. Therefore, users must be able to identify multiple articles of interest that altmetrics data is to be generated for.

The search should return articles in an identifiable manner. Articles must be uniquely addressable, so that one article can be distinguished from another. In the academic fields this is achieved using the Digital Object Identifier (DOI) System, standardised under ISO 26324. It is practical to adopt this system for use in an academic application. Therefore, to conform with this standard, the system will return a list of DOIs that match the search parameters.

The search system will be released separately from the main artefact, and therefore must be developed with best practices. Search logic must be encapsulated within the search system, so that third party developers can reuse the code in novel ways.

<div class="page-break-avoid">

__Requirement 4. Altmetrics data collection__

As is evident in the previous requirements, altmetrics data is required for this system. This data could be generated by the system itself, by querying the APIs of the various services described in section 2.3 and building its own altmetrics data. However, as described in section 2.5, there are several technical problems with collecting this data. In addition, it would take time and resources to create this data that are unavailable for this project, especially for work that is somewhat out of scope for this project. There are existing altmetrics providers that have built their own data stores which can be queried to collect this data, each of which will be assessed for suitability with this project (see requirement 10).

</div>

The system must pass the DOIs of the selected articles to a provider API. The system must then provide a way of accessing data from these providers that is relevant to the selected articles. Finally, the system must be able to interpret their response for use in the visualisation. Errors in the response must be handled by the system, so that the application can recover.

The data collection system will also be released separately from the main artefact, as a module. Therefore, best practices for module development must be adopted. Data collection logic will be encapsulated within the module to create, so that it can be reused in other applications.

<div class="page-break-avoid">

__Requirement 5. Storage of results__

The system must provide a way of saving visualisation results so that users can revisit later. The visualisation will be created in such a way that results can be recreated using the same dataset. Therefore, storage of altmetrics data is required to enable users to revisit visualisation results. If no storage was provided, altmetrics data would need to be accessed from the provider every time the visualisation results were viewed. This creates unnecessary network traffic and would significantly slow down the application and increase bandwidth costs.

</div>

In addition, the system must provide a mechanism for accessing the stored visualisation results. This will be done using a permalink - a URL that will show the user the same visualisation, using the same altmetrics data every time the URL is visited. This allows users to demonstrate altmetrics impact to assessors without having to regenerate the data every time.

<div class="page-break-avoid">

__Requirement 6. Ease of use__

The secondary goal of the project states that the system must be easily understandable by users who are not familiar with technical altmetrics terms and those outside of the technology industry. The project is aimed at users in scientific fields and the general public, who do not have a background in computer science and therefore cannot be expected to understand complex systems or obscure vocabulary. The system must not be confusing to use.

</div>

As part of this requirement, the system must have good visual design. Scientific software is not known for its striking visuals, and therefore a well-designed, clean layout will attract users to the system, increasing its popularity.

<div class="page-break-avoid">

__Requirement 7. Open source__

As discussed in sections 2.4 and 2.6, transparency is a founding principle of altmetrics. The altmetrics community supports the use of permissive open source licences, as they allow users to inspect how metrics were collected and calculated. Ultimately, this transparency enhances trust in altmetrics. Furthermore, reuse of code could be beneficial to researchers investigating altmetrics. An open source licence allows researchers to reuse code, without having to obtain permission, and even develop it further by contributing changes back to the source.

</div>

For these reasons, source code for the system must be released under an open source licence, as defined by the Open Source Initiative (OSI). The OSI has several compatible licences listed on its website.

<div class="page-break-avoid">

__Requirement 8. Suitability of Node.js for this project__

The system will be created using the server-side JavaScript platform, Node.js. The project must evaluate whether Node.js is suitable for applications of this nature. Node.js was created in 2009 by Ryan Dahl, using the Google V8 JavaScript engine. It is an interesting new technology, but there is some debate about its stability and scalability.

</div>

For Node.js to be considered suitable, it must provide a platform that can be quickly and easily built upon to construct an application such as the one described in these requirements. The platform must enable applications to be well structured and efficient.

<div class="page-break-avoid">

__Requirement 9. Suitability of D3.js for this project__

Similar to requirement 8, D3.js will be used to provide the visualisation required for this project, and this JavaScript library will be evaluated to see whether it is suitable for creating visualisations. As described in requirements 1 and 2, a complex graph will be built using a large amount of altmetrics data. If D3.js can provide a stable and efficient platform for creating such graphs, then it can be considered suitable.

</div>

<div class="page-break-avoid">

__Requirement 10. Assessment of existing altmetrics providers__

As discussed in requirement 4, altmetrics data will be sourced from an altmetrics provider. The PLOS Article Level Metrics (ALM) API and the Altmetric.com API all provide access to this data, with varying levels of compatibility for this project. They will be compared to see which is the most suitable for this implementation.

</div>

The selected provider must include historical data - metadata on when altmetric citations occurred. For example, the API would provide a breakdown of each data source, showing the increase in altmetric citations for each year. This breakdown must be no longer than a year, otherwise any analysis would be too general to be useful. The system requires this to be able to show the difference in altmetrics data between time periods, as discussed in requirement 2.

In addition, the provider must be able to provide altmetrics data for a representative sample of articles. Altmetrics data can be time-consuming and resource-heavy to generate, and therefore it can be expected that not every article ever published will be covered by the provider. However, users must be able to access data for a reasonable set of articles.

Finally, the provider must offer data for a useful set of altmetric data sources. If a provider only tracks altmetric citations from a small group of data sources, then some diversity and context is lost from the analysis. As discussed in section 2.6, this is a core concept within altmetrics, so if significantly lost, the provider would become unsuitable for this project.

