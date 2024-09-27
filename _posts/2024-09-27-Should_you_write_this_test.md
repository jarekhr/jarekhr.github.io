---
title: Should you write this test?
date: 2024-09-27 21:10:15 +0200
categories: [Software, Testing]
tags: [testing, programming]     # TAG names should always be lowercase
comments: on
---

Not much convincing needs to be done that having automated tests in your project is the best way to future-proof project for maintainability.
But is every test worth creating? 


## Return on Investment [ROI] in testing

To write a test you need invest some time and effort. It's an investment you're making, and you're expecting a return 
on that investment. Your return is assurance, that part of your codebase is working as expected. Therefore, best to think about 
the price you need to pay and find option which is "the cheapest"

### Your costs
#### Time spent on writing the test
  * does that "type of test" already exist in your project? if not maybe you're laying foundations for many tests in the future 
  * following the pattern laid out earlier is faster and cheaper

#### Time and resources needed to run it
What are the test dependencies? The fewer, the better.
  * does it require any external dependencies?
  * how much time and resources it takes to run the test in tour CI

#### Is test data cheap to maintain?
  * how easy it is to modify test data over time? Inserting your test data using JPA model is cheaper and more future-proof than maintaining a raw SQL file which will need to be
modified a lot whenever a field changes.
  * will test data evolve automatically when your data model changes?

## Can you make and an alternative investment instead?
Maybe you can write a different type of test which will be cheaper, but still provide the same (ideally even higher) 
level of assurance about your code's correctness? Always run the list of questions (costs) above and compare your options.
Then choose the one which yields the highest Return on Investment


