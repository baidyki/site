# Project

Hugo static site about AI, focused on Claude. Deployed via GitHub Pages. The repository is **public** — treat every commit as published to the internet.

## Public-site rules

Everything committed here becomes world-readable. Reject or strip:

- **PII** — names, emails, phone numbers, addresses (yours or anyone else's).
- **Employer references** — company names, internal projects, clients, internal tool names. If content was inspired by work, generalize it until it could apply to any team.
- **Secrets** — API keys, tokens, credentials, private URLs.

If something is borderline, leave it out and ask.

## Structure

```
hugo/
├── content/           ← all Markdown lives here
│   ├── posts/         ← dated blog entries
│   ├── articles/      ← long-form + evergreen docs
│   └── links/         ← curated external-resource pointers
├── themes/            ← theme (do not modify)
├── static/            ← images, downloadable files
├── layouts/           ← optional theme overrides
└── hugo.toml          ← site config
```

## Content naming

Two categories, two rules:

1. **Dated content** — blog posts under `content/posts/`, and any article tied to a specific moment (news write-up, video reaction, dated essay). Filename **must** be prefixed with publication date: `YYYY-MM-DD-slug.md`.
   - Example: `content/posts/2026-04-15-claude-routines-just-dropped.md`
2. **Evergreen content** — persistent reference material maintained in place over time (contributing guide, style docs, policy pages, section indexes). Filename is a **plain slug, no date prefix**.
   - Example: `content/articles/how-to-contribute.md`

When in doubt: will this file be updated continuously, or does it describe a moment? Continuous → no prefix. Moment → prefix.

## Front matter

Minimum fields for a post:

```yaml
---
title: "..."
date: YYYY-MM-DD
draft: false
tags: ["..."]
---
```

Keep `draft: true` while working; flip to `false` to publish. For video-backed posts, also include `originalDate`, `author`, `language`, `keywords`.

## Links

Curated pointers to outside resources live under `content/links/`. Evergreen — filenames are plain slugs, no date prefix. Rendered as a single JS-filterable list at `/links/` by [layouts/links/list.html](layouts/links/list.html); link files have **empty bodies** — all data lives in front matter.

### Link front matter

```yaml
---
title: "..."
link: "https://..."              # external URL — MUST be `link`, not `url` (Hugo reserves `url`)
source: "..."                    # optional display label (the publisher/author — do NOT repeat the category, e.g. use "Anthropic", not "YouTube · Anthropic")
category: "..."                  # single value, e.g. "YouTube"
language: "en"                   # ISO code; renders a flag via the site's flag dict
linktags: ["tag one", "tag two"] # array; see taxonomy + tag-style notes below
classifiers: ["general"]         # optional; zero or more of: general, technical, deep — see Classifiers below
rating: 0                        # integer 0–5
description: "..."               # one sentence; do NOT mention the language (the flag covers it)
---
```

### Classifiers

Orthogonal to tags/category — describe the *audience altitude* of the content. Stored under `classifiers`, rendered as emoji badges after the flag, and exposed as a single-select filter row. Multiple classifiers per link are allowed and combine (a post can be both technical and deep). Leave empty to opt out.

| Key         | Icon | Meaning                                                                 |
| ----------- | ---- | ----------------------------------------------------------------------- |
| `general`   | 🌍   | General knowledge, accessible to everyone interested in AI.             |
| `technical` | ⚙️   | Technical content, requires engineering background.                     |
| `deep`      | 🔬   | Deep/advanced content, covers internals or mechanics.                   |

Use only the keys above. New keys require a matching entry in the `$classifiers` dict in [layouts/links/list.html](layouts/links/list.html).

### Taxonomy separation

Link tags use a **separate `linktag` taxonomy** declared in `hugo.toml` so they don't intermix with the `tags` taxonomy that posts and articles use. The default `tag` taxonomy is retained explicitly in the same `[taxonomies]` block — keep it.

### Tag style

Link tags follow the global tag rules below. Keep existing values consistent when adding new links.

### Filter UI

Five dimensions — Tag, Category, Rating, Language, Classifier — each **single-select** (click a chip to set; click the same chip to clear). Dimensions AND together. Rating match is exact-star (5, 4, 3, …). Inline tag badges on each item double as tag-filter toggles. Classifier icons on items are display-only (tooltip on hover); use the Classifier filter row to filter.

## Tags

Rules below apply to **all** tags across the site — `tags` (posts/articles) and `linktags` (links) alike.

### Style

- **Lowercase only** — `"prompt engineering"`, not `"Prompt Engineering"`.
- **Spaces as-is** — no hyphens, underscores, or other substitutions (e.g. `"best practices"`, not `"best-practices"`).

### Allowed tag subjects

Tags should be **terms**, **significant events**, or **product/company names**. Examples: `"security"`, `"leak"`, `"claude"`, `"prompt engineering"`.

**No person tags** — do not tag individual people (e.g. ~~`"dario amodei"`~~). If authorship matters, use front-matter fields (`author`, `source`) instead.

### Blacklist

Never assign the following tags (case-insensitive) to any content, even if the source material suggests them. If existing content already carries a blacklisted tag, remove it.

- `AI` — the entire site is about AI; the tag adds no signal.

## Local preview

```bash
hugo server -D
```

`-D` includes drafts. Site serves at `http://localhost:1313/`. Saves auto-reload.

## Skills

The project ships Hugo-aware skills under `.claude/`. Notable:

- `youtube-post-writer` — drafts a post under `content/posts/` from a YouTube URL, using Hugo's built-in `{{< youtube >}}` shortcode and YouTube metadata.
- `article-writer` — writes a well-structured article under `content/articles/`. Researches sources, builds an outline, produces content with a TL;DR, inline citations, and clear heading hierarchy.

Prefer skills over hand-rolling when a matching one exists.

## Contributing guide

The canonical onboarding doc for humans is [content/articles/how-to-contribute.md](content/articles/how-to-contribute.md). When install steps, repo URLs, or the naming rules change, update that file too — it is the public-facing source of truth.
