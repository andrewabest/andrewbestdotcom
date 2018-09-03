---
title: "Discover, Decide, Deliver: Part Two, Decide"
date: 2018-09-03T18:31:04+10:00
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
>
>   * Jumping straight into solutioning
>
>   * Spending too long in up-front planning

The first problem, _jumping straight into solutioning_, we solved during [Discover](https://www.andrew-best.com/posts/discover-decide-deliver-part-one/).

The second problem, _spending too long in up-front planning_, we solve **now**.

Decide
======

At this point we should have not invested an large amount of time and effort in discovery, and so will not be tempted to give into [Sunk Cost Fallacy](https://en.wikipedia.org/wiki/Sunk_cost#Loss_aversion_and_the_sunk_cost_fallacy) when making a decision to proceed or not with a given initiative. 

Making a decision should be relatively quick, and most importantly, based on empirical data.

Data
----

To make a decision, we need the right data on hand. We cannot decide on whether to proceed with a software initiative without the requisite minimum amount of data present - any coversations that occur in the absence of this data are simply speculation and conjecture. We want to avoid the [HiPPO](http://www.askahippo.com/) based decision, or any other gut-feel type decision making, and proceed with eyes wide open.

The three pieces of data we need to gather at this stage are:

* How much will it cost?
* What are the percieved risks, assumptions, issues, and dependencies?
* What is the expected ROI?

Or more succinctly: **cost, risk,** and **return**.

Remember that we should already have a clear picture of how the initiative contributes to the businesses vision, strategy, and goals, which were formed during [Discover](https://www.andrew-best.com/posts/discover-decide-deliver-part-one/).

How Much Will It Cost
---------------------

The first data we need on hand to make the decision is _How much will it cost?_ - and that will involve estimation.

The *most important* part of this step is to understand that we are **not** estimating trying to figure out exactly how much effort it will take to produce precisely what we have defined to date - what we end up delivering in any given software initiative rarely resembles exactly what we understood up-front. 

Estimation as a tool to fix scope, drive 'accountability', and railroad software development initiatives is why the #NoEstimates movement gained some steam. In the real world, businesses need some assurance around how much they will likely need to invest to see a desired return.

It is critical to understand is that what we are attempting to do with our up-front estimation is to **create a budget within which we are confident we can succeed**. Or in other words - we need to create a budget within which success is likely.

I want to take a moment to  belabour the above point even further. I've used the word _budget_ instead of estimate. It is important to know that the ulitmate output of our up-front estimation isn't an "estimate". An estimate implies an assumption that the work to be done will remain reasonably constant, and that accountability will be driven by tracking actuals towards the estimate. This is actively harmful behaviour in a software delivery, but one I still encounter far too often, usually at larger enterprises with more arduous, dogmatic governance processes. The ultimate output of our up-front estimation is a _budget_. It is an amount of money and time within which we can guide a software delivery, accommodate change and risk, and make decisions at the correct time to make them, all contributing to delivering _a successful outcome_ before the budget is exhausted. 

Will we be _done_ by the time the budget is exhausted? Unlikely, most software initiatives are rarely ever 'done'. There are always other things that could be done. But what we have delivered by the time the budget is exhausted will be enough for us to ship, realise a return, satisify stakeholders, and contribute to the business goals identified in [Discover](https://www.andrew-best.com/posts/discover-decide-deliver-part-one/).

My colleague [Liam McLennan](https://withouttheloop.com/) shared an article on Twitter recently on estimation, entitled [Blink Estimation](https://dannorth.net/2013/08/08/blink-estimation/), by [Dan North](https://dannorth.net/). I heartily recommend reading the whole thing, it is truly excellent. The part in particular I wish to draw attention to is the section entitled _Estimation as an investment decision_. If you set a budget within which you are confident of success, these two quotes from that section become the essence of how you then deliver to that budget:

> You simply work towards the date and declare victory when you get there.

> It’s like firing an arrow and then painting the target around it — you start hitting a lot of bulls-eyes!

So how do we go about crafting our estimate?

_Experts estimate only_

The best estimates come from people who have delivered a lot of software in a variety of environments and conditions, _on the tools_. The context they have built over this time gives people an innate sense of how long things take, from simple forms to complex system of systems concerns, and what can impact those timelines.

They also have a historical mental catalog of 'this shape solution takes around this long to produce' to santiy check against.

_Wrap your arms around it_

We have our proposed solution scope from our Discover phase - this might be in the form of an Impact Map, or a set of wire frames, or some rough sketches on paper. Whatever format it is in, we need to be able to 'wrap our arms around it' so that we can estimate how much effort it will take to produce it.

The Dan North article referenced previously elicits interesting fact in regards to estimate sizing: the more resolution or detail we attempt to plan to up front, the larger estimates tend to become! This means that if we do have a set of detailed wireframes, it is likely that our estimate will be larger than that if we had estimated against our Impact Map's high-level functional statements.

If we are working with a business who understands the constraints and realities of software delivery, we may want to harness the fact that [scarcity drives successful innovation](https://hbr.org/2011/01/the-number-one-key-to-innovati) and estimate on less granular information - confident that we can work in close collaboration with that business to make appropriate decisions to deliver a successful outcome within a leaner project budget.

If we know we are working in an organization that has less mature software delivery capabilities, where _"everything is a priority"_, we may want to go into more detail, knowing that we are likely to need both more planning collateral to drive hard conversations regarding scope and budget as the delivery evolves and changes and needs to adapt, and more budget to deal with the inflexibility of stakeholders for that adaptation to occur.

My personal preference when estimating a given initiative is to have a reasonable view on the high level _Functions_ the system presents, the _Fixed Overheads_ required to build the system (such as setting up a CI/CD pipeline), and any _Cross-Cutting Concerns_ that are likely to impact any function delivered within the system (such as WCAG compliance).

A trivial example of a functional breakdown at what I'd consider to be "the ideal level" would be:

```
- Sortable, Filterable list of users
- Create, View, Update, Delete user details
- Assign Azure AD Groups to user 
```

as opposed to

```
- User Management
```

It matters less about any specific granularity, and more that you understand the granularity you used during estimation when you start delivering. If we have this information on hand, we can drive conversations during delivery to say "We budgeted x days for User Management, but given the scope discovered over the last weeks of delivery, we now can see it will take y days", and we can drive a conversation around whether we trade scope, or increase budget, to accommodate our current reality.

_Use the right units_

Nothing takes "Just an hour" at this granularity. Almost nothing takes "Just an hour" in software development anyway. Between discovering the scope of a particular story, refining it down to an executable state, developing it, testing it, deploying it, verifying it, potentially iterating on it, and ultimately shipping it, even the most innoccuous piece of scope will take longer than you anticipate, as per [Hofstadter's Law](https://en.wikipedia.org/wiki/Hofstadter%27s_law).

As a rule of thumb, _everything takes at least half a day_. No exceptions.

_Understand the scopes propensity to grow or change_

How sure are you of the proposed solution scope? This is quite a subjective question, and will always depend on your unique situation. Sometimes a solution scope is both very obvious, and quite limited due to its nature. At other times, it can be very amorphous, with stakeholders only having a vague idea of what ultimately needs to be delivered.

If we have an appropriately-bounded software initiative defined by our [Discover](https://www.andrew-best.com/posts/discover-decide-deliver-part-one/) phase, understand the environment we will be delivering within, and have a good understanding of the scope's propensity to grow, we should be both well positioned to support the anticipated change during our delivery by planning for it within our estimate, and confident that the _number_ of functions present will not fluctuate to a magnitude that will impact the estimate.

_Create a high level solution architecture_

We need to know roughly what technical components will comprise our solution. It may not matter if we use Angular or React for our web front end, but it will certainly impact us if we need to use Xamarin instead of Cordova for cross-platform mobile, or need to host in AWS EC2 auto scale groups for our compute, instead of Azure Web Applications. Fundamental technical decisions will have an impact on time and budget.

The main rule here is to defer as many decisions as possible until it is the appropriate time to make them, during delivery. And if you aren't the one on the ground delivering, remember: no one likes being hung by someone else's rope.

_Understand the environment we are delivering within_

Are we working within a medium size, privately owned company, with the CEO as our Product Owner, their credit card powering our cloud tenancies, and almost nothing standing in between our delivery team, the customer, and our desired outcome?

Or are we working within state government, with an appointed government Project Manager, reporting to a Project Board, with large overheads to our ideal procurement, governance, and delivery processes?

_Understand the team we are delivering with_

Is our team comprised of already-aligned, senior people who are deeply skilled in the technologies that will be used to deliver the proposed solution?

Or are we working to build capability, upskill juniors, and transfer knowledge?

Are we all going to be co-located? Or will there be a remote portion?

_Summary_

The information suggested above should not take long to gather, create, and validate. Our ideal timeframe will be days, not weeks. Once it is on hand, we have everything we need to **create a budget within which we are confident we can succeed**.

For larger projects that involve multiple phases or milestones, I will always estimate the first or most well-understood milestone using the above technique, and then use statistical forecasting techniques to attempt to predict a program level budget. [Troy Magennis](http://focusedobjective.com/people/) has published [some excellent free materials and tools](http://focusedobjective.com/forecasting-techniques-effort-versus-reward/) for this that I have utilized with great success before.

Always remember, and communicate to your customers and stakeholders: spending more time up-front discovering and qualifying scope usually won't change it's size, it simply provides a diminishing additional amount of assurance to them as to how big it may be. Make your decision as soon as you have enough information and confidence to do so.

What Are Our Risks, Assumptions, Issues, and Dependencies
---------------------------------------------------------

Risks are things that have a likelihood of impacting us during a given software delivery. Assumptions are things we hold to be true up-front. Issues are risks that have eventuated, and are having an impact already. Dependencies are things our solution will require, but are outside of our direct control.

To make a decision on a given software initiative, we need to understand the **risk** involved in the delivery - what is the chance that the scope will explode due to unforseen reasons? What is the chance that our dependencies simply will not be delivered within a timeframe or to the standard required? What is the chance current issues that are already present will stop us from creating a successful outcome?

An effective way to discover and quanitfy the risks posed by a given software initiative is to create a [RAID map](https://www.groupmap.com/map-templates/raid-analysis/). Whilst _risk_ is only the first item in a RAID map, the RAID map as a whole is a risk management tool.

The best way to build a RAID map is collaboratively with our customer, involving technical and non-technical stakeholders who will either be impacted by the solution, or will impact the solution. Ensuring we have a reasonably wide amount of input will ensure previously unforeseen issues will be highlighted, captured, discussed, and understood.

Some examples of things that I have seen come up during building a RAID map with my customers, which have directly impacted my estimates, are:

- Assumption: solution must be WCAG AA compliant
- Risk: current automated build process is brittle
- Issue: scope is currently volatile due to different parties conflicting interests
- Assumption: we need to build the API for public consumption, although initially the only consumer will be the web client also being built
- Risk: internal resourcing constraints

_Summary_

Our RAID map will serve a few purposes:

- It will further inform our estimate and budget
- It will surface risk and allow us to take it into account when deciding
- It will further align our stakeholders participating at this point of the process

One we have it on hand, we can move on from cost and risk, and start looking at **return**.

What Is Our Expected Return On Investment
-----------------------------------------

The next data we need to understand is _How much will we benefit from the initiative?_

Remember from Part One that **success in any software endeavour is not simply having software at the end**.

Our business may benefit from a given initiative either in a generative way, such as by developing something that produces income by creating additional value in a product or service, or reduces operating costs; Or in a preventative way, such avoiding costly fines that may arise if a given system isn't compliant with legislation or commercial agreements by a certain time.

To calculate our return on investment, we need:

- The estimated value we are looking to gain, defined against our goal or goals, over a set period
- The allocated budget for the delivery
- The estimated implementation, change management, and operational costs of the initiative over the same period

The more complicated you make this calculation, the more assumptions you are baking into it, and the less likely it is going to be a good metric in determining success. Several articles, such as [this one on CIO.com](https://www.cio.com/article/2969353/enterprise-software/why-you-should-always-estimate-roi-before-buying-enterprise-software.html) suggest estimating ROI by applying many different factors and adjustments. Most of this is subjective complexity, and will ultimately make it much harder to determine whether a given software initiative has succeeded or not, as most of the inputs are largely unmeasurable.

Instead we should simply be comparing our [SMART](https://www.mindtools.com/pages/article/smart-goals.htm) goal's agreed measurable metrics over a period of time to the cost of the delivery and ongoing operational costs.

Ideally we want the outcome of this comparison to point to **at least a 300% return on investment**. Sound high? It's not.

The Standish Group's annual [CHAOS report](https://www.standishgroup.com/outline), which surveys thousands of companies each year to analyse software project successes and failures, as of 2016 still shows a huge 70% of projects either outright failing, or not meeting a bar that can be called successful. It also reports over half of all undertaken software projects costing double their original estimates.

Whilst it would be easy to attribute these failures to poor software delivery maturity on behalf of both vendors and customers, the reality is that software is that delivering software is **hard** (we should always seek to be  [humble programmers](https://www.cs.utexas.edu/~EWD/transcriptions/EWD03xx/EWD340.html)), and even the best delivery team, working for an ideal customer, can and will face unforeseen challenges that threaten the success of the initiative from time to time. 

This is why we want at least a 3x return. 

If the numbers don't stack up, but the goal is important to the business, go back to the drawing board with your scope and estimates - there is almost always a way to deliver less software to achieve very similar goals, as per the [Pareto Principle] (https://en.wikipedia.org/wiki/Pareto_principle).

---
A slight aside on the topic of ROI is the idea of _technical initiatives_.

What do we do with technical initiatives that do not contribute directly to a businesses products or services, but either reduce latent risk in a platform or improve it in such a way that future initiatives will benefit from it? These initiatives likely aren't products of our [Discover](https://www.andrew-best.com/posts/discover-decide-deliver-part-one/) phase, but are put forward by development teams who are the custodians of the platform.

The answer is to *always strive to have an initiative deliver some sort of value for the business*. You can almost always find a business initiative to deliver on when tacking a technical initiative - so if you want to rewrite a component in a new technology for example, find a significant business improvement to that component that could be delivered at the same time. If you can't find _anything_, then why undertake the technical initiative? We will have software before, and will have software after - this is not success.

Another perspective on technical initiatives, in this [post](https://gojko.net/2018/08/30/sprints-marathons-root-canals.html) penned by Gojko Adzic, is that if we allowed technical teams a budget for appropriate technical improvements and refactorings, we largely wouldn't need to devote large chunks of time to aggregate technical tasks.

---

Decision Time
-------------

Proceeding with a software initiative that is not likely to deliver an appropriate return on investment is not only costly from a financial point of view for the business, but the knowledge that the initiative is borderline profitable or worse tends to permeate the delivery. This leads to both substandard product development due to overly constrained resources, and to general negative impacts on delivery teams and cultures due to the oppresive nature of cut-throat product development.

If the return on investment meets our minimum threshold of 300%, the initiative gets the green light! If it doesn't, we either abandon the initiative, or find a way to reshape or redefine it until it represents a safe and sound investment to the business.

It is likely that our business may have a set of initiatives that are being evaluated at the same time, all with different costs and returns, addressing different stakeholder needs, that we will need to prioritise somehow in order to best benefit the business. All initiatives will have an associated [Opportunity Cost](https://en.wikipedia.org/wiki/Opportunity_cost). One way to base our prioritisation on empirical data, just as we based our decision to proceed with a given initiative on empirical data, is to calculate the [Cost of Delay](https://blackswanfarming.com/cost-of-delay/) for each opportunity, and prioritise accordingly.

It can be useful to capture all approved initiatives on a physical card wall, giving all stakeholders clear visibility of validated initiatives and their current priority.

Once an initiative rises to the top of the businesses priority stack, it is time to Deliver. Part 3 of this series will cover software delivery, and the behaviours and processes we can exhibit and embrace to improve our chances of success.

It is important to understand that evaluating the cost and return of a given initiative doesn't stop at Decide - that just gives it the green light to proceed. Cost and return are two sides of the same coin, and will both be closely monitored during Deliver, as we [Build, Measure, and Learn](http://theleanstartup.com/principles).