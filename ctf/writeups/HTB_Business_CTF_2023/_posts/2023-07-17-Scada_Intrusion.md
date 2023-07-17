---
layout: post
title: "[Scada] Intrusion (325 pts, 78 solved)"
published : true
permalink: /ctf/writeups/HTB_Business_CTF_2023/Scada_Intrusion
tags: [CTF, WriteUps, HTB Business CTF]
---
## Description
After gaining access to the enemy's infrastructure, we collected crucial network traffic data from their Modbus network. Our primary objective is to swiftly identify the specific registers containing highly sensitive information and extract that data.

## Files
- ics_watch_tower.zip

## Solution
zipファイルを展開すると以下の二つのファイルが現れる。

- "tower_logs.pcapng"
- "client.py"

この問題にはDockerインスタンスが用意されているが、ncで接続しても反応はなかった。

"tower_logs.pcapng"を見ると、プロトコル"Modbus/TCP"の通信が確認できた。

また、全ての通信**Unit**が**52**となっていることがわかる。

まずは、"Watch Tower"と同じように"Modbus/TCP"のデータを抽出する。

```sh
┌──(kali㉿kali)-[/ctf/…/htb/HTB Business CTF 2023/scada/Intrusion]
└─$ python3 modbus_show.py >network_logs.txt
```

次に、配布された"client.py"の中身を見てみると、以下のようになっている。

- client.py

```python
#!/usr/bin/python3

import socket
from time import sleep
from umodbus import conf
from umodbus.client import tcp

# Adjust modbus configuration
conf.SIGNED_VALUES = True

# Create a socket connection
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.connect(('127.0.0.1', 502)) # CHANGE THE IP & PORT to the dockers instance

# write your umodbus command here
# command =

# Send your message to the network
tcp.send_message(command, sock)

# Use sleep between messages
time.sleep(1)

# Close the connection
sock.close()
```

"command" に "umodbus command"を入れるよう指示がある。

"umodbus command"について検索すると、以下のサイトを発見した。

- https://umodbus.readthedocs.io/en/latest/client/tcp.html#

このサイトはModbusAPIについて説明しており、modbusを操作するためのAPIが確認できる。

ここで、改めて問題文を読むと、"Our primary objective is to swiftly identify the specific registers containing highly sensitive information and extract that data."と書かれており、registerの値を取得する必要がありそうなことがわかる。

さらに、以下のサイトではModbusのregister typeについて説明している。

- https://www.csimn.com/CSI_pages/Modbus101.html

> Review of Modbus Register Types  
> The types of registers referenced in Modbus devices include the following:  
> • Coil (Discrete Output)  
> • Discrete Input (or Status Input)  
> • Input Register  
> • Holding Register  
> Whether a particular device includes all of these register types is up to the manufacturer. It is very common to find all I/O mapped to holding registers only. Coils are 1-bit registers, are used to control discrete outputs, and may be read or written. Discrete Inputs are 1-bit registers used as inputs, and may only be read. Input registers are 16-bit registers used for input, and may only be read. Holding registers are the most universal 16-bit register, may be read or written, and may be used for a variety of things including inputs, outputs, configuration data, or any requirement for "holding" data.

Modbus APIを説明しているサイトによると、"Holding Register"の取得は以下を使用すれば良い。

> umodbus.client.tcp.read_holding_registers(slave_id, starting_address, quantity)  
> Return ADU for Modbus function code 03: Read Holding Registers.
> 
> Parameters:	slave_id – Number of slave.  
> Returns:	Byte array with ADU.

slave_idに指定するidは、パケットキャプチャから52であることがわかっている。

よって、client.pyを以下のように修正した。

```python
#!/usr/bin/python3

import socket
from time import sleep
from umodbus import conf
from umodbus.client import tcp

# Adjust modbus configuration
conf.SIGNED_VALUES = True

def get_data(i,s,q):
    print('===', i, '===')
    # Create a socket connection
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect(('94.237.63.180', 40554)) # CHANGE THE IP & PORT to the dockers instance

    # write your umodbus command here
    command = tcp.read_holding_registers(i, s, q)

    # Send your message to the network
    r = tcp.send_message(command, sock)
    print(r)
    result = ''
    for n in r:
        result += chr(n)

    # Use sleep between messages
    sleep(1)

    # Close the connection
    sock.close()
    return result

result = get_data(52,0,125)
print(result)
result += get_data(52,125,125)
print(result)
result += get_data(52,250,125)
print(result)
```

なお、一度に取得できるデータは125文字のようだったので、3回接続している。

出力:

```sh
┌──(kali㉿kali)-[/ctf/…/htb/HTB Business CTF 2023/scada/Intrusion]
└─$ python3 client.py
=== 52 ===
[48, 86, 33, 41, 37, 81, 72, 121, 74, 33, 84, 121, 66, 32, 116, 36, 55, 80, 94, 43, 61, 123, 50, 62, 57, 90, 51, 83, 89, 89, 115, 120, 74, 120, 111, 67, 119, 44, 94, 53, 44, 35, 45, 104, 119, 123, 110, 57, 33, 97, 90, 38, 104, 49, 79, 120, 109, 97, 48, 33, 44, 67, 76, 53, 57, 104, 84, 89, 72, 50, 121, 119, 43, 110, 56, 57, 79, 55, 100, 82, 77, 39, 64, 51, 122, 113, 50, 121, 62, 53, 82, 85, 91, 68, 44, 95, 109, 79, 76, 66, 119, 33, 102, 110, 49, 82, 54, 80, 84, 32, 41, 60, 103, 40, 50, 100, 58, 56, 52, 53, 62, 112, 81, 57, 121]
0V!)%QHyJ!TyB t$7P^+={2>9Z3SYYsxJxoCw,^5,#-hw{n9!aZ&h1Oxma0!,CL59hTYH2yw+n89O7dRM'@3zq2y>5RU[D,_mOLBw!fn1R6PT )<g(2d:845>pQ9y
=== 52 ===
[40, 42, 123, 104, 70, 54, 55, 80, 124, 95, 60, 100, 78, 114, 104, 61, 119, 73, 49, 100, 100, 42, 65, 51, 119, 57, 114, 54, 51, 49, 45, 56, 68, 44, 52, 68, 48, 113, 95, 71, 66, 50, 93, 53, 46, 93, 109, 54, 51, 46, 51, 59, 117, 77, 99, 32, 41, 98, 90, 38, 62, 40, 120, 51, 62, 57, 63, 81, 50, 95, 37, 51, 59, 65, 81, 65, 70, 114, 50, 45, 48, 51, 109, 73, 90, 55, 83, 94, 108, 53, 33, 95, 33, 71, 64, 78, 36, 108, 67, 50, 54, 48, 77, 76, 126, 86, 57, 73, 75, 40, 78, 42, 101, 87, 94, 49, 121, 98, 98, 41, 54, 59, 89, 65, 107]
0V!)%QHyJ!TyB t$7P^+={2>9Z3SYYsxJxoCw,^5,#-hw{n9!aZ&h1Oxma0!,CL59hTYH2yw+n89O7dRM'@3zq2y>5RU[D,_mOLBw!fn1R6PT )<g(2d:845>pQ9y(*{hF67P|_<dNrh=wI1dd*A3w9r631-8D,4D0q_GB2]5.]m63.3;uMc )bZ&>(x3>9?Q2_%3;AQAFr2-03mIZ7S^l5!_!G@N$lC260ML~V9IK(N*eW^1ybb)6;YAk
=== 52 ===
[97, 45, 102, 125, 37, 95, 52, 42, 48, 71, 50, 93, 100, 98, 82, 43, 68, 71, 57, 48, 87, 105, 71, 101, 101, 65, 52, 115, 103, 117, 74, 63, 111, 76, 101, 77, 43, 109, 110, 32, 101, 83, 99, 82, 68, 70, 92, 108, 83, 74, 121, 60, 35, 108, 88, 89, 37, 66, 39, 39, 62, 95, 71, 63, 102, 125, 35, 42, 101, 67, 80, 100, 110, 37, 85, 81, 35, 73, 99, 80, 106, 45, 126, 38, 71, 36, 54, 90, 87, 91, 42, 112, 35, 50, 72, 32, 80, 32, 107, 104, 97, 71, 96, 102, 94, 66, 92, 106, 103, 104, 66, 47, 73, 125, 106, 114, 64, 93, 75, 90, 74, 124, 100, 35, 49]
0V!)%QHyJ!TyB t$7P^+={2>9Z3SYYsxJxoCw,^5,#-hw{n9!aZ&h1Oxma0!,CL59hTYH2yw+n89O7dRM'@3zq2y>5RU[D,_mOLBw!fn1R6PT )<g(2d:845>pQ9y(*{hF67P|_<dNrh=wI1dd*A3w9r631-8D,4D0q_GB2]5.]m63.3;uMc )bZ&>(x3>9?Q2_%3;AQAFr2-03mIZ7S^l5!_!G@N$lC260ML~V9IK(N*eW^1ybb)6;YAka-f}%_4*0G2]dbR+DG90WiGeeA4sguJ?oLeM+mn eScRDF\lSJy<#lXY%B''>_G?f}#*eCPdn%UQ#IcPj-~&G$6ZW[*p#2H P khaG`f^B\jghB/I}jr@]KZJ|d#1
```

さらに、抽出したModbusのデータから、"register"の"startAddr"を取り出す。

```sh
┌──(kali㉿kali)-[/ctf/…/htb/HTB Business CTF 2023/scada/Intrusion]
└─$ cat network_logs.txt | grep 'Registers ' -A2|grep start|cut -d'=' -f2|sed -z 's/\n/,/g'
 0x6, 0xa, 0xc, 0x15, 0x16, 0x1a, 0x2f, 0x35, 0x3f, 0x4d, 0x53, 0x56, 0x59, 0x5f, 0x60, 0x68, 0x7b, 0x80, 0x83, 0x86, 0x8b, 0x8f, 0x90, 0x91, 0x99, 0xa3, 0xa8, 0xad, 0xb3, 0xc1, 0xce, 0xd2, 0xd6, 0xd7, 0xdb, 0xdd, 0xe0, 0xe1, 0xe2, 0xe7, 0xef, 0xfd,                                             
```

上記より、以下のスクリプトを作成しフラグを得た。

- solver.py

```python
data = [48, 86, 33, 41, 37, 81, 72, 121, 74, 33, 84, 121, 66, 32, 116, 36, 55, 80, 94, 43, 61, 123, 50, 62, 57, 90, 51, 83, 89, 89, 115, 120, 74, 120, 111, 67, 119, 44, 94, 53, 44, 35, 45, 104, 119, 123, 110, 57, 33, 97, 90, 38, 104, 49, 79, 120, 109, 97, 48, 33, 44, 67, 76, 53, 57, 104, 84, 89, 72, 50, 121, 119, 43, 110, 56, 57, 79, 55, 100, 82, 77, 39, 64, 51, 122, 113, 50, 121, 62, 53, 82, 85, 91, 68, 44, 95, 109, 79, 76, 66, 119, 33, 102, 110, 49, 82, 54, 80, 84, 32, 41, 60, 103, 40, 50, 100, 58, 56, 52, 53, 62, 112, 81, 57, 121, 40, 42, 123, 104, 70, 54, 55, 80, 124, 95, 60, 100, 78, 114, 104, 61, 119, 73, 49, 100, 100, 42, 65, 51, 119, 57, 114, 54, 51, 49, 45, 56, 68, 44, 52, 68, 48, 113, 95, 71, 66, 50, 93, 53, 46, 93, 109, 54, 51, 46, 51, 59, 117, 77, 99, 32, 41, 98, 90, 38, 62, 40, 120, 51, 62, 57, 63, 81, 50, 95, 37, 51, 59, 65, 81, 65, 70, 114, 50, 45, 48, 51, 109, 73, 90, 55, 83, 94, 108, 53, 33, 95, 33, 71, 64, 78, 36, 108, 67, 50, 54, 48, 77, 76, 126, 86, 57, 73, 75, 40, 78, 42, 101, 87, 94, 49, 121, 98, 98, 41, 54, 59, 89, 65, 107, 97, 45, 102, 125, 37, 95, 52, 42, 48, 71, 50, 93, 100, 98, 82, 43, 68, 71, 57, 48, 87, 105, 71, 101, 101, 65, 52, 115, 103, 117, 74, 63, 111, 76, 101, 77, 43, 109, 110, 32, 101, 83, 99, 82, 68, 70, 92, 108, 83, 74, 121, 60, 35, 108, 88, 89, 37, 66, 39, 39, 62, 95, 71, 63, 102, 125, 35, 42, 101, 67, 80, 100, 110, 37, 85, 81, 35, 73, 99, 80, 106, 45, 126, 38, 71, 36, 54, 90, 87, 91, 42, 112, 35, 50, 72, 32, 80, 32, 107, 104, 97, 71, 96, 102, 94, 66, 92, 106, 103, 104, 66, 47, 73, 125, 106, 114, 64, 93, 75, 90, 74, 124, 100, 35, 49]




flag_pos = [0x06, 0x0a, 0x0c, 0x15, 0x16, 0x1a, 0x2f, 0x35, 0x3f, 0x4d, 0x53, 0x56, 0x59, 0x5f, 0x60, 0x68, 0x7b, 0x80, 0x83, 0x86, 0x8b, 0x8f, 0x90, 0x91, 0x99, 0xa3, 0xa8, 0xad, 0xb3, 0xc1, 0xce, 0xd2, 0xd6, 0xd7, 0xdb, 0xdd, 0xe0, 0xe1, 0xe2, 0xe7, 0xef, 0xfd]

print(len(data))
for p in flag_pos:
    print(chr(data[p]),end='')
```

出力:
```sh
┌──(kali㉿kali)-[/ctf/…/htb/HTB Business CTF 2023/scada/Intrusion]
└─$ python solver.py
375
HTB{239157325_m19h7_h1dd3_53c2375!@$2609^}
```

Flag: `HTB{239157325_m19h7_h1dd3_53c2375!@$2609^}`