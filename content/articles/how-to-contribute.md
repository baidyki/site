---
title: "How to Contribute"
date: 2026-04-15
draft: false
tags: ["baidyki", "contributing"]
---

# How to contribute

## TL;DR

- **Hugo extended edition required** — install it via Homebrew, Snap, or direct download, then run `hugo server -D` to preview locally
- **No PII, no employer references, no secrets** — the repo is public, treat every commit as published to the internet
- **Claude Code is recommended** — the project ships with skills and docs that automate naming conventions, front matter, and content drafting
- **Submit via pull request** — branch, commit, push, and open a PR at the repository

---

This is a public, open-source site. Anyone is welcome to propose posts, articles, and improvements. This guide walks you through setting up a local environment, previewing changes, and submitting them back.

## Ground rules before you start

This repository is **public**. Everything you commit becomes part of the public record on GitHub and, once deployed, on the live site.

- **No PII.** Do not include personal names, email addresses, phone numbers, home addresses, or any other personally identifiable information — yours or anyone else's.
- **No employer references.** Do not mention the company you work for, internal projects, internal tools, clients, or anything that could be traced back to your employer. If you want to write about something you learned at work, generalize it until it could apply to any team.
- **No secrets.** No API keys, tokens, credentials, or private URLs. Treat every commit as if it were published to the front page of the internet, because effectively it is.

If you are unsure whether something crosses a line, leave it out or ask in a pull request discussion before merging.

## 1. Install Hugo

This site is built with [Hugo](https://gohugo.io/), a static site generator written in Go. You need the **extended** version.

### macOS

Using Homebrew (recommended):

```bash
brew install hugo
```

Verify:

```bash
hugo version
```

You should see something like `hugo v0.xxx.x+extended darwin/...`. The `extended` suffix matters — the regular build will not work with some theme features.

### Windows

Option A — using [winget](https://learn.microsoft.com/en-us/windows/package-manager/winget/) (recommended, ships with Windows 10/11):

```powershell
winget install Hugo.Hugo.Extended
```

Option B — using [Scoop](https://scoop.sh/):

```powershell
scoop install hugo-extended
```

Option C — using [Chocolatey](https://chocolatey.org/):

```powershell
choco install hugo-extended
```

Option D — manual install:

1. Download the latest `hugo_extended_*_windows-amd64.zip` from the [Hugo releases page](https://github.com/gohugoio/hugo/releases).
2. Extract `hugo.exe` into a folder, for example `C:\Hugo\bin`.
3. Add that folder to your `PATH` (System Properties → Environment Variables → Path → Edit → New).
4. Open a new terminal and run `hugo version` to verify.

### Linux

On Debian / Ubuntu the packaged version is often outdated. Prefer Snap or a direct download.

Using Snap:

```bash
sudo snap install hugo
```

Using Homebrew on Linux:

```bash
brew install hugo
```

Manual install (works on any distro):

```bash
curl -LO https://github.com/gohugoio/hugo/releases/latest/download/hugo_extended_Linux-64bit.tar.gz
tar -xzf hugo_extended_Linux-64bit.tar.gz
sudo mv hugo /usr/local/bin/
hugo version
```

Arch Linux:

```bash
sudo pacman -S hugo
```

## 2. Install Git

You need Git to clone the repository and push changes.

- **macOS:** `brew install git`, or install [Xcode Command Line Tools](https://developer.apple.com/xcode/).
- **Windows:** download [Git for Windows](https://git-scm.com/download/win).
- **Linux:** `sudo apt install git` (Debian/Ubuntu), `sudo pacman -S git` (Arch), `sudo dnf install git` (Fedora).

## 3. Clone the repository

```bash
git clone https://github.com/baidyki/site.git
cd site
```

If the repository uses submodules for the theme, pull them too:

```bash
git submodule update --init --recursive
```

## 4. Preview the site locally

From the repository root:

```bash
hugo server -D
```

- `-D` includes draft posts so you can preview work-in-progress.
- Open [http://localhost:1313/](http://localhost:1313/) in your browser.
- The server watches files and reloads the page automatically when you save.

Stop the server with `Ctrl+C`.

## 5. Add a new post

Posts live under `content/posts/`. Articles (long-form) live under `content/articles/`.

**Naming convention:** every file must be prefixed with its publication date in `YYYY-MM-DD-` format, followed by a slug. For example:

```
content/posts/2026-04-15-my-new-post.md
content/articles/2026-04-15-deep-dive-on-something.md
```

The fastest way is to use Hugo's archetype:

```bash
hugo new posts/2026-04-15-my-new-post.md
```

Then edit the generated file. A minimal front matter looks like:

```yaml
---
title: "My New Post"
date: 2026-04-15
draft: false
tags: ["claude"]
---

Your content here.
```

Keep `draft: true` while you are working, and flip it to `false` when you are ready to publish.

## 6. Preview your changes

With `hugo server -D` running, save the file and the browser will reload. Check:

- The post appears on the section index page.
- Tags and titles render correctly.
- Internal links work.
- Images load.
- Nothing on the page reveals PII or employer-identifying information.

## 7. Commit and push

```bash
git checkout -b my-change
git add content/posts/2026-04-15-my-new-post.md
git commit -m "Add post on <topic>"
git push -u origin my-change
```

Then open a pull request at [https://github.com/baidyki/site](https://github.com/baidyki/site).

## 8. Use Claude Code

This project ships with a set of documentation, skills, and agent configurations tailored for [Claude Code](https://claude.com/claude-code). If you are contributing regularly, using Claude Code will make your life significantly easier — it already knows the naming conventions, the front-matter style, where files belong, and which skills to invoke.

Examples of what the project-local setup helps with:

- Drafting posts from a YouTube URL via the `youtube-post` skill.
- Enforcing the `YYYY-MM-DD-slug.md` filename rule.
- Writing commit messages and changelog entries in the house style.
- Reviewing pull requests before you open them.

Install Claude Code, open this repository in it, and the project's `CLAUDE.md` and skill definitions are picked up automatically. New skills and docs will be added over time — check `CLAUDE.md` and the `.claude/` directory for the current set.

You do not have to use Claude Code to contribute, but if you are going to spend any meaningful time on the site, it is strongly recommended.

## Resetting Hugo caches

Hugo caches modules, resources, and build artefacts. If you see stale content, broken assets, or unexplained build errors after switching branches or updating the theme, clear the caches:

```bash
hugo mod clean          # remove the module cache
rm -rf resources/       # remove processed images / SCSS artefacts
rm -rf public/          # remove the previous build output
```

Then restart the dev server:

```bash
hugo server -D
```

On rare occasions the OS-level cache directory also matters. You can nuke everything Hugo stores outside the repo with:

```bash
hugo mod clean --all
```

This removes all cached modules for every Hugo project on the machine, so use it only when the per-project clean is not enough.

## Getting help

Open an issue on [the repository](https://github.com/baidyki/site/issues) if something is unclear, broken, or missing from this guide. Improvements to this page are welcome too — it lives at `content/articles/how-to-contribute.md`.

## References

1. [Hugo](https://gohugo.io/) — static site generator used to build this site
2. [Hugo releases](https://github.com/gohugoio/hugo/releases) — download page for Hugo extended edition
3. [Git for Windows](https://git-scm.com/download/win) — official Git installer for Windows
4. [Claude Code](https://claude.com/claude-code) — Anthropic's CLI tool with project-aware skills used by this site
