---
layout: post
title:  "My approach to Product Feature Development"
date:   2022-11-08 16:24:00 +0000
categories: development documentation
---

As a Software Engineer, feature development is a core part of the job. For some, it is all about writing code and shipping it to production after being tested by a QA Engineer. But as the company grows in maturity and you grow in seniority, more is asked from you. 

Settling in only writing code does not feel acceptable anymore. You need to know how to communicate with other engineers, your manager, your PM, and a bunch of other people from different teams. Planning the work and aligning the expectations is no longer an exclusive task of your PM or your Manager.

In this post, I'll show you the steps I take to put a feature in production while keeping everyone involved.

## Start with why

How often do you ship a feature without knowing how your PM came up with this idea? The first step I take is to ask why I have to build that. What I expect in return is to understand key points so I can talk with everyone about the project.

- What is the problem I'm solving?
- Will it have a financial impact on the company? What are the numbers?
- Who is going to be affected (in a positive way) by this feature?

If you cannot find the numbers or it is difficult to tell who is the target group, you may be working with someone's guess that this is the right feature to develop at this time.

This is a very important aspect of the whole process as you want to be able to discuss the project with anyone in the company. As much as you know about it, easier it gets to adapt your speech to the audience. 

One thing I realized after asking why is that I started to feel more engaged with the project knowing the impact of my work.

## Written documentation is your superpower

Most of the engineers were told that in agile software development you shouldn't have documentation. This is one thing that I completely disagree with. The agile manifesto, which dates from 2001, was never about *go horse*, but all about keeping a positive mind about changes and reacting fast to them.

>Individuals and interactions over processes and tools  
Working software over comprehensive documentation  
Customer collaboration over contract negotiation  
Responding to change over following a plan
>- Agile Manifesto, 2001

A wrong interpretation of the manifesto led to many managers with this same mindset. Believing that documentation and planning before starting the actual development is Waterfall and not Agile (this one I heard recently from an actual manager in a meeting).

I mostly use documentation to understand the problem on a technical level, collect feedback, and give context to my colleagues. When I look back at the projects I wrote proper documentation that got a proper review, I can see many occasions where mistakes and bad design decisions were avoided. Also, I'm sure that everyone that reviews the document has enough context about the project and can start working on it at any point in time.

### Document structure

I use a 3-section structure to write the documentation: Context, Technical analysis, and Proposals.

#### Context

In this step, I write in my own words what I got from asking why. It is super important that the reader understands the problem, who is affected by it, and the timeline to start the project.

As the goal of this section is to give everyone context over the project, I always list the key metrics to keep an eye on and what success will look like if you monitor them.

#### Technical Analysis

When developing a new feature, I try to understand if there is nothing else like this already. If something similar is in place, the reader must know why it can't be used to solve this particular problem.

This is also a good place to list all the touching points of the project. Which parts of the system need to change so the user can interact with the new feature? Is there any internal tool in the middle?

If you have clear functional requirements, use them to understand how the system must behave so you can build clear technical requirements.

#### Proposals

Now that the problem is clear to everyone, all the requirements were listed, and you have a better view of the whole, it is time to work on proposals. I like to offer two different choices so the team can have a chance to question my decisions and bring a third solution (which, when they do, usually is related to one or another).

When more than one proposal solves the problem well (technically speaking), it is up to the team to make a decision that is the right balance between time-to-market and technical quality.

## Project management tool

I'm currently using Jira, but this is step is more or less the same for whatever tool you use (get rid of Excel if this is what you use today 😅).

As a feature usually involves many changes in the entire codebase, I set the feature as an Epic in Jira. In the description, I copy and paste the content of the `Context` section of the document with a link to the file for those who need more information.

Knowing what is going to change and when is super helpful to break down the work into independent tasks. Having them listed under the Epic card will help your manager and PM to follow the work.

Breaking down the tasks is not straightforward. If it is the first time you are doing this, you'll probably do it wrong. Learn from your mistakes, so the next time you know what to avoid. There is no magic formula to that, as each person and each team are different.

## The usual engineering flow

There is not much to talk about in this step. Make good use of code reviews and tests. Look into the best practices in the market to achieve quality in your work. Ask for help whenever you find yourself stuck.
