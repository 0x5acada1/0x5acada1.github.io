---
layout: post
title: "Hash crack"
published : true
tags: CTF Common cheatsheet linux hash "hash crack" hashcat "john the ripper"
---
## hashcat
### 基本コマンド
```sh
hashcat -a 3 -m 3200 hash2.txt wordlist.txt
```

### hashcat ruleを使ってwordlistを作る。
- keyword.txtには、wordlistのベースとなる単語が入っている。
```sh
hashcat --force keyword.txt -r /usr/share/hashcat/rules/best64.rule --stdout > wordlist.txt
```
