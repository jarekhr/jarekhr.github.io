---
title: Code reviews that work
date: 2025-10-29 21:50:45 +0200
categories: [Software, Leadership, Teamwork]
tags: [programming, reliability, quality, teamwork]     # TAG names should always be lowercase
comments: on
---

Code reviews are becoming a standard practice in modern development teams. They can be done in many different ways, depending on the company culture, team experience, pace of work, goals.
I wanted to share my insights on the way my team approaches code review during the Pull Request review process.

# Goals
Let's start by stating what's the goal of code review and why it matters. 

Benefits of Code Reviews:
- allow to spot obvious bugs, improve code quality by ensuring standards are followed, best approach was taken, etc.
- force team to make themselves familiar with code which will be incorporated into the project. Forces team to read the code first, then when working on some other features developers may reuse existing solutions/classes/methods instead of 
reimplementing from scratch. This develops an attitude that good code must be also easy to read to be maintainable in the future.
- creates a stage for team to ask questions about the code. Reviewers, when unsure if they understand the code well, can ask questions to upscale their knowledge of the project or technologies used. 
Code review is also and exploratory task for a reviewer. 


# Code Reviews in practice

## The review
What to focus on when doing a code review:
 - check if changes make sense in the context of the task/user story/feature. We want to make sure that the right thing was implemented.
 - are the automated tests implemented according to the standard (code guidelines)?
 - is code readable and understandable by the rest od the team?
 - are there any obvious improvements that can be done to changes that make it better (we will elaborate ont this later)
 - do you, as a reviewer, know what is going on? If not, you need to ask questions to get yourself to the same page as the author of the PR/other reviewers. 


## Code guidelines
Every developer needs to know what is expected of him/her when doing code changes in the project. Create a Wiki page, write an `*.md` file and add it to your git repo, and 
define rules that need to be followed at all times.

Some things to consider:
- which libraries are allowed or not allowed. Ideally would be good to know what is the rationale for any decision in this regard (aka Decision Record)
- how the code should be formatted? Code formatting is one of sources of flame wars between developers. Write down what to follow. Best is to use code linters when possible. 
This removes arguments at the code review - if code passes linter rules it means it is formatted well and does not need to be changed. Some tools to consider are `checkstyle` for Java, 
`flake8` for python, `pretty` for TypeScript.
- define which language constructs are prohibited and why, define replacements. Example: please don't use `var` in Java because it obfuscates type information.


## Problems to deal with

### It's about the code, not about the developer 
Developers who are not experienced with code reviews quite often get very defensive when they find some comments from other team members under their PR. At first, it feels like a natural
reaction to somebody criticising your code, your work, so also indirectly you as a developer. When introducing your teams to code reviews it's important to make everybody aware, that code review
comments have to be about the code, not about the developer. It's all about the code, not about the person who wrote the code!

Because entire team cares about the changes which are about to be introduced to `master`, entire team is there to make sure these changes are of a good quality. I like to bring sailing 
experience here a little bit and make analogy to a crew on a sailing boat. Each crew member when doing any changes to the boat/taking action at sea is indirectly potentially 
putting other crew members life at stake. Entire crew has a deep understanding that they can't afford to make big mistakes, because it may compromise their own safety. 
We're  all on the same boat in the end, if the boat fails, everybody will be impacted. 
I believe it's a bit like that in a software project - bad code will make everybody's life harder. It's much better to avoid possible problems than fight with results of bad code 
which was introduced to the codebase. It's in everybody's best interest.


### Ruinous empathy

It's about the code as we said earlier. Your really need to get your team to be comfortable with this, or you will find yourself in a situation, when each code review ends with no comments,
no ideas how to improve it, no ideas why it can cause trouble. Just _APPROVED_. Developers will just approve their colleague's changes, not to upset or offend them.


To avoid any stir and any discussions they will choose a way with the least resistance - just _approve_.

Think about it. If you **just approve** the PR, you:
- don't have to put too much effort into getting deep into what code is doing
- you don't risk putting yourself into potentially difficult technical discussions which may be started by your comments
- you don't risk that author of code changes will be offended or upset about your comment (event though it is professional and focusing on the code, not on the developer)

As comfortable as this strategy may seem, it leads to a ruin, to a disaster. _Ruinous empathy_ is a term coined in a book "Radical candor" by Kim Scott. I really recommend this book and the topic.
Developers are so empathetic to the code that their colleagues wrote, that they choose not to raise valid concerns over it. As a result their lack of action, lack of comments under the PR, hurting entire team, **including themselves and the author of the code changes**. 
Remember, we're on the same boat. If boat sinks, we're all going down.

Don't let your team be falsely too nice to each other, code reviews are about the code and entire team is working together to make the code better. It's not an assessment of skills of the author of the code changes under review.

### Petty discussions / flame wars
Some developers have a strong viewpoint about some things. Some developers just like to watch the world burn ;) It's just the way things are in a diverse teams. Developers are like _snowflakes_ as somebody said the other day.

You, as a team leader, need to make sure that pointless discussions are cut early in the process. You want to keep your team spirit up, keep people open and positive. 
If somebody is arguing about code formatting, direct them to your _Code Guidelines_. If somebody is focusing on other pretty insignificant topics try to 
address it by adding it to _Code Guidelines_ document, etc. You will fix the problem for now and for the future.

## Payoff

End goal is to have high quality, maintainable codebase which is well known by all developers in a team. Code review practiced in a serious yet pragmatic way is an important 
factor in getting your there. 




