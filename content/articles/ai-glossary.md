---
title: "AI Glossary"
date: 2026-04-17
draft: false
description: "A working vocabulary for anyone navigating the AI landscape, grouped by theme."
pagetags: ["glossary", "basic"]
---

## Glossary

A working vocabulary for anyone navigating the AI landscape. Terms are grouped by theme. Each definition aims to be precise enough to be useful and short enough to actually read.

---

## Core Concepts

- <a id="llm"></a>**LLM (Large Language Model)** — A neural network trained on massive text corpora to predict and generate language. Examples: Claude, GPT-4, Gemini.
- <a id="slm"></a>**SLM (Small Language Model)** — A language model with fewer parameters, optimized for efficiency over raw capability. Typically runs on edge devices or handles narrow tasks.
- <a id="token"></a>**Token** — The atomic unit a language model reads and writes. Text is split into tokens by a tokenizer before the model processes it — the model never sees raw characters. In English, one token is roughly ¾ of a word: "unhappiness" becomes `un` + `happi` + `ness`. Other languages tokenize less efficiently — the same sentence in Japanese or Arabic may cost 2–3x more tokens than in English. Tokens matter because they determine cost (API pricing is per-token), speed (more tokens = slower), and whether your input fits within the context window. Numbers are tokenized digit-by-digit or in small chunks — `42` may be one token, but `123456` becomes several. This is why LLMs struggle with arithmetic: they don't see numbers as values, they see them as sequences of digit-tokens. Both input and output count toward token usage.
- <a id="context"></a>**Context** — Everything the model can see when generating a response: the system prompt, full conversation history, injected documents, tool results, and any other text passed in the current request. Context is the model's entire working memory — it has no access to information outside of it. If something isn't in the context, the model cannot use it (aside from what it learned during training). Managing context is a core skill: stuffing it with irrelevant data degrades quality, while missing key information leads to hallucination. In agentic workflows, context is assembled dynamically — the harness decides what to include, summarize, or drop to stay within limits.
- <a id="context-window"></a>**Context window** — The maximum number of tokens a model can process in a single call — input and output combined. Current frontier models range from 128K to over 1M tokens. A larger window allows longer conversations, bigger documents, and more few-shot examples, but costs more compute and money. In practice, model quality can degrade on information buried in the middle of very long contexts (the "lost in the middle" problem). Managing the context window — deciding what to keep, compress, or evict — is one of the key challenges in building production AI systems.
- <a id="knowledge-cutoff"></a>**Knowledge cutoff** — The date after which a model has no training data. The model doesn't know about events, papers, or releases that happened after this date.
- <a id="parameters"></a>**Parameters** — The numerical values (weights and biases) a model learns during training. Parameter count is a rough proxy for model capacity — GPT-3 has 175 billion.

## Alignment & Safety

- <a id="alignment"></a>**Alignment** — The problem of making AI systems behave according to human values and intentions. A model is "aligned" when it does what users actually want, not just what it was literally asked.
- <a id="rlhf"></a>**RLHF (Reinforcement Learning from Human Feedback)** — A training technique where human raters rank model outputs and a reward model learns their preferences. The main method used to align chat models.
- <a id="rlaif"></a>**RLAIF (Reinforcement Learning from AI Feedback)** — Like RLHF, but using AI-generated feedback instead of human raters. Scales better but inherits the evaluator model's biases.
- <a id="constitutional-ai"></a>**Constitutional AI** — [Anthropic's approach](https://arxiv.org/abs/2212.08073) where a model critiques and revises its own outputs based on a set of written principles (a "constitution"). Reduces reliance on human labelers for safety training.
- <a id="dpo"></a>**DPO (Direct Preference Optimization)** — An [alignment method](https://arxiv.org/abs/2305.18290) that skips the reward model entirely, training directly on preference pairs. Simpler pipeline than RLHF with competitive results.
- <a id="reward-model"></a>**Reward model** — A model trained to predict human preferences, used to guide RLHF training. Scores candidate outputs so the policy model can optimize for what humans prefer.
- <a id="guardrails"></a>**Guardrails** — Rules, filters, or systems layered around a model to constrain its behavior. Can be prompt-based, code-based, or a combination — they enforce boundaries the model itself might not respect.
- <a id="safety-filter"></a>**Safety filter** — Automated screening that blocks or flags model inputs or outputs matching predefined harmful categories. Operates independently of the model's own judgment.
- <a id="red-teaming"></a>**Red teaming** — Deliberately trying to make a model produce harmful, incorrect, or policy-violating outputs. Used to find vulnerabilities before deployment.
- <a id="jailbreak"></a>**Jailbreak** — A prompt technique that tricks a model into bypassing its safety training. Typically involves role-play scenarios, hypotheticals, or instruction overrides.
- <a id="prompt-injection"></a>**Prompt injection** — An attack where untrusted input (from a user or external data) hijacks the model's instructions. The model treats the injected text as a new instruction rather than data.
- <a id="hallucination"></a>**Hallucination** — When a model generates confident, plausible-sounding text that is factually wrong or entirely fabricated. Not lying — the model has no concept of truth, only of probable next tokens.
- <a id="grounding"></a>**Grounding** — Techniques that anchor a model's outputs to verifiable sources — retrieved documents, search results, or structured data. Reduces hallucination by giving the model facts to reference.
- <a id="watermarking"></a>**Watermarking** — Embedding a statistical signal in AI-generated text or images that lets detectors identify it as AI-produced. Invisible to humans but measurable algorithmically.
- <a id="ai-safety"></a>**AI safety** — The field studying how to build AI systems that remain beneficial and controllable. Spans technical alignment research, governance, and policy.
- <a id="x-risk"></a>**Existential risk (x-risk)** — The hypothesis that sufficiently advanced AI could pose a catastrophic or irreversible threat to humanity. Motivates long-term safety research and policy efforts.
- <a id="capability-overhang"></a>**Capability overhang** — A situation where a model's latent capabilities exceed what's been demonstrated or deployed. Safety concern: a model might be more capable than its evaluations suggest.
- <a id="hhh"></a>**HHH (Helpful, Honest, Harmless)** — [Anthropic's framework](https://arxiv.org/abs/2204.05862) for evaluating AI assistant behavior. A model should provide useful answers, avoid deception, and refuse harmful requests.
- <a id="model-card"></a>**Model card** — A standardized document describing a model's intended use, training data, performance, limitations, and ethical considerations. The nutrition label for AI models.
- <a id="system-card"></a>**System card** — A broader disclosure document covering an entire AI system — not just the model, but the surrounding infrastructure, safety mitigations, and deployment context.

## Prompting & Interaction

- <a id="prompt"></a>**Prompt** — The text input sent to a model. Everything the model sees before generating a response — from a single question to a multi-thousand-token document package.
- <a id="system-prompt"></a>**System prompt** — Instructions provided to the model that set its behavior, persona, and constraints. Processed before the user's message; typically hidden from the end user.
- <a id="prompt-engineering"></a>**Prompt engineering** — The practice of crafting inputs to reliably get desired outputs from a model. Part writing skill, part empirical science — small phrasing changes can dramatically shift results.
- <a id="in-context-learning"></a>**In-context learning** — The model's ability to pick up patterns from examples provided in the prompt, without any weight updates. Few-shot prompting works because of this.
- <a id="chain-of-thought"></a>**Chain-of-thought (CoT)** — Prompting a model to show its [reasoning step by step](https://arxiv.org/abs/2201.11903) before giving a final answer. Dramatically improves accuracy on math, logic, and multi-step problems.
- <a id="chat-completion"></a>**Chat completion** — The API paradigm where you send a list of messages (system, user, assistant) and get back the next assistant message. The standard interface for conversational AI.
- <a id="completion"></a>**Completion** — The older API paradigm where you send a text prefix and the model continues it. No role structure — just raw text in, text out.
- <a id="temperature"></a>**Temperature** — A parameter controlling output randomness. 0 = deterministic (always picks the most likely token), 1 = standard sampling, >1 = increasingly creative/chaotic.
- <a id="top-k"></a>**Top-k** — Sampling only from the k most likely next tokens. Top-k of 40 means the model ignores every token outside the top 40 candidates at each step.
- <a id="top-p"></a>**Top-p (nucleus sampling)** — Sampling from the smallest set of tokens whose cumulative probability exceeds p. Top-p of 0.9 means the model considers tokens until their probabilities sum to 90%.
- <a id="logprobs"></a>**Logprobs** — The log-probabilities the model assigns to each candidate token. Useful for confidence estimation, classification tasks, and understanding why the model chose what it did.
- <a id="stop-sequence"></a>**Stop sequence** — A string that tells the model to stop generating when encountered. Prevents runaway output and enforces structured responses.
- <a id="max-tokens"></a>**Max tokens** — The hard limit on how many tokens the model will generate in a response. Prevents unexpectedly long (and expensive) outputs.
- <a id="structured-output"></a>**Structured output** — Constraining model output to a specific schema — typically JSON matching a defined structure. The model generates valid data rather than free-form text.

## Agentic AI

- <a id="agent"></a>**Agent** — An AI system that takes actions autonomously — reading files, calling APIs, writing code, making decisions — rather than just generating text. Goes beyond question-and-answer.
- <a id="agentic"></a>**Agentic** — Describes AI behavior involving autonomous planning, tool use, and multi-step execution. A workflow is "agentic" when the model decides what to do next, not just what to say.
- <a id="tool-use"></a>**Tool use** — Giving a model access to external functions it can call during generation — search, calculators, code execution, APIs. The model decides when and how to invoke them.
- <a id="function-calling"></a>**Function calling** — The API mechanism for tool use. The model outputs a structured request to call a specific function with specific arguments; your code executes it and returns results.
- <a id="mcp"></a>**MCP (Model Context Protocol)** — An [open standard](https://modelcontextprotocol.io/) for connecting AI models to external tools and data sources. Provides a unified interface so tools work across different AI applications.
- <a id="harness"></a>**Harness** — The software layer that wraps a model and manages its execution — routing prompts, handling tool calls, enforcing policies, and managing conversation state. The model runs inside the harness.
- <a id="orchestration"></a>**Orchestration** — Coordinating multiple AI components (models, tools, memory, retrieval) into a coherent workflow. The "conductor" layer that decides what runs when.
- <a id="planning"></a>**Planning** — An agent's ability to break a complex goal into sub-tasks and sequence them. The step between understanding a request and executing it.
- <a id="reflection"></a>**Reflection** — When an agent evaluates its own outputs or actions, catches errors, and self-corrects. A form of internal quality control — "did my code actually compile?"
- <a id="memory"></a>**Memory (in AI agents)** — Mechanisms that let an agent retain information across turns or sessions. Short-term memory is the conversation context; long-term memory persists to storage.
- <a id="human-in-the-loop"></a>**Human-in-the-loop** — A design pattern where a human reviews, approves, or corrects AI outputs before they take effect. Balances automation with oversight.
- <a id="autonomous-agent"></a>**Autonomous agent** — An agent that operates with minimal human intervention, making decisions and taking actions independently. The far end of the automation spectrum from human-in-the-loop.

## RAG & Retrieval

- <a id="rag"></a>**RAG (Retrieval-Augmented Generation)** — A pattern where the model retrieves relevant documents before generating a response, combining the model's language ability with up-to-date, specific knowledge it wasn't trained on. A typical RAG pipeline has three stages: indexing (chunk documents, compute embeddings, store in a vector database), retrieval (find the most relevant chunks for a query), and generation (feed those chunks into the model's context alongside the user's question). RAG solves two fundamental LLM limitations at once — the knowledge cutoff (the model can access fresh data) and hallucination (the model can cite actual sources instead of guessing). The quality of a RAG system depends heavily on chunking strategy, embedding model choice, and how many chunks you retrieve — too few and the model lacks information, too many and relevant facts get buried in noise.
- <a id="tag"></a>**TAG (Tool Augmented Generation)** — A pattern where the model invokes external tools — calculators, code interpreters, APIs, databases, web searches — during generation rather than relying solely on its training data. Where RAG augments with *data*, TAG augments with *functionality*: the model doesn't just read information, it takes actions and gets live results back. A TAG workflow might look like: user asks a question, the model decides it needs to run a SQL query, calls a database tool, reads the result, then generates a natural-language answer. This makes the model's capabilities extensible — any API or function you expose becomes something the model can use. Most production agent systems (Claude Code, ChatGPT with plugins, Copilot) are TAG systems under the hood, often combined with RAG for document retrieval alongside tool calls.
- <a id="retrieval"></a>**Retrieval** — Fetching relevant information from an external source (database, search index, API) to feed into the model's context. The "R" in RAG.
- <a id="vector-database"></a>**Vector database** — A database optimized for storing and querying embedding vectors. Enables fast similarity search — "find documents whose meaning is closest to this query."
- <a id="chunking"></a>**Chunk / Chunking** — Splitting documents into smaller pieces for embedding and retrieval. Chunk size is a key design choice — too small loses context, too large dilutes relevance.
- <a id="semantic-search"></a>**Semantic search** — Finding results based on meaning rather than keyword matching. "How to fix a leaky faucet" matches "plumbing repair guide" even though they share no words.
- <a id="cosine-similarity"></a>**Cosine similarity** — A mathematical measure of how similar two vectors are, based on the angle between them. The standard metric for comparing embeddings — 1.0 means identical direction, 0 means unrelated.

## References

1. [Chain-of-Thought Prompting Elicits Reasoning](https://arxiv.org/abs/2201.11903) — CoT prompting paper (Wei et al., 2022)
2. [Constitutional AI: Harmlessness from AI Feedback](https://arxiv.org/abs/2212.08073) — Anthropic's constitutional AI approach (Bai et al., 2022)
3. [Training a Helpful and Harmless Assistant (HHH)](https://arxiv.org/abs/2204.05862) — Anthropic's HHH framework (Askell et al., 2022)
4. [Direct Preference Optimization](https://arxiv.org/abs/2305.18290) — DPO alignment method (Rafailov et al., 2023)
7. [Anthropic Glossary](https://docs.anthropic.com/en/docs/about-claude/glossary) — official terminology for the Claude platform
8. [Google Machine Learning Glossary](https://developers.google.com/machine-learning/glossary) — comprehensive ML term reference
9. [Stanford HAI — Key Terms in AI](https://hai.stanford.edu/policy/brief-definitions-of-key-terms-in-ai) — policy-oriented AI definitions
10. [Model Context Protocol](https://modelcontextprotocol.io/) — open standard for AI tool connectivity
