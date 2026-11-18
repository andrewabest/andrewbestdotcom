---
title: "Engineering Vitals in the Age of AI"
date: 2026-11-18T06:00:00+10:30
draft: false
canonical: "https://octopus.com/blog/engineering-vitals-in-the-age-of-ai"
---

Every engineering organization is being asked the same question right now: is AI actually making us better at building software?

It’s a fair question. We’re spending real money on tokens and tooling, and we’re reshaping how our teams work. We should be able to say what we’re getting for it.

The answers I see tend to fall into two camps. The first counts activity: lines of code generated, tokens consumed, the percentage of code written by agents. The second skips straight to the destination, and declares that the only thing worth measuring is _value_.

LaunchDarkly’s recent [Release Autopilot announcement](https://launchdarkly.com/blog/introducing-release-autopilot/) is a good example of the second camp. It describes the goal they set for their AI software factory: maximize the valuable software reaching customers for each hour of human attention it takes to get it there.

That isn’t a goal. It’s an aspiration.

How do you count _valuable software_? Value shows up weeks or months after changes ship, and it’s the product of many decisions made across product, design, engineering, and sales. Value is a team sport. How do you count an _hour of human attention_? I won’t be the person who introduces timesheets for attention.

It’s worth looking at the evidence the same post offers. With roughly the same number of engineers, monthly merged pull requests more than doubled, and time spent in major and critical incidents fell by about 70%. Those are throughput and stability measures. They’re good evidence, and they sit a lot closer to a pull request count than they do to value.

This post is about what we’ve chosen to measure at Octopus, and why. None of it is revolutionary. But some of it is counter to the current product development zeitgeist.

## Outcomes are made of outputs

_Outcomes over outputs_ is one of the most repeated phrases in product development. It’s true that the outcome is ultimately the thing that matters. But the phrase obscures how we get to one.

Outcomes are realized through the continuous delivery of outputs. We make a decision, ship something, watch what happens, and decide again. It’s the [OODA](https://en.wikipedia.org/wiki/OODA_loop) loop. The first thing we ship is almost never the most valuable thing. It helps us learn, pivot or persevere, and eventually reach a point where we’ve realized a lot of value.

You can’t ship just one thing and win. You also could ship infinite things and not win. You’ve got to produce outputs and go through the loop.

This is the _value discovery loop_. The faster we complete it, the more value we accumulate over time.

> Velocity: the speed with which we complete the value discovery loop.

Outputs are a proxy to value.

## Engineering vitals

Who is the primary driver of the value discovery loop? Engineering.

“But Besty, our product managers and designers are all shipping changes to our product these days thanks to Claude!” you might say. Congratulations to them - they are now also engineers, and their efforts will be reflected in engineering’s vitals.

One of the jobs of engineering leaders is to unblock and enable their organization’s teams to ship as effectively and efficiently as they can. Once you go past a handful of engineers, you can’t put your hand on your heart and say you are fulfilling this responsibility without having some sort of data to back it up.

At Octopus there have been _multiple_ times when we have paused to measure something in our delivery pipeline, and discovered some _healthy_ optimizations we could make.

Your vitals should give you insight into how effectively your engineering organization is producing outputs and maintaining its service level objectives. They will help you move beyond vibes, and to a more concrete view of how well you are delivering and contributing to your organization’s outcomes.

## Why we rebooted our vitals

AI is a disruptive force, and at Octopus leaning into this disruption matters. AI is a lever - it can help us achieve more, and provide an increasingly better customer experience, without significantly increasing our costs. But even more importantly - our customers are undergoing the same disruption. Our customers are engineers - they ship software day in, day out, and most are somewhere along the same AI adoption journey we are on at Octopus. To truly empathize with our customers’ pain on this journey, we need to live it ourselves.

Our hypothesis about where software engineering is heading is simple: _an overwhelming volume of change_. We’re already seeing two-engineer teams become normal thanks to the leverage AI gives them, and the time it takes to build and ship is compressing.

We are rebooting our _engineering vitals_ because our AI transformation is changing the shape of our work. I don’t use the word “transformation” hyperbolically here - the changes are actually _transformational._ We need to know whether the shifts we’re making in our AI-driven development lifecycle are helping us get through the value discovery loop faster, while maintaining our SLOs.

If the hypothesis holds, these are the questions we need to be able to answer:

- How frequently are we shipping changes and progressively releasing them to customers?
- Are our engineering feedback loops meeting our standards for speed and reliability?
- Is the volume of change increasing as we lean into agentic workflows, while headcount stays stable?
- Is work starting that previously wouldn’t have been done regularly, or at all?
- Is our operational burden shrinking? Fewer people, less time, less toil.
- Is quality improving at the same time?
- Are people still finding joy and satisfaction in their work, as the nature of that work changes?

These questions help us understand the effectiveness of our engineering organization, and help to identify which levers we might be able to pull if we spot things that are slowing us down.

AI adoption deserves its own set of measures that sit alongside engineering vitals - I'll talk about those in a future post.

## The measures

We’ve divided our vitals into three areas.

**Delivery objectives** measure the effectiveness of our delivery pipeline and feedback loops. They’re measured per system, and the team that owns the system sets the service level objective (SLO).

**Quality objectives** measure the quality of what we ship, and the quality of our developer experience. They’re measured across the whole organization, and I as CTO own the SLOs.

**Trends** are measured across the whole organization, and deliberately have no SLO. We capture them because they give us insight, particularly when read alongside the objectives.

| Area | Measure | What it tells us |
| --- | --- | --- |
| Delivery | Deployment lead time | The time from the first commit on a pull request to that change running on 100% of production instances. How long it takes to put work into customers’ hands once we’ve started it. |
| Delivery | Pull request churn, cycle time, and review latency | Where changes slow down between being opened and being merged. |
| Delivery | Time to recovery | The time from a defect being merged to a fix being available to affected customers, split into detect, resolve, and deploy. |
| Delivery | Build time and build stability | Rolling 7-day averages for branch builds and `main` builds. The health of our innermost feedback loop. |
| Delivery | Batch size and deployment delta | How far what’s deployed lags behind the tip of `main`, in commits and in time. |
| Quality | Sev 0-2 count | The number of high-severity defects that reached customers each month. |
| Quality | R&D NPS | Whether our platforms and developer experience are getting better in the eyes of the people who use them. |
| Trend | Pull request volume | Pull requests merged over time, in aggregate. Our test of the _overwhelming volume of change_ hypothesis. |

If you know the [DORA metrics](https://dora.dev/guides/dora-metrics/), most of this looks familiar. However there is one particular fight I’ll pick with DORA’s metrics - change failure rate does nothing to help you understand customer impact.

### Do not obscure the customer impact of failure

We don’t measure change failure rate.

For a long time our change failure rate sat below 2%, which is healthy by any benchmark. Yet sometimes the failures that did occur still had significant customer impact. The signal said everything was fine. Our customers’ lived experience said otherwise.

AI makes this problem worse. A rate has a denominator, and the denominator is the thing we expect to explode. If we double the changes we merge and hold our failure rate steady, we’ve doubled the defects reaching customers, and the dashboard hasn’t moved.

Customers don’t experience a percentage. They experience the defect.

So we count. Every month we look at the number of Sev 0-2 defects that reached customers, and as leaders we dig into the specific issues behind the number. We can still derive a rate if we want one.

This only works if all work is tracked. If a defect has been merged to `main` and deployed, it’s logged with a severity, whether a customer found it or we did. We’re our own _customer zero_, so any issue that affects us affects a customer.

### Trends without targets

We care about velocity, and need to reason about it in aggregate over time. In this, we take heed of Goodhart’s law for trends: when a measure becomes a target, it ceases to be a good measure. So we don’t set goals for it.

Pull request volume is our proxy for velocity, and our window into the value discovery loop.

We care how quickly we are getting through the value discovery loop. We want to see a trend up and to the right as we lean further into agentic development.

We set _other_ measurable goals that seek to impact velocity. An example might be the % of PRs that are shipped without human intervention.

Trends are particularly useful in combination with service level objectives. Pull request volume rising while Sev 0-2 counts hold flat and lead time stays steady tells a richer story than just understanding velocity is improving.

## We’re in good company

While counter to some popular product development ideologies, none of this is particularly esoteric.

I’m a big fan and a regular reader of Gergely Orosz’s Pragmatic Engineer newsletter. In one publication, Laura Tacho and Gergely looked at [how 18 tech companies measure the impact of AI](https://newsletter.pragmaticengineer.com/p/how-tech-companies-measure-the-impact-of-ai), including Google, GitHub, Microsoft, Dropbox, and Monzo. Two findings stood out to me:

- Most of the types of measures look the same as they did before AI. The companies getting clear answers are the ones that had already invested in understanding their engineering performance.
- Almost every company pairs a measure of speed with a measure of quality, so each keeps the other in check.

More recent data tells the same story with bigger numbers. [Atlassian’s write-up](https://www.atlassian.com/blog/ai-at-work/measuring-and-orchestrating-ai-across-the-full-engineering-lifecycle) of DX data from more than 500 companies reports that median pull request throughput has doubled. It also reports that pull request size grew 122% in four quarters, engineers’ confidence in their changes dropped, and the share of capacity going to new customer value moved by a single percentage point.

That’s the _overwhelming volume of change_ arriving, without the value arriving with it. It’s also why pull request volume is a trend for us and not a goal.

The same write-up describes Dropbox pointing its unlocked capacity at long-deferred maintenance, like language migrations and dependency upgrades. That’s one of our own questions being answered: work is starting that previously wouldn’t have been done.

There’s one place we’re out of step. Nearly all of these companies track change failure rate as their quality measure, and we’ve chosen counts. I’ve explained why above.

And to be fair to LaunchDarkly, their numbers fit this pattern too. Merged pull requests up, incident time down, headcount flat. Speed and quality, measured side by side. It’s good evidence that their machine is working. Whether the software was _valuable_ is a different question, answered somewhere else.

Speaking of LaunchDarkly, did you know Octopus now has [Feature Flags](https://octopus.com/docs/feature-flags)? Octopus feature flags are available in all license tiers at no additional cost, are built on [OpenFeature](https://openfeature.dev/), and are a natural fit for organizations deploying with a high frequency, who want more control over the release of changes to their customers and users.

## Conclusion

AI hasn’t changed what makes an engineering organization effective. It has changed how much flows through one, and how quickly a weakness gets exposed.

Measuring value per hour of human attention is a fine thing to want. But value is a lagging signal with many owners, and you can’t steer your organization by it week to week.

So start with the measuring what you can steer. Know how long it takes to get a change into customers’ hands, how quickly you recover when a change is wrong, how many serious defects reached customers this month, and whether the volume of change is growing while those numbers hold.

When you can answer those questions on demand, you can then raise your gaze: if your measures are healthy and the needle still isn’t moving, you know where to look next.

Measure the efficiency of your engineering machine. Then set your sights on value.

## Sources

- [AI changed how we write software. We must change how we release it.](https://launchdarkly.com/blog/introducing-release-autopilot/), LaunchDarkly, November 2026
- [State of AI-assisted Software Development](https://dora.dev/research/2025/dora-report/), DORA, 2025
- [Balancing AI tensions: Moving from AI adoption to effective SDLC use](https://dora.dev/insights/balancing-ai-tensions/), DORA, March 2026
- [How tech companies measure the impact of AI on software development](https://newsletter.pragmaticengineer.com/p/how-tech-companies-measure-the-impact-of-ai), The Pragmatic Engineer, September 2025
- [Measuring and orchestrating AI across the full engineering lifecycle](https://www.atlassian.com/blog/ai-at-work/measuring-and-orchestrating-ai-across-the-full-engineering-lifecycle), Atlassian, November 2026
