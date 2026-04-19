---
title: "TOON: Token-Oriented Object Notation for LLMs"
date: 2026-04-19
draft: false
description: "A lossless, token-efficient JSON encoding for LLM prompts, with 30–60% fewer tokens and accuracy benchmarks across Claude, Gemini, GPT, and Grok."
pagetags: ["toon", "json", "tokens"]
---

# TOON: Token-Oriented Object Notation for LLMs

## TL;DR

- **What it is** — TOON is a lossless, human-readable encoding of the JSON data model designed to use ~30–60% fewer tokens in LLM prompts ([spec](https://github.com/toon-format/spec)).
- **How it saves tokens** — drops braces, brackets, and repeated keys; uses YAML-style indentation for nesting and a CSV-style header-plus-rows layout for uniform arrays of objects ([toon-format/toon](https://github.com/toon-format/toon)).
- **Sweet spot** — large, uniform arrays of records (orders, users, metrics). Worst case: deeply nested or non-uniform data, where JSON often wins.
- **Accuracy holds up** — across 209 retrieval questions on Claude Haiku, Gemini Flash, GPT-5 Nano, and Grok-4, TOON hit 76.4% accuracy at ~40% fewer tokens vs JSON's 75.0% ([benchmarks](https://github.com/toon-format/toon)).
- **Vendor support is community-only so far** — no native TOON in Claude Code, Claude.ai, Codex, or Cursor. Adoption today goes through MCP servers (Antigravity, Gemini CLI), hooks, or skills. Spring AI is the one mainstream framework with built-in support.
- **Loud critics exist** — skeptics on Hacker News and Medium argue TOON's training-data novelty causes hallucinations and that token cost is no longer the bottleneck. Benchmark on your own prompts before adopting.

---

JSON dominates because it's the lingua franca of APIs, not because it's efficient for language models. Repeated keys, mandatory quoting, and structural punctuation all consume tokens that LLMs pay for in latency and dollars. **TOON (Token-Oriented Object Notation)** is a recent format that targets exactly this overhead: same data model as JSON, dramatically fewer tokens, still readable to humans and parseable by models.

This article covers what TOON is, how its specification works, what the published benchmarks actually show, when to reach for it, and which libraries exist today across JavaScript/TypeScript, Java, and Go.

## Why TOON Exists

JSON pays a tokenization tax that nobody notices until token bills land. Three sources of waste dominate:

1. **Repeated keys in arrays of objects** — `{"id":1,"name":"Ada"}` and 999 siblings re-encode `id` and `name` every row.
2. **Structural punctuation** — `{`, `}`, `[`, `]`, `"` are individually small but multiply across long payloads.
3. **Quoting strings that don't need it** — most identifiers, enum values, and short labels could be bare.

CSV solves (1) and (2) but loses structure: no nesting, no types, no schema. YAML drops braces but spends tokens on whitespace and is ambiguous in ways LLMs handle poorly. TOON splits the difference: **YAML-style indentation for nested objects**, **CSV-style headers and rows for uniform arrays**, and **explicit length markers** so a model knows exactly how many records to expect ([toonformat.dev](https://toonformat.dev/)).

The format was published in late 2025 and is currently at **specification version 3.0** (dated 2025-11-24) ([spec](https://github.com/toon-format/spec)). It is a Working Draft — file extension `.toon`, provisional media type `text/toon`, IANA registration pending.

## The Specification at a Glance

The full grammar is in the [official spec](https://github.com/toon-format/spec/blob/main/SPEC.md). The rules below cover what you'll actually see in practice.

### Encoding basics

- **UTF-8** text, **LF** line endings.
- **Indentation is spaces only.** Default 2 spaces per level. Tabs are forbidden for indentation. Strict mode requires depth to be an exact multiple of `indentSize`.
- **No comments.** TOON deliberately omits a comment syntax.

### Primitives

| Type    | Form                            |
| ------- | ------------------------------- |
| String  | Bare or `"quoted"`              |
| Number  | `42`, `-3.14`, `1e-6`           |
| Boolean | `true`, `false`                 |
| Null    | `null`                          |

Numbers normalize to a canonical form on output: no exponent (`1e6` → `1000000`), no leading zeros except `0`, no trailing fractional zeros (`1.50` → `1.5`), and integer collapse (`1.0` → `1`). Negative zero becomes `0`.

### When strings need quotes

A string MUST be quoted when it:

- Is empty.
- Has leading or trailing whitespace.
- Equals `true`, `false`, or `null` (case-sensitive).
- Matches a numeric literal pattern (including `"05"` — leading-zero strings).
- Contains `:`, `"`, `\`, `[`, `]`, `{`, or `}`.
- Contains a control character (newline, carriage return, tab).
- Contains the active delimiter (comma, tab, or pipe in the current array context).
- Equals `-` or starts with `-`.

Only five escape sequences are valid inside quoted strings: `\\`, `\"`, `\n`, `\r`, `\t`. Anything else is an error.

### Objects

Standard YAML-style key-value pairs with indentation:

```toon
context:
  task: Our favorite hikes together
  location: Boulder
```

### Arrays of primitives

Inline, comma-delimited, with an explicit length marker:

```toon
friends[3]: ana,luis,sam
```

The `[3]` is not optional — it's how the model (and the parser) knows where the array ends.

### Tabular arrays of objects

This is where TOON earns its keep. For uniform arrays — same fields in every row — declare the schema once and stream rows beneath:

```toon
hikes[3]{id,name,distanceKm,elevationGain,companion,wasSunny}:
  1,Blue Lake Trail,7.5,320,ana,true
  2,Ridge Overlook,9.2,540,luis,false
  3,Cascade Falls,5.1,210,sam,true
```

Each row's value count MUST equal the declared field count, and (in strict mode) the row count MUST equal the declared length. These constraints double as cheap validation — a malformed row is a parse error, not a silent data bug.

### Delimiters

Three delimiter choices: **comma** (default, no symbol), **tab** (`[N\t]`), and **pipe** (`[N|]`). The same symbol must appear inside both the bracket length marker and the field-list braces. Pipe is useful when row values commonly contain commas; tab is useful for paste-friendly editing.

## Token Savings: What the Benchmarks Show

The headline numbers come from the [official TOON benchmark suite](https://toonformat.dev/guide/benchmarks): 209 data-retrieval questions evaluated across four current models — **Claude Haiku 4.5, Gemini 3 Flash, GPT-5 Nano, and Grok 4.1 Fast**.

### Cross-format efficiency ranking

The single most useful chart from the benchmark — accuracy per 1,000 tokens, all formats:

| Format       | Acc / 1K tokens | Accuracy | Tokens |
| ------------ | --------------: | -------: | -----: |
| **TOON**     |        **27.7** |    76.4% |  2,759 |
| JSON compact |            23.7 |    73.7% |  3,104 |
| YAML         |            19.9 |    74.5% |  3,749 |
| JSON         |            16.4 |    75.0% |  4,587 |
| XML          |            13.8 |    72.1% |  5,221 |

TOON is the most efficient format on the *information per token* axis by a wide margin — beating compact JSON by ~17%, full JSON by ~70%, and XML by ~100%.

### Per-model accuracy

| Model            | TOON      | JSON  | Notes                     |
| ---------------- | --------- | ----- | ------------------------- |
| Claude Haiku 4.5 | **59.8%** | 57.4% | TOON leads                |
| Gemini 3 Flash   | 96.7%     | 97.1% | XML actually tops at 98.1% |
| GPT-5 Nano       | **90.9%** | 89.0% | TOON tied with JSON compact |
| Grok 4.1 Fast    | **58.4%** | 56.5% | TOON leads                |

TOON wins outright on Claude Haiku and Grok; ties on GPT-5 Nano; loses by a fraction of a point on Gemini Flash. The gap is rarely huge in either direction — the tokens are the durable win, the accuracy is a wash or a slight tailwind.

### By question type

The benchmark also breaks accuracy down by what the model is being asked to do — and this is where TOON's structural metadata really earns its keep:

| Question type         | TOON  | JSON  | Δ      |
| --------------------- | ----- | ----- | ------ |
| Field retrieval       | 99.6% | 99.3% | +0.3   |
| Aggregation           | 61.9% | 61.9% | tied   |
| Filtering             | 56.8% | 53.1% | +3.7   |
| Structure awareness   | 89.0% | 87.0% | +2.0   |
| Structural validation | 70.0% | 60.0% | **+10.0** |

TOON's `[N]` length markers and `{field,…}` headers are exactly the metadata that helps a model answer "is this row count consistent?" or "does every record have the expected fields?" — which is why the structural-validation gap is by far the largest.

### By dataset shape

**Mixed-structure datasets** (nested or semi-uniform):

| Dataset       | TOON tokens | JSON tokens | Reduction |
| ------------- | ----------: | ----------: | --------: |
| E-commerce    |      73,126 |     109,599 |    −33.3% |
| Event logs    |     154,084 |     181,201 |    −15.0% |
| Nested config |         620 |         911 |    −31.9% |

**Flat tabular datasets** (where CSV is competitive):

| Dataset          | CSV tokens | TOON tokens | TOON vs CSV |
| ---------------- | ---------: | ----------: | ----------: |
| Employee records |     47,102 |      49,919 |       +6.0% |
| Time-series      |      8,383 |       9,115 |       +8.7% |
| GitHub repos     |      8,512 |       8,744 |       +2.7% |

CSV wins on raw size for genuinely flat data — TOON pays a 3–9% overhead for typed fields and an explicit schema. Whether that's worth it depends on whether your model needs the structure.

### Real-world payload examples

- **E-commerce orders (500 rows):** 11,842 JSON tokens → 4,617 TOON tokens, a **61% reduction** ([Tensorlake](https://www.tensorlake.ai/blog-posts/toon-vs-json)).
- **100 uniform records:** 2,703 JSON tokens → 1,009 TOON tokens, **62.7% reduction** — about $169 saved per 10,000 requests at $0.01/1K input ([LogRocket](https://blog.logrocket.com/reduce-tokens-with-toon/)).

These savings are not free. They concentrate in **uniform, repetitive, table-shaped** data. The next section is how to tell whether your payload qualifies.

## When to Use TOON (and When Not To)

### Reach for TOON when

- The payload is a **large array of objects with the same fields** — orders, log lines, products, time-series rows.
- You're paying a measurable token bill on **structured-input prompts** (RAG retrieval, tool results, batched lookups).
- You need types and structure (so CSV is out) but don't need machine interoperability with JSON consumers.
- You want **explicit length markers** so the model is less likely to hallucinate or truncate rows.

### Stick with JSON when

- The payload is **deeply nested or schema-irregular** — TOON's tabular advantage disappears, and the per-line indentation overhead can make TOON *larger* than minified JSON.
- The data flows **machine-to-machine**. Every existing tool, log pipeline, and SDK already speaks JSON.
- **Latency on the encoder/decoder side** matters more than prompt token count. JSON parsers are decades-optimized.

### Stick with CSV when

- The data is genuinely flat, single-typed, and you don't need nesting or per-row schema validation. CSV beats TOON by ~5% on tokens for this case.

The architectural pattern that emerges: **keep JSON as the system-of-record format and convert to TOON only at the LLM prompt boundary.** Treat TOON like a compression layer for a single hop, not a replacement for your APIs.

## Best Practices

These are drawn from the official docs, the LogRocket guide, and the spec's own design notes.

1. **Convert at the prompt boundary, not earlier.** Your services keep speaking JSON. A small adapter encodes to TOON immediately before the LLM call and (if needed) decodes the response.
2. **Always include length markers.** They are mandatory in the spec and they materially help models stay aligned on row counts. Don't strip them to save a token.
3. **Pick the right delimiter for your data.** Default to comma. Switch to pipe (`|`) when values commonly contain commas, or tab when you want paste-into-editor ergonomics.
4. **Teach the model the format in the system prompt.** A two- or three-line example of TOON syntax inside the prompt costs almost nothing and dramatically improves first-attempt parsing — particularly on smaller models. The TOON team explicitly recommends naming the format and showing a snippet ([LogRocket](https://blog.logrocket.com/reduce-tokens-with-toon/)).
5. **Decode and validate the model's output.** Whether the model returns JSON or TOON, parse it before trusting it. TOON's strict-mode length checks turn malformed responses into hard errors instead of silent corruption.
6. **Benchmark with your own tokenizer.** Token counts vary between Claude, GPT, Gemini, and open-weights tokenizers. The published numbers are representative, not contractual.
7. **Don't use TOON for response schemas with structured-output APIs.** Provider-side JSON Schema enforcement (OpenAI structured outputs, Anthropic tool use, etc.) expects JSON. Use TOON for *input* payloads and let the provider keep enforcing JSON on output.

## Library Ecosystem

The reference implementation is in TypeScript. Community ports exist for the major server languages, with quality and feature coverage that is mostly catching up to the TS SDK.

### TypeScript / JavaScript

The official SDK lives at [`@toon-format/toon`](https://github.com/toon-format/toon).

```bash
npm install @toon-format/toon
```

```ts
import { encode, decode } from "@toon-format/toon";

const data = {
  hikes: [
    { id: 1, name: "Blue Lake Trail", distanceKm: 7.5 },
    { id: 2, name: "Ridge Overlook", distanceKm: 9.2 },
  ],
};

const toon = encode(data);
const back = decode(toon);
```

There is also a CLI for quick conversion:

```bash
npx @toon-format/cli input.json -o output.toon
```

### Java

The community-driven Java implementation is **JToon**, published as `dev.toonformat:jtoon` on Maven Central — latest version `1.0.9` (Feb 2026), Java 17+, with Jackson annotation support ([toon-format/toon-java](https://github.com/toon-format/toon-java)).

```xml
<dependency>
  <groupId>dev.toonformat</groupId>
  <artifactId>jtoon</artifactId>
  <version>1.0.9</version>
</dependency>
```

```java
import dev.toonformat.jtoon.JToon;

record User(int id, String name, List<String> tags) {}

User user = new User(123, "Ada", List.of("reading", "gaming"));
String toon = JToon.encode(user);

Object decoded = JToon.decode(toon);
String json = JToon.decodeToJson(toon);
```

JToon supports custom delimiters (comma, tab, pipe) for tabular arrays and validates declared array lengths during decoding. The mature serialization library [json-io](https://www.baeldung.com/java-json-toon-format-libraries) has also added TOON as an additional output format.

Java users on **Spring AI** get TOON as a built-in tool-response format option as of November 2025, alongside XML, CSV, and YAML ([Spring blog](https://spring.io/blog/2025/11/25/spring-ai-tool-response-formats/)).

### Go

The official community Go implementation is [`toon-format/toon-go`](https://github.com/toon-format/toon-go), with no external dependencies.

```bash
go get github.com/toon-format/toon-go
```

```go
import "github.com/toon-format/toon-go"

encoded, err := toon.Marshal(payload, toon.WithLengthMarkers(true))

var result Payload
err = toon.Unmarshal(encoded, &result)
```

It supports `toon:"fieldname"` struct tags (mirroring `encoding/json`), dynamic decoding into `map[string]any`, and the standard delimiter options. Two notable third-party Go libraries also exist: [`alpkeskin/gotoon`](https://github.com/alpkeskin/gotoon) and [`wilchen558/go-toon`](https://pkg.go.dev/github.com/wilchen558/go-toon), both pure-Go with zero dependencies.

### Other languages

- **Python:** [`python-toon`](https://github.com/xaviviro/python-toon) and the `toon_format` package referenced in published examples.
- **Rust, .NET:** community implementations linked from the [official org](https://github.com/toon-format).
- The [TOON Kit](https://www.toon-kit.com/docs) documentation site tracks language coverage as it evolves.

## Vendor and IDE Support

The honest summary: **no major LLM vendor or coding agent ships native TOON support today.** Every integration in production is community-maintained, and most route through MCP servers, hooks, or skills. The picture below is current as of April 2026.

### Anthropic — Claude Code, Claude.ai, API

- **Native support: none.**
- A formal feature request to add TOON to Claude Code ([issue #14374](https://github.com/anthropics/claude-code/issues/14374), opened December 17, 2025) was **closed as "not planned"** with no recorded Anthropic response. The proposal asked for `.toon` extensions alongside `.json` for `settings.json`, `plugin.json`, `hooks.json`, and `.mcp.json`, plus a `claude convert-to-toon` migration command.
- **Community workarounds for Claude Code:**
  - A [JSON-to-TOON hook gist](https://gist.github.com/maman/de31d48cd960366ce9caec32b569d32a) that compresses MCP tool responses on the fly.
  - A [TOON Format Reference skill](https://mcpmarket.com/tools/skills/toon-format-reference) packaged for Claude Code.
  - A `claude-code-toonify` MCP server published on community registries.
- **Claude.ai chat / Claude API:** No format-aware affordance — TOON is just another text payload. Add a one- or two-sentence primer to your system prompt teaching the format, and Claude will read it. Anthropic's API still expects JSON for tool definitions and structured output schemas.

### OpenAI — Codex CLI

- **Native support: none.**
- Codex has no hook or skill mechanism for runtime payload transformation. The integration path is to **mention TOON in [`AGENTS.md`](https://developers.openai.com/codex/guides/agents-md)** — the per-repository instructions file Codex reads at session start — and let the model emit/consume TOON in the conversation. Decoding happens on your side.
- OpenAI's structured-outputs API is JSON-only.

### Cursor

- **Native support: none.**
- Cursor exposes [Model Context Protocol](https://cursor.com/docs/mcp) for tools, so the same MCP-based encode/decode pattern that works in Antigravity also works here. There is no first-party TOON extension on the Cursor directory at the time of writing.

### Google — Antigravity IDE and Gemini CLI

- **Native support: none, but the integration is the smoothest.**
- Google Cloud DA Karl Weinmeister published a [TOON MCP server](https://medium.com/google-cloud/save-tokens-with-toon-using-google-antigravity-and-the-gemini-cli-e9a641c06ea8) (Nov 19, 2025) that exposes `encode_toon` and `decode_toon` tools to any MCP client.
- **Antigravity setup** — paste into MCP config:
  ```json
  {
    "mcpServers": {
      "toon": {
        "command": "npx",
        "args": ["-y", "git+https://github.com/kweinmeister/toon-mcp.git"]
      }
    }
  }
  ```
- **Gemini CLI setup:**
  ```bash
  gemini mcp add toon npx -y "git+https://github.com/kweinmeister/toon-mcp.git"
  ```
- After registration, prompt the agent to "encode this dataset to TOON before sending it to the model."

### Frameworks and gateways

- **Spring AI** is the standout adopter: TOON is a built-in [tool-response format](https://spring.io/blog/2025/11/25/spring-ai-tool-response-formats/) alongside XML, CSV, and YAML as of November 2025. This is currently the only major Java/Kotlin AI framework with first-class TOON support.
- **LLM gateways** — at least one gateway provider has shipped TOON-based prompt compression as a transparent middleware layer ([HN discussion](https://news.ycombinator.com/item?id=46882035)). This pattern — gateway encodes JSON → TOON before forwarding to the model — sidesteps client-side library work entirely.

The takeaway: vendor uptake is happening *around* the major coding agents (via MCP, hooks, and gateways) rather than *inside* them. If you want TOON in a tool that doesn't list it, the answer is almost always "wire up the MCP server."

## Criticism

TOON has provoked a real, well-articulated backlash. The core arguments are worth understanding before you commit infrastructure to the format.

### "Models weren't trained on TOON, so they hallucinate it"

The most-repeated objection. JSON appears in training corpora at massive scale — every public API, every config file, every Stack Overflow answer. TOON has effectively zero presence in pretraining data. A widely-shared [Medium critique](https://medium.com/@time_less/why-you-shouldnt-use-toon-in-production-and-what-to-use-instead-757f55c003c0) frames it bluntly: models "hallucinate, reorder fields, break the schema, and subtly distort numeric and boolean values" when emitting TOON, because they're approximating a format they barely know.

The benchmark numbers above push back somewhat — accuracy is comparable or better on multiple models — but the critique is about *generation* (model emits TOON), while the benchmark mostly measures *comprehension* (model reads TOON, answers questions). These are different tasks. **For consuming TOON in prompts the evidence is favorable; for asking models to produce TOON the case is much weaker** — and you should generally prefer JSON for model output regardless.

### "Token cost isn't the bottleneck anymore"

The same critic argues: "A prompt that costs 8 cents but yields correct output beats a prompt that costs 6 cents but produces approximation soup." In 2025–2026, model capability and reliability dominate cost in most production decisions. Saving 30% on input tokens for a workload that already costs pennies is a microoptimization compared to picking the right model.

This is a real point — but it's domain-specific. RAG pipelines pushing 100K+ token contexts per call, batched evaluations across millions of records, or agent loops that re-read tool results dozens of times per task all see the savings compound into real money.

### "Yet another format" (XKCD 927)

Hacker News commenter `s1mon` invoked the obligatory [XKCD 927](https://xkcd.com/927/) — every "we'll standardize the existing 14 formats" effort produces 15 formats. Without ubiquity, tooling, and ecosystem network effects, TOON faces the same uphill climb as any reform attempt. No `jq` equivalent. No editor support. No widespread schema validators.

### Specific technical objections

From the [Hacker News thread](https://news.ycombinator.com/item?id=45715632):

- **Semantic ambiguity around "void" values.** Commenter `inopinatus` notes JSON deserializers already have to disambiguate absent vs. `null` vs. `false` vs. `0` vs. `""`. TOON's compact array representations add another axis of ambiguity to that mess.
- **The bracket-balancing argument doesn't uniquely favor TOON.** `hdjfjkremmr` observes that models struggle to track nested braces, "which is exactly why xml/csv (or toon) tends to work better than json" — but that argument applies to YAML and CSV too.
- **You can already get most of TOON's wins inside JSON.** Commenter `3cats-in-a-coat` argues compact list-of-lists JSON (header row + value rows) captures most of the savings without inventing a new format.

### The reasonable middle ground

Strip the polemics and the consensus is roughly: **TOON is a real win for high-volume input payloads of uniform tabular data, and a wash-or-loss for everything else.** The training-data critique is strongest as a warning against asking models to *emit* TOON. The cost critique is strongest for low-volume workloads where the overall bill is small. The XKCD critique is strongest if you're betting infrastructure on TOON becoming the next universal interchange format — which it almost certainly won't.

## Caveats and Current State

A few things to keep in mind before betting infrastructure on TOON:

- **The spec is a Working Draft.** Version 3.0 is recent (Nov 2025), the IANA media-type registration is pending, and breaking spec changes are still possible. Pin library versions.
- **Tokenizer-dependent savings.** Reported numbers cluster around the OpenAI/Anthropic tokenizer families. Other tokenizers may yield different ratios — usually still favorable, but verify.
- **Tooling gap vs JSON.** No `jq`-equivalent yet. No widespread editor support. Logging pipelines, schema validators (`ajv`, JSON Schema, Pydantic), and observability tools all assume JSON.
- **Provider response APIs still want JSON.** Use TOON for prompt input. Leave structured-output enforcement to JSON Schema on the provider side.
- **Not a silver bullet.** On nested or irregular data, the savings often disappear and can invert. Always benchmark on your own payloads.

## Where to Go Next

- Read the [official specification](https://github.com/toon-format/spec/blob/main/SPEC.md) — it's short and worth the 15 minutes.
- Run the [benchmark suite](https://github.com/toon-format/toon) on your own data with your own tokenizer before adopting at scale.
- For Java teams on Spring AI, enable TOON as a tool-response format and measure the difference in production traffic.
- For TypeScript teams, drop the `@toon-format/toon` SDK behind your existing prompt-construction layer and A/B-test against JSON on a single high-volume endpoint.

## References

1. [TOON specification (toon-format/spec)](https://github.com/toon-format/spec) — official spec repository, version 3.0 (2025-11-24).
2. [SPEC.md — formal grammar and encoding rules](https://github.com/toon-format/spec/blob/main/SPEC.md) — authoritative source for syntax, quoting, and delimiter rules.
3. [toon-format/toon](https://github.com/toon-format/toon) — TypeScript reference implementation and benchmark suite.
4. [toonformat.dev](https://toonformat.dev/) — official project site and best-practice guidance.
5. [TOON benchmarks (toonformat.dev/guide/benchmarks)](https://toonformat.dev/guide/benchmarks) — full cross-format benchmark tables (TOON, JSON, JSON compact, YAML, XML, CSV) with per-model and per-question-type breakdowns.
6. [TOON vs JSON: A Token-Optimized Data Format for Reducing LLM Costs (Tensorlake)](https://www.tensorlake.ai/blog-posts/toon-vs-json) — real-world payload comparisons including the 500-row e-commerce example.
7. [How to use TOON to reduce your token usage by 60% (LogRocket)](https://blog.logrocket.com/reduce-tokens-with-toon/) — practical guide with cost projections and best-practice recommendations.
8. [toon-format/toon-java (JToon)](https://github.com/toon-format/toon-java) — official community Java implementation; Maven coordinates `dev.toonformat:jtoon`.
9. [Introduction to TOON Format in Java (Baeldung)](https://www.baeldung.com/java-json-toon-format-libraries) — overview of Java library options including json-io.
10. [toon-format/toon-go](https://github.com/toon-format/toon-go) — official community Go implementation.
11. [alpkeskin/gotoon](https://github.com/alpkeskin/gotoon) — third-party Go implementation, zero dependencies.
12. [Beyond JSON: Spring AI tool response formats](https://spring.io/blog/2025/11/25/spring-ai-tool-response-formats/) — Spring AI's adoption of TOON alongside XML, CSV, and YAML for tool responses.
13. [TOON Kit documentation](https://www.toon-kit.com/docs) — supplemental syntax reference and language tracker.
14. [python-toon](https://github.com/xaviviro/python-toon) — Python encoder/decoder for TOON.
15. [Claude Code issue #14374 — Add TOON support](https://github.com/anthropics/claude-code/issues/14374) — feature request closed as "not planned" (Dec 2025); documents what was proposed and rejected.
16. [Claude Code JSON-to-TOON hook (gist)](https://gist.github.com/maman/de31d48cd960366ce9caec32b569d32a) — community workaround that compresses MCP tool responses on the fly.
17. [TOON Format Reference — Claude Code skill](https://mcpmarket.com/tools/skills/toon-format-reference) — packaged skill for using TOON inside Claude Code.
18. [Codex AGENTS.md guide](https://developers.openai.com/codex/guides/agents-md) — how Codex CLI reads per-repository instructions; the integration surface for telling Codex to use TOON.
19. [Cursor MCP documentation](https://cursor.com/docs/mcp) — the integration surface for adding TOON encode/decode tools to Cursor.
20. [Save Tokens with TOON using Google Antigravity and the Gemini CLI](https://medium.com/google-cloud/save-tokens-with-toon-using-google-antigravity-and-the-gemini-cli-e9a641c06ea8) — Karl Weinmeister's TOON MCP server and setup walkthrough (Nov 2025).
21. [Why You Shouldn't Use TOON in Production (Medium)](https://medium.com/@time_less/why-you-shouldnt-use-toon-in-production-and-what-to-use-instead-757f55c003c0) — the most-cited critical takedown; training-data and reliability arguments.
22. [TOON discussion on Hacker News](https://news.ycombinator.com/item?id=45715632) — original launch thread; primary source for the technical objections cited in the Criticism section.
23. [LLM gateway with TOON compression (HN)](https://news.ycombinator.com/item?id=46882035) — example of gateway-level TOON adoption as transparent middleware.
