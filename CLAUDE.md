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
│   └── articles/      ← long-form + evergreen docs
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

### Tags blacklist

Never assign the following tags (case-insensitive) to any content, even if the source material suggests them. If existing content already carries a blacklisted tag in any casing, remove it.

- `AI`

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
