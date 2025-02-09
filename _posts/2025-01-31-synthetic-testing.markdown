---
layout: post
title:  "Synthetic Testing (Monitoring)"
date:   2025-01-31 20:11:17 -0800
categories: jekyll update
---
Disclaimer  
Article below is related to Java + SpringBoot ecosystem.

# Intro
Synthetic Testing (also known as Synthetic Monitoring) is very interesting concept originally introduced by Mark Fowler [[1](#ref_1)]

The idea is very simple. There is only one method to 100% prove that the system is working fine, this is to run a business process
(for example placing the order) on a production system. Any other type of environmental testing, for example PRE_PROD or UAT,
gives results with very-very low (or ZERO) trust. Figure below shows the base principles of  Synthetic Testing.
It is performed at certain intervals, can be used to check system performance during inactivity period and also used as a “smoke test” after system upgrade.
Sounds like killing ~~app~~ feature for any system.

![Figure 1: Sequence diagram for Synthetic Test Activity](/assets/images/SyntheticTest.png)

# If Synthetic Testing is so good then why …
Why is it not widely used, like JUnit, for example? Because:

- It's required extra work and everything that required extra work (except JUnit), even embedded testing (see [here](https://ipeonte.github.io/jekyll/update/2024/11/11/tdd-and-microservices.html), is not widely used;
- It's required special handling to avoid accidentally making unwanted changes to the system. For example placing a test order might cause money balance, inventory and other places to be changed that would be very hard to correct.

# Major requirements to Synthetic Testing
Taking all above into consideration the requirements are next:

1. To replicate as closely as possible a real life business process.
2. Be invisible to the public, i.e. do not leave anything at all that could allow the public to detect that synthetic testing is being performed.


# Can something else be used instead to make it cheaper ?
Short answer is No 

Every system indicator, like CPU or bandwidth, might show OK, no errors in a log, no crushes but some services might still not work for the public
and it will only be revealed when someone complains (if they complain at all). The worst case scenario the system without Synthetic Tests setup can be down for a long period of time.

# Demo
It’s better one time to see than read long articles so below some simple demo that based on [PetCorpKafkaDemo project](https://github.com/ipeonte/PetCorpKafkaDemo).

# The Story
The screenplay for most longest (and critical) business story is next

ADMIN  
Add new dog without vaccination
Check it visible for Admin
Change vaccination status so dog became visible to public

USER  
Find new dog and ask for adoption

ADMIN  
Change dog status to “adopted”

USER  
Check that adopted dog no longer visible to public

ADMIN  
Check that dog is assigned to user by background process

SYSTEM  
Remove everything that was done above
 
# The demo scripts for synthetic testing of the Story.
The curl and jq system utilities are used to execute each step from the story by calling API.
The entire script can be found [here](https://github.com/ipeonte/PetCorpKafkaDemo/blob/master/scripts/synth_test.sh).
The test script is not 100% synthetic test because it's not using UI but it is still valid because it executes the same API as UI does.

Some example for curl and jq commands shown below

Login
```shell
curl -c sso.txt -X POST -d "username=admin&password=password" http://localhost/login
```
Make POST request with JSON body and SSO token
```shell
curl -s -b sso.txt -o - -X POST -H "Content-Type: application/json" -d '{"name":"Test","sex":"Male","vaccinated":"N"}' http://localhost/api/v1/admin/pet?st > new_pet.json
```
Make GET request with SSO token and parse json request
```shell
curl -s -b sso.txt -o - http://localhost/api/v1/admin/pet/3?st | jq -r ".id"
```
# Conclusion
Answering the typical question that is usually asked when referrals are checked - would you ~~hire~~ use this guy - I would answer definitely yes.
Yes, the synthetic tests could be tricky to implement, yes it involves the cost, BUT in order to finish the project in reasonable time the implementation of
Synthetic Testing is mandatory. Just before start implementation find the answer for the simple question "How to run a production smoke test after any update?"

# References

1. <a name="ref_1"></a>Fowler, Martin. Synthetic Monitoring. https://martinfowler.com/bliki/SyntheticMonitoring.html
