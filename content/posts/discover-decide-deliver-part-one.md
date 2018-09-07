---
title: "Discover, Decide, Deliver: Part One, Discover"
date: 2018-07-30T16:00:00+10:00
draft: false
---

_This is the first part of a three part series in which I cover my methods of delivering successful business outcomes via software.

[Part One: Discover](https://www.andrew-best.com/posts/discover-decide-deliver-part-one/)

[Part Two: Decide](https://www.andrew-best.com/posts/discover-decide-deliver-part-two/)

Part Three: Deliver

Backstory
=========

A customer approached me, who works in a key ICT management position within a large, publically traded company. They stated that their company was keen to adopt agile methodologies, but were not sure where they should start. This person was keen for me to deliver some informal, brown-bag style educational sessions to start building some knowledge about the _why_, _what_, and _how_ of agile delivery. 

They gave me the following list of _areas_ of their business they would be inviting, along with their current perception of each areas represented interest within an agile delivery:

> **Business Partners** Capture and prioritise requirements. Ensure business case sufficient to justify investment
> 
> **Architecture** Ensure design of solution is sound and adhere to standards/overall strategy
> 
> **Project Delivery** Completion of work scope on time, within budget, at right quality
> 
> **Ops** Transition of build to operations and support 

This was a pretty good start! They were looking to bring a broad cross-section of their business and ICT capability together to learn about agile - which means as an organization they understand that software delivery is a collaborative effort - the responsibility of many, not just a few. The noted interests of each area would need some calibration, but we could skip the _"agile doesn't work in isolation"_ part of the syllabus.

I find the best way to start a conversation about agility is to first introduce all parties to a few ***Undeniable Truths of Software Delivery*** that can serve as constraints:

> * If you don't know your goals, the outcome doesn't matter
> * The more software you attempt to deliver, the less likely you are to succeed
> * Upfront analysis adheres to a law of diminishing returns
> * Chaos, uncertainty, and change are absolutes within software deliveries
> * Software delivery in *any* methodology requires rigour and discipline to succeed
> * If you can't measure your outcomes, you'll never know if you succeeded

I was in the process of building a slide deck to help facilitate the sessions. After capturing these _undeniable truths_, considering what I wanted to present, and shuffling a heap of placeholder slides around in the deck I was curating, I came to a taxonomy that I thought accurately reflected the structure of what I wanted to present.

Discover, Decide, Deliver
=========================
* *Discover* what our problems and goals are, and what value we are looking to create for our business
* *Decide* on a solution shape, associated risks, budget, ROI, and whether we should proceed
* *Deliver* the solution incrementally, embracing agile and lean tenents. Evaluate whether each increment is delivering the expected value.

I was hoping the parties who would be attending would eventually see that their involvement, whilst spiking and waning during the process, would be required _throughout_ the process, all aligned towards delivering valuable solutions that contribute to business goals.

The above structure may sound familiar to some, and certainly anyone studying agile or lean methods have likely come across many similar circular constructs:

[Colonel John Boyd's](https://en.wikipedia.org/wiki/OODA_loop) *Observe, Orient, Decide, Act*

[The Demming Cycle's](https://en.wikipedia.org/wiki/PDCA)  *Plan, Do, Check, Act*

[The Toyota Kata's](https://en.wikipedia.org/wiki/Toyota_Kata) *Vision, Current, Target, Iterate*

[Lean Startup's](http://theleanstartup.com/principles) *Build, Measure, Learn*

All of these methodologies champion small iterative efforts, just-in-time or last-responsible-moment contextual decision making, empirical observation, and continuous improvement, enabling positive progress in uncertain conditions.

*Discover, Decide, Deliver* is slightly more opinionated and fitted towards how I and my teams deliver outcomes for customers, but is very much embracing the same fundamental principles. It applies equally to start-ups, product teams, or software consultancies.

Discover
========
There are many reasons an enterprise may think they need to develop custom software:

* Software may already be a core part of their business strategy
* They may be struggling to compete in their industry and need to differentiate or disrupt 
* Someone within the executive may have decided they _must_ have a thing (a [HiPPO](http://www.askahippo.com/) driven decision) 
* There may be a clear pain that could be automated away

I see all of these with a reasonable amount of frequency.

The _two biggest mistakes_ that are made at this stage of a software initiative are:

* Jumping straight into solutioning
* Spending too long in up-front planning

The first problem, _jumping straight into solutioning_, is solved during **Discover**.

The second problem, _spending too long in up-front planning_, is solved during **Decide**.

Before we start talking about implementation detail, budgets, scopes, timeframes, teams, tech, or anything else, we need to identify our problems, and our goals. 

Why problems? Because software needs to deliver value, and value is delivered by solving problems that exist for our business, its staff, or its customers. Problems don't necessarily mean "cannot do a thing", we are using the definition of problem here as "thing to be solved". As my good friend and colleage [Mehdi Khalili](https://www.mehdi-khalili.com/) said to me when I was discussing this article with him, _"Problems don't need to be problematic"_.

Why goals? Because **success in any software endeavour is not simply having software at the end**. It is not having this feature or that report. Software exists to serve humans, to deliver value to them and advance their enterprises, commercial or otherwise.

Ensuring we know the goals of our business, its strategies, and its envisioned future state, means we can hypothesize how the software we might build can enable that success. Goals provide focus to solutions  and communicate intent to executive, stakeholders, and delivery teams. 

There are many good ways to discover and validate problems, and evaluate goals - some of them require more investment, and typically these are employed when there is less certainty or higher risk; and some less, employed when we have more certainty around our problem and potential solution, and more alignment between stakeholders. Sometimes a problem will have an obvious goal and solution. More often though, it will not.

Here are some that I would typically recommend or employ. Some of them I can execute on my own with a customer or business, and some require a team of people to drive the effort. This list is ordered by the rough effort listed with each approach, with the most costly first:

* [Design Thinking](https://en.wikipedia.org/wiki/Design_thinking) ~2-6 weeks
* [Design Sprints](https://designsprintkit.withgoogle.com/) ~1 week
* [Impact Mapping](https://www.impactmapping.org/) ~4 hours
* [Brainstorming, Affinity Mapping, and Multi-voting](http://asq.org/learn-about-quality/decision-making-tools/overview/multivoting.html) ~2-4 hours
* [5 Whys](https://en.wikipedia.org/wiki/5_Whys) for goals ~2 hours
* A [Mind Map](https://www.mindmup.com/) built with key decision makers ~1-2 hours

The top three techniques also produce artefacts for potential solution scope that can be taken into the **Decide** phase, and the bottom three can be extended to accomplish the same.

It is extremely valuable to have a visualised map from your problems and goals to potential solutions for those goals, as it visualises the assumptions you are making in this phase. At this point we are simply stating *"We assume that should we produce software of this shape, it will help us achieve these goals"*. Anything that cannot be tied back to contributing to the goals should be firmly rejected. Assumptions can only be validated completely once working software is in users hands.

**Impact Mapping** tends to hit the sweet spot of allowing you to both discover and quantify organizational goals, and connect that via a web of visual assumptions to a potential scope. I won't detail the technique here - [buy Gojko's book](https://www.impactmapping.org/book.html), it is a very easy read, and will prepare you well for applying the technique. Something my colleagues and I have been discussing is extending Impact Mapping by adding problem discovery and validation prior to goal discovery, to ensure goals aren't arbitrary, and can be connected back to humans. This may be the subject of another post in the future.

_A word of warning_: Impact mapping tends to lose its effectiveness if you have strong constraints on your solution space - i.e. if there is a mandate that all solutions are some sort of mobile app. If there is a strong solution constraint, I'd be working backwards in a mind-map to ensure the solution constraint supports clear goals and solves real problems.

Remember when I earlier stated:

> The more software you attempt to deliver, the less likely you are to succeed

Regardless of the technique employed to create our goals, we want to make sure they are [SMART](https://www.mindtools.com/pages/article/smart-goals.htm).

If the scope discovered seems too large, this is a symptom of either too generic or broad of a goal, or aiming for too many goals at once. At any given time, we only want to be focussing on one goal per initiative or team. Doing so means we can equip the team with a lazer-focus to achieve that goal, and closely scruitinize any potential solution scope to ensure it will support the goal, maximising our impact and minimizing waste.

Who should participate?
-----------------------

During **Discover**, we want to talk to people who have depth in their respective parts of the business, who can adequately represent that portion of the business in the discussion. We want to be talking to key executive, product, service, IT, customer facing representatives, and customers themselves, if possible. 

With most discovery activities, less is more for stakeholder count - you absolutely want less than a dozen stakeholders participating, and for the lower-ceremony techniques such as Impact Mapping, 4-8 is the sweet spot. Any more will dilute conversations, introduce too many opinions, and take away from our goal of having a light weight, agile discovery process. 

You will likely not be able to identify your 'perfect audience' immediately - starting with executive stakeholders and key business decision makers is a good idea, and look to pull in or solicit the input of other stakeholders when it is deemed necessary.

Decision
--------

Once we have a clear view of the problem we want to solve, a SMART goal established and agreed upon that supports the problem, and have identified a potential solution scope that could be delivered that may take us toward that goal, the business needs to **Decide** whether it should proceed with the initiative, or not.

Stay tuned for the second article in this series, **Discover, Decide, Deliver: Part Two, Decide**.