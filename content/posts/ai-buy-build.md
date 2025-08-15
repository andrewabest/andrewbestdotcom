---
title: "Rebalancing Buy vs Build with AI"
date: 2025-08-15T08:00:00+09:30
draft: false
---

I've currently been using Claude Code a lot in my day to day work. There are a lot of AI hot takes floating around the tech industry right now. One way you can cut through those hot takes is to observe the impact it is having _where you are_.

I work at [Octopus](https://octopus.com/) and am surrounded by some of the very best software engineers in the industry. I value and leverage their opinions continuously in my own work, and they always improve the quality of my execution and my decision making.

We've gone to a bit of effort recently to ensure all of our engineers have an AI tool available to them that they can use reflexively in their day to day work. There is likely an entire post to be written on that process, how we've approached it, and the impact we are seeing, but I'll reserve that for another time.

Much of the teeth gnashing and hyperbole around AI tools centers around them replacing human software engineers. That they are evolving in a way that leads us toward some sort of singularity where AI growth becomes uncontrollable and irreversible, replacing humans and altering the course of history.

But if you observe _really good engineers_ working with AI, you don't see them despondent. You don't see them belligerently avoiding the tools, espousing the values of hand-wrought code and human toil. The response spectrum I see goes from humorous observation when the tools occasionally and inevitably go off the rails, through to delight and awe when they create solutions with a fraction of the effort or cost than it would have taken previously.

The famous Steve Jobs quote "Computers are like a bicycle for the mind" has been reframed several times, most recently as "AI is like a motorcycle for the mind". This is what I see when great engineers get their hands on these tools and start using them in anger. Once engineers start building an intuition for where the tools are strongest, and how they can accelerate or completely eliminate toil and tedious tasks, or make tasks that were previously too costly now surmountable and achievable, you see them eagerly leaning into these tools, realising how much _better_ they make the craft of building software.

## Buy vs Build

We've had a policy in place at Octopus for some time for making buy vs build decisions. 

> If solving a problem is NOT core to our business, we are strongly inclined to buy a solution, than spend the time and effort building one.

However, there is a caveat to this policy. Often we run into problems of this shape, but _there are no good options that exist in the market_ to buy.

This might mean that the options that exist don't solve the problem well. Or they don't solve it well in our context. Or they are not economically feasible - it might be a small problem to us, but solutions are priced for enterprise-grade versions of the problem.

This leaves us in a tricky situation. Either we overpay to solve the problem; or we try and ignore the pain the problem is causing us; or we spend quite a bit of time hacking together a solution, which also won't usually have a positive cost vs value outcome.

I ran into this problem recently.

I'm heading up the effort to bring AI capabilities into Octopus, helping our teams solve problems that were previously unsolvable using the same LLM technology our engineers are enjoying in their daily work. We are hiring by the way: [octopus.com/careers](octopus.com/careers).

Building solutions that integrate LLMs, typically called agents, requires new tooling to support new feedback loops that aren't found in existing software systems. Core to these new feedback loops are evals. 

Evals are human-driven, LLM-assisted feedback loops that help you assess and manage the quality of your agents outputs. They are your unit-tests for non-deterministic workloads. They give you confidence that your agent is delivering quality outputs across a broad range of inputs. If you want to learn more about evals, go read [Hamel Husain's](https://hamel.dev/) posts from the past two years - he is the authority on evals.

Now there are solutions in the market to this problem at the moment. I've evaluated a handful of them, including [LangSmith](https://www.langchain.com/langsmith) and [Braintrust](https://www.braintrust.dev/). All the solutions have a number of drawbacks _in my particular context_. Common to these were:
- You only get a first-class experience with them if you are using python (or occasionally TypeScript)
- They have opinionated workflows for iterating on prompt and agent development that don't take first-class function calling into account. They want to be walled gardens for agent development

These problems are both showstoppers for me. I'm developing agents with .NET, using [Semantic Kernel](https://github.com/microsoft/semantic-kernel). And the agent is deeply integrated with Octopus's core domain, giving the agent access to fine-grained capabilities that help it fulfil its duties.

What I need from an eval tool is:
- It must work purely by ingesting OpenTelemetry traces, without additional instrumentation
- It must give me a way to create eval sessions of arbitrary size driven from existing common automated test tooling (like XUnit or Jest)
- It must exercise the agent as-built, with full access to the functions it can call within Octopus to fulfill its responsibilities. 
- It must provide me a productive interface for human assessment of trace sessions that emphasizes readability and rapid review

Later, I'll want to graduate into managing golden datasets of labelled traces and automating evals leveraging LLM-as-judge. But for now, I have the former requirements.

Previously I'd be in a bind at this point. I'd buy one of these tools and deal with the shortcomings. Or I'd do something cheap and cheerful, like export traces to disk, parse them into CSV, and do evals in spreadsheets. Or I'd spend a week or so building the starts of a tool that would suit my needs.

AI has put its thumb on the scales of the buy vs build conversation though. It is now _much more viable_ to build bespoke tools to solve problems than it was previously. What previously might have taken days or weeks, now typically takes minutes or hours.

## Building an AI Eval Tool

I chose to build my own AI Eval tool for this reason - it now seemed like a viable path to take. I'd build it primarily via Claude Code, and tweak and finesse it as I went along.

I'd already scaffolded some automated tests around our agent that drove it with a set of representative sample inputs gathered from production scenarios, and the agent emitted OpenTelemetry traces that contained the details we would want to evaluate. I used XUnit for these tests, but this approach is technology-agnostic, you could do the same with JUnit, or Jest.

I also added an [OpenTelemetry file exporter](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter/fileexporter) to our local otel collector to ensure I could capture the traces on disk and feed them to my eval tool.

One of the most important thing you can do when building tools with AI is to use a tech stack you are comfortable with, and often one that aligns with where you will use the tool. In my case I chose to use [Blazor](https://dotnet.microsoft.com/en-us/apps/aspnet/web-apps/blazor), a lightweight web framework for .NET. The benefit of this is that the tool could live alongside the agent and its automated tests in our solution, and could be developed within and executed by the same IDE environment.

With those pieces in place, the solution came together over a two-hour session. 

One of the keys to success with AI tools is managing context. The best way to do this is to break work down into chunks. 

One of the first jobs I needed to do was to select and upload an OpenTelemetry jsonl file, then read it into memory and parse it into a data structure I would use for evaluation and storage. I broke this down into the following steps:
- Build a UI to select and upload a file, and log its contents on the backend
- Parse the uploaded file into a particular data structure, called Sessions. I gave Claude the input file structure via example, and defined the target model by describing it in words, and how the inputs would map to the model. It would then write each individual session to disk
- Display a list of the uploaded sessions in the UI, and allow the user to click on one and open up a session details page, using the session ID as a route. Stub out the details page

By using Claude's plan mode through each significant increment, adjusting the plan if required, then `Shift+Tab`-ing into auto apply mode, I quickly built out an application that satisfied my requirements.

## Conclusion 

There are a few great things about vibe-coding your own tools:

- You can get results in less time than it would take you to discover tools in market that solve your problem, set up trials of them, and integrate and test their effectiveness.
- You can tailor them to your context and needs. No more fitting your problem to their solution.
- Once you are done with it, or your needs exceed what you're willing to build into the tool, you can throw it away. You've only spent a handful of hours on it, not days or weeks. It is not your precious. There is no sunk cost. You can discard it when you're done.
