---
title: "I Built a Home SIEM - Here's What Wazuh Taught Me"
date: 2026-04-09
categories: [Blue Team, Home Lab]
tags: [wazuh, siem, detection-engineering, homelab]
description: "How I deployed Wazuh 4.14.4 on a home VM, what broke along the way, and how a full disk turned into a working SIEM in a few hours."
---

There's something satisfying about running a SIEM at home.

Not because it's particularly practical - but because you start understanding things you take for granted at work. Why the indexer eats so much memory. What a decoder actually does before a rule fires. What it feels like to see an alert you wrote yourself trigger for the first time.

So I decided to build a lab. The tool: **Wazuh 4.14.4**. The environment: Ubuntu Server VM. And along the way - a few interesting failures.

---

## What Is Wazuh, In One Sentence

Wazuh is an open source SIEM + XDR platform. It ties together three components that run side by side:

- **Wazuh Manager** - receives events from agents, runs rules against them, generates alerts
- **Wazuh Indexer** - search engine based on OpenSearch (an Elasticsearch fork). This is where everything is stored
- **Wazuh Dashboard** - the visual interface, based on Kibana

On top of that, you deploy the **Wazuh Agent** - a lightweight process that runs on monitored endpoints and ships logs to the Manager.

---

## The Environment
<img width="1036" height="640" alt="image" src="https://github.com/user-attachments/assets/fad47b5d-c045-49be-8d7c-79652c5ae13e" />


```
Ubuntu Server VM (Bridged Network)
Disk: 30GB (LVM)
Wazuh: 4.14.4 - Single-Node All-in-One
```

Single-node means the Manager, Indexer, and Dashboard all run on the same machine. Not production-grade, but for a home lab - more than enough.
<img width="1405" height="116" alt="image" src="https://github.com/user-attachments/assets/b5f9d14d-a113-47a1-95f1-3b9fc7c6bb3a" />


---

## What Broke, and How I Fixed It

### The Disk Was Completely Full

The installation stalled mid-way. I ran `df -h` and saw this:

```bash
/dev/mapper/ubuntu--vg-ubuntu--lv   14G   13.9G   0   100% /
```

Completely full. But the VM was configured with 30GB. What happened?

**The reason:** Ubuntu's default installer doesn't expand the LVM to use the entire disk. It allocates a portion to the root LV and leaves the rest *free inside the Volume Group* - but unassigned to any LV. In practice: you have a 30GB disk, but root only sees 14GB.

**The fix, step by step:**

**Step 1 - buy some breathing room:**
```bash
apt-get clean
```
This freed ~800MB from the apt cache. Not a real fix, but enough to keep going.

**Step 2 - find the unallocated space:**
```bash
vgdisplay
```
`vgdisplay` shows information about the Volume Group. The relevant line:
```
Free PE / Size   3584 / 14.00 GiB
```
14GB free in the VG - sitting there, not assigned to anything.

**Step 3 - expand:**
```bash
lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
```

Two commands, two different layers:

- `lvextend` - this is LVM. It takes the 14GB free from the VG and adds them to the LV.
- `resize2fs` - this is not LVM, it's a filesystem-level tool (ext4). After the LV grows, the filesystem inside it still doesn't know there's more space available. `resize2fs` tells it to expand and fill the entire LV.

Result:
```bash
/dev/mapper/ubuntu--vg-ubuntu--lv   28G   13.1G   14G   47% /
```

Root is now at 47%, with 14GB free. Installation resumed.

---

## After Installation: Writing Rules

Once everything was up, the interesting part started - writing custom detection rules.

In Wazuh, every alert goes through two stages:
1. **Decoder** - parses the raw log into structured fields (user, ip, action, etc.)
2. **Rule** - evaluates those fields and decides whether to fire an alert

A basic rule that detects repeated SSH login failures looks like this:

```xml
<rule id="100100" level="7">
  <if_matched_sid>5716</if_matched_sid>
  <description>Multiple SSH authentication failures from same IP</description>
  <mitre>
    <id>T1110</id>
  </mitre>
</rule>
```

You test rules with `wazuh-logtest` - a tool that lets you feed a raw log line manually and see exactly which decoder and rule would match, without waiting for a real event to come in. Invaluable for iteration.

<img width="1918" height="679" alt="image" src="https://github.com/user-attachments/assets/c51ff48e-fdcb-493d-96f2-49ab3915fa2e" />

---

## What's Next

- **GitHub module** - Wazuh can monitor repository activity. Interesting use case for custom detection
- **Custom dashboards** - visualizing alerts by category and severity
- **Asset inventory** - using Wazuh as a lightweight CMDB for the lab

---

## Takeaway

If you work in security and haven't run a home SIEM - it's worth doing. Not because you're going to catch an APT on your laptop, but because you understand the tools on a completely different level when you're the one who built them.

The thing that surprised me most: how much broke along the way - and how much I learned from exactly that.

---

*Questions? Found something off? Hit me on [Twitter/X](https://x.com/cyterous) or [LinkedIn](https://linkedin.com/in/tomer-zamir).*
