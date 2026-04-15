---
name: article-writer
description: Write a well-structured Hugo article under content/articles/. Produces evergreen, reference-quality content with a TL;DR, inline sources, and clear information hierarchy. Use when the user asks to write, draft, or create an article (not a dated blog post).
---

# Hugo Article Writer

Write a long-form, evergreen article for `content/articles/`. The output is a single Markdown file ready for Hugo — well-structured, source-backed, and human-readable.

## Guiding principles

1. **TL;DR first.** Every article over ~300 words must open with a TL;DR section immediately after the front matter. Bullet points only — no prose. Each bullet is one self-contained takeaway a reader can act on without reading the rest.
2. **Structure for scanning.** Use clear heading hierarchy (`##` for major sections, `###` for subsections). Keep paragraphs short (3–5 sentences max). Use lists, tables, and blockquotes to break up walls of text. A reader skimming headings alone should get the article's arc.
3. **Claims need sources.** Every significant factual claim, statistic, or quote must link to its source inline. Prefer primary sources (research papers, official docs, original announcements) over secondary coverage. If a source cannot be found or verified, state the claim as opinion or omit it.
4. **No filler.** Every sentence must earn its place. Cut throat-clearing ("In this article, we will explore..."), hedging ("It's worth noting that..."), and padding. If a section doesn't add information the reader can use, delete it.
5. **Respect the reader's time.** Front-load important information in every section. Put the conclusion before the evidence, the recommendation before the reasoning. Inverted pyramid at every level.

## Inputs

The user provides a **topic** and optionally:

- Key points or angles to cover
- Target audience
- Specific sources or references to include
- Desired length or depth
- A slug or title preference

## Steps

### 1. Clarify the request

Before doing any research or writing, analyze what the user asked for and identify gaps. Consider:

- **Scope** — Is the topic narrow enough for a single article, or does it need to be split?
- **Audience** — Who is this for? A technical reader, a decision-maker, a beginner? If unclear, ask.
- **Angle** — Is the user asking for an explainer, a comparison, an opinion piece, a how-to? The same topic can produce very different articles depending on the angle.
- **Missing context** — Are there constraints, preferences, or prior decisions the user hasn't stated but you need to know?

If the request is clear and complete, proceed. If anything is ambiguous or underspecified, stop and ask clarifying questions before moving on. A few seconds of alignment here saves a full rewrite later.

### 2. Research the topic

Before writing, gather authoritative sources on the topic. Use `WebSearch` and `WebFetch` to find:

- Primary sources: official documentation, research papers, original announcements
- Data: statistics, surveys, benchmarks from reputable organizations
- Expert perspectives: quotes, frameworks, or analysis from recognized authorities

Collect at minimum **3 credible sources** before starting to write. More is better. If the topic is niche and sources are scarce, tell the user what you found and proceed with what's available.

**Source quality hierarchy** (prefer higher):
1. Original research, official docs, peer-reviewed papers
2. Reports from recognized institutions (McKinsey, Gartner, MIT, etc.)
3. Posts by domain experts on their own platforms
4. Reputable tech journalism (Ars Technica, The Verge, etc.)
5. Community content (blog posts, Medium, dev.to) — use only to supplement, not as sole sources

### 3. Determine the article type

Two filename conventions exist (see CLAUDE.md):

- **Evergreen content** (guides, frameworks, reference material updated over time) — plain slug, no date prefix: `content/articles/my-topic.md`
- **Dated content** (tied to a specific moment — news, reactions, event write-ups) — date-prefixed: `content/articles/YYYY-MM-DD-my-topic.md`

Default to **evergreen** (no date prefix) unless the topic is inherently time-bound. Ask the user if ambiguous.

### 4. Build the outline

Before writing, present a short outline to the user:

- Proposed title
- Proposed slug / filename
- TL;DR bullets (draft)
- Section headings with one-line descriptions
- Key sources found

Wait for user approval or adjustments before proceeding. If the user says to skip the outline step, proceed directly.

### 5. Write the article

Follow this structure:

```markdown
---
title: "<title>"
date: <YYYY-MM-DD>
draft: true
tags: ["tag1", "tag2", ...]
keywords: ["keyword1", "keyword2", ...]
---

## TL;DR

- **Key point 1** — one-sentence takeaway with the most important finding or recommendation
- **Key point 2** — another self-contained takeaway
- ...
- (3–7 bullets; each starts bold, uses an em dash, keeps to one or two lines)

---

<opening paragraph — establish the problem, question, or context in 2–4 sentences; no "In this article we will...">

---

## <Section 1>

<content>

## <Section 2>

<content>

...

## <Final section — practical takeaways, recommendations, or "where to go from here">

<optional resources/further reading list>

## References

1. [Source title](URL) — one-line description of what this source covers
2. [Source title](URL) — one-line description
3. ...
```

#### Writing rules

**Headings:**
- `##` for top-level sections (these appear in the Hugo table of contents)
- `###` for subsections
- Don't skip levels. No `####` unless `###` exists above it in the same section.

**TL;DR section:**
- Placed immediately after front matter, before any other content
- Header is exactly `## TL;DR`
- Each bullet starts with a **bolded key phrase** followed by an em dash and the takeaway
- Self-contained — a reader should be able to read only the TL;DR and walk away informed
- 3–7 bullets. If you can't distill the article into 3 bullets, the article may lack focus
- Followed by a horizontal rule (`---`)

**Paragraphs and prose:**
- 3–5 sentences per paragraph maximum
- One idea per paragraph
- Active voice. Direct address where natural ("you" over "one" or "the reader")
- Technical terms in bold on first use, then plain text after

**Lists:**
- Use bullet lists for unordered items, numbered lists for sequences
- Keep list items parallel in structure (all sentences, all fragments, all start with a verb)
- Tables for structured comparisons (features, tools, options)

**Quotes and callouts:**
- Use `>` blockquotes for direct quotes — always attribute
- Use `> **Warning:**` or `> **Note:**` for callouts (sparingly)

**Sources and references:**
- Link inline at the point where the claim is made: `[source name](URL)`
- For statistics: cite the specific report and year — e.g., "McKinsey's 2025 State of AI survey found..."
- For quotes: attribute to a named person with context — e.g., 'As Ethan Mollick [frames it](URL): "..."'
- If multiple sources support one section, a "Sources:" footer at section end or article end is acceptable in addition to inline links
- Never use bare URLs in prose

**References section (required):**
- Every article must end with a `## References` section as the last section before any closing remarks
- Format: ordered list (`1.`, `2.`, ...) of all sources used in the article or during research
- Each entry: `[Source title](URL) — short description` (one line, under ~120 characters for the description)
- Include every source linked inline in the article body, plus any additional sources consulted during research that informed the content
- Order by first appearance in the article
- Do not duplicate the References section with a separate "Sources" block — References replaces any freestanding source lists

**Code blocks:**
- Use fenced code blocks with language identifiers (```bash, ```yaml, etc.)
- Only include code that the reader would actually run or reference

**Front matter:**
- `title`: descriptive, concise — no clickbait, no ALL CAPS
- `date`: today's date or user-specified date
- `draft: true` — always start as draft; the user flips to `false` when ready
- `tags`: 3–6 lowercase tags; see CLAUDE.md for the tags blacklist
- `keywords`: same as tags plus long-tail search terms (6–10 total)
- Never include blacklisted tags (see CLAUDE.md)

### 6. Self-review checklist

Before presenting the article, verify:

- [ ] TL;DR exists and each bullet is self-contained
- [ ] Every major factual claim has an inline source link
- [ ] No paragraph exceeds 5 sentences
- [ ] Headings form a scannable outline on their own
- [ ] No filler phrases ("It's important to note...", "In conclusion...", "As we all know...")
- [ ] No PII, employer references, or secrets (per CLAUDE.md public-site rules)
- [ ] Tags do not include any blacklisted terms
- [ ] Front matter has all required fields
- [ ] The file is placed in the correct directory with the correct naming convention
- [ ] Sources are from credible, verifiable origins
- [ ] A `## References` section exists at the end with an ordered list of all sources used

### 7. Write the file and confirm

Write the file to `content/articles/<slug>.md` (or `content/articles/<YYYY-MM-DD>-<slug>.md` for dated content).

Report to the user:
- File path
- Title
- Number of sources cited
- Tags assigned
- That `draft: true` is set — remind them to flip it when ready to publish

## Notes

- If the user provides a URL as the basis for the article, fetch and read it first — but write original content, don't copy-paste.
- If research turns up conflicting information, present both sides with sources rather than picking one.
- Prefer linking to free/open resources over paywalled content. If a paywalled source is the best one, note it.
- If `content/articles/` doesn't exist, create it along with a `_index.md`.
- Article length is not prescribed — write as much as the topic demands, no more. A 500-word article on a focused topic is better than a 2000-word article padded to hit a word count.
