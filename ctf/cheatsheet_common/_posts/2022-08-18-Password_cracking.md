---
layout: post
title: "Password cracking"
published : true
permalink: /ctf/cheatsheets/common/password_cracking
tags: [CTF, Common, cheatsheet, password, Password cracking, hash, hash cracking, cewl, hashcat, john]
---

## カスタムワードリストの作成
```sh
cewl http://10.10.10.191 > wordlist
```
## Hash cracking
### Online tools
- [CrackStation](https://crackstation.net/)

### Hash identifer
- hashid
- haiti
- ...

### hashcat
### 基本コマンド
```sh
hashcat -a 3 -m 3200 hash2.txt wordlist.txt
```

### hashcat ruleを使ってwordlistを作る。
- keyword.txtには、wordlistのベースとなる単語が入っている。
```sh
hashcat --force keyword.txt -r /usr/share/hashcat/rules/best64.rule --stdout > wordlist.txt
```

### John The Ripper
#### 辞書
```sh
WL_PW=/usr/share/wordlists/rockyou.txt
john --wordlist=$WL_PW hash.
```
#### ブルートフォース
```sh
john --incremental=ASCII hash.txt
```