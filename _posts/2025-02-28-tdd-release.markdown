---
layout: post
title:  "TDD and Release Action"
date:   2025-02-28 13:14:22 -0800
categories: jekyll update
---
Disclaimer  
Article below is related to Java + SpringBoot ecosystem.

# Intro
In the previous (article)[https://ipeonte.github.io/jekyll/update/2024/11/11/tdd-and-microservices.html] I reviewed the concept of TDD and the importance for the stable project development progress. In this article I’ll show how the concept of TDD can be applied for release build.

# What is Release
Here I need to digress a bit from the topic and define what is meant by the term release. In the simplest sense, it is a set of services that are tightly or loosely connected to each other. A release can be schematically displayed using a UML packaging diagram (see Figure 1 below). Because microservices directly or indirectly depend on each other they all need to be tested together and deployed at the same time, all or changed ones. For simplicity, I do not include the data schema and configuration in the release. I will cover these topics in future articles.

![Figure 1: PetCorp Relase Packages](/assets/images/PetCorpPackaing.png)  
Figure 1: PetCorp Relase Packages

# Simple scenario
Back to action, as a developer, I have a task ,for example, to make changes into PetStore API Service. Following the TDD approach I create first the test for changes in a particular project, make sure the test fails, add implementation and check that test successfully passes. It would be OK but I also need to know that dependent service(s) are still functioning OK. One solution can be to run regression tests that are supposed to be published by QA but from my past I never saw such facilities existed. Another solution will be to ask QA to initiate a regression test but it can be costly. 

Some projects try making a shortcut and only test limited functionality that technical experts believe may be affected by change above. The smart project owner usually only trusts the result of QA tests and follows the simple decision schema (see Figure 2 below), i.e. only approves release after it passes the regression test. To complicate things the changes in business logic might affect the multiple services where not only requires to run costly regression tests but also the new test(s) that are related to the new business logic.

![Figure 2: Generic Relase Flow](/assets/images/ReleaseFlow.png)  
Figure 2: Generic Relase Flow

Before providing the solution to the problem above I’ll explain the prerequisite that needs to be added into microservice design to solve the problem above. It might sound strange and many times I’ve heard “Really, we need a design change(s) for TDD?” Here I can only repeat here statement from previous article 

    > Software that is delivered non-tested costs nothing – How are we supposed to test it?

# Scope of design changes
Coming back to the topic, the list of changes is very simple. Each microservice (or adapter) needs to be split into a “core” library with business logic, including networking layer (REST controllers, etc) and the wrapper with “spring-boot-maven-plugin” plugin that launches a standalone microservice process. The scope of changes for typical API microservice is shown on Figure 3 below.

![Figure 3: Typical MicroService Component Diagram](/assets/images/TypicalMicroServiceComponent.png)  
Figure 3: Typical MicroService Component Diagram

In the production system each microservice is deployed in a separate container which we can’t reproduce in maven test phase (embedded integration tests) but combining all “core” libraries under SpringBootTest “umbrella” is possible and can reproduce the production environment almost one-to-one. JUnit in this scenario is used as a global “launcher”. All API’s are running under the same Tomcat that is provided by SpringBootTest and also in the same SpringBoot Context. This requires a certain approach in API and microservice design (again) to avoid duplicate namespaces in API URL and in configuration, but it's common sense. The PetCorp test configuration is shown on Figure 4 below. Each “core” library, except UI, is pulled directly from artifactory (Nexus). The UI is pulled directly from a relative project but it can be stored in an artifactory as well. Configuration file qa.properties is common for all microservices and adapters. UI adjusted in a way that it always calls relative URLs instead of absolute ones.

![Figure 4: PetCorp setup for embedded integration testing](/assets/images/PetCorpTestComponent.png)  
Figure 4: PetCorp setup for embedded integration testing

# Design notes
UI tests based on Selenium WebDriver are the “icing on the cake” that are capable of executing any complex BDD scenario. Coming back to my task after I’ve made changes to standalone microservice (or set of microservices) and publish it with new version, 1.2.4, for example, all I need to todo is update the release configuration [here]( https://github.com/ipeonte/PetCorpKafkaDemo/blob/master/Release/pom.xml#L28). Change version of petstore-api.version to same 1.2.4 and execute the entire regression test alone or with changes related to new business logic just by running “mvn clean install”

The entire flow can be found in workflow configuration file that generously provided by Github Action facility [here]( https://github.com/ipeonte/PetCorpKafkaDemo/blob/master/.github/workflows/release.yml)

# Conclustion
Coming back to the topic I would like to highlight one more time that TDD is not a fashion or some “fancy way” of handling project management. These days TDD is simply necessary for any modern project to survive and evolve.
