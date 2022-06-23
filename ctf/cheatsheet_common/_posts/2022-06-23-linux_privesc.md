---
layout: post
title: Linuxの特権昇格
published : true
tags: CTF Common cheatsheet linux 特権昇格 Privesc "privilage escaration"
---
## 列挙
### SUID
```sh
find / -perm -4000 2>/dev/null
```

### sudo
```sh
sudo -l
```

### プロセス
```sh
ps awxww
```

### サービス
```sh
ss -antul
```

### apache2 config
```
/etc/apache2/sites-enabled
```

## SSH port forwerding
```sh
ssh daniel@panda.htb -L 9999:localhost:80
```