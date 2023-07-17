---
layout: post
title: "[Scada] Watch Tower (300 pts, x solved)"
published : true
permalink: /ctf/writeups/HTB_Business_CTF_2023/Scada_WatchTower
tags: [CTF, WriteUps, HTB Business CTF]
---
## Description
Our infrastructure monitoring system detected some abnormal behavior and initiated a network capture. We need to identify information the intruders collected and altered in the network.

## Files
- ics_watch_tower.zip

## Solution

zipファイルを解凍して出現した"tower_logs.pcapng"を見るとプロトコル"Modbus/TCP"の通信が確認できた。

"Modbus/TCP"について調べると、以下のサイトにレスポンスデータの抽出コードが記載されていた。

参考URL: https://hastaluegoblog.hatenablog.com/entry/2023/02/05/200337

それを参考にスクリプトを作成。

```python
#!/usr/bin/env python  
# -*- coding: utf-8 -*- 

# URL: https://hastaluegoblog.hatenablog.com/entry/2023/02/05/200337

import scapy.all as scapy 
import scapy.contrib.modbus as mb 
 
for pkt in scapy.PcapReader("tower_logs.pcapng"): 
    if mb.ModbusADUResponse in pkt: 
        pkt.show() 
```

以下のようにデータが抽出できた。

```sh
┌──(kali㉿kali)-[/ctf/…/htb/HTB Business CTF 2023/scada/Watch Tower]
└─$ python3 modbus_show.py > out.txt
```

- out.txt

```
###[ Ethernet ]### 
  dst       = 14:7d:da:da:c8:31
  src       = 08:00:27:0e:34:8d
  type      = IPv4
###[ IP ]### 
     version   = 4
     ihl       = 5
     tos       = 0x[0
     len       = 64
     id        = 37870
     flags     = DF
     frag      = 0
     ttl       = 64
     proto     = tcp
     chksum    = 0x2272
     src       = 192.168.1.252
     dst       = 192.168.1.11
     \options   \
###[ TCP ]### 
        sport     = 502
        dport     = 58240
        seq       = 3055521896
        ack       = 2777555299
        dataofs   = 8
        reserved  = 0
        flags     = PA
        window    = 509
        chksum    = 0xcbb6
        urgptr    = 0
        options   = [('NOP', None), ('NOP', None), ('Timestamp', (3119843410, 127261158))]
###[ ModbusADU ]### 
           transId   = 0x8cbe
           protoId   = 0x0
           len       = 6
           unitId    = 0x1
###[ Write Multiple Coils Response ]### 
              funcCode  = 0xf
              startAddr = 0x1
              quantityOutput= 0x15
...snip...
```

"Write Multiple Registers Response"のstartAddrを抽出してみた。

```sh
┌──(kali㉿kali)-[/ctf/…/htb/HTB Business CTF 2023/scada/Watch Tower]
└─$ cat out.txt|grep 'Write Multiple Registers Response' -A 3|grep startAddr|cut -d'=' -f2|sed -z 's/\n/,/g'
 0x34, 0x4c, 0x52, 0x30, 0x50, 0x33, 0x55, 0x6e, 0x38, 0x46, 0x2d, 0x48, 0x54, 0x42, 0x7b, 0x6d, 0x30, 0x64, 0x38, 0x75, 0x35, 0x5f, 0x37, 0x32, 0x34, 0x66, 0x66, 0x31, 0x63, 0x5f, 0x31, 0x35, 0x5f, 0x75, 0x6e, 0x33, 0x6e, 0x63, 0x32, 0x79, 0x70, 0x37, 0x33, 0x64, 0x21, 0x40, 0x5e, 0x7d, 0x2d, 0x72, 0x36, 0x5a, 0x4a, 0x61, 0x30,
 ```

HEXを文字に変換するとフラグが現れた。

 ```sh
┌──(kali㉿kali)-[/ctf/…/htb/HTB Business CTF 2023/scada/Watch Tower]
└─$  python3
Python 3.11.4 (main, Jun  7 2023, 10:13:09) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> arr = [ 0x34, 0x4c, 0x52, 0x30, 0x50, 0x33, 0x55, 0x6e, 0x38, 0x46, 0x2d, 0x48, 0x54, 0x42, 0x7b, 0x6d, 0x30, 0x64, 0x38, 0x75, 0x35, 0x5f, 0x37, 0x32, 0x34, 0x66, 0x66, 0x31, 0x63, 0x5f, 0x31, 0x35, 0x5f, 0x75, 0x6e, 0x33, 0x6e, 0x63, 0x32, 0x79, 0x70, 0x37, 0x33, 0x64, 0x21, 0x40, 0x5e, 0x7d, 0x2d, 0x72, 0x36, 0x5a, 0x4a, 0x61, 0x30]
>>> for h in arr:
...     print(chr(h),end='')
... 
4LR0P3Un8F-HTB{m0d8u5_724ff1c_15_un3nc2yp73d!@^}-r6ZJa0>>> 
 ```

Flag:　`HTB{m0d8u5_724ff1c_15_un3nc2yp73d!@^}`