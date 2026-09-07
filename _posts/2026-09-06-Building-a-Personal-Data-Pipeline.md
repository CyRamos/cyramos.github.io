---
title: "Building a Personal Data Pipeline"
date: 2026-09-06
categories: [Automation]
tags: [AI, Obsidian, Data Pipeline, PKM, Personal Knowledge Management]
---

A small note before we start: this post is longer than my usual posts.

Most of my previous posts are shorter and more focused. This one is different because it goes deeper into the full process: the problem, the architecture, the tradeoffs, the mistakes, and the direction this system is heading.

If you are interested in AI, personal knowledge systems, automation, or just the problem of saving too much information and never using it again, I think it is worth the read.


<blockquote class="prompt-info">
  <details>
    <summary><h3 style="display: inline; margin: 0; color: var(--prompt-info-text-color);">TL;DR</h3></summary>
    <div style="margin-top: 1rem;">
      <p>I am building a personal data pipeline for turning saved content into structured, searchable, reusable knowledge.</p>
      
      <p>The system takes sources like YouTube videos, Instagram reels, carousel posts, articles, and course lessons, creates raw transcripts, processes them with a custom summary skill, stores them in Obsidian with consistent metadata, and exposes them through dashboards, queries, and eventually direct AI access.</p>
      
      <p>The goal is not to save more information.</p>
      
      <p>The goal is to make saved information useful again.</p>
      
      <p>Long term, I would like to package and share this process so others can install something similar for themselves. Right now, that is not straightforward because parts of the setup depend on local machines, my Obsidian environment, and a VPS-based infrastructure.</p>
    </div>
  </details>
</blockquote>


Most people have too many places they save information.

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

<img width="4500" height="3840" alt="image" src="https://github.com/user-attachments/assets/8aa2ff39-1955-4f4e-9281-c09774fb2900" />


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
That lifecycle is the real difference.
A saved video is not knowledge.
A transcript is better, but still raw.
A summary is better, but still limited if it is isolated.
A structured, linked, queryable note can start becoming useful.

That is what I wanted to build.

---
## The Four Major Parts
The system has four major parts.

### 1. Intake and Processing

This is the layer that turns external content into structured notes.
It handles sources like YouTube videos, Instagram reels, carousel posts, articles, course lessons, and other web or social content.
The important part is that the system does not jump directly from source to summary.
First, it extracts the raw content and creates a full transcript note.
The transcript is the raw data layer: the closest version I have to what was actually said or shown in the source.
Only after that, I run a custom summary skill over the transcript.
That skill does more than summarize. It extracts:
- main concepts
- practical ideas
- tools mentioned
- step-by-step workflows, when the source contains them
- relevant connections to existing Obsidian notes
- bidirectional links between the summary, transcript, and related notes

The transcript is raw data.
The summary is interpretation.
I want both.

{[IMAGE: Screenshot of Auto-Summary UI or terminal/process view]
[IMAGE: Screenshot of transcript note in Obsidian]
[IMAGE: Screenshot of summary note linking back to transcript]}

---
### 2. Exploration and Correlation

Once the notes exist, I need a way to explore them.
This is where Obsidian, metadata, Dataview, Bases, Hearth dashboards, and MOCs come in.
The goal is not to create pretty dashboards for the sake of dashboards. The goal is to expose useful questions:

- What did I recently process?
- Which sources are still waiting for review?
- Which finance sources are useful for idea mining?
- Which tools appear across multiple sources?
- Which summaries are connected to transcripts?
- Which topics are starting to repeat?

Dashboards are useful only if they help me act.
If a dashboard only looks good but does not help me retrieve, compare, review, or generate ideas, it is decoration.

{[IMAGE: Home dashboard in Hearth]
[IMAGE: Research dashboard showing tools / summaries / idea-mining material]
[IMAGE: Learning HUB dashboard]}

---

### 3. Direct Access
The second way to access the data is through Hermes.
This is different from dashboards.
Dashboards are visual. Hermes is conversational.
The idea is that I can talk directly with my notes and ask questions over the data.
But there is an important safety boundary: Hermes does not get write access to the original vault.

The setup uses a one-way Syncthing mirror. Obsidian stays local and remains the source of truth. A local folder is projected toward the server side, where Hermes can read and analyze the notes, but it cannot write back into the live vault.

That gives me a safer way to ask questions over personal data without giving an external agent full control over the source.

This deserves its own post, because it touches permissions, sync direction, trust boundaries, and how much access an AI agent should have to personal data.
[IMAGE: Obsidian -> Syncthing -> Hermes read-only architecture]

---

### 4. Quick Capture
The fourth part is fast capture from mobile.
Sometimes I do not want to process anything yet. I just want to capture a thought, link, screenshot, or idea before it disappears.
For that, I use the Quick Draft widget.

This is the fast input layer.

The point is not that quick capture solves everything. It does not. Quick capture can easily become another dead inbox if nothing happens after capture.
The key is that quick capture feeds the same pipeline.

```text
phone
  -> Quick Draft
    -> inbox / staging
      -> processing
        -> metadata
          -> dashboards
            -> reuse
```

Capture is only useful if it has a path back into the system.
In the future, I want this inbox to become more active.

One option is to run a scheduled job that periodically scans quick captures, identifies what each item is, and turns them into proper notes with metadata and links.

Another option is to surface them inside a Hearth dashboard first, so I can review and approve what should happen next.

I have not fully decided which path is better yet. Full automation is tempting, but review-based processing may be safer for personal notes.
[IMAGE: Quick Draft widget / mobile capture flow]

---

## Auto-Summary
The first major implementation piece is Auto-Summary.
The flow is roughly:
```text
source URL
  -> Auto-Summary dashboard
    -> download or extract source
      -> send to transcription
        -> create raw transcript
          -> run custom summary skill
            -> create structured summary
              -> add metadata
                -> link transcript and summary
                  -> expose in dashboards
```

Right now, part of this flow is handled through an Auto-Summary dashboard, but I do not want the dashboard to be the only entry point.

The next step is to expose the pipeline through a webhook.
The idea is simple: I should be able to send a link from Telegram, or another quick input channel, and let the system handle the rest automatically: extract the source, generate the transcript, create the summary, add metadata, and place it in the right processing flow.

For video sources, the transcript is created from the actual source content. It becomes its own note, with metadata that marks it as a transcript.
```yaml
type:
  - transcript
source: youtube
url: https://example.com/video
```
Then the custom summary skill processes that raw transcript and creates a separate summary note.
Example:

```YAML
type:
  - summary
source: youtube
url: https://example.com/video
tags:
  - finance
  - idea_mining
```

This separation is important.

The raw transcript remains available.
The summary becomes readable and structured.
The metadata makes it queryable.
The links make it part of the graph.

A normal AI summary gives me an answer.

This gives me an artifact I can reuse.
