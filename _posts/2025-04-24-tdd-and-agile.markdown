---
layout: post
title:  "Agile, TDD and The Zen"
date:   2025-04-26 11:15:03 -0800
categories: jekyll update
---
Disclaimer  
All conclusions in the article are controversial and are based solely on the author's 25 years of experience in the software industry.

# Agile is great
These days the Agile Methodology has completely replaced the Waterfall approach for project management and there are many reasons for this. Agile is flexible, incremental, has continuous delivery which increases client satisfaction, etc. However nothing in this life comes easy and there is a cost for all those nice features.

# Agile is great BUT ...
The major disadvantage of Agile (in my humble opinion) is constant refactoring that can lead to a situation when the project falls off the track completely. Let me explain it on the simple example:

Spring #1

Requirement: Add functionality A  
Implementation: Add functionality A

Spring #2

Requirement: Functionality A changed and added functionality B.  
Implementation: 
- Adjust functionality A.
- Add functionality B

Spring #3

Requirement: Functionality A removed, functionality B changed and added functionality C  
Implementation: 
- Carefully remove functionality A 
- Adjust functionality B.
- Add functionality C


If no proper handling done with documentation and correspondent test facilities (and this it what I usually seen) the new developer that comes at Spring #4 will be facing the challenge of maintaining some dead code remaining from logic A and understanding what B is about since it no longer corresponds to initial requirements.

Multiplying the scope of changes above on 10 creates a mess, multiplying on 100 creates a huge mess, than epic mess, than legendary and after a certain “iteration” the project might simply be stuck in an infinite bug fixing loop when each fixed bug opens another one or introduces the new bug.

![Typical situation when fixing bugs in legacy system](/assets/images/What-A-Mess.png)

I would say even more based on my past experience. Once the developer starts typing the first lines of code the all previously created documentation can be declared obsoleted. The only “source of truth” in any more/less mature project is source code where none except a few technical senior people can understand the current business logic.
 
    > Once the first lines of code typed the all documentation can be solemnly announced obsoleted

# So what should we do ?

The only way to neglect the chaos that created by constant Agile refactoring is to establish proper TDD facility and sync. all changes for business logic with appropriate tests. TDD brings balance back to the project and at the same time it’s became the new documentation source. In simple allegory the successful project is the one that exists in the constant struggle of opposites i.e. chaos vs order or Agile vs TDD.

![ChaosVsOrder](/assets/images/AgileVsTdd.png)

# How much it cost ?
For example, with TDD approach the steps for the example above will be the next:

Spring #1

Requirement: Add functionality A  
Implementation:
- Add tests for A
- Added functionality for A
- Ensure test coverage is 100%


Spring #2

Requirement: Functionality A changed and added functionality B.  
Implementation: 
- Adjust tests for A and ensure new functionality fails.
- Adjust functionality A and ensure that all tests for A pass.
- Ensure test coverage is still 100%
- Add tests for B
- Added functionality for B.
- Ensure test coverage is 100%

Spring #3

Requirement: Functionality A removed, functionality B changed and added functionality C  
Implementation: 
- Remove all tests for A.
- Check and remove all dead code that should be related to A.
- Ensure test coverage is still 100%
- Adjust tests for B and ensure new functionality fails.
- Adjust functionality B and ensure that all tests for B pass.
- Ensure test coverage is still 100%
- Add tests for C. Added functionality for C.
- Ensure test coverage is still 100%

As it is obvious from the example above the TDD has certain overhead (and sometimes it is big) for each Sprint but at the end of each iteration the source code is bug free. Also it is easy for any new developer to jump in and catch up quickly without worrying about accidentally damaging any existing business logic.

# Summary
Summarizing all above, the TDD methodology is an only way to order the chaos that Agile creates and it will allow the project to develop steadily in any conditions. It's comes at cost but it pays off very quick.
