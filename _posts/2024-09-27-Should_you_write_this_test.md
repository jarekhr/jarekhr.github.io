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
on that investment. Your return is assurance, that part of your codebase is working as expected. If you have multiple options
to get the assurance you need, best to select the one which will cost you as little as possible. As in regular economy, 
you want to get the most for "your money".

### Your costs
#### Time spent on writing the test
Time needed to write a test can vary greatly:
  * does that "type of test" already exist in your project? if not maybe you're laying foundations for many tests in the future 
  * following the pattern laid out earlier is faster and cheaper

It can also vary depending on which system layer your locating your test at. Most economic option is test as close (low in terms of system layers) as possible
to the layer where the covered code is located. This limits the amount of setup for your code and limits dependencies.  

#### Time and resources needed to run it
Each test in the system slows down your CI builds. This cost is usually acceptable, because we get the assurance about
code correctness. But there are limits to what one is willing to sacrifice. If test you're adding gives you
"just a bit more" assurance, but slows down you build by a lot, does it still make sense to have this test?
Again, maybe you can find another type of tests which ill give you the same assurance, but run faster? 

HINT: should the test be implemented with less dependencies, on lower system layer than initially planned?

#### Test data maintenance
  * how easy it is to modify test data over time? Inserting your test data using JPA model is cheaper and more future-proof than maintaining a raw SQL file which will need to be
modified a lot whenever a field changes.
  * will test data evolve automatically when your data model changes?

## Can you make an alternative investment instead?
Maybe you can write a different type of test which will be cheaper, but still provide the same (ideally even higher) 
level of assurance about your code's correctness? Always run the list of questions (costs) above and compare your options.
Then choose the one which yields the highest _Return on Investment_


