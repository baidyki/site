---
title: "Writing Better Prompts: Spring 2026 State of the Practice"
date: 2026-04-19
draft: false
pagetags: ["prompts"]
---

## TL;DR

- **Prompt engineering is now context engineering** — the unit of work is the full information environment (system prompt, retrieved docs, tools, memory), not a single string.
- **Structure every prompt around scene, task, acceptance criteria** — the anatomy that composes with everything else in this guide.
- **Be literal.** Opus 4.7 and GPT-5-class models follow instructions strictly and burn tokens reconciling contradictions; drop "try to," "if possible," and clauses that quietly negate each other.
- **Design for cache hits** — static content at the top, variables at the bottom. Up to 90% input-cost and 80% latency reductions on cache hits.
- **Treat injection as a runtime threat, not a prompt-wording problem** — tag untrusted content, limit tool privileges, validate outputs.
- **Version prompts like code** — evaluate on a small test set, use prompt optimizers for variants, review diffs.

---

## Anatomy of a prompt: scene, task, acceptance

Settle the structure before anything else. A durable pattern in spring 2026 guidance — Anthropic's Claude Code documentation, OpenAI's GPT-5 guide, and Vahe Aslanyan's [Claude Code Handbook](https://www.freecodecamp.org/news/claude-code-handbook/) — is to break every non-trivial prompt into three parts:

1. **Scene.** Who the model is, what domain it operates in, what constraints apply, what tools and data it can see. The answer to "what world is the model in?"
2. **Task.** One unambiguous action, phrased in the imperative. If it won't fit in a single sentence, it is probably two tasks that should be split or sequenced.
3. **Acceptance criteria.** The specific shape, format, or validation rule the output must satisfy — and, for longer tasks, what counts as "done." Without this, the model optimizes for plausibility, not fit.

The Claude Code Handbook proposes a practical test: *"if a competent engineer read your prompt with no other context, could they build exactly what you have in mind?"* If not, one of the three parts is missing or underspecified.

When each type of instruction sits in its own block, conflicts become visible instead of hiding inside a paragraph — which matters because both Claude 4.7 and GPT-5-class models now penalize contradictions directly.

### A worked example

**Before** — a typical 2024-era prompt:

> Please try to review this Python code and suggest improvements if possible. Don't use complex language, and think step by step about what could be wrong. Here's the code: ...

**After** — the same intent, rewritten:

```
<scene>
You are reviewing Python code for a data pipeline that runs nightly.
Codebase: production, Python 3.11, mypy strict mode enabled.
</scene>

<task>
Identify defects in the code below. Focus on correctness, concurrency,
and resource leaks. Ignore style unless it causes a bug.
</task>

<acceptance_criteria>
Output exactly three sections:
- Bugs: bullet list, each with line number and one-sentence explanation.
- Risks: correctness-adjacent concerns that are not outright bugs.
- Verdict: one sentence — ship, ship-with-fixes, or do-not-ship.
If a section is empty, write "none".
</acceptance_criteria>

<code>
{{code}}
</code>
```

What changed, and why:

- **Hedges removed** (`please`, `try to`, `if possible`) — Opus 4.7 reads them as genuine optionality.
- **Negative framing replaced** — `"don't use complex language"` becomes an explicit scope in the task block.
- **"Think step by step" deleted** — reasoning is now a model-level parameter (`effort`), not prose scaffolding.
- **Scene block added** — Python version and mypy mode change what counts as a bug.
- **Task scoped** — "correctness, concurrency, resource leaks; ignore style" is actionable; "suggest improvements" is not.
- **Output shape pinned** — three named sections, what goes in each, what to do when empty. No room for the model to optimize for plausibility.
- **Static content on top, variable code on bottom** — the prompt prefix is cacheable across calls.

This same structure scales. For agents, the task block gets a `<persistence>` clause ("only terminate your turn when the problem is solved"); for long documents, the task gets restated at the bottom because models over-attend to the edges of the context window.

## Practical checklist

If you are updating an existing prompt for spring 2026:

1. Restructure it into scene, task, acceptance criteria — one block each.
2. Audit for contradictions between clauses before tuning wording.
3. Delete hedging words ("try," "might," "if possible").
4. Replace "don't do X" with "do Y" where possible.
5. Remove "think step by step" — set effort/reasoning higher instead.
6. Move static content (instructions, examples) to the top; put variables at the bottom.
7. Add section tags (`<instructions>`, `<context>`, `<input>`, `<output_format>`).
8. Drop sampling parameters (`temperature`, `top_p`, `top_k`) for Claude Opus 4.7 — they now 400.
9. For non-trivial tasks, ask for a plan or outline first and review it before generation.
10. For agents, decide your compaction, note-taking, and sub-agent strategy before tuning wording.
11. Treat all retrieved or tool-returned content as untrusted data.
12. Add your prompt to version control and write at least one regression test for it.

---

The rest of this piece unpacks *why* the checklist reads the way it does, with inline sources from February–April 2026. New model releases — most visibly Claude Opus 4.7 on [April 16, 2026](https://aws.amazon.com/blogs/aws/introducing-anthropics-claude-opus-4-7-model-in-amazon-bedrock/) and the GPT-5 family — fold more reasoning into the model itself and punish vague prompts harder than before. Analysts and vendors are simultaneously shifting vocabulary from "prompts" to "context."

## Context engineering has absorbed prompt engineering

The framing change is the headline of the last twelve months. Gartner's Avivah Litan argued in October 2025 that [prompt engineering is fading into context engineering](https://www.gartner.com/en/articles/context-engineering), defining the latter as "designing and structuring the relevant data, workflows and environment so AI systems can understand intent, make better decisions and deliver contextual, enterprise-aligned outcomes — without relying on manual prompts." Anthropic's engineering post on [effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) uses the same lens: the job is to "find the smallest set of high-signal tokens that maximize the likelihood of your desired outcome."

For practical prompt-writing, this shift means three things:

- The string you send is only one input among many — tool definitions, retrieved documents, and prior turns matter as much.
- Static prompt text should be short, specific, and bounded. Put variable context (user question, retrieved passages) in separate, clearly-labeled blocks.
- Long-running agents need explicit strategies — compaction, scratch-pad notes, sub-agents — rather than a single giant system prompt.

If you are writing a one-shot prompt, classic prompt engineering still applies. If you are building an agent, you are doing context engineering whether you call it that or not.

## Structure prompts for parsing, not prose

Unambiguous structure beats clever wording. Claude was trained to parse **XML-style tags**, and [Anthropic's official guide](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/use-xml-tags) recommends wrapping sections in tags like `<instructions>`, `<context>`, `<examples>`, and `<output_format>`. For GPT-5 and other models that are less tag-trained, equivalent results come from Markdown headings, code fences, and consistent delimiters. Either way, the rule is the same: a model that can see where one section ends and another begins makes fewer mistakes than one reading a wall of prose.

Three structural habits worth adopting:

1. **Name your sections.** `<background>`, `<task>`, `<input>`, `<output_format>` — and refer to those names in the instructions.
2. **Keep the hierarchy shallow.** Deep nesting hurts readability without helping the model.
3. **Put the task at the top.** For long prompts (especially with retrieved documents), restate the task near the bottom as well — models over-attend to the start and end of the context window.

For agentic prompts, OpenAI's [GPT-5 prompting guide](https://developers.openai.com/cookbook/examples/gpt-5/gpt-5_prompting_guide) shows that domain-specific tag blocks double as behavior controls: `<context_gathering>` to cap tool-call exploration depth (and even fix a tool-call budget), `<persistence>` to prevent premature hand-back to the user ("only terminate your turn when you are sure the problem is solved"), and `<tool_preambles>` to specify the cadence and style of progress updates. They work because the tag name telegraphs intent and the contents stay scoped to that concern.

## Be literal. The era of "try to" is over

Claude Opus 4.7 is explicit about this on Anthropic's [what's new page](https://platform.claude.com/docs/en/about-claude/models/whats-new-claude-4-7): it exhibits "more literal instruction following, particularly at lower effort levels. The model will not silently generalize an instruction from one item to another, and will not infer requests you didn't make."

Concrete edits that follow:

- Strip hedges like "try to," "if possible," and "might."
- Replace open-ended format requests ("write something about…") with precise format specs ("output exactly five bullet points, each under 20 words").
- Convert implicit assumptions into stated requirements.
- **Frame positively.** Tell the model what the desired output looks like rather than listing what to avoid; this is a reversal of the "don't do X" pattern common in 2024-era system prompts.

The pattern holds outside Anthropic's line. OpenAI's GPT-5 prompting guide warns that "poorly-constructed prompts containing contradictory or vague instructions can be more damaging to GPT-5 than to other models, as it expends reasoning tokens searching for a way to reconcile the contradictions rather than picking one instruction at random." Audit for contradiction — clauses that quietly negate each other, acceptance criteria that conflict with the task, escape hatches that undo the stated goal — before you tune wording.

## Use examples — but start small

Few-shot prompting remains a workhorse, but the 2026 guidance is to start with **one** example and add more only when output quality demands it. Anthropic's [prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices) calls this out explicitly, and the [context engineering post](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) reinforces it: prefer "diverse, canonical examples that effectively portray the expected behavior of the agent" over exhaustive edge-case enumeration.

Why the shift? Every example consumes context and attention budget. On a 1M-token context window, the temptation is to pile on examples; the result is usually worse because the signal-to-noise ratio drops. A short, carefully chosen demonstration teaches the shape of the output; twenty variations teach the model to over-index on surface patterns.

## Plan before you prompt

For anything beyond a one-liner, the planning step before generation is where most of the quality leverage sits. Anthropic's [Claude Code](https://claude.com/blog/best-practices-for-using-claude-opus-4-7-with-claude-code) ships a dedicated **Plan Mode** — activated by prepending *"please do not write any code yet"* — that forces the model to produce a structured approach for review before any tool call or file edit. In the [Claude Code Handbook](https://www.freecodecamp.org/news/claude-code-handbook/), Boris Cherny reports using Plan Mode for roughly 80% of sessions because "thirty minutes of structured planning frequently reduces a ten-hour build to three hours."

The pattern generalizes past code:

- **Generation tasks:** ask for an outline first, review it, then release the model to expand into full prose.
- **Analysis tasks:** ask for the approach, data sources, and assumptions before the model produces conclusions.
- **Agentic tasks:** use the `<self_reflection>` and `<context_gathering>` blocks from the GPT-5 guide to make the model enumerate its plan and tool-call budget before it acts.

Skipping planning tends to produce output that reads right but misses the brief, and every correction round after a skipped plan costs more tokens than the plan itself would have. The planning step is also where you catch underspecified acceptance criteria — before the model has baked them into a 400-line first draft.

## Design for cache hits

Prompt caching has gone from optimization to default. OpenAI's [prompt caching guide](https://developers.openai.com/api/docs/guides/prompt-caching) reports automatic caching on all gpt-4o-and-newer models, with up to 80% latency reduction and 90% input-cost reduction on cache hits. Anthropic offers the equivalent with explicit cache breakpoints. The rule for writing cache-friendly prompts is mechanical but often overlooked:

- **Static content at the top** — system instructions, few-shot examples, reference material.
- **Dynamic content at the bottom** — user query, per-request variables, freshly retrieved context.
- **Don't reshuffle prefixes between calls** — even cosmetic changes (whitespace, reordering) break the cache.

For production systems, this single layout choice is now worth more than most wording tweaks.

## Stop scaffolding reasoning by hand

"Think step by step" is now the wrong tool in most cases. Anthropic's [Opus 4.7 with Claude Code guide](https://claude.com/blog/best-practices-for-using-claude-opus-4-7-with-claude-code), published April 16, 2026, tells users to delete that scaffolding and raise the `effort` parameter instead. Opus 4.7 uses adaptive thinking, deciding turn-by-turn whether to reason more or respond faster; fixed thinking budgets now return a 400 error.

Independent analysis agrees with the direction. OpenAI's [March 5, 2026 post on chain-of-thought controllability](https://openai.com/index/reasoning-models-chain-of-thought-controllability/) adds a safety twist: hidden reasoning is increasingly used as a monitorability signal, and forcing it into the visible prompt interferes with that. Wharton research — summarized in [The Register's March 24, 2026 write-up](https://www.theregister.com/2026/03/24/ai_models_persona_prompting/) — also casts doubt on another long-standing trick, finding that telling a model it is an expert ("you are a physics expert") does not improve factual accuracy and sometimes hurts it.

Practical upshot:

- On reasoning models (Opus 4.7, GPT-5 reasoning variants), control reasoning with effort/mode parameters, not prose.
- Save CoT prompting for weaker or non-reasoning models where it still helps.
- If you ship reasoning traces to end-users, opt in explicitly — Opus 4.7 omits thinking content by default.

## Build the interaction, not just the prompt

Long-running tasks are now first-class citizens. Anthropic shipped **task budgets** as a March 2026 beta in Opus 4.7 — an advisory token ceiling the model sees as a countdown and uses to pace its own work, distinct from the hard `max_tokens` cap. The [what's new page](https://platform.claude.com/docs/en/about-claude/models/whats-new-claude-4-7) describes the feature and warns against using it for open-ended quality-first work.

For genuinely long-horizon runs, Anthropic's [context engineering guidance](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) recommends three techniques that belong in the prompt author's toolkit:

- **Compaction** — summarize prior turns into a running context block before they get evicted.
- **Structured note-taking** — have the agent write to a file-system-backed scratchpad it can re-read.
- **Sub-agents** — spawn focused child agents for bounded tasks, keeping the main agent's context clean. Opus 4.7 spawns fewer sub-agents by default; if you want parallelism, ask for it explicitly.

## Treat prompt injection as a first-class threat

Anthropic's [February 5, 2026 Claude Opus 4.6 system card](https://venturebeat.com/security/prompt-injection-measurable-security-metric-one-ai-developer-publishes-numbers) — notable for being the first to publish prompt-injection failure rates as a formal metric — shows why this matters. In a constrained coding environment, direct prompt-injection attacks fail 100% of the time against Claude Opus 4.6. On a GUI-based agent with extended thinking enabled, the same attack succeeds 17.8% of the time on the first attempt and 78.6% by the 200th without safeguards, dropping to 57.1% by the 200th *with* safeguards. The pattern: a single attack is often blocked; a persistent attacker is not.

**Indirect injection** — malicious content reaching the model through retrieved documents, web pages, or tool outputs — is the enterprise-relevant case. No public primary source gives a reliable industry-wide percentage for how often it occurs in the wild, but the architectural conclusion does not depend on one: any pipeline that renders untrusted text into the context window is exposed.

The defensive posture the 2026 literature converges on is not "write a safer prompt" — it is layered:

- **Never process untrusted content as instructions.** Tag retrieved text with explicit boundaries (`<untrusted_document>`), and remind the model in the system prompt that content inside those tags is data, not directives.
- **Separate privileges.** An agent with a summarization task does not need write access. Least-privilege tool design is now a prompt-adjacent concern.
- **Validate outputs.** External runtime checks on model output catch many injections that slip past the prompt layer. OpenAI's [prompt-injection research post](https://openai.com/index/prompt-injections/) and Lakera's [2026 prompt engineering guide](https://www.lakera.ai/blog/prompt-engineering-guide) both emphasize this multi-layer approach.

No single prompt pattern reliably blocks a skilled attacker. Budget for runtime defenses, not just careful wording.

## Test, version, and optimize prompts like code

The final 2026 trend is methodological. Prompts are moving from one-off strings to versioned, tested artifacts. Vendor tooling is catching up: OpenAI shipped a [prompt optimizer](https://developers.openai.com/api/docs/guides/prompt-optimizer) that generates and evaluates variants automatically, and meta-prompting (using an LLM to generate and refine prompts for another LLM) is now mainstream in enterprise pipelines.

A workable meta-prompt template, distilled from OpenAI's GPT-5 guide, avoids the "rewrite from scratch" trap:

> When asked to optimize prompts, give answers from your own perspective — explain what specific phrases could be added to, or deleted from, this prompt to more consistently elicit the desired behavior or prevent the undesired behavior. The desired behavior is [X]; the observed behavior is [Y]. Keep as much of the existing prompt intact as possible; propose minimal edits.

The output is a diff you can inspect for regressions, not a replacement prompt whose behavior is unknown. The same model that writes your application prompts can audit them — ask it what phrases are causing the failure mode, and it will usually name them.

A minimal, low-friction discipline:

- **Version prompts.** Commit them to source control next to the code that calls them.
- **Evaluate them.** Maintain a small test set of representative inputs and expected shapes; run it on every prompt change.
- **Automate the boring part.** Use prompt optimizers or meta-prompting to generate variants; keep humans in the loop for selection and review.

Empirical work on [RAG prompt templates in small language models](https://arxiv.org/abs/2602.13890), published February 14, 2026, shows how much measurable variance there is between templates even on a fixed task — the study evaluates 24 distinct prompt variants on HotpotQA across two small models and reports gains of up to 83% on Qwen2.5-3B and 84.5% on Gemma3-4B-It from template choice alone. Without evaluation, you are guessing.

## References

**Official model and platform documentation**

1. [Introducing Anthropic's Claude Opus 4.7 model in Amazon Bedrock](https://aws.amazon.com/blogs/aws/introducing-anthropics-claude-opus-4-7-model-in-amazon-bedrock/) — AWS announcement of the April 16, 2026 release.
2. [What's new in Claude Opus 4.7](https://platform.claude.com/docs/en/about-claude/models/whats-new-claude-4-7) — Official breaking-changes and behavior-changes list.
3. [Best practices for using Claude Opus 4.7 with Claude Code](https://claude.com/blog/best-practices-for-using-claude-opus-4-7-with-claude-code) — Anthropic's April 16, 2026 guidance on effort levels, adaptive thinking, and session structure.
4. [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices) — Anthropic's canonical reference for Claude 4.x-family prompting.
5. [Use XML tags to structure your prompts](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/use-xml-tags) — Official Claude XML-structure guide.
6. [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — Anthropic's engineering-team primer on context as the unit of work.
7. [GPT-5 prompting guide](https://developers.openai.com/cookbook/examples/gpt-5/gpt-5_prompting_guide) — OpenAI cookbook entry on agentic eagerness, tool preambles, and contradiction-penalty behavior.
8. [Prompt caching guide (OpenAI)](https://developers.openai.com/api/docs/guides/prompt-caching) — Cache-hit mechanics, latency and cost numbers, and layout rules.
9. [Prompt optimizer (OpenAI)](https://developers.openai.com/api/docs/guides/prompt-optimizer) — OpenAI's automatic prompt-optimization tool.

**Research and analysis**

10. [Context engineering vs. prompt engineering](https://www.gartner.com/en/articles/context-engineering) — Gartner's 2026 framing of the practice shift.
11. [Reasoning models struggle to control their chains of thought](https://openai.com/index/reasoning-models-chain-of-thought-controllability/) — OpenAI's March 5, 2026 post on CoT-Control and reasoning-model behavior.
12. [Understanding prompt injections: a frontier security challenge](https://openai.com/index/prompt-injections/) — OpenAI's threat-model overview.
13. [Evaluating prompt engineering techniques for RAG in small language models](https://arxiv.org/abs/2602.13890) — February 14, 2026 empirical study of 24 prompt templates on HotpotQA.

**Practitioner guides**

14. [The Claude Code Handbook](https://www.freecodecamp.org/news/claude-code-handbook/) — Vahe Aslanyan's March 25, 2026 handbook on professional AI-assisted development, including Boris Cherny's prompt-discipline and Plan Mode guidance.
15. [Prompt engineering guide (Lakera, 2026)](https://www.lakera.ai/blog/prompt-engineering-guide) — April 16, 2026 guide with defensive-prompting emphasis.

**News coverage**

16. [Anthropic published the prompt injection failure rates enterprise teams were asking for](https://venturebeat.com/security/prompt-injection-measurable-security-metric-one-ai-developer-publishes-numbers) — VentureBeat coverage of the February 5, 2026 Claude Opus 4.6 system card.
17. [Telling an AI model that it's an expert makes it worse](https://www.theregister.com/2026/03/24/ai_models_persona_prompting/) — The Register's March 24, 2026 coverage of Wharton research on persona prompting.
