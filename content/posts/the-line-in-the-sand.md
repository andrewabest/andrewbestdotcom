---
title: "The Line in The Sand"
date: 2019-03-21T08:00:00+10:30
draft: false
---

Expectation management is _the most important thing_ we do in software delivery. It should be the first thing we do, and the last thing we do. My feelings for expectation management are evidenced by the fact it is the very first thing I covered in the _Execution_ section of [Discover, Decide, Deliver Part Three: Deliver](https://www.andrew-best.com/posts/discover-decide-deliver-part-three/).

My colleague [Darren Neimke](https://twitter.com/digory) came to me recently to discuss how to best set expectations with his Product Owner on an engagement he was delivering. He felt like he and the team were making good progress, were publishing a wealth of delivery health information, and were well-poised to deliver a solid outcome within the available project budget; but was not convinced that their Product Owner had the same level of confidence or clarity.

Setting expectations in software delivery is hard. We work with abstract definitions of work in a prioritised product backlog, with abstract defintions of capacity and progress with velocity and burnup, and against a rapidly evolving set of software and accompanying understanding. 

It is not reasonable to expect our stakeholders can simply be provided with several sets of information - a backlog, a burnup and velocity, remaining budget, and a working piece of software - and arrive at reasonable conclusions as to what the future holds.

What Will I _Get_?
==================

At the start of our engagement, we set a goal that we are seeking to achieve, and a budget within which we think we can achieve it. Achieving this goal is the _outcome_ we want, and the expectation as to whether we will achieve it or not is the most important to manage - however often this will be a lag metric. We will only know whether we have succeeded once the software is running in production and can measure our level of success.

Most stakeholders - Product Owners or otherwise - care about more than just the goal. They want to know about what they are going to get - what will the software do? Will it have all of the functionality that has been requested? Will it have that report the CFO requested last week after he sat in on sprint review? Are we going to be able to implement configurable communication types for users? 

At the start of a software delivery, we have a target _outcome_ - progressing toward our [identified goals](https://www.andrew-best.com/posts/discover-decide-deliver-part-one/) via our outputs - the software we write. The expectation we set at the start is that we have confidence that we can achieve the goal within the available budget, we will make frugal decisions in regard to budget expenditure to make sure we maintain the capacity to respond to change, and that we will incrementally progress toward our goal and deal with new information in a _just in time_ fashion, as it is discovered.

As the delivery progresses and our solution takes shape, our backlog will continue to grow. It will tend to both house things that absolutely _must be done_ to deliver on the intended outcome, and things that are _nice to have_. 

Often there isn't a clear distinction between these two things in our backlog. The backlog is prioritised, but aside from the top small handful of items, who is to say whether item 50 is more important than 51? A large linear product backlog is also a _very poor_ tool for imparting a clear view of what it represents in our real-world product.

---

 As an aside, for a better solution to the problems caused by linear product backlogs, check out [User Story Mapping](https://agilevelocity.com/agile-tools/story-mapping-101/) - I'm waiting for a great software product to arrive that does this well and can tie into developer workflows.

 ---

This was the core of the problem facing the Product Owner we were discussing earlier - given a diminishing budget, an evolving set of software, and a still reasonably large backlog which represented an amount of work that could in no way fit inside that budget, how could they have a clear understanding of exactly what would be achieved by the time the budget was exhausted?

The Line In The Sand
====================

The solution I employ to solve this problem is relatively straightforward, but very effective. In your product backlog (Jira, Azure Devops, Rally, whatever tool you use), create an item like so:

```
================== Line In The Sand ==================
```

With your Product Owner and team, collectively work to move **everything** that absolutely _must_ be done to achieve our target outcome above this item. All nice to haves stay below it. 

This can also be defined in another way: things that, come hell or high water, we are going to do our utmost to get done; and things we probably aren't going to do unless we have a budget surplus and our customer would like to continue to invest. 

Once we have this established, the next thing we can do is to evaluate whether we have enough budget to ship everything that lies _before_ the line in the sand based on our current understanding of velocity. If we don't, we can do three things - make things above the line smaller, move more things below the line in the sand, or procure more budget.

---

**No we can't just work faster**. When these sorts of things tend to be out, they aren't out by a few days - those sort of blips can be ironed out by a few frugal implementation decisions. They tend to be out by weeks or months. And guess what the only solution to "speeding up" a team by that amount is? _Build different (usually less) software_.

---

It is surprising how often, when given a simple and clear definition of remaining capacity informed by budget, and a line to move things above or below, stakeholders will come up with novel ways to reduce scope to the point that the target outcome can reasonably be achieved.

_But why not just use tags / filters / queries?_

Sure, if it works, go for it! I've had great success using tagging for "Release 1" / "MVP" / "VNext", and then working this in to a cumulative burnup to communicate scope growth within those 'buckets'. However this tends to work better if whoever driving the tooling is confident with it, as you need to work this into how you define your work items, your filters and queries, and your burnup.

Nothing beats the line in the sand for simplicity. And for setting expectations, simplicity is best.