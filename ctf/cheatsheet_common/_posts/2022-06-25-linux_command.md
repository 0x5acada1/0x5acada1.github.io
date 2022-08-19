---
layout: post
title: "linux_command"
published : true
permalink: /ctf/cheatsheets/common/linux_command
tags: [CTF, Common, cheatsheet, linux]
---
## ヒアドキュメントでスクリプトを書く
```sh
cat << EOF > test.sh
#!/bin/sh
id
EOF
```
