---
layout: post
title: Worrying trends in the modern IT pipeline
subtitle: It's not about if. It's when...
excerpt_image: /assets/images/excerpts/canvassupplychain_articlemay.png
tags: Canvas CrowdStrike infrastructure supplyChainAttack education supplyChain verification
categories: news
---

It seems that every day now we hear about a cyber attack or
some form of breach in the news. It is a consistent cycle of bad news.

## The Modern Landscape

It is estimated that 74% [^1] of the world's population is using the internet, that's 6 billion people. Just six years ago,
it was 60% [^1] and a decade before that 28% [^1] . There are more people using the internet today than possible unique IP 
addresses in the entire IPv4 address space.

[^1]: [Individuals using the Internet - ITU](https://www.itu.int/en/ITU-D/Statistics/pages/stat/default.aspx)

From wearable tech that tracks your heart rate to high-end network devices that can process 100+Gb/s worth of data, 
the world is full of technology and this technology is being pushed to its limits. The people who are in charge
of implementation of this technology bear a heavy responsibility to ensure that Confidentiality, Integrity and Availability
are all being kept as well as maintaining a functional business model.

## Know Thy Neighbour

You, unknowingly or knowingly, have connected to a free Wi-Fi point in your life. Who owns it? Who controls it? Who can
you point the finger to if something goes wrong? Often, you do not or cannot know. The same uncertainty that exists with 
a free Wi-Fi hotspot also exists with cloud platforms and hosted services: you rely on infrastructure you do not own and cannot fully audit.


## The underlying issue

A company can secure everything they own, apply patches and be cyber vigilant. However, are the people you are buying 
services from doing the same?

### Canvas (Owned by Instructure)

On May 7th 2026, Canvas had been taken down by a group known as [ShinyHunters](https://www.grip.security/blog/shinyhunters-why-so-shiny-and-who-are-they-really-hunting).
This was Instructure's second breach in the timespan of a week. The first breach, May 1st, resulted in names, email addresses, student ID numbers, and messages
between individuals on Canvas being compromised [^2] .  Steve Proud, Instructure's CISO, confidently issued that the threat had been contained with
remedial steps including the revocation of credentials, rotation of keys, and applying system patches [^2] . By May 6th, the final update had been
issued: "Canvas is fully operational".

This brings us back to May 7th, in which many students, teachers, and educational leaders were met with the exact same message: "SHINYHUNTERS rooting your systems since '19 ;)" [^3] .
This time around though, Canvas was made completely unavailable. An embarrassing sight to manage and ShinyHunters mockingly quoted Canvas that they 'did some "security patches".'.
The net result? 8000+ [^4] schools affected with 275 [^4] million users globally having personal information compromised. Trust for at least some organisations 
is now certainly broken.

[^2]: [Incident Page - Instructure](https://www.instructure.com/incident_update)

[^3]: [Canvas defacements - Malwarebytes](https://www.malwarebytes.com/blog/news/2026/05/shinyhunters-escalates-canvas-attacks-with-school-login-defacements)

[^4]: [Canvas Hack - CNN](https://edition.cnn.com/2026/05/07/us/canvas-hack-strands-college-students-finals-week)

--- 

### CrowdStrike

Not all incidents are external attacks though. On 19th July 2024, CrowdStrike released a faulty content update for its Falcon Sensor software that caused 
approximately 8.5 million Windows devices to crash [^5] with BSOD errors, many entering reboot loops. Although this figure represented less than 1% of all Windows 
machines globally [^5] , the use cases for where Falcon had been installed represented some of the most critical endpoints for businesses. 

[^5]: [Assistance with CrowdStrike outage - Microsoft](https://blogs.microsoft.com/blog/2024/07/20/helping-our-customers-through-the-crowdstrike-outage/)

CrowdStrike's response after the incident? Include a handful of extra testing safeguards and implement a staggered approach to software releases [^6] . CrowdStrike
also dealt with a lawsuit with Delta Air Lines. Delta had been hit the hardest with this and filed a lawsuit against CrowdStrike on the 25th October 2024 seeking 
$550 million in damages over lost revenue for over five days of disruptions [^7] . Although the actual outcome of this lawsuit is irrelevant, it highlights the
problems that can arise from overreliance on systems beyond our control.

[^6]: [Preliminary PIR - CrowdStrike](https://www.crowdstrike.com/en-us/blog/falcon-content-update-preliminary-post-incident-report/)

[^7]: [Delta lawsuit against CrowdStrike - Reuters](https://www.reuters.com/sustainability/boards-policy-regulation/delta-can-sue-crowdstrike-over-computer-outage-that-caused-7000-canceled-flights-2025-05-19/)

## What can we do?

In the modern age, it can be quite difficult to determine what will break where and when however, businesses and individuals alike can take steps
to reduce their chances of a cyber incident.

### For Individuals

Individuals can start by ensuring they set unique and complex passwords for each site they use, and should strongly consider using a password manager.
By using a unique password for each service, users significantly reduce the risk posed by credential stuffing attacks, where attackers take previously 
leaked username and password combinations and attempt to reuse them across multiple websites in the hope that you used the same credentials elsewhere.

As well as a uniquely strong password, individuals should enable two-factor authentication (2FA) on accounts where 2FA is possible. Individuals should also consider 
other forms of 2FA away from SMS. SMS is susceptible to SIM swapping and for people at risk from more advanced threats, SS7 network attacks. 
With this in consideration, it shows that not all forms of 2FA are equal. Better alternatives would be the use of an Authenticator app that issues 
TOTP (Time-based One Time Passwords) or a Hardware security key such as a Yubikey.

Individuals should keep a level of pragmatic skepticism. Is the person you are talking to at the end of the phone really who they say they are? Vishing (Phone phishing)
and phishing in general is still a viable attack vector to gain a high level of access into an organisation. There are many key signs to look out for with any
attempt at a phishing. Some key questions to ask yourself include: 

1. Is there a sense of urgency? Will something negative happen to you if you do not act now or in a short timeframe such as 24 hours?
2. Is this an authority figure? Sometimes bad actors will pretend to be someone important such as your doctor or your boss and attempt to trick you into doing something.
3. Is this scarce? By implying that the offering is in limited supply, bad actors may cause you to feel the need to respond quickly.
4. Is this something I am meant to receive? Sometimes bad actors will take advantage of current events to make their story seem more relevant and credible.
5. Is this playing with my emotions? Bad actors often will attempt to weaponise your emotions to manipulate your train of thought.

These bad actors can use more than one of these techniques too. Usually by chaining multiple together, it can provide a sense of urgent unease which is 
uncomfortable for everyone involved.

### For Businesses

Businesses should be aware of their entire supply-chain and should often review who they work with. Has a company had a turbulent history with
their Cyber Security? This is where businesses should review their risk directives and ensure that they are operating within their risk appetite.

Do the businesses in your ecosystem hold credible qualifications? Standards such as the ISO 27001 outline a good structure on how information
security management is handled. For UK-based businesses, [Cyber Essentials](https://www.ncsc.gov.uk/cyberessentials/overview) should be the bare minimum 
with Cyber Essentials Plus being the go-to. Cyber Essentials Plus proves, through validation with a third party, that you are maintaining good level of information 
security management practice.

Employee education is also just as important. Whether an incident is an accidental mistake or a malicious act. Employees are already privileged to 
the internals of a network making them a prime target for bad actors to attempt to engage with. Employees should often be reminded to stay vigilant
and to speak up if something suspicious is to occur. 

Businesses should enforce strong cyber hygiene through clear, well-defined IT and security policies that are consistently applied in practice.
Shadow IT and BYOD/COPE devices should also be watched closely. It is very easy for an internal network to get bogged down with technical debt that
employees begin to turn over to using their own unvetted devices on a network. This leads to an entirely new (nearly invisible) attack surface.

## Conclusion

As we move constantly forward with even more devices being connected to the internet. Big Data is not slowing down, and our reliance on infrastructure 
we do not control is increasing at the same pace. We must make sure we are aware of who we are working with, why we chose to work with them and how they 
operate. Both individuals and organisations can take steps to prove their own credibility through small changes in personal security through to 
standards implementation. Everyone is responsible to make a secure future.
