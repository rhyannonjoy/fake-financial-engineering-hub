---
layout: default
title: "Teaching as Engineering Infrastructure"
parent: Blogs
nav_order: 4
---

# Teaching as Engineering Infrastructure

![The More You Know](../../static/img/the-more-you-know.png)

## Description and Objective

In this piece, I explore teaching as the overlooked foundation of
engineering work. While most engineers learn to write code, few
receive training in how to effectively share knowledge with their
peers. As AI-generated code becomes ubiquitous, teaching engineers
to understand and critically evaluate systems becomes even more
critical; _you can't vet what you don't understand_. Every line of
code you write, every system you design, every review you give is
teaching - the question is whether you're doing it intentionally or
accidentally. Drawing from experience facilitating skill-up sessions,
reading groups, and mentoring programs, this blog provides actionable
strategies for making technical education engaging, useful, and
respectful of time.

---

## In This Post

- [Why Teaching Matters](#why-teaching-matters)
- [Effective Technical Teaching](#effective-technical-teaching)
- [Practical Applications](#practical-applications)
- [Common Pitfalls](#common-pitfalls)
- [Summary](#summary)

---

## Why Teaching Matters

1. **Knowledge Silos Are Expensive**

    When only one or two engineers understand a critical system, you're one vacation
    or job change away from a crisis. I learned this lesson during my first MIP,
    monthly-invoicing process. The knowledge needed to run this multi-million dollar
    financial process lived primarily in senior engineers' heads. When they weren't
    available, the rest of us scrambled through Slack history and old tickets.

    Knowledge silos create operational risk and scaling bottlenecks. If only one
    engineer understands a business-critical service, that engineer becomes the
    bottleneck for every feature, bug fix, and incident related to that service.
    Teaching distributes that siloed knowledge into a capability that belongs
    to the entire team.

2. **Future-You Won't Remember**

    Remember that database migration strategy you architected six months ago? The
    nuanced trade-offs you considered when choosing between eventual consistency
    and strong consistency? The specific reason you structured the invoice-grouping
    logic that particular way?

    Future-you won't remember. Neither will the engineer who inherits your code.

    This is the same cognitive load problem we discussed in
    [Anti-pattern: Magic Numbers](antipattern-magic-numbers.md). If you don't externalize
    knowledge through teaching and documentation, you're trapping your team in
    expensive discovery cycles - forcing everyone (including yourself)
    to re-scavange and re-discover the same insights over and over.

3. **Async Work Requires Teaching**

    When your team spans multiple time zones and you can't tap someone on the shoulder
    for a quick explanation, teaching is infrastructure. Good technical teaching means:

    - **Self-service learning materials** that answer questions at 3 AM
    - **Searchable knowledge** that survives team turnover
    - **Recorded skill-up sessions** that new engineers can watch during onboarding
    - **Documentation that teaches context**, not just what the code does

    As discussed in
    [How to Write a Reference Guide for a Financial Process Responsible for Millions of Dollars](how-to-write-reference-guide.md),
    documentation is only as good as its discoverability and clarity. The same applies
    to any form of technical teaching.

---

## Effective Technical Teaching

1. **Respect Their Time**

    Everyone is busy. Streamline the learning process with actionable content,
    not fluffy preambles. Set the stage with an actual problem, then show
    them how to solve it.

    ```markdown
    <!-- Bad Teaching -->
    "Today we're going to learn about database indexing. Indexing is important 
    because it makes queries faster. Let's start with the history of databases..."

    <!-- Good Teaching -->
    "Our invoice queries were timing out during MIP. Here's the query, here's 
    the execution plan showing a table scan, and here's how adding an index 
    on (`account_id`, `invoice_date`) fixed it. Now let's talk about when indexes 
    help and when they hurt."
    ```

    Context and theory come after you've proven you're not wasting their time.
    The most effective teaching sessions started with "this doc will save you
    countless hours of firefighting."

    **In Practice**:

    - **Tutorials**: put the working code example at the top, explain it afterward
    - **Skill-Up Sessions**: start with "here's the bug this technique prevents"
    not "here's an interesting technique"
    - **Documentation**: answer the question first, provide background second
    - **Code Reviews**: "this pattern prevents race conditions in our outbox
    publisher" beats "consider using this pattern"

2. **Show the Real Problem, Not Just "Hello World"**

    The engineers you hired probably don't need a "Hello World" snippet,
    they need to know:

    - How to handle errors when production data doesn't match your schema
    - How to test components that depend on external services
    - How to debug when things fail in ways the docs didn't anticipate
    - How an elegant design pattern behaves under load

    **Example: Teaching Message Queues**

    ```markdown
    <!--'Hello World' Approach -->
    Here's how to publish a message to SQS:
    [code snippet that works in ideal conditions]
    Done! You now know SQS.

    <!-- Real-Problem Approach -->
    During MIP, we process 500k+ invoice requests through SQS.
    Here's what we learned:

    1. Messages can arrive out of order - here's how we handle that
    2. Processing can fail mid-batch - here's our retry logic
    3. The DLQ fills up with messages that need manual investigation - here's 
    how we redrive them
    4. Visibility timeout needs tuning based on processing time - here's how 
    we monitor and adjust

    [Working code examples for each scenario]
    ...
    ```

    The real-problem approach teaches application in prod, not
    how to call an individual method. While creating the
    [MIP Reference Guide](../mip-reference-guide/index.md),
    happy paths didn't create meaningful sections. Meaningful
    learning happens when troubleshooting failures.
    **This doesn't mean** conceptual writing and/or teaching shouldn't
    exist, it means, as my tech writing instructor says: "_knowing your
    audience is everything_".

    **In Practice**:

    - **Demos**: show complete workflows, including error handling
    - **Tutorials**: include a "Common Gotchas" section based on real issues
    - **Architecture Docs**: explain the trade-offs and edge cases, not just the happy path
    - **Onboarding Materials**: share actual production incidents and their resolutions

3. **Meet Engineers Where They Are**

    Everyone's got different learning styles and context.
    Rather than forcing everyone through the same linear path,
    meaningful teaching offers access and options.

    **The Three Archetypes**:

    > AKA [the end-user software engineer framework](https://rhyannonjoy.github.io/api-docs-glossary/docs/frameworks-strategy#end-user-software-engineer)
    in technical writing contexts, and if you're me,
    you have three wolves inside you -

    **The Pragmatist** wants to solve their immediate problem -
    - "Just show me the code that works"
    - "What are the gotchas I need to know right now?"
    - "Where do I find X when Y happens?"

    **The Architect** wants to understand the system -
    - "Why did we choose this approach over alternatives?"
    - "How does this scale?"
    - "What are the failure modes and how do we handle them?"

    **The Tinkerer** wants to explore and experiment -
    - "Can I try this in 5 minutes?"
    - "What happens if I change this parameter?"
    - "Show me something cool this can do."

    **Example: Teaching [the MIP](../mip-reference-guide/index.md)**

    ```markdown
    <!-- For the Pragmatist -->
    - Quick Reference - DFR Checklist, step-by-step guides
    - Common queries ready to copy-paste
    - Datadog dashboards bookmarked and labeled

    <!-- For the Architect -->
    - System architecture diagrams
    - "Why We Built It This Way" docs
    - Incident retrospectives that explain design decisions

    <!-- For the Tinkerer -->
    - Sandbox environment to experiment
    - "Try These Scenarios" exercises
    - Code examples they can modify and run
    ```

    [The MIP Reference Guide](../mip-reference-guide/index.md)
    works to serve all three archetypes and respond to change.
    The pragmatist could jump to "Remediate → Retry Failed Pending
    Invoice Requests" and get their answer in 30 seconds. The
    architect could read the historical context re: replication lag.
    The tinkerer could run the example queries against the test
    database.

    **In Practice**:

    - **Documentation**: use clear navigation that lets people skip
    to what they need
    - **Presentations**: offer "deep dive" appendix slides for the
    architects
    - **Code Examples**: provide both minimal examples and complete
    implementations
    - **Office Hours**: let people bring their specific problems
    rather than forcing topics

---

## Practical Applications

1. **Structuring Skill-Up Session Tutorials**

    - **Before the Session** - share context, provide pre-reading,
    set clear expectations
    - **During the Session** - start with a demo with real code,
    leave time for questions, record it
    - **After the Session** - share the recording, create a
    summary doc, follow-up with suggested implementations

2. **Facilitating Meaningful Reading Groups**

    - **Pick Relevant Material** - tie readings to real problems,
    mix academic and practical content, rotate who picks the reading
    - **Structure the Discussion** - frame the meeting around specific
    types of questions, "is this preventative?" to "would this
    work within our domain?" to "what's the quickest way to
    experiment with this?"
    - **Make it Actionable** - create tickets, document what you
    tried, share learnings with the broader org

3. **Creating Effective Onboarding Materials**

    Don't force engineers to reconstruct your team's context
    from scratch. Assemble information that teach the "why"
    behind your systems and include:

    - **System Overview** - annotated architecture diagrams,
    links to docs that deep-dive
    - **Historical Context** - describe the outage that led to
    specific monitoring with the trade-offs
    - **Practical Workflows** - how-to deploy, investigate, ask
    great questions
    - **Gotchas and Tacit Knowledge** - misleading naming conventions,
    procedural reminders, "the cron job is critical, but the
    docs suck"

4. **Mentoring Engineers**

    Meaningful mentorship relationships at any stage are
    bidirectional - ask questions that expose gaps in docs,
    learn by teaching, and together they both improve the code.

    - **Don't Assume Knowledge** - ask questions, check their
    understanding, share your own learning journey
    - **Provide Context, Not Just Answers** - "we use this
    library because ..., here's the incident where manual
    retries caused problems"
    - **Encourage Experimentation** - break stuff in a test
    environment, debug together
    - **Celebrate Questions** - don't be afraid to say
    "I don't know," encourage documenting edge cases,
    acknowledge that confusion can mean unclear docs

---

## Common Pitfalls

1. **TMI vs. Not Enough Context**

    Too much information is overwhelming, not enough context
    blocks people. Offer options - quick steps and detailed
    explanations if needed, start with the essential path
    and link to deeper docs, and always ask for feedback,
    "too detailed or not enough?"

2. **Teaching to One Learning Style**

    If you're only offering deep-dive presentations, you'll
    lose people who learn by doing; if you're only offering
    docs, you'll lose people who learn by watching demos.
    Offer format variety, multiple entry points, and let
    people choose.

3. **Assuming Knowledge Without Checking**

    Computers are great at storing info, but humans,
    not always, not so much. It can be a challenge to remember
    what it's like to not know a system after you've done the work
    to build and maintain it for years. Help engineers by defining
    acronymns the first time, linking background info, checking
    their understanding, and/or
    [creating a glossary](https://rhyannonjoy.github.io/api-docs-glossary/).

4. **Teaching What You Wish Existed vs. What Actually Exists**

    Teaching the ideal state isn't actionable. Help engineers by
    separating the aspirational from the current conditions by
    acknowledging what's real, like technical debt.

5. **Making Learning Feel Like Extra Work**

    No one wants extra work. Document as you build and pair program,
    respect everyone's time with focused sessions, and show the ROI
    by connecting the content to real situations and/or incidents.

---

## Summary

1. **Teaching IS Engineering Work**

    Teaching isn't a nice-to-have skill, it's critical infrastructure.
    When teams prioritize teaching, everyone ramps up faster,
    production incidents happen less, and technical debt gets paid
    through shared understanding. Every task is a teaching opportunity -
    pull requests, docs, and support questions are all changes to
    improve collective knowledge.

2. **Write for the Engineer Who Has No Context**

    This principle shows up repeatedly across these blogs because it's
    universally applicable: _write for the engineer who has no context,
    which will include future-you, because future-you is not going to
    remember_. Teach as if you're teaching yourself months from now,
    because you essentially are.

3. **Respect Your Audience**

    Everyone is busy! Get to the point by showing the solution,
    then explaining the context. Solve real production problems,
    don't linger on abstract theory. Provide multiple paths and
    accomodate different learning styles. Value everyone's time
    by offering short, focused sessions - no one is retaining
    hours of fluff.

4. **Make It Easy to Do the Right Thing**

    Streamlining learning paths to be actionable won't make the
    learning experience feel like extra work, it will just feel
    like effective training. Meaningful teaching with clear examples
    ready to be copy-pasted, searchable docs, step-by-step instructions
    that prevent mistakes, gotchas before people hit them -
    when the content is proven to be immediately useful, people
    will it take it with them, and actually use it.

5. **Teaching Scales Your Impact**

    Teaching is a career-long practice, not a role you graduate into.
    You're already teaching with everything that you do - the question
    is whether you're teaching clearly or creating confusion.
    As an IC, your might think your impact is limited by your coding
    hours, but when you intentionally teach, your teaching multiplies.
    Every engineer you teach can teach others, every doc you write helps
    countless engineers, every tutorial prevents infinite confusion, and
    every mentoring relationship improves both engineers. That's the power
    of teaching - you transform isolated knowledge into capability that
    belongs to everyone.
