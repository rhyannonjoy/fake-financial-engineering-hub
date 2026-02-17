---
layout: default
title: "No More Gaslight, Gatekeep, Girlboss: An AI-Ready Docs Strategy"
parent: Blogs
nav_order: 5
---

# No More Gaslight, Gatekeep, Girlboss: An AI-Ready Docs Strategy

![Retro computer mascot with eyes on screen saying "vagueness doesn't scale" with network diagram showing connected nodes](../../static/img/vagueness-doesnt-scale.png)

Concerned that your docs aren't AI-ready?

The cool thing is, tech writers and accessibility advocates solved this
problem decades ago. AI can't effectively parse unstructured text or unlabeled
diagrams any better than screen readers can, so if companies care about AI-powered
docs, they're accidentally doing the accessibility work they should have been doing
anyway.

The techniques that make diagrams accessible to screen reader users -
clear labels, descriptive alt text, thoughtful [IA](https://en.wikipedia.org/wiki/Information_architecture) -
are exactly the same techniques that help AI produce accurate, meaningful results.
["AI tools aren't introducing new requirements."](https://docsbydesign.com/2026/02/15/what-makes-documentation-ai-ready-structure/)
The practices haven't changed, the pitch has.

---

## In This Post

- [The Accidental Accessibility Win](#the-accidental-accessibility-win)
- [The Communication Tax Comes Due](#the-communication-tax-comes-due)
- [Why This Matters](#why-this-matters)
- [Practices That Serve Everyone](#practices-that-serve-everyone)
- [Inheriting Unlabeled Diagrams](#inheriting-unlabeled-diagrams)
- [AI-Ready Actions (That Are Just Accessibility)](#ai-ready-actions-that-are-just-accessibility)
- [Test AI and Accessibility Tools](#test-ai-and-accessibility-tools)
- [The Payoff](#the-payoff)
- [Summary](#summary)

---

## The Accidental Accessibility Win

The proliferation of AI hasn't invented new and improved software development
principles. While companies have historically put accessibility on the backburner,
what AI-readiness incentives do offer is reprioritizing principles that have
been around for decades. The work is identical, the techniques are identical.
The only thing that has changed is the motivation - and I'll take it.

Whether companies are making docs accessible because they care about
disabled users or because they want meaningful AI integration - the end result
is the same: docs that serve everyone better. This is what Geri
Reid calls
["AI accidentally making documentation accessible"](https://gerireid.com/blog/ai-is-accidently-making-documentation-accessible/) -
when self-interest aligns with doing the right thing, you get change faster than
years of advocacy ever could.

---

## The Communication Tax Comes Due

AI hasn't exposed unique culture debts, but reinforces stale communication patterns
that make every tech worker's life worse. I've experienced engineering cultures
that don't offer opportunities to improve dead-end dialogue. I've worked with
engineers who couldn't or wouldn't explain their work in plain terms in various
contexts including, but not limited to:

- **Vague Slack Messages**: "it's broken"  - _What's broken? How?_
- **Cryptic Code Reviews**: "needs work" - _Which part? Why?_
- **Unhelpful Commit Messages**: "fix bug"  - _What bug? What was the behavior?_
- **Opaque Docs**: jargon city graveyard, missing context
- **Unclear Comms Expectations**: assuming everyone shares their mental model

I wasn't an AI-early-adopter, but I was willing to experiment. My initial response
was "cool, this is just like teaching a junior engineer - I have tons of experience
sharing info." But I knew that the stereotypical engineer would struggle with this,
because effective prompting requires the same skills they're rarely incentivized to
practice:

- Breaking down complex concepts into steps
- Explaining the _why_, not just the _what_
- Providing context, not assuming shared knowledge
- Being explicit, not implicit
- Teaching, not gatekeeping

The culture rewards coding-in-isolation and gatekeeping information is often
interpreted as job security. Docs were someone else's problem. Explaining anything
was unnecessary "hand-holding." Then AI integrations snowballed and vagueness
didn't scale. No more gaslight, gatekeep, girlboss. If you struggle to explain
what you need plainly, you just won't get useful results.

Accessibility has faced the same road blocks. The communication skills that help
you with junior engineers, that make your code maintainable, that make your docs
practical - these are the exact same skills that help you effectively use AI.

| **Communication Pattern** | **Junior Engineers** | **Screen Readers** | **AI Tools** | **Future-You** |
| ------------------------- | -------------------- | ------------------ | ------------ | -------------- |
| Vague, Implicit | Confused, asks lots of questions | Misses context, can't navigate | Hallucinate, give wrong answers | Forget why you did this |
| Clear, Explicit | Learn faster, productive sooner | Understand content, navigate easily | Generate accurate responses | Remember your reasoning |
| "Figure it out yourself" | Frustrated, slow progress | Excluded | Ineffective output | Waste time re-learning |
| Well-documented | Empowered, independent | Included | Helpful | Saves time |

Development cultures that "don't have time to explain things" are now paying
that debt back with interest, except now it's holding back everyone, including
engineers.

---

## Why This Matters

Docs are a system of text, visuals, structure, and metadata that need to serve
multiple audiences simultaneously. Creating docs for AI-compatibility
reinforces accessibility principles. For example, visuals aren't decoration,
they're navigation and comprehension aids. Patterns I'm observing across the
industry:

| **Stated Motivation** | **Actual Practice** | **Who Benefits** |
| --------------------- | ------------------- | ---------------- |
| "Make diagrams LLM-readable" | Add descriptive alt text | Screen reader users, AI tools, search engines |
| "Help AI parse our architecture" | Label all diagram components | Anyone who looks at the diagram |
| "Improve AI docs responses" | Write clear, structured content | Human readers, AI tools, translation systems |
| "Enable AI-powered search" | Add semantic HTML and proper headings | Screen readers, SEO, AI tools, distracted skimmers |

As Bob Watson says, ["the fundamentals persist."](https://docsbydesign.com/2026/02/16/what-makes-documentation-ai-ready-quality-writing/)
It's all the same work. Use the AI-wave to reframe it. While companies
have budget for AI-readiness, rebrand accessibility work as AI-enablement.
You now have a better pitch for getting resources.

---

## Practices That Serve Everyone

I have a zig-zag resume that includes full-stack development and archival work,
and both taught me that information systems need multiple access points and
that communication clarity compounds:

| **Software Development Lessons** | **Archival Work Lessons** |
| ---------------------------------- | --------------------------- |
| Systems Thinking: text, visuals, code, and navigation work together | Information Architecture: non-linear navigation is normal |
| User Flow Awareness: people don't follow prescribed paths | Different Users, Different Needs: researchers, donors, and public all need different entry points |
| Spatial Mental Models: technical concepts often work better visually | Metadata Serves Multiple Purposes: discovery, context, preservation |
| Teaching and Explaining: if you can't explain it to a junior engineer, you don't understand it | Clear Docs: if you can't explain the collection, no one can use it |
| Testing Assumptions: verify rather than guess | Accessibility is Foundational: it shapes organization from the start |

These principles apply whether you're serving junior engineers, screen reader users,
busy developers, search engines, AI, or future-you that'll forget all your
architectural decisions. Similarly - while looking for a historical recipe from a
special collections archive, a researcher is initially seeking the basics: ingredients,
measurements, steps - but they also need metadata to confirm that it's the _right_
recipe. Code comments can work the same way - the code functionality may be visible,
but the _why_ needs explanation.

---

## Inheriting Unlabeled Diagrams

You're more than likely not [greenfield](https://en.wikipedia.org/wiki/Greenfield_project)
developing, but working on highly-specialized or icon-heavy diagrams that weren't designed
with accessibility or AI-readiness in mind. Consider the following approaches:

| **Path** | **What You Do** | **Pros** | **Cons** | **Best For** |
| -------- | --------------- | -------- | -------- | ------------ |
| 1. Add Metadata | Write comprehensive alt text &rarr; add text descriptions below diagrams &rarr; create accompanying tables &rarr; link to glossaries | Doesn't require source files, can be done immediately | Doesn't help people looking at diagram directly, maintenance burden when diagrams update | No source files, no organizational buy-in, need quick wins, blocking users right now - immediate fix |
| 2. Progressive Enhancement | Identify critical diagrams &rarr; recreate high-value diagrams with labels using [D2](https://d2lang.com/), [Ilograph](https://www.ilograph.com/), [Mermaid](https://mermaid.ai), [PlantUML](https://plantuml.com/), [Python Diagrams](https://diagrams.mingrammer.com/), [Structurizr](https://structurizr.com/),  &rarr; establish standards for new diagrams &rarr; deprecate old unlabeled versions | Focuses effort where it matters, creates sustainable practices going forward | Requires organizational buy-in, takes time | Limited resources, want to prevent future problems, blocking users right now - long-term solution |
| 3. Automate Descriptions | Feed diagrams to AI tool &rarr; validate and correct output &rarr; use descriptions as alt text &rarr; iterate over time | Faster than manual work, immediately improves accessibility | AI descriptions may be inaccurate, requires human verification | No source files, many diagrams to process, limited budget/timeline - want quick improvement |
| 4. Accept The Limitation | Document the problem &rarr; provide alternative text-based explanations &rarr; flag as technical debt &rarr; focus on new content | Honest about constraints, focuses effort on preventable problems | Doesn't help users struggling with current diagrams | Dozens+ diagrams need updating, limited budget/timeline, overwhelmed by scope |

Retrofitting is hard, prevention is easier. If you establish standards for _new diagrams
now_ - require alt text, descriptive labels, [diagram-as-code](https://dev.to/r_elena_mendez_escobar/diagram-as-code-creating-dynamic-and-interactive-documentation-for-visual-content-2p93)
tools - you have a better chance preventing this problem from compounding. Legacy content
can be fixed incrementally while you protect against creating more work for future-you.

---

## "AI-Ready" Actions (That Are Just Accessibility)

1. Add Alt Text to Diagrams

    **Current Framing**: "LLMs need alt text to understand diagrams"

    **Accessibility Benefits**: Screen readers have benefitted from this since the 1990s;
    helps AI tools, search engines, and anyone with a slow connection that
    interrupts image rendering

    ```html
    <!-- Insufficient -->
    <img src="architecture.svg" alt="">

    <!-- Basic -->
    <img src="architecture.svg" alt="System architecture diagram">

    <!-- Helpful -->
    <img src="architecture.svg" alt="Microservices architecture showing API Gateway routing requests through Auth Service to three backend services: Payment, User Management, and Notifications. Each service connects to its own database instance.">
    ```

2. Label Diagram Components

    **Current Framing**: "AI vision models need text labels to
    identify components accurately"

    **Accessibility Benefits**: Not just recognizable icons, but explicit
    labels help everyone - future-you, future-maintainers, anyone unfamiliar
    with the domain icons

    | **Before** | **After** | **Why** |
    | ---------- | --------- | ------- |
    | AWS Lambda icon only | AWS Lambda icon + "Order Processor" | Clarifies the specific function |
    | Generic database cylinder | Database icon + "User DB (PostgreSQL)" | Specifies both purpose and technology |
    | Arrow between services | Labeled arrow: "REST API calls" | Explains the interaction type |

3. Standardize Commit Messages

    **Current Framing**: "AI coding assistants need context to suggest relevant changes"

    **Accessibility Benefits**: Essential for code archaeology and debugging - helps
    AI coding assistants, incident responders, anyone using `git blame`

    Consider implementing [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/).
    I tend to use
    [Angular's Commit Message Format](https://github.com/angular/angular/blob/main/contributing-docs/commit-message-guidelines.md)
    in my personal projects.

4. Add Context to Code Comments

    **Current Framing**: "AI needs to understand intent to generate relevant suggestions"

    **Accessibility Benefits**: Essential for efficient collaboration involving
    future-you, future-maintainers, AI code review bots, auditors, and anyone
    debugging unexpected behavior

    ```python
    # Insufficient - what, not why
    # Check if user is admin
    if user.role == 'admin':

    # Helpful - explains the why
    # Admin users bypass rate limiting because they need unrestricted
    # access for emergency operations and system monitoring.
    # Reference `docs/rate-limiting.md` for full policy
    if user.role == 'admin':
    ```

    > _Note: This topic is also discussed more broadly in
    [Anti-pattern: Lack of Documentation and Comments](https://rhyannonjoy.github.io/fake-financial-engineering-hub/docs/blogs/antipattern-doc-comments/)_

5. Write Semantic HTML

    **Current Framing**: "Structured content helps AI extract information accurately"

    **Accessibility Benefits**: Screen readers navigate by headings, search
    engines index better, AI tools extract effectively, RSS readers format
    correctly

    ```html
    <!-- Insufficient structure -->
    <div class="heading">API Authentication</div>
    <div>Use Bearer tokens in the Authorization header...</div>

    <!-- Semantic HTML -->
    <h2>API Authentication</h2>
    <p>Use Bearer tokens in the Authorization header...</p>
    <code>Authorization: Bearer {token}</code>
    ```

6. Create Descriptive Link Text

    **Current Framing**: "AI needs context to understand where links lead"

    **Accessibility Benefits**: "Click here" has been an accessibility anti-pattern for
    decades because screen reader users navigate via links; AI tools generate accurate
    responses, distracted skimmers will get where they need to go

    ```markdown
    <!-- Unhelpful -->
    For more information, [click here](auth-guide.md).

    <!-- Helpful -->
    Visit the [Authentication Guide](auth-guide.md) for implementation details.
    ```

7. Frontload Answers

    **Current Framing**: "AI extracts information more accurately when answers come first"

    **Accessibility Benefits**: [Inverted pyramid style](https://en.wikipedia.org/wiki/Inverted_pyramid_(journalism))
    has been around since Morse code and it helps those moving fast (AI) and those
    who want to move faster (everyone, because
    [everyone is busy](https://rhyannonjoy.github.io/fake-financial-engineering-hub/docs/blogs/everyone-is-busy/))

    ```markdown
    <!-- Buried Answer -->
    ## Timestamp Handling

    Our API uses timestamps in several places. Understanding timestamp 
    formats is important because they affect caching, rate limiting, 
    and data consistency. When you send a request, you should use 
    ISO 8601 format (YYYY-MM-DDTHH:mm:ssZ).

    ---

    <!-- Frontloaded Answer -->
    ## Use ISO 8601 format for all timestamps

    **Format:** `YYYY-MM-DDTHH:mm:ssZ`  
    **Example:** `2026-02-16T14:30:00Z`

    This ensures timezone consistency across services and prevents caching issues.

    For details on how timestamps affect rate limiting, see [Rate Limiting Guide](rate-limits.md).
    ```

---

## Test AI and Accessibility Tools

Successes can determine docs usefulness,
while failures can identify areas for improvement:

| **Test Type** | **How to Test** | **Key Questions** |
| ------------- | --------------- | ----------------- |
| **Quick Validation** | Attach a diagram, ask AI to describe it | _Is the response accurate and helpful?_ <br>**Good**: "This shows an API Gateway receiving requests, routing through a Lambda function for authentication, then forwarding to three microservices: Payment Processing, User Management, and Notifications." <br>**Needs Help**: "This appears to be an architecture diagram with connected components, but I cannot determine details without more context." |
| **Screen Reader** | Use [NVDA](https://www.nvaccess.org/download/), [JAWS](https://support.freedomscientific.com/JAWSHQ/JAWSHeadquarters01), or [VoiceOver](https://www.apple.com/accessibility/features/?vision) to navigate your docs | - _Can you understand diagrams from alt text alone? <br>- Can you navigate by headings? <br>- Do links make sense out of context? <br>- Is the reading order logical?_ |
| **Junior Engineer** | Give your docs to someone new | _- Can they understand architecture without asking questions? <br>- Do commit messages explain why? <br>- Do code comments provide context? <br>- Can they onboard without interruptions?_ |

---

## The Payoff

Whether you do accessibility work branded as accessibility,
AI-enablement, or good communication, everyone benefits. The beautiful
thing about communication work is that it compounds - each
improvement helps multiple audiences in ways you might not anticipate:

| **Practice** | **Screen Reader Users** | **AI Tools** | **Junior Engineers** | **Future-You** |
| ------------ | ----------------------- | ------------ | -------------------- | -------------- |
| **Alt Text on Diagrams** | Understand visual content | Parse and explain diagrams | Learn architecture faster | Remember system design |
| **Labeled Components** | Know what each part represents | Identify architecture accurately | Understand without asking | Recall component purposes |
| **Standardized Commit Messages** | Navigate code history accessibly | Suggest relevant changes | Learn from Git history | Debug faster |
| **Code Comments With Context** | Understand code intent when navigating | Generate better suggestions | Understand decisions | Remember your reasoning |
| **Semantic HTML** | Navigate by structure | Extract information accurately | Learn proper markup patterns | Maintain markup |
| **Descriptive Links** | Understand link purpose | Generate accurate references | Follow docs successfully | Find related info |
| **Frontloaded Answers** | Find information quickly | Quote accurately | Get unblocked faster | Solve problems faster |

---

## Summary

1. **AI Accidentally Created Accessibility Incentives**

    Companies that treated accessibility as a "nice to have" are now
    incentivized to change their tune because AI tools depend on the
    same practices. The work is similar, the motivation changed.

2. **Communication Debt Comes Due**

    Engineering cultures that don't chamption communication clarity -
    accept untangled abstractions, cryptic commits, and missing context -
    now struggle with AI tools that can't read minds. Clear communication
    compounds. The skills that help junior engineers and screen reader
    users are the same skills that make AI useful.

3. **Take the Win, Use the Momentum**

    Whether organizations improve docs for accessibility,
    AI, or better onboarding doesn't matter - the end result is
    transparency that serves everyone better. If your company
    has budget for "AI-readiness," use it to do the communication
    work you should've been doing anyway.

    When inheriting unlabeled diagrams or legacy content, choose your approach
    based on constraints: add metadata if you lack source files, progressively
    enhance high-value content, automate descriptions for scale, or accept
    limitations while preventing future problems. Retrofitting is hard; prevention
    is easier. Establish standards now to avoid compounding technical debt.

    Test the outcomes. If it works for screen readers, it works for AI.
    If junior engineers onboard from your docs alone, AI can too.

4. **The Gatekeepers Lost**

    The culture that rewarded gatekeeping information and coding-in-isolation
    has hit a wall. AI exposed that vagueness doesn't scale. The
    engineers who were already skilled communicators found AI immediately
    effective. The rest are learning that clarity was never optional.

The question "can AI read my diagrams?" is actually asking: "have I communicated
plainly enough that anyone — human or machine — can understand what I've built?"
The answer benefits everyone, regardless of how they access your docs.

Ubiquitous AI has not unearthed pressing new principles - it's just a communication
reframing that reinforces what companies should have been cheerleading all along.
The communication tax comes due eventually, and it's tax season.
