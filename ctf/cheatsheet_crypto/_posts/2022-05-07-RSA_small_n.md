---
layout: post
title: "RSA small N"
published : true
permalink: /ctf/cheatsheets/crypto/rsa_small_n"
tags: [CTF, crypto, cheatsheet, RSA]
---
小さいNが与えられたとき、素因数分解しp,qを得ることを試みる。

[http://factordb.com/](http://factordb.com/){:target="_blank"}

例:

n = 769457290801263793712740792519696786147248001937382943813345728685422050738403253

のとき、

p = 1617549722683965197900599011412144490161

q = 475693130177488446807040098678772442581573

に分解できる。  

c,eが与えられていれば、以下のようにしてmを求められる。  

```python
from Crypto.Util.number import long_to_bytes
from Crypto.Util.number import inverse

#[Gived value]
c = 8533139361076999596208540806559574687666062896040360148742851107661304651861689
n = 769457290801263793712740792519696786147248001937382943813345728685422050738403253
e = 65537
# Calculate with factordb.com
p = 1617549722683965197900599011412144490161
q = 475693130177488446807040098678772442581573

phi = (p-1)*(q-1)
d = inverse(e, phi)
m = pow(c, d, n)
print(long_to_bytes(m))
```
