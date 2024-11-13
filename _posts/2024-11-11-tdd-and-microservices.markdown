---
layout: post
title:  "Test Driven Development and Microservice architecture."
date:   2024-11-11 10:27:17 -0800
categories: jekyll update
---
Disclaimer  
Article below related to Java + SpringBoot ecosystem.

# Intro
Test Driven Development (**TDD**) is a part of “Extreme Programming” concept that was published in distant 2001 [[1](#ref_1)]. It's based on the concept of writing unit tests ahead of writing business logic. Monolithic service architecture prevailed  at that time so unit could be anything, from single classes until modules but it was still the part of single application. Writing unit tests was just a matter of defining logic boundaries and writing tests against that covers functionality in those boundaries.

Many changes have happened since that time in the IT industry. Microservice architecture drops monolithic approach in favor of small services that are easier develop and manage. Spring framework introduced Dependency Injection (DI) pattern and SpringBoot framework introduced SprintBootTest library where entire service can be started by JUnit and running in test Spring context. All those changes made the process of writing unit tests inefficient and replaced it with an embedded integration test (EIT) approach where the entire service can be “black box” tested based on original specification.

# How it works.
Microservice architecture is similar to “virtual” 3D brick wall where each service is related to another by invoking an API with a pre-defined set of parameters (See Figure 1 below). All API’s that are published by each service is the “glue” that links all services together in analogy with mortar layer that glued all bricks in real wall.

![Figure 1: Simplified Microservice Architecture](/assets/images/MicroserviceArchitectureLayer.png)  
Figure 1: Simplified Microservice Architecture

In contrast with the real brick wall in “virtual” one, each microservice can be extracted, enhanced, tested upside down and inserted back. Like testing microchips in a test harness (see Figure 2 below). The tests must challenge each declared API with a set of parameters and check output against expected. This is what SpringBootTest allows to do out of the box, lots of examples and manuals can be found on the Internet. All the developer needed is to write API tests before actual service is written and then follow the other TDD principles.

![Figure 2: Test harness for microchip](/assets/images/MicroChip.png)  
Figure 2: Test harness for microchip

# Testing microservice with internal dependencies
As usual, not everything is so simple in real life. The approach above works well for simple microservice that has no internal dependencies, for example service that multiplies A and B. For services that have dependency, for example database or messaging system, the implementation of TDD approach might be a challenging task. To cover such scenarios the Spring community develops lots of embedded modules, for example in-memory SQL database H2 [[2](#ref_2)] and embedded kafka [[3](#ref_3)] and so on. Worst case scenario when no “out-of-the-box” solution is available the simple emulator can be written that only simulates the functionality required by service and “auto-wired” with DI pattern provided by Spring. For example the JPA interface provided by NoSQL database can be implemented to store data on local disk, index by required JSON fields and return by request.

# Testing Microservice with external dependencies
Emulation approach described above also works when functionality of 3rd party service needs to be simulated. Additional @RestController needs to be added to the test source where the required functionality should be implemented. The configuration needs to be created to allow replacing external service call with local one, i.e replace https://example.com/abcd with just /abcd. 

In the future articles I’ll explain how to test external dependencies that are part of domain services.


# Mock is the devil.
The most common response I’ve heard from colleaguess when developing and implementing the TDD approach was *“Why do we need such complexity ? Why just use a mock and quickly finish the task ?”*. Yes, sure, a quick and dirty approach works when creating something simple, like a calculator app. For big projects it’s the dead end.

The advantage of using Mock is a single and it’s huge – it is fast. Every IT manager likes it a lot. However the list of shortcomings is off the charts. It’s only works on input data that defined by developer, the testing code is complex and not human readable, it needs to be re-written when service refactored, quality of test depends of qualification of developer and hard to validate … but all together they cannot beat the single Mock advantage until project comes to QA phase.

Badly dev-tested project will guarantee generate multiple bugs during QA. Without a properly implemented TDD approach and regression test suite in place it is very common situation when fixing one bug produces another one or reopens existing. Such cycles can be infinite, I have seen it many times. In general, without TDD, the dependency between overall testing time that is needed by the developer to finish “feature” implementation and code complexity is similar on Figure 3 below. After certain complexity the project simply “hits the roof” because implementing any feature and following multiple QA iterations takes so much time that development simply stops. Very often management simply halts such projects due raising cost of development.

![Figure 3: Relationship between testing time and complexity without TDD](/assets/images/Complexity-Time.png)  
Figure 3: Relationship between testing time and complexity without TDD

With proper TDD implementation the relation between testing time and complexity is mostly flat (see Figure 4 below). It gives more/less predictable time on each feature development and allows delivery of projects in time and with decent quality, sometimes even bug free.

![Figure 4: Relationship between testing time and complexity with TDD](/assets/images/Complexity-Time-Flat.png)  
Figure 4: Relationship between testing time and complexity with TDD

Summarizing all above, the good development practice these days is to exclude (or even ban) Mock completely from functional tests and replace it with embedded integration tests.

# From TD-Development to TD-Design
**Software that delivered non-tested cost nothing.** Not only software, I think it's the universal rule for everything. To prove it, anyone can answer a simple question: *“Would you agree to purchase a car that was assembled and delivered right out of the factory door but never driven?”*  &nbsp; I’m personally not. Even with assurance of technician that every bolt/nut/cable installed according to specification. I don’t care, just drive one test cycle and I’m good, I don’t need any assurance.

TDD concept asks to write the tests before actual logic implementation and it immediately raises the question “How to test it?”. From my past experience this question in 99% of the cases driving design changes and very often it receives negative response from non-experience architects and co-workers. To support the design changes start from the beginning. **Software that delivered non-tested cost nothing – How are we supposed to test it?**

# TDD expansion. BDD and DDD
Adding ability test individual service in the test Spring context expands TDD into a new universe. Embedded integration tests can not only test single API but also test entire user stories. Such an approach called Behavior Driven Development (BDD). The example testing multiple API’s that change pet vaccinate status can be found [here](https://github.com/ipeonte/PetCorpKafkaDemo/blob/master/PetStoreDemoApi/PetStoreDemoCore/src/test/java/com/example/demo/petstore/rest/test/PetStoreDemoRestStoryTest.java).

Another extension of TDD is Domain Driven Development (DDD) when multiple services that are related to a single domain are tested together. The example of DDD where two API services, Kafka message transfofrm adapter and UI tested together can be found [here](https://github.com/ipeonte/PetCorpKafkaDemo/blob/master/UiTests/PetStoreUiTestShared/src/test/java/com/example/demo/petcorp/ui/web/test/AbstractPetStoreDemoUiTest.java).

# TDD neglection
With all advantages of TDD there is a reasonable question why it’s very often neglected and/or ignored? I’m stating this based on my past 10 years of working with SpringBoot on multiple projects, hoping general statistics is better. The answer is that shown on Figure 4. TDD requires big initial efforts that bring more cost on the beginning of the project (shift left), and no manager like that, in comparison to standard approach where expenses related to QA moved to the end. 2 factors needs to match in order for TDD to be implemented successfully:

- the necessary qualifications of developer to properly quote and implement TDD
- understanding of management the importance of TDD approach for successful project delivery.

2 factors above are unrelated so total probability of TDD to be successfully implemented is P = (A) * (B). Unfortunately with low A and B the P is somewhere around zero which explains the subject of this section.

# Summary
TDD is a great concept that allows transforming the routine and sometimes boring process of software development into art. And like any art it has a cost and requires understanding of the process. And like any art, when  done well, it rewards participants with amazing results.

## References
1. <a name="ref_1"></a>Lee Copeland (December 2001). "Extreme Programming". Computerworld. Archived from [the original](http://www.computerworld.com/softwaretopics/software/appdev/story/0,10801,66192,00.html) on June 5, 2011. Retrieved November 11, 2024.
2. <a name="ref_2"></a>Maven repository for H2 Database Engine https://mvnrepository.com/artifact/com.h2database/h2
3. <a name="ref_3"></a>Maven repository for embedded Kafka https://mvnrepository.com/artifact/embedded-kafka/embedded-kafka
