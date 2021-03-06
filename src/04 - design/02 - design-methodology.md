<div class="page-break-avoid">

#### 3.2 Design Methodology

The design and development of the system was supported by the methodology described in this section. The methodology is based on an agile approach that is similar to the Scrum approach.

</div>

Initially, the project took a waterfall approach during the research and initial design phase. This methodology takes a sequential approach where tasks are ordered and one task cannot start before another. One of the benefits of this methodology is that design must be done first, leading to greater efficiency later. It is useful in this situation, as there are few specific requirements early in the process, and research is required before design can start. Using this research, decisions about the direction and requirements for the system will be made, decisions that drive the design. The design of the system changed several times during the research phase, as more was discovered about the field of altmetrics. Research continued throughout the project, continually feeding into the design of the system.

After this phase, the agile approach was adopted, where a prioritised list of tasks is created based on the system requirements. These are then split into short "sprint" periods where focussed work on the assigned tasks was completed. During each period, planning for the task, design of the implementation, and finally the actual implementation are performed, creating a working iteration of the final product. This methodology is useful as it is good at adapting to change in the design and requirements. As change is encountered, the list of tasks can be modified, and the currently assigned task can be redesigned to fit with the change.

Within individual sprints of this agile phase, implementation of the final product is completed. To ensure that changes to the code base do not affect previous iterations, testing was performed. A Test Driven Development (TDD) approach was adopted, where unit tests were written before production code was written. This approach drives design before implementation by forcing consideration of the final result and of any edge cases before any implementation is attempted. It also encourages a modular design, as tests can be written for smaller parts easily and reused elsewhere. Finally, TDD encourages refactoring of poor or outdated code, as it greatly reduces the fear that rewriting the old code will introduce new bugs. The approach is increasingly becoming popular because of these attributes.

