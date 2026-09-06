---
title: "Building a Personal Data Pipeline"
date: 2026-09-06
categories: [Automation]
tags: [AI, Obsidian, Data Pipeline, PKM, Personal Knowledge Management]
---

A small note before we start: this post is longer than my usual posts.

Most of my previous posts are shorter and more focused. This one is different because it goes deeper into the full process: the problem, the architecture, the tradeoffs, the mistakes, and the direction this system is heading.

If you are interested in AI, personal knowledge systems, automation, or just the problem of saving too much information and never using it again, I think it is worth the read.

<details>
  <summary>## TL;DR</summary>
  <p>I am building a personal data pipeline for turning saved content into structured, searchable, reusable knowledge.

The system takes sources like YouTube videos, Instagram reels, carousel posts, articles, and course lessons, creates raw transcripts, processes them with a custom summary skill, stores them in Obsidian with consistent metadata, and exposes them through dashboards, queries, and eventually direct AI access.

The goal is not to save more information.

The goal is to make saved information useful again.

Long term, I would like to package and share this process so others can install something similar for themselves. Right now, that is not straightforward because parts of the setup depend on local machines, my Obsidian environment, and a VPS-based infrastructure.
</p>
</details>



Most people do not need another place to save information.

They already have too many.

A WhatsApp group with themselves. Instagram saved collections. Facebook saved posts. YouTube Watch Later. Browser bookmarks. Screenshots. Notes apps. Random documents. Maybe even an Obsidian vault, a Notion dashboard, or something they call a second brain.

But the uncomfortable truth is simple:

Most of that information is never used again.

It is saved, but not processed.  
Stored, but not understood.  
Collected, but not connected.

And this is where many second brain setups fail in practice.

They can look impressive. Dashboards, folders, tags, graph views, icons, templates, carefully designed home pages. But the real question is not whether the information was saved.

The real question is:

> Can this information come back later in a useful form, connected to the things I care about, and help me make a better decision, understand something faster, or generate a new idea?

That is the gap I wanted to solve.

Not another place to dump information.

A pipeline that turns personal data into something I can actually use.

[IMAGE: Main architecture diagram showing social/web sources, Auto-Summary, Obsidian, Hermes, Syncthing, and Quick Draft]

---

## Collection vs Pipeline

Saving information is easy.

Building a useful lifecycle for that information is the hard part.

A collection usually looks like this:

```text
find something interesting
  -> save it somewhere
    -> forget about it
```

A pipeline should look more like this:
```text
capture
  -> extract
    -> preserve raw data
      -> process
        -> structure
          -> connect
            -> query
              -> reuse
```

