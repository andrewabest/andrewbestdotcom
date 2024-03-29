---
title: "Good Technical Strategy"
date: 2022-12-27T06:00:00+09:30
draft: false
---

The software you own will only ever get larger and more complex over time.

When your software is smaller, and your team is smaller - say 20 or less engineers - the best way to tackle problems is to get _the team_, or a subset of it, into a room. With a team of that size you can build consensus on the most important problems to solve, everyone can _agree/disagree and commit_ to solutions, and you can solve the biggest problems you are facing.

There is little need for technical strategy at this stage.

For smaller teams and systems, technical strategy likely amounts to a small set of technology choices made, generally based on the skillset of the team, possibly constrained by the problem space, and hopefully with some sensible architectural choices that will allow for the software to grow in a sane and predictable way.

The whole team likely knows the why behind these choices, and applies them day-to-day in their decision making. When the team encounters a problem that enough people agree needs to be solved, they can get together and do the needful to solve it.

At some point, as your software grows, and as your teams grow in size and in number, it will be harder to build consensus. People's perspectives will diverge, and they will think different things are the most important things. Overarching problems will exist that are bigger than any one team. Local optimizations will be invested in, and global optimizations will be overlooked.

If you are a technical leader - a VP of Engineering, a CTO, a Head of Engineering - it is your responsibility to identify this inflection point, and to help get ahead of this organizational problem. It is your problem to ensure the big problems do get solved in a timely and effective manner, so that the software can continue to evolve and scale to support the business's ongoing objectives.

One of the best tools to solve these types of big problems is a **good technical strategy**.

## Big Problems

There will come a time in your product's lifespan where problems exist that are bigger than any individual team can tackle.

Take the problem of ever increasing lead times as an example. Lead time is the time it takes you to deliver an idea from inception to production.

For most product companies, strong and dependable product velocity is vital. If you can't bring features to market in a reasonable timeframe, your competitors are going to eat your lunch, and your product may lose hard-won market share it had previously gained.

Reducing lead time in a meaningful way for a large and complex software product is a big problem. It is _hard_. No one team is going to be able to solve it. Where do you even start solving it? Is it skills based? Are we feeling the impact of previous technical choices? Is it previously accrued technical debt? Is it tooling?

A good technical strategy will frame one big problem, or possibly two at most. They are fundamental to the success of the product and the business. If you don't solve them, the implications are big. These problems are clearly too big for any one team to go and solve. They likely cut across several if not all teams. The ownership boundaries are fuzzy.

How do you go about tackling a hard problem like this?

You start by **creating clarity**. You need to clearly communicate the problem and its impact. The problem needs to be well understood by the organisation's decision makers. It has to be visible.

If the problem isn't visible and its impact isn't well understood, a solution won't be funded. Teams will not align on the importance of the problem. Ownership and budget will not be allocated to guide its success. It will continue to grow in impact and severity.

A technical strategy first and foremost communicates problems and impacts. Without technical strategy, big problems often do not get solved, as they are not well understood enough to be funded and prioritised above other localized work.

## Focus

With a _Big Problem_ identified, we need to be able to define _how_ we intend to solve it. This does not mean jumping straight into solutions though - these are the _what_ of the strategy, and will come later.

"How we intend to solve the problem" can be defined as a set of **guiding policies**, which provide a strong rationale and frame for solutions to be defined within.

Here is an excerpt from a technical strategy I recently developed:

> Completing Incomplete Migrations
>
> ...omitted for brevity...
>
> Much of this technical debt exists due to incomplete migrations. Migrations are large-scale pieces of work that trade investment now for productivity later. The biggest improvements to product velocity will come from finishing these migrations.

The guiding policy here is for completing incomplete migrations. The policy describes how it aligns with the identified problem (incomplete migrations are impacting lead time), and how following it will have a positive, large impact on the problem (completing migrations will significantly reduce lead time due to reduced engineering effort and increased engineering quality).

The policy level is a great place to sounding board your intentions. If you do not have confidence that investing in actions under this policy would have a strong impact on your big problem, it is probably a good sign you need to further analyze your problem to understand how you can best impact it.

Quite often your policies will sound obvious. _Deceptively obvious_. This is not a bad thing. Firstly, it is a sign you have achieved clarity of your problem. For the solution to seem obvious, the problem must be well understood. Secondly, for many big problems, you already likely know what the right courses of action are. You've thought about them at length. You've been impacted by them for a long time. A technical strategy does not need to be a revolution - just an evolution.

## Execution

You've built clarity on a _Big Problem_, and helped _Focus_ in on how the problem should be solved - now is the time for _Execution_ - what specific **actions** will we take to move the needle on our problem? How are we going to execute?

Execution means work. Doing the needful. Making it so. Taking action.

Without execution, we have no impact. We will discuss this further in the _Bad Strategy_ section below.

For the technical strategy referenced above, the actions under the _Completing Incomplete Migrations_ policy took the form of a handful of independently planned and executed projects that all aligned with the policy, and had a meaningful impact on the problem.

Sometimes the work that needs to be done will not neatly align with your current organizational structure. In fact, it probably wont. You also probably won't have "spare budget" laying around to fund the work either.

The hardest part of execution isn't writing code. It isn't shoveling mountains of technical debt. It isn't creating clever new architecture. Its figuring out how to get the work done in a way that is "commensurately disruptive" to all of the other very important things your company is doing right now. Your org will need to make tradeoffs to fund the execution of the technical strategy. You should weight the impact of disruption with the benefit of solving the identified Big Problems.

"Hold on! Shouldn't we just be able to get teams to do this alongside their other work?" you may say. Possibly. But this will still disrupt their work, at the very least prolonging timeframes and extending commitments.

The two things that will ensure you can execute effectively are _alignment_ - in which everyone agrees on the _Big Problem_ and its severity and impact, and _endorsement_, in which the strategy has appropriate executive or senior leadership buy-in, and prioritizing it above other in-flight initiatives is endorsed by them explicitly.

# Bad Strategy

We've talked about technical strategy having three main ingredients - a big problem, a set of guiding policies, and specific actions framed by those policies.

With these ingredients, we create clarity, provide focus, and deliver impact.

If you remove any one of the three ingredients, different problems can occur. I want to focus on the one that I've seen most often: strategy without execution.

Strategy without execution means **you never deliver impact**.

Many strategies simply look like a set of policies that do not clearly align with a specific problem, nor do they define specific actions that will solve those problems. They simply define policies that sound well-meaning, but ultimately have little impact on the product or the business.

Here is an excerpt from a technical strategy I consider to be a bad strategy. I'm happy to provide this as I authored it.

> Pits of success
>
> At {our company} you are free to focus on what is important - making changes to {our product} that delight our customers. We rely on automation to achieve this freedom.
>
> When we change a system, we rely on automation to test the system, with a green build signalling the software is shippable.
>
> When we contribute code to a system, automation ensures the contribution fits the system’s code conventions.
>
> When a system is changed and a new version is released, that release is automatically propagated to any dependent systems.
>
> These pits of success lower the cognitive overhead of working within the system - you can spend less time worrying about code style, release management, and other automatable tasks, and more time focused on your work.

I'd written a number of these policies into a document. They were well intentioned. They spoke of guiding principles we wanted our engineers to follow, and should they be followed, would result in high functioning, capable engineering teams with an optimal lead times and great control over the software under their stewardship.

I wasn't satisfied with the result.

I couldn't picture how teams, or the business, would be impacted by these policies. I couldn't put myself into the shoes of a team lead or engineer on a team, and see what value I would get out of this "strategy" - what specific behavior changes I would make after reading it.

Ultimately this was because the document did not create clarity on the specific problems it was looking to solve, nor did it define specific actions that would be taken to solve those problems. It relied on the reader to somehow translate its policies and connect them to localized problems, likely ending up with localized solutions.

I ended up throwing that strategy in the bin and starting again. I didn't regret the time I had invested in it - in fact it helped me build my own clarity on what the key ingredients of good strategy were, and gave me a concrete example of what bad strategy looked like.

# Can I Help?

Are you trying and failing to gain traction on big problems similar to what I've described above? Did this post resonate with you? Have a read of [my about page](https://www.andrew-best.com/about/) and drop me a line / DM / nerd-snipe on whichever of the channels at the bottom suits you best!

# Acknowledgements

The above is heavily influenced by Richard Rumelt's [Good Strategy / Bad Strategy](https://www.amazon.com.au/Good-Strategy-Bad-Difference-Matters/dp/0307886239), which strongly resonated with me, and helped build my own clarity on the ingredients of a good strategy.

I'd like to give props to my colleague [Alix Klingenberg](https://twitter.com/evolutionises) for using the phrase "deceptively obvious" recently to describe some of our organizational technical decision making, which to me perfectly encapsulates what many good technical decisions look like.

### Update Feb 2023

[Will Larson](https://lethain.com/) posted a [new article on writing technical strategy](https://lethain.com/eng-strategies/). I initially read Will's [original article on writing strategies](https://staffeng.com/guides/engineering-strategy) which I thought was insightful, but didn't quite gel with how our organization was working at the time. Will's new article more closely aligns with what I've written about above, and goes beyond that to provide some nice mental models for assessing your guiding policies, and some typical types of concrete actions you may execute under those policies.
