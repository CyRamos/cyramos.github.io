---
titile: "One Telegram Message, One DDoS Group: What Can We Uncover?"
date: 2025-02-10 15:14:00
categories: 
    - Investigations
tags: 
    - DDOS
    - YARA
    - OSINT
    - CTI
---

# One Telegram Message, One DDoS Group: What Can We Uncover?

## What Can We Learn from Cybercrime Forum Posts?
When browsing through cybercrime forums or encrypted messaging platforms, have you ever stopped to really examine what’s being shared? These posts—often written in coded language or filled with technical jargon—can contain a goldmine of information for investigators and analysts. But what exactly makes them so interesting? And more importantly, what kind of details can we extract from them to push an investigation forward?
In this post, we’ll take a closer look at real examples and break down how to interpret them, identify key indicators, and leverage their contents for deeper cyber threat intelligence.

## What Can a Random Telegram Message Reveal About a DDoS Group?
At first glance, a single Telegram message from a cybercrime channel might seem insignificant—just noise in a flood of chatter. But what if that message belongs to DXPLOITMY, a group known for orchestrating DDoS attacks? Could one post hold the key to understanding their operations, tactics, or even uncovering their infrastructure?

![image](https://github.com/user-attachments/assets/96384174-7e60-44e8-916d-19181f0a518a)

In this post, we’ll break down an actual message shared in a Telegram channel linked to DXPLOITMY. We’ll explore what makes it interesting, what clues it offers about the group’s activity, and how even a brief post can spark a deeper investigation.

## DXPLOITMY’s Role in the OpIsrael Timeline
The post is taken from the `DXPLOITMY` Telegram channel.
We could notice the actions were mentioned as part of activities related to the OPIsrael event—mostly during April—and this specific attack was on 14 Jan 2025. (“OpIsrael is an annual coordinated cyber-attack where hacktivists attack Israeli government and even private websites with DDoS attacks and more.”)

## Verifying the Attack: Was the Website Really Down?
According to [check-host](https://check-host.net/check-report/223da56akbc5) report show the servers went down on Tue Jan 14 07:40:58 UTC 2025, the same date @DXPLOITMY posted there post. I do want to mention that although the threat group published on Telegram that the website was "can’t be reached", This doesn’t necessarily mean the attackers succeeded. In some cases, asset owners may detect the attack in advance and implement proactive blocking measures. These may include automated defenses or policy-based restrictions based on geographic location. As a result, the attacker may be denied access to the website and perceive it as offline, even though it remains operational. Second, The attackers targeted a subdomain, not the entire website.

![image](https://github.com/user-attachments/assets/323edfd2-013e-4df2-bd79-880808263c49){: .left }
_During the attack_

![image](https://github.com/user-attachments/assets/1f601797-aeb7-43fd-ab46-9a0de527a3c5){: .right }
_Original State_

## Beyond DDoS: Looking for Additional Clues
I checked the Wayback Machine for unusual changes around these dates for [https://education.histadrut.org.il/](https://education.histadrut.org.il/) to understand another activity except the DDOS without any findings. 

## Breaking Down the Hashtags: Who’s Involved?
### noname05716
#noname05716 
[NoName057(16)](https://malpedia.caad.fkie.fraunhofer.de/actor/noname057(16)) [aka 05716nnm, Nnm05716, NoName057, NoName05716) are pro-russian hacktivist group performing DDoS attacks on websites belonging to governments, armies etc in Ukraine and other countries supporting Ukraine. The group developed software named ‘[DDOSIA](https://www.radware.com/security/threat-advisories-and-attack-reports/project-ddosia-russias-answer-to-disbalancer/)’ conducting DDoS attacks.
Fortunately, we could track for `win_dosia_w0` with [YARA](https://malpedia.caad.fkie.fraunhofer.de/details/win.dosia): 

```yara [TLP:WHITE] win_dosia_w0 (20230615 | No description)
rule win_dosia_w0 {
    meta:
        author = "B42 Labs"
        date = "2023-04-13"
        hash_md5 = "ac0d5e1ec2664ad36db8877078bcf6c3"
        tlp = "CLEAR"
        yarahub_license = "CC0 1.0"
        yarahub_reference_md5 = "ac0d5e1ec2664ad36db8877078bcf6c3"
        yarahub_rule_matching_tlp = "CLEAR"
        yarahub_rule_sharing_tlp = "CLEAR"
        yarahub_uuid = "873ebbf5-9f83-4cf5-9670-b159211dd3c2"
        
        malpedia_reference = "https://malpedia.caad.fkie.fraunhofer.de/details/win.dosia"
        malpedia_version = "20230615"
        malpedia_license = "CC BY-NC-SA 4.0"
        malpedia_sharing = "TLP:WHITE"
        
    strings:
         $s_0 = "HttpJob" wide ascii
         $s_1 = "SayHallo" wide ascii
         $s_2 = "StartJob" wide ascii
         $s_3 = "FastRequest" wide ascii
         $s_4 = "SetStatToBot" wide ascii
         $s_5 = "GetTargets" wide ascii

    condition:
        filesize < 10MB  and (5 of ($s_*))
}
```
### RipperSec
#RipperSec - RipperSec is a pro-Palestinian, likely Malaysian hacktivist group created in June 2023, known for conducting DDoS attacks, data breaches, and defacements primarily targeting government and educational websites. The group developed a NodeJS DDOS machine layer 7 tool called ‘[MegaMedusa](https://www.radware.com/blog/security/megamedusa-rippersec-public-web-ddos-attack-tool/)’. ([source_1](https://malpedia.caad.fkie.fraunhofer.de/actor/rippersec) / [source_2](https://malpedia.caad.fkie.fraunhofer.de/details/js.mega_medusa))

### Overflame
Additionally I looked into Telegram for searching the other alliance’s groups. 
#Overflame - seems it belongs to Russian Legion Z  | RKN: No. 5047147078
Another hacktivist group known for executing DDoS attacks and website defacements, primarily targeting government institutions and corporations in Europe and North America.

![image](https://github.com/user-attachments/assets/74aad262-c7d5-49c6-a702-0ab4f28a23e1){: .left }
![image](https://github.com/user-attachments/assets/438c77b3-1b82-4ba1-bd46-29d4b673a6a1){: .right }


## Expanding the Search: What Else Is Out There?
Finally, all the above, I searched for some of the groups (mostly DXPLOITMY) on several other platforms such as [defacer.net](http://defacer.net), [OTX Alienvault](https://otx.alienvault.com/), [Threatcrowd](http://ci-www.threatcrowd.org/) without any findings except this Medium report ‘[Emerging Hacktivist Group: DXPLOIT](Emerging Hacktivist Group: DXPLOIT)’.

Tracking hacktivist activity through Telegram and open-source tools offers valuable insight.
When monitoring DDoS campaigns:
- Cross threat actors claims with infrastructure status (e.g., Check-Host, uptime monitors).
- Map related groups and hashtags to understand potential alliances or tool reuse.
- Keep an eye on platforms like Telegram, OTX, and defacer archives.

Finally, even small posts can offer intelligence leads—sometimes a hashtag, sometimes a tool name. 
Knowing how to read between the lines makes all the difference. In our investigate we found YARA rule to detect one of `DXPLOITMY` DDOS tools that now we can monitor this activities. 
