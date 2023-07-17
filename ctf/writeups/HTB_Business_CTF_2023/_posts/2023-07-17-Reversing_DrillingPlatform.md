---
layout: post
title: "[Reversing] DrillingPlatform (300 pts, 575 solved)"
published : true
permalink: /ctf/writeups/HTB_Business_CTF_2023/Reversing_DrillingPlatform
tags: [CTF, WriteUps, HTB Business CTF]
---
## Description
Welcome to the latest and greatest in Vitralium mining technology. Our newest rig can penetrate depths never before reached! Unfortunately, we haven't yet been able to locate any. Can you get into the workings of the machine and find out where we need to drill?

## Files
- rev_drillingplatform.zip

## Solution
zipファイルを解凍すると"platform"が現れる。

```sh
┌──(kali㉿kali)-[/ctf/…/HTB Business CTF 2023/reverse/DrillingPlatform/rev_drillingplatform]
└─$ file platform
platform: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=8cf74effd156c4e05a51cd6a4420b69830fdd8dd, for GNU/Linux 3.2.0, not stripped
```

stringsでフラグを得た。

```sh
┌──(kali㉿kali)-[/ctf/…/HTB Business CTF 2023/reverse/DrillingPlatform/rev_drillingplatform]
└─$ strings platform | grep HTB
HTB{lucky_gu3ss_0r_s0m3th1ng_m0r3??}
```

Flag: `HTB{lucky_gu3ss_0r_s0m3th1ng_m0r3??}`