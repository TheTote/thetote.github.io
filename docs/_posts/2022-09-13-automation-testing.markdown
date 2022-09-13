---
layout: post
title:  "Automation Testing – service level and broader scoped testing"
date:   2022-09-13 09:00:00 +0100
categories: Automated Testing
---

Here at the Tote, we utilise a wide variety of services and technologies to allow for the day to day running of the company. These projects are built up of a lot of microservices and code stacks that are constantly developing and improving. As services get more complex and detailed, we need a way to ensure existing core functionality works to support business operations and new enhancements and features roll out with minimal impact on customers, without having to retest the entire solution manually. To do this, we have introduced automated testing for our services and the broader processes.

The automated testing suites for the new technologies are written in the BDD Specification, following the Given, When, Then syntax that is standard practice. We utilise Specflow as our framework to write these test cases, allowing for our developers, working in C#, and testers to collaborate to create complete and functional tests to cover all requirements

For our services, we have introduced the BDD framework to test end to end processes, such as checking responses from third parties process results in the correct manner. These tests utilise Mockoon’s mock REST API servers, configured to mimic the third-party supplier’s responses. We create common methods for the then statements to setup data for the API calls, a when statement to actual perform the call itself and then a then statement to verify that the response and resulting processes produce the correct outcome. See below for an example feature file for a service which runs tests for a pubsub:


``` @Integration
    Feature: Publishing to and reading messages from PubSub Emulator.

    @PubSub1
    Scenario: PubSub Emulator - deposit.success
        Given I am a 'PEP' who has registered for an account with The Tote
        And I have made a 'Deposit' and it was a 'Success'
        Then message is received from AML-transaction subscription and event is 'deposit.success'

    @PubSub2
    Scenario: PubSub Emulator - deposit.fail
        Given I am a 'PEP' who has registered for an account with The Tote
        And I have made a 'Deposit' and it was a 'Fail'
        Then message is received from AML-transaction subscription and event is 'deposit.fail'
```

As well as individual service automated testing, we run automated tests nightly for core functionality. These tests are also written in C# Specflow BDD format but use our own API endpoints and a custom API service set up to allow us to set up data via basic calls to our local services. These tests run in our non-production environment. Please see below for an example feature file where we are testing customer betting:

```
@Acceptance
@ContinuousIntegrationGroup2
    Feature: Customer
    Scenario: Customer Betting Real Money
        Given I create a legacy customer with a balance of 250 in the currency GBP
        And I get a logged in customer
        And a customer is created with Id
        And I want to create a Standard pool called Spec-CustomerBetPlace-PoolCreate with a unit value of 1
        And the pool has a leg called 'Spec-CustomerBetPlace-LegA' with selections
        | Name                     |
        | Spec-CustomerBetPlace-Selection1 |
        | Spec-CustomerBetPlace-Selection2 |
        When creating and opening the pool
        And a customer is created
        And a bet is placed ONLINE for a real user with amount 50.50
        Then the bet is created in the read model
        And I check the customer balance of 199.50
```

These testing suites will continue to be expanded upon and grow as functionality continues to grow and improve, with more test coverage and more automated scenarios. These will improve the efficiency and quality assurance of future releases, leading to better customer experiences and quicker improvements and enhancements, and make both developers and testers more assured of releasing new functionality
