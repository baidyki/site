---
title: "AI Adoption Roadmap for Business Functions: A 12-Month Plan for HR, Finance, Legal, Marketing, Sales, and Support"
date: 2026-04-16
draft: false
tags: ["methodology", "roadmap", "adoption"]
---

# AI Adoption Roadmap for Business Functions

## TL;DR

- **Months 1–2: Foundation** — acceptable-use policy, data-classification rules, procurement, champions per function. Zero production use yet.
- **Months 2–3: Pilot** — one high-leverage, low-risk workflow per function. Baseline captured before the pilot, not after.
- **Months 4–6: Controlled rollout** — 50% coverage inside each function, human-in-the-loop on every customer-facing output.
- **Months 7–9: Scale** — AI embedded in core workflows (close cycle, contract review, campaign production, ticket triage), with explicit escalation paths to humans.
- **Months 10–12: Operationalize** — redesigned processes, outcome metrics, permanent HITL gates, governance audits.
- **The failure mode is customer-facing autonomy** — Klarna, Air Canada, DPD, and Amazon's hiring AI are all the same story: a premature move from augmentation to autonomy on a path with real-world consequences.

---

Non-tech functions face a harder AI adoption problem than engineering, not an easier one. Engineering has [GitHub Copilot](https://github.com/features/copilot), [Cursor](https://cursor.sh/), [SpecKit]({{< ref "github-speckit" >}}), and dozens of mature tools with measurable telemetry. HR, finance, legal, marketing, and support operate on ambiguous text, personal data, and regulated decisions — and their outputs often touch customers, employees, and regulators directly.

The adoption data reflects this gap. McKinsey's 2025 survey shows **88% of organizations use AI in some business function**, but deployment is deeply uneven: the [CFO Connect State of AI in Finance 2026](https://www.cfoconnect.eu/resources/reports/state-of-ai-in-finance-2026/) reports 45% of finance teams are still in "limited pilot" mode and only **17% use AI in core workflows**. [SHRM's State of AI in HR 2026](https://www.shrm.org/topics-tools/research/state-of-ai-hr-2026/full-report) finds 75% of HR leaders expect AI to handle more than half of routine admin tasks by end of 2026 — but most haven't started.

This roadmap sequences the work across six functions over twelve months. It assumes an org of 500–5,000 people; compress for smaller orgs, expand for larger ones.

---

## The 12-month overview

| Phase | Timing | Coverage | Primary outcome |
|---|---|---|---|
| 0 — Foundation | Weeks 1–6 | 0% active use | Policy, data classification, champions, baselines |
| 1 — Pilot | Months 2–3 | One workflow per function | Real outputs, measured against baseline |
| 2 — Controlled rollout | Months 4–6 | ~50% of function, HITL on all external outputs | Playbooks, prompt libraries, first integrations |
| 3 — Scale | Months 7–9 | Core workflows AI-augmented | Close cycle, contract review, ticket triage, campaign production |
| 4 — Operationalize | Months 10–12 | Redesigned processes | Outcome metrics, permanent HITL, governance at scale |

---

## How this maps to the 5 Stages of AI Acceptance

Business-function adoption runs parallel to engineering's but lags by roughly one stage at most orgs. The roadmap phases below are the tactical execution underneath the strategic framework in [5 Stages of AI Acceptance]({{< ref "5-stages-of-ai-acceptance" >}}).

| Stage (strategic) | Roadmap phase (tactical) | What it looks like in business functions |
|---|---|---|
| **Stage 1 — Aware** | Pre-roadmap | Execs read about Harvey and Copilot for Finance. "Shadow ChatGPT" is widespread; no policy. |
| **Stage 1 → 2** | **Phase 0 — Foundation** (Weeks 1–6) | Policy, data classification, per-function champions. The step that's easiest to skip and most painful to skip. |
| **Stage 2 — Active** | **Phase 1 — Pilot** (Months 2–3) | One workflow per function, bounded and measured. Legal on NDAs, Finance on variance analysis, Support on agent-assist. |
| **Stage 2 → 3** | **Phase 2 — Controlled rollout** (Months 4–6) | Playbooks, prompt libraries, HITL on every external output. First real integrations with CRM, HRIS, contract systems. |
| **Stage 3 — Operational** | **Phase 3 — Scale** (Months 7–9) | AI in core workflows: close cycle, contract lifecycle, campaign production, Tier-1 deflection. Real KPIs, escalation paths, governance. |
| **Stage 3 → 4** | **Phase 4 — Operationalize** (Months 10–12) | Redesigned functions (legal hiring profiles shift, finance org charts change). Permanent HITL on customer/regulator-facing outputs. |
| **Stage 4 — Systemic** | Post-roadmap (Year 2+) | AI capability platform spans all functions. Executive ownership. Cross-function council for emerging regulation (EU AI Act, sector rules). |
| **Stage 5 — Transformational** | Long-horizon (Years 2–5+) | AI-native revenue lines — subscription legal research, autonomous finance close, zero-touch support Tier-1. Rare today. |

**Three things this mapping makes visible:**

1. **Stage 2 is where customer-facing mistakes happen.** Every failure cataloged in this article — Klarna, Air Canada, DPD, Amazon's recruiting AI — was an org that skipped ahead from Stage 2 pilot enthusiasm to customer-facing autonomy without the Phase 2 HITL discipline that would have caught it. The strategic stage language calls this out; the tactical phase gates enforce it.
2. **Stage 3 is harder for business functions than for engineering.** Engineering's Phase 3 involves internal workflows with reviewers in the loop. Business Phase 3 often touches customers, candidates, regulators, and financial disclosures. The [McKinsey finding]({{< ref "5-stages-of-ai-acceptance" >}}) that 65% of AI high performers have defined HITL processes vs. 23% of others matters most here.
3. **Stage 4 requires pairing with engineering.** You cannot reach Stage 4 if the tech side is still at Phase 1 — the platform, data, and governance infrastructure aren't there yet. Run the [engineering roadmap]({{< ref "ai-adoption-roadmap-engineering" >}}) in parallel, not sequentially.

---

## Phase 0 — Foundation (Weeks 1–6)

The business-function version of Phase 0 is more work than engineering's, because data classification is harder and compliance exposure is higher.

### Weeks 1–2: policy and data classification

**All functions:**
- Publish an **acceptable-use policy** specific to non-technical staff (shorter, less jargon, concrete examples). Starter templates: [aona.ai's governance library](https://aona.ai/resources/templates/), [Verifywise's template](https://verifywise.ai/ai-governance-library/policies-and-internal-governance/ai-acceptable-use-policy-template).
- Classify data in **four buckets**: public, internal, confidential, regulated. Decide which buckets can enter which AI systems. This is the single most-cited gap in [the Gallagher 2026 AI Adoption and Risk Benchmarking report](https://www.ajg.com/news-and-insights/features/ai-adoption-and-risk-benchmarking-2026/).
- Decide whether to use **enterprise SaaS** (e.g. [Claude Enterprise](https://www.anthropic.com/enterprise), ChatGPT Enterprise, Microsoft 365 Copilot), **purpose-built verticals** (Harvey for legal, Copilot for Finance, [Claude-based custom assistants built on the API](https://docs.claude.com/en/api/overview)), or both. Non-tech functions often land on a **two-tier posture**: a horizontal enterprise chat product for everyone + purpose-built vertical tools for the two or three functions with the highest-leverage workflows.

### Weeks 3–4: procurement, baselines, and function-specific risk review

Capture baselines per function — these numbers are the ROI proof later:

- **HR:** time-to-fill, screener-to-interview ratio, onboarding cycle time, employee-FAQ response time
- **Finance:** month-end close duration, time-to-reconcile, variance-analysis turnaround, forecast accuracy
- **Legal:** contract review cycle, NDA turnaround, matter-intake time, research-memo production time
- **Marketing:** asset production time, campaign launch lead time, content-per-persona coverage, video completion rate
- **Sales:** email response rate, proposal turnaround, lead-qualification time, forecast accuracy
- **Support:** first response time, deflection rate, CSAT, average handle time, agent training time

**Function-specific risk review:**
- **HR:** anti-discrimination compliance (EU AI Act classifies hiring AI as "high-risk"), Amazon's 2014–2018 recruiting-tool failure (biased training data) is the canonical lesson.
- **Legal:** privilege, confidentiality, hallucination liability. Mata v. Avianca is the canonical cautionary tale.
- **Finance:** SOX controls, audit trails, segregation of duties.
- **Marketing:** IP and likeness rights for AI-generated content, FTC endorsement disclosure, brand safety.
- **Sales:** data residency for CRM-linked AI, GDPR/CCPA for outbound sequences.
- **Support:** [Air Canada chatbot liability precedent](https://customerthink.com/chatbots-under-fire-navigating-ai-pitfalls-with-insights-from-dpd-and-air-canada/) — the company is accountable for the bot's statements.

### Weeks 5–6: champions

**All functions:**
- One champion per 25–50 people in each function. In non-tech functions, **domain expertise matters more than technical curiosity** — the champion must know when the AI is wrong, which requires deep functional knowledge.
- Cross-function **AI practice council** meets biweekly. Not a bureaucracy — a place where Legal tells Marketing about emerging IP rulings, Finance warns HR about new SOX guidance for AI, etc.

---

## Phase 1 — Pilot (Months 2–3)

One workflow per function. Real outputs. Measured.

### HR (weeks 7–12)

- **Pilot workflow:** internal policy Q&A and HR knowledge-base assistant. Employee-facing, not candidate-facing. Zero hiring decisions assisted by AI yet.
- Week 7: ground the assistant on the employee handbook, benefits docs, and approved internal policy corpus. Lock it down so it refuses to answer outside that corpus. A [Claude Project](https://www.anthropic.com/news/projects) with uploaded policy docs, or a Claude API integration with RAG over the policy corpus, both fit — Claude's default conservatism on uncertain questions is an asset in HR contexts where "I don't know, ask HR" is the right answer more often than it is in engineering.
- Weeks 8–10: pilot with 10% of employees; champions monitor every incorrect answer.
- Weeks 11–12: measure HR ticket volume deflection and first-response time.

### Finance (weeks 7–12)

- **Pilot workflow:** variance analysis and flux commentary drafting. The AI drafts the narrative; a finance analyst owns the numbers and the sign-off. Microsoft's own finance org (5,000 people) used this pattern at launch — see [CFO Dive on Microsoft's Copilot rollout](https://www.cfodive.com/news/microsoft-finance-copilot-generativeai-software/727176/).
- Week 7: integrate with the financial-reporting stack in a read-only posture.
- Weeks 8–10: run the AI draft alongside the human-authored version. Measure drift.
- Weeks 11–12: measure variance-analysis turnaround. Benchmarks: [30–40% close-cycle reduction, 50% faster variance analysis, 60–75 minutes saved per user daily](https://adoption.microsoft.com/en-us/scenario-library/finance/) in mature deployments — but these are Phase 3 numbers, not pilot numbers.

### Legal (weeks 7–12)

- **Pilot workflow:** NDA review and first-pass markup on a defined contract template library. This is the [A&O / Harvey starting use case](https://www.aoshearman.com/en/news/ao-announces-exclusive-launch-partnership-with-harvey) — well-bounded, reversible, measurable. Purpose-built legal tools (Harvey, Lexis+ AI, Thomson Reuters CoCounsel) are often the right choice; general chat tools work for smaller firms and in-house teams. Claude's long context window and strength on nuanced text analysis make it a common underlying model for legal applications.
- Week 7: upload playbooks, preferred clauses, and fallback positions into the AI's knowledge context.
- Weeks 8–10: AI drafts the first-pass redline; attorneys review and record every correction.
- Weeks 11–12: measure cycle time and attorney correction rate. A&O's firmwide benchmark: **2–3 hours saved per lawyer per week, 30% reduction in contract review time, 7 hours saved on complex documents**. One in four lawyers uses the tool daily; 80% at least monthly.

### Marketing (weeks 7–12)

- **Pilot workflow:** variant generation for already-approved campaigns. Not original ideation yet — localized, personalized variants of a human-authored hero asset.
- Week 7: establish brand-voice prompts, banned-term lists, and legal-review hooks.
- Weeks 8–10: generate variants at scale; every external-facing asset passes through human brand review.
- Weeks 11–12: measure time-per-asset. [Unilever's benchmark](https://www.unilever.com/news/news-search/2025/how-ai-is-helping-drive-desire-at-scale-across-unilever/): **30% faster asset creation, 2× Video Completion Rate and Click-Through Rate, 22.5% TikTok visibility lift** for brands using the system.

### Sales (weeks 7–12)

- **Pilot workflow:** account research and first-draft outbound sequences. Not auto-sending — drafts in the rep's inbox.
- Week 7: connect AI to CRM and approved account-intelligence sources. No scraping of regulated datasets.
- Weeks 8–10: reps review and edit; measure edit distance between AI draft and sent version.
- Weeks 11–12: measure rep time-to-send, response rate. If response rate drops vs. baseline, the AI is producing generic slop at scale — the fix is more context, not more sending.

### Customer Support (weeks 7–12)

- **Pilot workflow:** AI-suggested responses shown *to the agent*, not the customer. The agent always sends. This is the most important posture decision in the entire roadmap — see the Klarna case below. Claude is a common choice for support assist because its [trained behavior leans cautious](https://www.anthropic.com/claude) on uncertainty and hedges rather than fabricates — a meaningful advantage when the downstream cost of a confident wrong answer is a tribunal ruling or a viral screenshot.
- Week 7: ground on the full knowledge base and historical ticket corpus.
- Weeks 8–10: measure agent acceptance rate, edit distance, handle time.
- Weeks 11–12: measure CSAT. If CSAT is flat or worse, the AI is producing confident-sounding wrong answers — keep iterating before letting it anywhere near customers directly.

---

## Phase 2 — Controlled rollout (Months 4–6)

Fifty percent coverage inside each function. Human-in-the-loop on every external-facing output. No direct customer-facing AI autonomy.

### HR

- Expand the assistant to **new-hire onboarding** and policy self-service. Still no hiring-decision support.
- **Screener assistance** (not screener automation) arrives in Month 5: the AI clusters applications by relevance; humans decide who advances. All hiring decisions logged with the reasoning.
- **Mandatory bias audit** of any ranking model. EU AI Act requires it for high-risk uses; do it regardless of jurisdiction.

### Finance

- AI drafts **month-end commentary, board-deck variance slides, and AR/AP reconciliation explanations**. Humans sign off on every external artefact.
- **Spend analysis** moves to AI-assisted; CFO still signs the expense report.
- Onboarding benchmark: CFO Connect's report recommends expanding the pilot cohort **3× per quarter** after baseline; targets include close-cycle and time-to-reconcile.

### Legal

- Broaden from NDAs to **commercial contracts, DPAs, and vendor MSAs**.
- Introduce **matter-intake triage**: AI categorizes incoming requests, routes to the right attorney. Humans always own the "is this privileged?" call.
- Add **legal research assistants** (Harvey, Lexis+ AI, Westlaw Precision AI) with mandatory citation-verification — **every cited case verified against the primary source** before anything leaves the firm.

### Marketing

- AI in **content production at scale** — email sequences, social variants, ad creative. Humans own brand voice and final approval.
- [Coca-Cola's FIFA World Cup campaign generated 120,000 unique videos](https://digitaldefynd.com/IQ/ways-coca-cola-uses-artificial-intelligence/); the 2024 holiday campaign's AI Santa avatar ran in 26 languages, **1M+ consumer engagements across 43 markets in 60 days**. Those numbers are possible only with a mature Phase 2 posture.

### Sales

- AI drafts **proposals, SOWs, and deal summaries**. Reps own every send.
- **Forecast assistance**: AI surfaces risks in CRM data (stalled deals, slipping dates); sales leadership makes the call.
- Ban AI from **auto-sending** to external contacts. Trust is the asset; generic AI emails erode it fast.

### Customer Support

- Agent-assist mode expands to 100% of agents.
- **Tier-1 self-service deflection** can launch, with strict guardrails: the bot answers only from a curated FAQ, refuses to discuss policy, escalates any sentiment flag, and makes *no commitments on the company's behalf*. [Air Canada's tribunal loss](https://customerthink.com/chatbots-under-fire-navigating-ai-pitfalls-with-insights-from-dpd-and-air-canada/) is the lesson: if the bot says it, the company owes it.

---

## Phase 3 — Scale (Months 7–9)

AI embedded in core workflows. Explicit escalation paths. The productivity numbers cited in vendor decks become reachable here, not before.

### HR

- Performance-review summarization, compensation-benchmarking drafts, L&D content generation.
- **Hiring assistance remains human-led**. The EU AI Act's high-risk classification is not going away.
- Target metric: 50% reduction in HR admin time (the [SHRM benchmark for end-of-2026](https://www.shrm.org/topics-tools/research/state-of-ai-hr-2026/full-report)).

### Finance

- AI in **close cycle, reconciliations, forecast scenarios, vendor-spend analysis**.
- **Mature deployment benchmarks:** 30–40% close-cycle reduction, 50% faster variance analysis, 60–75 minutes saved per user daily, 20% operating-cost reduction within 90 days of scale. Source: [Microsoft Copilot for Finance adoption data](https://adoption.microsoft.com/en-us/scenario-library/finance/).

### Legal

- **Agentic workflows** for complex matters ([A&O Shearman and Harvey announced this pattern in 2025](https://www.aoshearman.com/en/news/ao-shearman-and-harvey-to-roll-out-agentic-ai-agents-targeting-complex-legal-workflows)): multi-step legal analysis with explicit human checkpoints.
- **Contract lifecycle** end-to-end: drafting, negotiation playbooks, clause comparison, obligation tracking.
- Human sign-off remains mandatory for anything going to a regulator, court, or counterparty.

### Marketing

- AI in **personalization at scale, dynamic creative optimization, and multi-market localization**.
- Coca-Cola benchmarks: **20% social engagement lift, 15% higher email conversion, 25% online sales boost** on AI-enabled campaigns.
- Humans own **brand stewardship** permanently. No AI-only approval path for flagship campaigns.

### Sales

- Full sales-cycle assist: discovery-call summaries, proposal generation, deal-desk answers, competitor-battlecard synthesis.
- CRM hygiene becomes the most valuable thing AI does — AI maintains the data humans never had time to.
- Escalation rule: **any AI-generated message that mentions price, legal terms, or commitments requires human approval.**

### Customer Support

- Tier-1 deflection is mature; Tier-2 is agent-assist with AI drafting responses.
- Benchmark ambition: 30–50% deflection on Tier-1 volume without CSAT regression. Anything higher than that invites the Klarna failure mode.

---

## Phase 4 — Operationalize (Months 10–12)

### All functions

- **Redesign workflows around the new capability.** A legal team that no longer *drafts* first-pass NDAs but *reviews and negotiates* them needs a different hiring profile, billable-hour model, and junior-lawyer development path.
- **Outcome metrics only.** Stop reporting "AI-generated outputs." Start reporting **function-level KPIs that were baselined in Week 4** — cycle time, error rate, customer satisfaction, revenue per headcount, cost per transaction.
- **Permanent HITL gates** on anything that touches customers, candidates, regulators, or financial disclosures. McKinsey's 2025 data: **65% of AI high performers have defined human-in-the-loop processes, versus 23% of others** — the single largest differentiator in the survey.
- **Quarterly governance audits**: usage telemetry, bias testing, incident log, vendor reassessment.

---

## Success stories worth studying

- **[A&O Shearman + Harvey (Legal)](https://www.aoshearman.com/en/news/ao-announces-exclusive-launch-partnership-with-harvey):** 4,000+ lawyers across 43 jurisdictions; **2–3 hours saved per lawyer per week, 30% reduction in contract review time, 7 hours saved on complex documents**; 1 in 4 lawyers uses daily. Recognized as Europe's Most Innovative Law Firm 2024.
- **[Microsoft Finance (5,000-person org)](https://www.cfodive.com/news/microsoft-finance-copilot-generativeai-software/727176/):** Copilot rolled into close cycle, variance commentary, and reconciliation. **30–40% close-cycle reduction** in mature deployments.
- **[Unilever (Marketing)](https://www.unilever.com/news/news-search/2025/how-ai-is-helping-drive-desire-at-scale-across-unilever/):** 30% faster asset creation, 2× VCR and CTR, 22.5% TikTok visibility lift.
- **[Coca-Cola (Marketing)](https://digitaldefynd.com/IQ/ways-coca-cola-uses-artificial-intelligence/):** 20% social engagement, 15% email conversion, 25% sales boost on AI-enabled campaigns; 120k unique FIFA videos; AI Santa reached 1M engagements in 60 days across 26 languages.

## Cautionary tales worth studying more

- **[Klarna (Support)](https://www.reworked.co/employee-experience/klarna-claimed-ai-was-doing-the-work-of-700-people-now-its-rehiring/):** In 2024, Klarna said AI was doing the work of 700 customer-service agents. By 2025 the company was [rehiring humans](https://fortune.com/2025/05/09/klarna-ai-humans-return-on-investment/) — CSAT had collapsed. The lesson: **augmentation scales, replacement doesn't.** Customer judgment requires empathy and policy-edge cases that AI cannot adjudicate.
- **[Air Canada (Support)](https://customerthink.com/chatbots-under-fire-navigating-ai-pitfalls-with-insights-from-dpd-and-air-canada/):** Tribunal held the airline liable when its chatbot misquoted refund policy. If the bot commits, the company owes. Every customer-facing bot needs **no-commitment guardrails and clear disclosure**.
- **[DPD (Support)](https://customerthink.com/chatbots-under-fire-navigating-ai-pitfalls-with-insights-from-dpd-and-air-canada/):** Chatbot was prompt-injected into profanity and self-criticism; screenshots went viral (800k+ views in 24 hours). Red-team your support bots against prompt injection before launch — not after.
- **[Amazon recruiting AI (HR)](https://leoforce.com/blog/amazon-ai-hiring-bias-case-study/):** Trained on a decade of resumes from a male-dominated workforce; the model learned to penalize "women's" as a keyword. Scrapped in 2018. The lesson: **historical data encodes historical bias**. Any HR ranking model needs ongoing bias audits, not one-time validation.
- **[General failure rate](https://fortune.com/2025/05/09/klarna-ai-humans-return-on-investment/):** 42% of enterprises scrapped most AI projects in 2024; 70% of generative-AI deployments missed ROI targets. The common denominator is skipping Phase 0.

---

## Metrics that actually matter

| Function | Leading | Lagging | Quality guard |
|---|---|---|---|
| HR | Time-to-screen | Time-to-fill | Adverse-impact ratio (bias audit) |
| Finance | Time-to-reconcile | Close-cycle duration | Audit findings |
| Legal | First-pass redline time | Matter cycle time | Correction-rate on AI output |
| Marketing | Asset production time | Campaign ROI | Brand-safety incidents |
| Sales | Proposal turnaround | Win rate | Response rate (not just send rate) |
| Support | Handle time, deflection | CSAT | Escalations-to-human, policy errors |

If the leading metric improves while the quality guard regresses, you are shifting cost to customers, candidates, or auditors. Stop and fix it before scaling.

---

## Where Claude and Anthropic tools fit in the roadmap

The roadmap is platform-agnostic — every phase works with ChatGPT Enterprise, Microsoft 365 Copilot, Claude Enterprise, or a mix. That said, business functions have a few specific needs where Anthropic's product lineup tends to map cleanly.

| Function | Typical Anthropic fit | Why here |
|---|---|---|
| **HR** | [Claude Project](https://www.anthropic.com/news/projects) or Claude API + RAG on policy corpus, inside [Claude Enterprise](https://www.anthropic.com/enterprise) | Conservative default behavior on uncertain questions; strong refusal discipline; audit logs for compliance reviews |
| **Finance** | Claude API with [prompt caching](https://docs.claude.com/en/docs/build-with-claude/prompt-caching) on chart-of-accounts + policy context; Claude chat for ad-hoc analysis | Long context handles full quarter's GL + commentary; caching cuts per-query cost at month-end close volume |
| **Legal** | Purpose-built (Harvey, CoCounsel) + Claude Enterprise for in-house teams; Claude API for custom clause-library applications | 200k-token context window fits full contract + playbook in a single call; nuance on clause negotiation language |
| **Marketing** | Claude chat + Artifacts for long-form; Claude API + [batch processing](https://docs.claude.com/en/docs/build-with-claude/batch-processing) for variant generation at scale | Batch API is the right price point for generating thousands of localized variants; Artifacts for interactive preview during drafting |
| **Sales** | Claude API integrated with CRM for account research and draft sequences; Claude Code for sales-ops automation | Long context for full account history + news + prior emails; structured output for CRM-write-back |
| **Support** | Claude API + [tool use](https://docs.claude.com/en/docs/build-with-claude/tool-use) for agent-assist; Claude-based vendor platforms (Intercom Fin, etc.) for deployed bots | Trained cautious stance on uncertainty reduces the Klarna / Air Canada failure mode; tool use lets the model look things up rather than fabricate them |

A few enterprise-grade features that matter more in business functions than in engineering:

- **[Zero Data Retention](https://privacy.anthropic.com/en/articles/10023638-is-my-data-used-for-model-training) by default on enterprise tiers.** HR, finance, and legal functions often cannot clear a vendor review without this posture. Confirm the contractual language, not just the marketing page.
- **[SSO, SCIM, and audit logs](https://www.anthropic.com/enterprise).** These are table stakes for any rollout that crosses Security and Compliance. Claude Enterprise covers them; consumer Claude does not.
- **[Prompt caching](https://docs.claude.com/en/docs/build-with-claude/prompt-caching) and [batch processing](https://docs.claude.com/en/docs/build-with-claude/batch-processing).** The two levers that make large-context, high-volume patterns (analyst grounding on policy docs, marketing variant generation, support ticket classification) affordable at scale. Factor them into your Phase 4 cost model.
- **Model selection matters per function.** Opus for complex reasoning (legal analysis, variance explanations, executive briefings), Sonnet for day-to-day drafting and routing, Haiku for high-volume classification and routing. Model IDs evolve — default to the latest and most capable models for new builds.

The roadmap structure, gates, and metrics do not change whether you pick Anthropic, OpenAI, Microsoft, or a mix. But on a site focused on Claude, this is the shortlist to keep open as you execute.

## The honest summary

Non-tech AI adoption is harder than tech adoption for three reasons: the data is messier, the blast radius is larger, and the tools are less mature. The compensating advantage is that the [BCG 10/20/70 rule](https://www.bcg.com/publications/2026/scaling-ai-requires-new-processes-not-just-new-tools) is even more true here — the people and process investment matters more, and the organizations willing to make it see outsized gains.

Every failure cited above is a Phase-skip. Klarna skipped the Phase 2 gate (no HITL on customer outputs). Air Canada skipped the Phase 0 guardrails (no no-commitment rule). Amazon skipped the Phase 0 bias audit. The roadmap is simple; the discipline to follow it is rare.

Pair this with the [5 Stages of AI Acceptance]({{< ref "5-stages-of-ai-acceptance" >}}) for the strategic view, and the [AI Fluency framework]({{< ref "ai-fluency" >}}) for the individual-competency view.

## References

1. [McKinsey: The State of AI 2025](https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai) — adoption rates, HITL statistics, high-performer differentiators
2. [BCG: Scaling AI Requires New Processes, Not Just New Tools (2026)](https://www.bcg.com/publications/2026/scaling-ai-requires-new-processes-not-just-new-tools) — the 10/20/70 rule
3. [SHRM: State of AI in HR 2026](https://www.shrm.org/topics-tools/research/state-of-ai-hr-2026/full-report) — HR-specific adoption data
4. [CFO Connect: State of AI in Finance 2026](https://www.cfoconnect.eu/resources/reports/state-of-ai-in-finance-2026/) — finance adoption trends, 30/90/365-day roadmap
5. [Gallagher: 2026 AI Adoption and Risk Benchmarking](https://www.ajg.com/news-and-insights/features/ai-adoption-and-risk-benchmarking-2026/) — risk and data-classification data
6. [A&O Shearman: Harvey Launch Partnership](https://www.aoshearman.com/en/news/ao-announces-exclusive-launch-partnership-with-harvey) — legal AI firmwide rollout
7. [A&O Shearman + Harvey: Agentic AI for Legal Workflows (2025)](https://www.aoshearman.com/en/news/ao-shearman-and-harvey-to-roll-out-agentic-ai-agents-targeting-complex-legal-workflows) — complex-workflow agent deployment
8. [CFO Dive: Microsoft Finance Team Puts Copilot to the Test](https://www.cfodive.com/news/microsoft-finance-copilot-generativeai-software/727176/) — 5,000-person finance-org adoption
9. [Microsoft: Using Copilot in Finance (Scenario Library)](https://adoption.microsoft.com/en-us/scenario-library/finance/) — workflow-specific benchmarks
10. [Unilever: How AI Is Helping Drive Desire at Scale](https://www.unilever.com/news/news-search/2025/how-ai-is-helping-drive-desire-at-scale-across-unilever/) — marketing-asset production gains
11. [Digital Defynd: 12 Ways Coca-Cola Is Using AI](https://digitaldefynd.com/IQ/ways-coca-cola-uses-artificial-intelligence/) — FIFA World Cup and holiday-campaign case studies
12. [Reworked: Klarna Claimed AI Was Doing the Work of 700 People — Now It's Rehiring](https://www.reworked.co/employee-experience/klarna-claimed-ai-was-doing-the-work-of-700-people-now-its-rehiring/) — support-automation failure
13. [Fortune: Klarna Flips from AI-First to Hiring People Again](https://fortune.com/2025/05/09/klarna-ai-humans-return-on-investment/) — 42% project scrap rate, 70% missed ROI
14. [CustomerThink: Chatbots Under Fire — DPD and Air Canada](https://customerthink.com/chatbots-under-fire-navigating-ai-pitfalls-with-insights-from-dpd-and-air-canada/) — liability and prompt-injection failures
15. [Leoforce: Lessons from Amazon's Sexist AI Recruiting Tool](https://leoforce.com/blog/amazon-ai-hiring-bias-case-study/) — bias-in-training-data case study
16. [aona.ai: Free AI Governance Framework Templates](https://aona.ai/resources/templates/) — policies, checklists, DPAs
17. [Verifywise: AI Acceptable Use Policy Template](https://verifywise.ai/ai-governance-library/policies-and-internal-governance/ai-acceptable-use-policy-template) — ISO/IEC 42001-aligned starting point
18. [Claude Enterprise](https://www.anthropic.com/enterprise) — enterprise tier for non-tech functions with ZDR, SSO, SCIM, and audit logs
19. [Claude Projects](https://www.anthropic.com/news/projects) — grounding Claude on internal document corpora per function
20. [Anthropic: Prompt caching](https://docs.claude.com/en/docs/build-with-claude/prompt-caching) — cost lever for large-context, high-volume business workflows
21. [Anthropic: Batch processing](https://docs.claude.com/en/docs/build-with-claude/batch-processing) — cost lever for marketing and support-volume use cases
22. [Anthropic: Data handling and privacy](https://privacy.anthropic.com/en/articles/10023638-is-my-data-used-for-model-training) — default posture on customer data and model training

---

*The roadmap for business functions is not different from engineering's in structure — it is different in stakes. A bad line of AI-generated code gets caught in review. A bad line of AI-generated customer communication gets caught on the news.*
