---
title: "QGIS Developer Explorer: who has 100 merged pull requests?"
date: 2026-10-14
description: "An interactive explorer of every pull request to qgis/QGIS, built at the QGIS Contributor Meeting 2026 in Laax to answer a question raised by a new QEP: how many developers have at least 100 merged PRs?"
tags: ["QGIS", "Open Source", "Community", "Data Visualization", "GitHub", "AI", "Side Project"]
category: "Development"
draft: false
toc: true
ogImage: "/media/qgis-developer-explorer-merged.jpg"
---

A few days after [QGIS Lists Explorer](/blog/introducing-qgis-lists-explorer), here is its sibling. Where the first one looks at twenty years of mailing lists, [**QGIS Developer Explorer**](https://ismailsunni.id/qgis-developer-explorer/) looks at every pull request to [qgis/QGIS](https://github.com/qgis/QGIS): who opens, merges, reviews and discusses them, how long merging takes, and how that has changed over the years.

- **Try it:** [ismailsunni.id/qgis-developer-explorer](https://ismailsunni.id/qgis-developer-explorer/)
- **Source:** [github.com/ismailsunni/qgis-developer-explorer](https://github.com/ismailsunni/qgis-developer-explorer)

I built it while attending the QGIS hackfest — the Contributor Meeting 2026 in Laax, Switzerland.

## The question: who counts as a "trusted contributor"?

QGIS currently has a strict AI tool policy ([QEP 408](https://github.com/qgis/QGIS-Enhancement-Proposals/blob/master/qep-408-ai-tool-policy.md)): no LLM, AI or agent-assisted code contributions are allowed in the qgis/QGIS repository.

During the contributor meeting, Martin Dobias and Denis Rouzaud proposed an exception in [QEP 429](https://github.com/qgis/QGIS-Enhancement-Proposals/pull/388). It defines **trusted contributors** as people with **at least 100 merged pull requests** in qgis/QGIS, and allows only them to use AI tools, provided they label such contributions, review all generated code, take full accountability for it, and link the full transcript of their AI session from the PR. The idea is to keep the PR queue free of low-effort AI submissions while letting experienced contributors use these tools.

The proposal has sparked a lively discussion — about copyright, the environmental and societal impact of LLMs, and whether the project should distinguish between contributors at all. I'm not going to take a side here. But reading the thread, I kept wondering about something simpler: **how many people would actually pass that bar?** So I built a tool to find out.

## What the numbers say

![Most active developers by merged PRs, and the distribution of PRs per author](/media/qgis-developer-explorer-merged.jpg)
*Most active developers by merged PRs (left) and how many PRs each author opened (right)*

The repository has **32,731 pull requests** since May 2011, of which **27,965 were merged**, from **859 authors**. Ignoring bots, which open a lot of backport PRs, here is how the 100-merged-PR threshold looks:

- **31 developers** have at least 100 merged PRs, out of 629 people who ever got a PR merged.
- Counting **only the last five years** (2022–2026), that drops to **16 developers**.
- Those 31 people account for **82% of all merged human PRs**.
- Five more people sit just below the line, with 80 to 99 merged PRs.

The table on the page has a **min. merged** slider, so you can move the threshold and see who is in and out — at 50 merged PRs the list grows to 51 people, at 500 it shrinks to 8.

![The developer table filtered to people with at least 100 merged PRs](/media/qgis-developer-explorer-table.jpg)
*The developer table with the threshold set to 100 merged PRs*

## Some interesting facts

**Nyall Dawson is in a league of his own.** I knew Nyall was productive, but the numbers are still staggering: **6,837 merged PRs**, about **30% of every merged human PR** in the repository, and more than the next four developers combined. He has also reviewed **9,378 PRs** and merged **15,442 PRs** opened by other people. His median PR is merged in about 17 hours. Thank you, Nyall — and everyone else in that table.

**Contribution is very lopsided.** Half of all PRs come from just 6 people (0.7% of authors), while 48% of authors opened exactly one PR. That is normal for open-source projects, but it's striking to see it drawn out.

**A PR count is not the whole story.** The threshold measures one specific kind of contribution. QGIS has been around since 2002, long before GitHub pull requests, and some core people did much of their work outside this workflow, through direct commits, infrastructure, releases or project leadership. Tim Sutton, involved since the earliest days of the project, has 45 merged PRs, for example. Documentation, translations, plugins, testing, and community work don't show up here at all.

**QGIS isn't alone in figuring this out.** Open-source projects are landing in very different places on AI contributions:

- [Gentoo and NetBSD](https://www.theregister.com/2024/05/18/distros_ai_code/) banned AI-generated code back in 2024, citing copyright, quality and ethical concerns.
- [LLVM](https://itsfoss.com/news/llvm-ai-policy/) adopted a "human in the loop" policy in January 2026: AI-assisted work is fine if the contributor has reviewed it and can defend it.
- [GCC](https://itsfoss.com/news/gcc-bans-ai-code/) decided in July 2026 to decline "legally significant" AI-made contributions (roughly 15+ lines), and requires an `Assisted-by:` tag on smaller AI-assisted changes.
- A [2026 study of 118 AI policies](https://arxiv.org/abs/2605.16706) across popular GitHub repositories found that 78% allow AI-assisted contributions and 22% discourage AI use.

As far as I know, a threshold based on a contributor's track record, like the one in QEP 429, is a less common approach.

## What else it shows

Beyond merged PRs, the page answers:

- **Activity over time** — PRs opened, merged and closed per month, authors and comments, with QGIS releases marked. Drag across it to filter everything else.
- **Most active developers** by PRs, merges, comments, reviews, or merges done for others.
- **Who works with whom** — a graph of the 80 most connected people, joined when one comments on or reviews another's PR.
- **Time to merge** per year, **how PRs end** (merged vs. closed), and **when PRs are opened** (weekday × hour, UTC).
- **Notable pull requests** — the most discussed, most reviewed, longest running and largest. The record holder is [#9648, "Symbol aware legend expression"](https://github.com/qgis/QGIS/pull/9648), with 122 comments and 190 reviews.
- **Newcomers and regulars** — is the contributor base renewing itself?

## How it works

A Python script mirrors every PR through the GitHub GraphQL API, and a second one aggregates them into a single JSON file. The page itself is static HTML/CSS/JS with hand-rolled SVG charts, no build step and no tracking — the only dependency is d3-force for the graph. A GitHub Action refreshes the data daily; after the initial backfill, an update only fetches PRs that changed, so it takes seconds.

A few definitions matter when reading the numbers:

- **Merged** counts the PRs a person *opened* that were eventually merged.
- **Reviewed** counts PRs reviewed, not review events, and reviews on your own PR are left out.
- **Bots** such as `qgis-bot` (which opens the backport PRs) are hidden by default, and every chart moves with that toggle.

## Try it

Have a look at [ismailsunni.id/qgis-developer-explorer](https://ismailsunni.id/qgis-developer-explorer/), drag the threshold slider, and find yourself in the table. If something looks wrong, [issues and PRs are welcome](https://github.com/ismailsunni/qgis-developer-explorer). And if you have an opinion on the AI policy itself, the place for it is the [QEP discussion](https://github.com/qgis/QGIS-Enhancement-Proposals/pull/388).
