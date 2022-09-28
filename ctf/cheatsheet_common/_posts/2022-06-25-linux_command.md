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

## 更新されるファイルを自動的に出力する
```sh
tail -f /var/log/apache2/access.log
```