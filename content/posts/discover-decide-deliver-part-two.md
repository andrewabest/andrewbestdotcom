---
title: "Discover, Decide, Deliver: Part Two, Decide"
date: 2018-07-22T18:31:04+10:00
draft: true
---

_This is the second part of a three part series in which I cover my methods of delivering outcomes via software, using a mixture of best practice, agility, and lean methods._

[Part One: Discover](https://www.andrew-best.com/posts/discover-decide-deliver-part-one/)

[Part Two: Decide](https://www.andrew-best.com/posts/discover-decide-deliver-part-two/)

Part Three: Deliver

---

We have now spent some time [Discovering](https://www.andrew-best.com/posts/discover-decide-deliver-part-one/) potential problems or opportunities within our or our partner's business, ensuring we have identified business goals that our delivery can contribute to, and spent a commensurate amount of time building a high level solution scope. 

If you haven't read the first post I'd encourage you to do so, as it wil be assumed knowledge for this post - if you find yourself wondering why something is missing here, it was likely covered during [Discover](https://www.andrew-best.com/posts/discover-decide-deliver-part-one/), or will be covered in Deliver.

The question is now: **should our business invest time and money in the proposed initiative?**

I previously stated:

> The _two biggest mistakes_ that are made at this stage of a software initiative are:
> * Jumping straight into solutioning
> * Spending too long in up-front planning

The first problem, _jumping straight into solutioning_, we solved during [Discover](https://www.andrew-best.com/posts/discover-decide-deliver-part-one/).

The second problem, _spending too long in up-front planning_, we solve **now**.

Decide
======

At this point we should have not invested an large amount of time and effort in discovery, and so will not be tempted to give into [Sunk Cost Fallacy](https://en.wikipedia.org/wiki/Sunk_cost#Loss_aversion_and_the_sunk_cost_fallacy) when making a decision to proceed or not with a given initiative. 

Making a decision should also be relatively quick, and most importantly, based on empirical data. 

Data
----

To make the decision, we need the right data on hand. We cannot decide on whether to proceed with a software initiative without a minimum amount of data present - any coversations that occur in the absence of this data are simply speculation and conjecture. We want to avoid the [HiPPO](http://www.askahippo.com/) based decision, and proceed with eyes wide open.

The three pieces of data we need to gather are:

* How much will it cost?
* What is the expected ROI?
* What are the percieved risks, assumptions, issues, and dependencies? ***

How Much Will It Cost
---------------------

The first data we need on hand to make the decision is _How much will it cost?_ - and that will involve estimation.

The *most important* part of this step is to understand that we are **not** estimating trying to figure out exactly how much effort it will take to produce precisely what we have outlined to date - what we end up delivering in any given software initiative rarely resembles exactly what we understood up front. 

Estimation as a tool to fix scope, drive 'accountability', and railroad software development initiatives is why the #NoEstimates movement gained some steam. In the real world, businesses need some assurance around how much they will likely need to invest to see a desired return.

What it is critical to understand is that what we are attempting to do with our up-front estimation is to **create a budget within which we are confident we can succeed**.

My colleague [Liam McLennan](https://withouttheloop.com/) shared an article on Twitter recently on estimation, entitled [Blink Estimation](https://dannorth.net/2013/08/08/blink-estimation/), by [Dan North](https://dannorth.net/). I heartily recommend reading the whole thing, it is truly excellent. The part in particular I wish to draw attention to is the section entitled _Estimation as an investment decision_. If you set a budget within which you are confident of success, these two quotes from that section become the essence of how you then deliver to that budget:

> You simply work towards the date and declare victory when you get there.

> It’s like firing an arrow and then painting the target around it — you start hitting a lot of bulls-eyes!

So how do we go about crafting our estimate?

_Experts estimate only_

The best estimates come from people who have delivered a lot of software in a variety of environments and conditions, _on the tools_. The context they have built over this time gives people an innate sense of how long things take, from simple forms to complex system of systems concerns, and what can impact those timelines.

They also have a historical mental catalog of 'this shape solution takes around this long to produce' to santiy check against.

_Get visibility on the proposed scope at the right granularity_

We have our proposed solution scope from our Discover phase - this might be in the form of an Impact Map, or a set of wire frames, or some rough sketches on paper. 

Whatever form it is in, we need to ensure we have enough detail to estimate to. If we have a set of wireframes, and understand they are reasonably complete - great! Job done, we have more than enough information to work with.

If we have an impact map and a set of high level statements on what scope could be delivered to enable actor behaviour, we likely have a little more work to do.

For any given solution, we need to have a reasonably complete view on the high level _Functions_ the system presents, and the _Fixed Overheads_ required to build the system. 

A trivial example of a functional breakdown at what I'd consider to be "the right level" would be:

```
- Sortable, Filterable list of users
- Create, View, Update, Delete user details
- Assign Azure AD Groups to user 
```

as opposed to

```
- User Management
```

It matters less that you get the exact granularity right, and more that you understand the granularity you used during estimation when you start delivering. If we have this information on hand, we can drive conversations during delivery to say "We assumed user management would take x days, but given the scope discovered over the last weeks of delivery, we now can see it will take y days", and we can drive a conversation around whether we trade scope, or increase budget, to accommodate our current reality.

_Estimate at the correct granularity_

Nothing takes "Just an hour" at this granularity. Almost nothing takes "Just an hour" anyway. Between discovering the scope of a particular story, refining it down to an executable state, developing it, testing it, deploying it, verifying it, potentially iterating on it, and ultimately shipping it, even the most innoccuous piece of scope will take longer than you anticipate, as per [Hofstadter's Law](https://en.wikipedia.org/wiki/Hofstadter%27s_law).

As a rule of thumb, _everything takes at least half a day_. No exceptions.

_Understand the scopes propensity to grow or change_

How sure are you of the proposed solution scope? This is quite a subjective question, and will always depend on your unique situation. Sometimes a solution scope is both very obvious, and quite limited due to its nature. At other times, it can be very amorphous, with stakeholders only having a vague idea of what ultimately needs to be delivered.

Understanding the likelihood and magnitude of change to our initially proposed scope can help us add appropriate contingency to our estimates, as change always has an impact on budget - and we want to accommodate change, so we need to budget appropriately.

_Understand our Risks, Assumptions, Issues, and Dependencies_

Risks are things that have a likelihood of impacting us during a given software delivery. Assumptions are things we hold to be true up-front. Issues are risks that have already eventuated, and are having an impact already. Dependencies are things our solution will require, but are outside of our direct control.

It is critical to dig in to all of these areas with our key stakeholders to create a reasonably complete RAID map to support our estimate. 

Some examples of things that I have seen come up during building a RAID map with my customers, which have directly impacted my estimates, are:

- Assumption: solution must be WCAG AA compliant
- Risk: current automated build process is brittle
- Issue: scope is currently volatile due to different parties conflicting interests
- Assumption: we need to build the API for public consumption, although initially the only consumer will be the web client also being built
- Risk: internal resourcing constraints

_Map a high level solution architecture_

We need to know roughly what technical components will comprise our solution. It may not matter if we use Angular or React for our web front end, but it will certainly impact us if we need to use Xamarin instead of Cordova for cross platform mobile, or need to host in AWS EC2 auto scale groups for our compute, instead of Azure Web Applications.

The main rule here is to leave as many decisions as possible to the team who will be delivering the solution. 

No one likes being hung by someone else's rope. 

_Understand the environment we are delivering within_

Are we working within a medium size, privately owned company, with the CEO as our Product Owner, their credit card powering our cloud tenancies, and almost nothing standing in between our delivery team, the customer, and our desired outcome?

Or are we working within state government, with an appointed government Project Manager, reporting to a Project Board, with large overheads to our ideal procurement, governance, and delivery processes?

_Understand the team we are delivering with_

Is our team comprised of already-aligned, senior people who are deeply skilled in the technologies that will be used to deliver the proposed solution?

Or are we working to build capability, upskill juniors, and transfer knowledge?

Are we all going to be co-located? Or will there be a remote portion?

_Summary_

The information suggested above should not take long to gather, create, and validate. Our ideal timeframe will be days. Once it is on hand, we have everything we need to **create a budget within which we are confident we can succeed**.

For larger projects that involve multiple phases or milestones, I always err to estimating the first or most well-understood milestone using the above technique, and then use statistical forecasting techniques to attempt to predict a program level budget. [Troy Magennis](http://focusedobjective.com/people/) has published [some excellent free materials and tools](http://focusedobjective.com/forecasting-techniques-effort-versus-reward/) for this that I have utilized with great success before.

Always remember, and communicate to your customers and stakeholders: spending time up-front discovering and qualifying scope usually won't change it's size, it simply provides a diminishing additional amount of assurance to them as to how big it may be.

What Is Our Expected Return On Investment
-----------------------------------------

The next data we need to understand is _How much will we benefit from the initiative?_

This may either be in a positive form, such as either income, or reduced operating costs, or negative form, such as the cost of delaying changes that are required to comply to agreements that involve damages for non-compliance.

So what is an appropriate return on investment?

3-10x. Anything lower than 3x should be considered risky if the 'return' is financial. 

The third data we need to understand is _What risks are present in this delivery?_

...

Once we have these on hand, we can look at

Decision Time
-------------

Proceeding with a software initiative that is not likely to deliver an appropriate return on investment is not only costly from a financial point of view for the business, but the knowledge that the initiative is borderline profitable or worse tends to permeate the delivery. This leads to both substandard product development due to overly constrained resources, and to general negative impacts on delivery teams and cultures due to the oppresive nature of cut-throat product development.









