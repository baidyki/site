---
name: post-writer
description: Create a laconic 1–3 paragraph blog post under content/posts/ from a URL or free-form text. Fetches and researches linked material, distills key points, adds reference links. Use when the user wants a short written post (not a video embed or a long-form article).
---

# Post Writer

Create a short, dense blog post for `content/posts/`. The output is a dated Markdown file — 1–3 paragraphs max, with reference links and key points. Think news brief, not essay.

## Guiding principles

1. **Brevity is the point.** 1–3 paragraphs. Every sentence carries information. No filler, no throat-clearing, no "In this post we explore…".
2. **Key points up front.** Lead with the most important takeaway. If a reader stops after the first sentence, they should still walk away informed.
3. **Always cite sources.** Inline links for every factual claim. A References section at the end collects all links used.
4. **Ask before guessing.** If the input is ambiguous — unclear topic, vague angle, missing context — stop and ask clarifying questions before writing.
5. **No marketing language.** Write like an engineer, not a press release. No superlatives ("groundbreaking", "revolutionary", "game-changing"), no hype ("exciting", "incredible", "unleash the power of"), no empty praise ("best-in-class", "world-class", "cutting-edge"). State what a thing does and let the reader decide if it's impressive. If a source uses marketing language, translate it to plain facts.

## Inputs

The user provides **one of**:

- **A URL** — a link to an article, blog post, announcement, discussion thread, or any web page.
- **Free-form text** — a topic description, a paste of content, bullet points, or rough notes.

Optionally:

- A specific angle or framing
- Target tags
- A preferred date (defaults to today)
- A preferred slug or title

## Steps

### 1. Clarify the request

Before any research, evaluate what the user gave you. Ask clarifying questions if:

- The **topic or angle is unclear** — "Write about MCP" could be an explainer, a news post, an opinion piece. Ask which.
- The **scope is too broad** — a URL pointing to a 50-page report needs a focus. Ask what aspect matters.
- The **audience isn't obvious** — technical deep-dive vs. general interest changes tone and detail level.
- **Key context is missing** — if you need to know why this matters to the site's readers, ask.

If the input is clear and specific, proceed without asking. Don't ask questions for the sake of asking — only when the answer would materially change the post.

### 2. Research the material

**If the input is a URL:**

1. `WebFetch` the URL to read its contents.
2. Identify the core claims, announcements, or findings.
3. Use `WebSearch` to find 1–3 additional related sources — supporting context, reactions, original announcements, or counterpoints.
4. `WebFetch` the most relevant additional sources.

**If the input is free-form text:**

1. Parse the key claims or topics from the text.
2. Use `WebSearch` to find 2–4 credible sources that support, contextualize, or expand on the topic.
3. `WebFetch` the most relevant results.

**Source quality** — prefer primary sources (official docs, announcements, research) over secondary coverage. If the input URL is itself secondary coverage, try to find the original.

### 3. Determine the dates

Each post carries up to two dates:

- `date` — **blog publishing date**. Governs filename prefix and sorting. Always **today's date** unless the user explicitly says otherwise.
- `originalDate` — **original publication date of the source material**. Informational only. Include it when the source has a clear publication date that differs from today. Omit it entirely if unknown or same as `date`.

No need to ask about dates — default `date` to today, and fill `originalDate` from the source if available.

### 4. Slugify the title

Lowercase, replace non-alphanumeric runs with `-`, trim leading/trailing `-`. Strip punctuation. Keep slugs short (~5 words max) — drop filler words when the title is long.

### 5. Write the post

Path: `content/posts/<YYYY-MM-DD>-<slug>.md`

Structure:

```markdown
---
title: "<concise, informative title>"
date: <YYYY-MM-DD>
originalDate: <YYYY-MM-DD — OMIT if unknown or same as date>
draft: false
tags: ["tag1", "tag2", ...]
---

<1–3 paragraphs of dense, informative prose>

Key points:

- **Point 1** — one-line takeaway
- **Point 2** — one-line takeaway
- **Point 3** — one-line takeaway

## References

1. [Source title](URL) — short description
2. [Source title](URL) — short description
```

#### Writing rules

**Prose:**
- 1–3 short paragraphs. Total length: 100–300 words of body text (excluding key points and references).
- Active voice. Direct. No hedging.
- Inline source links at the point of each claim: `[source](URL)`.
- First paragraph states what happened or what matters. Second paragraph adds context or implications. Third paragraph (if needed) covers reactions, caveats, or what's next.

**Key points:**
- 2–5 bullet points after the prose.
- Each starts with a **bolded phrase**, em dash, then a one-line takeaway.
- Self-contained — a reader can skim only these and get the post.

**References:**
- Ordered list of all URLs used (inline + research).
- Format: `[Title](URL) — one-line description`.

**Front matter:**
- `title`: descriptive, concise — no clickbait.
- `date`: post date from step 3.
- `draft: false` — publish immediately.
- `tags`: 3–6 lowercase tags per CLAUDE.md rules. No blacklisted tags.

### 6. Self-review checklist

Before presenting the post, verify:

- [ ] 1–3 paragraphs, no longer
- [ ] Every factual claim has an inline source link
- [ ] Key points section exists with 2–5 self-contained bullets
- [ ] References section lists all sources
- [ ] No filler phrases
- [ ] No marketing language — no superlatives, no hype, no empty praise
- [ ] No PII, employer references, or secrets (per CLAUDE.md public-site rules)
- [ ] Tags follow CLAUDE.md rules (lowercase, no blacklisted tags)
- [ ] Front matter has all required fields
- [ ] Filename is date-prefixed: `YYYY-MM-DD-slug.md`
- [ ] `draft: false` is set

### 7. Write the file and confirm

Write the file to `content/posts/<YYYY-MM-DD>-<slug>.md`.

Report to the user:
- File path
- Title
- Sources cited
- Tags assigned
- That the post is published (`draft: false`)

## Notes

- This skill is for **short posts**. If the user wants a long-form article, suggest the `article-writer` skill instead.
- If the user provides a YouTube URL, suggest the `youtube-post-writer` skill instead — it handles video embeds.
- If `content/posts/` doesn't exist, create it along with a `_index.md`.
- If research turns up conflicting information, note both sides briefly with sources.
- Prefer linking to free/open resources over paywalled content.
