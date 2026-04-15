---
name: youtube-post
description: Create a Hugo blog post under content/posts/ from a YouTube URL. Use when the user asks to add/publish a blog post about a YouTube video. The post embeds the video via Hugo's built-in {{< youtube >}} shortcode and takes its title/author from YouTube metadata.
---

# YouTube Post Publishing

Create a Hugo blog post for a given YouTube URL. Post body is **only** the embedded video — no prose, no summary.

## Inputs

- A YouTube URL in any of these forms:
  - `https://youtu.be/VIDEO_ID`
  - `https://www.youtube.com/watch?v=VIDEO_ID`
  - `https://www.youtube.com/shorts/VIDEO_ID`
  - `https://www.youtube.com/embed/VIDEO_ID`

## Steps

### 1. Extract the 11-character video ID

Parse the URL. The video ID is the 11-char token after `v=`, `youtu.be/`, `shorts/`, or `embed/`. Strip any `&`/`?` query string.

### 2. Fetch metadata via YouTube oEmbed

Use `WebFetch` with this exact URL pattern (no auth required, returns clean JSON):

```
https://www.youtube.com/oembed?url=https://www.youtube.com/watch?v=VIDEO_ID&format=json
```

Prompt the fetch to return the raw JSON. Extract:
- `title` → post title
- `author_name` → post author

**Do not** use `youtu.be/...` with WebFetch — it 303-redirects and fails. Always use the canonical `youtube.com/watch?v=...` form.

**Do not** try to scrape keywords from the HTML page — meta tags are not reliably returned. Derive keywords from the title + topic instead, and ask the user if they want specific tags.

### 2.5. Resolve the post's two dates

Each post carries two dates in front-matter:

- `date` — **blog publishing date**. Governs the filename prefix, sorting, and the date hugo-book displays. Set from user input.
- `originalDate` — **original publication date of the source** (YouTube upload date). Informational only; rendered as "Originally published: ..." below the main date.

Always try to fetch the video's upload date up front so both fields can be filled:

- `WebFetch` the canonical watch URL (`https://www.youtube.com/watch?v=VIDEO_ID`) and extract `uploadDate` or `datePublished` from the JSON-LD / `<meta itemprop="datePublished">` on the page.

Then resolve `date` (the blog publishing date):

- **If the user explicitly specified a date** in the original request (e.g., "on Apr 1st", "date=2026-04-01", "publish as of yesterday"): use that date for `date` without asking.
- **Otherwise**: you **must** stop and ask the user. Present both candidates — today's date vs. the video's upload date — and accept any other date the user types in response. If the upload date couldn't be fetched, still ask, stating that only today's date is known.

Fill `originalDate`:

- If the upload date was fetched, use it.
- If it couldn't be fetched, **omit the `originalDate` field entirely** from the front-matter. The template renders "Originally published: …" whenever `originalDate` is present, so leaving it out avoids a redundant line.

Only after both dates are resolved, continue to the next step.

### 3. Slugify the title

Lowercase, replace non-alphanumeric runs with `-`, trim leading/trailing `-`. Strip punctuation like apostrophes and commas. Example:
`"Claude Routines Just Dropped, And It's Perfect"` → `claude-routines-just-dropped`

Keep slugs short (~5 words max) — drop filler words when the title is long.

### 4. Derive keywords/tags

From the title, extract topical nouns. Always include the author's name and any obvious product/brand (e.g., `claude`, `anthropic`, `gpt`, `openai`). Keep it to 4–6 tags.

### 5. Write the post file

Path: `content/posts/<YYYY-MM-DD>-<slug>.md` — filename **must** start with the post date resolved in step 2.5, in `YYYY-MM-DD-` format, followed by the slug (e.g., `2026-04-15-claude-routines-just-dropped.md`). The filename date and the `date:` front-matter field must match.

Content (exact structure — no body beyond the shortcode):

```markdown
---
title: "<title from oembed>"
date: <blog publishing date in YYYY-MM-DD>
originalDate: <video upload date in YYYY-MM-DD — OMIT this line entirely if upload date was not fetched>
author: "<author_name from oembed>"
language: <ISO 639-1 code>
keywords: ["tag1", "tag2", ...]
tags: ["tag1", "tag2", ...]
---

{{< youtube VIDEO_ID >}}
```

The `language` field is **required**. Use the ISO 639-1 two-letter code (`en`, `ru`, `es`, `de`, `fr`, ...) inferred from the oEmbed title. If the language is ambiguous or you cannot confidently detect it, ask the user rather than guessing.

### 6. Confirm to the user

Report: the file path, the title, the author, and the derived keywords. Note if keywords were derived (not scraped) so the user can override them.

## Notes

- The `youtube` shortcode is built into Hugo — no theme needed.
- If the oEmbed call fails (private/deleted video, region block), stop and tell the user; don't fabricate metadata.
- If `content/posts/` doesn't exist, create it along with a `_index.md`.
- Front-matter uses TOML-compatible YAML that Hugo accepts regardless of `hugo.toml` format.
