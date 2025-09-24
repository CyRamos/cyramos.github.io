---
title: "PIoC: A Practical Fix for a Daily Pain"
date: 2025-09-24 19:40:00 +0300
categories: [Threat Intelligence]
tags: [IoC, Threat Intel, SOC, Automation]
---

On paper, working with multiple threat intelligence feeds / vendors sounds great. More visibility, more coverage, more data.
But in practice, it often turns into a daily headache.

If you have ever worked in threat intel or SOC investigation, you will be familiar with the drill. Feeds from different vendors keep coming, you have CSVs piling up, and suddenly, you have so many indicators that all look remotely the same.

Some have had their fangs removed, some have been renamed, and others are just duplicates. You are cleaning, validating, cross-checking instead of focusing on what's new.

I kept encountering a problem where different providers sent the same IoCs repeatedly.
You get one feed with 50 indicators in a CSV. Another feed comes in with 38 more, and at least a dozen are the same ones. When I played the role of an analyst, I would spend a considerable amount of time validating IoCs that I had most probably already looked at anyway. Meanwhile, my systems were busy processing duplicates. Repeated entries that on their own did nothing but add noise and slow everything down.

I eventually came to understand that this was not just something annoying but rather a structural issue of how threat intel gets consumed. I built a small tool to take care of the part that always bugged me.


## What PIoC Does
> PIoC (Pretty Indicators of Compromise) focuses on one thing:
> take messy, broken, redundant IoCs and make them clean, consistent, and usable.

📤 Upload a feed (CSV, JSON, STIX).

🔍 Extract the indicators and classify them (domains, IPs, hashes, emails).

🛡️ Refang them (hxxp → http, [.] → .).

🔗 Check against what’s already in the database.

💾 Store only what’s new.

📊 Show the difference so you can instantly see what just arrived.

🏥 Run basic health checks to avoid obvious false positives.

It doesn’t try to do everything. It just makes sure the starting point of the pipeline is clean, consistent, and less error-prone.


## Why the API Matters

At first, I wanted a GUI to quickly paste or upload feeds. But real workflows aren’t manual — they’re automated. 
That’s why PIoC also has a separate API service, which means it can sit at the start of a bigger process.

Receive a feed.
Detect indicator types.
Check if they already exist.
Normalize and refang.
Store clean results.
Validate against false positives (like Cloudflare — something I plan to strengthen further).
Send the findings to SIEM, XSOAR, or other relevant systems.

This way, PIoC doesn’t try to “be everything.” It’s just the first filter — the one that saves analysts from doing the same work twice.

## Looking Ahead
I’m planning to add a validation engine that goes deeper into separating truly malicious IoCs from legitimate infrastructure. Because no one wants to be the person who blocked a core CDN by accident.

## Closing Thoughts
PIoC wasn’t born as a product idea. It started as me being frustrated with wasted time and duplicate work.
Now it’s a small tool that helps keep threat intel feeds manageable, reduces errors, and frees up time for the parts of the job that actually require human judgment.

**Checkout yourself** : 

PIoC : <https://pioc.cyterous.com>

Github : <https://github.com/CyRamos/PIoC/>
