---
layout: post
title:  "TDD in Action"
date:   2025-03-20 11:15:03 -0800
categories: jekyll update
---
Disclaimer  
Article below is related to Java + SpringBoot ecosystem.

# Intro
In the previous (article)[https://ipeonte.github.io/jekyll/update/2024/11/11/tdd-and-microservices.html] I reviewed the concept of TDD (Test Driven Development) and the importance for the stable project development progress. It was lots of theory and talks but in this article I'll show the practical implementation of TDD i.e. 

    > TDD in Action

# Start Point
The start point is API. To be exact the start point is real life which provides the requirement on which API is based on. The first phase is create test per API that works on isolated test of data. The test of "Create" API is straightforward i.e. call API -> check the result. The Get, Update and Delete API requires some data to be present and it's usually happened in @BeforeEach method. The test sequence is straigh forward as well, i.e. Populate Data - > Execute Update -> Validate Result. At the end of each test the data set needs to be restored back to original to avoid any interweaving between tests. The example of initial TDD suit can be found [here](https://github.com/ipeonte/PetCorpKafkaDemo/blob/master/PetStoreDemoApi/PetStoreDemoCore/src/test/java/com/example/demo/petstore/rest/test/PetStoreDemoRestApiTest.java)

# BDD
BDD is related to "Behavior Driven Development". It's basically testing the story, for example add -> update -> delete or any other level of complexity. The example of BDD can be found [here](https://github.com/ipeonte/PetCorpKafkaDemo/blob/master/PetStoreDemoApi/PetStoreDemoCore/src/test/java/com/example/demo/petstore/rest/test/PetStoreDemoRestStoryTest.java)

# Framework
There are a lot of different test frameworks for API testing. This article is not for framework review but from my past experience the best result can be achieved based on custom code with TestRestTemplate and @SpringBootTest. The advantage of @SpringBootTest that it can load all beans into the test SpringContext and also start Tomcat on a random port. The advantage of TestRestTemplate that it can automatically link to the random port where test Tomcat started. Custom code gives the ability to dynamically generate input data and validate results. The validation aspect of such approach will be revealed in the next section.

# TDD Validation
With all the discussions and examples above, a reasonable question arises "How can we be sure that everything is thoroughly tested and nothing is missed ?" The answer is the "test coverage" result. General requirement for test coverage to be 80% of overall code but for TDD the requirement is different. It needs to be 100% or the result is not trusted. Sounds a little cruel for average app but for certain industries, like power generation, medical equipment of military it's the usual requirement. 

    > The test coverage for TDD needs to be 100% or the app is not trusted. And yes, no Mockito. Mock is the devil

There is a simple explanation of the requirement above that relates to the definition of perfection. If all line of code is tested and nothing left aside then it means all test scenarios are covered, including error one's. If any test scenario is left untested then test coverage will be less than 100% and extra tests need to be added. If any dead code present the test coverage will be less then 100% again and that dead code needs to be simply removed. In case when all test scenarios covered and no dead code present then the test coverage will be exactly 100% which means the app is perfect. From my past experience the hardest part is cover error scenario and in next article I'll explain some approaches.

# TDD KPI (Key Performance Indicator)

As I explained above the TDD quality is measured based on test coverage and one of the best plugin for that is [JaCoCo Maven Plug-in](https://www.eclemma.org/jacoco/trunk/doc/maven.html). It's also very easy to integrate with CI/CD (Maven) pipeline and if test coverage < 100% the build pipeline should be stopped. The simple configuration for JaCoCo can be found [here](https://github.com/ipeonte/PetCorpKafkaDemo/blob/master/pom.xml#L69)

# Conclusion
Time spent on creating tests according to TDD concept is not much higher or even the same as it would be spent on creating standard "Mock" tests. But the advantage is incomparable - the TDD allows creating bug free code which pays back very quickly.

