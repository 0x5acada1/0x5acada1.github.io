---
layout: post
title: "Chrome remote debug"
published : true
permalink: /ctf/cheatsheets/common/chrome_remote_debug
tags: [CTF, Common, cheatsheet]
---

# 前提条件
Remote box でChromeのリモートデバッガポートが解放されていること。
```
google-chrome --remote-debugging-port=12345
```

#　Exploitation
- ローカルにポートフォワードする。
- Local boxのChromeで、
```
chrome://inspect/#devices
```

- Configure ボタンでローカルのポートを指定
```
localhost:12345
```

