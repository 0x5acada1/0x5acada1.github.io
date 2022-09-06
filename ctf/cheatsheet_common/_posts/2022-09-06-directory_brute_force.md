---
layout: post
title: "Directory Brute Force"
published : true
permalink: /ctf/cheatsheets/common/directory_brute_force
tags: [CTF, Common, cheatsheet, ffuf, FUFF, dirsearch, directory search, directory brute force]
---
## ディレクトリ探索
### FFUF
Options
```sh
-u Target URL
-w Wordlist file path and (optional) keyword separated by colon. eg. '/path/to/wordlist:KEYWORD'
-c Colorize output. (default: false)
-ic Ignore wordlist comments (default: false)
-o Write output to file
-of Output file format. Available formats: json, ejson, html, md, csv, ecsv (or, 'all' for all formats) (default: json)
```
Commands
```sh
WL_DIR=/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
ffuf -u http://10.10.11.161/FUZZ -w $WL_DIR -c -ic -o ffuf.log -of md
```

#### POST
JSONのやり取りをするときは、ヘッダをつけることを忘れずに。


Options
```sh
-H Header `"Name: Value"`, separated by colon. Multiple -H flags are accepted.
-X HTTP method to use
-d POST data
```
Command
```sh
ffuf -u http://10.10.11.161/api/v1/user/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -c -ic -X POST -H 'Content-Type: application/json' -d '{"username":"admin","password":"P@ssw0rd"}'
```
#### 拡張子の指定
システムに適した拡張子を指定することは検出率を上げる重要な要素となる。FUFFでは-eで指定する
##### PHPのときの例
```
-e .php,.htnl,.txt
```
##### cgiのときの例
```
-e .sh,.cgi,.pl,.py
```

#### APIの探索
API探索時、デフォルトのResponse status Matcher(200,204,301,302,307,401,403,405,500)だと、[422:Unprocessable Entity]などを見落とす可能性がある。確実なのは、Matcherをallとした上で不要なstatus codeを除外すること。

Options
```sh
-mc Match HTTP status codes, or "all" for everything. (default: 200,204,301,302,307,401,403,405,500)
-fc Filter HTTP status codes from response. Comma separated list of codes and ranges
```
Command
```sh
ffuf -u http://10.10.11.161/api/v1/user/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -c -ic -X POST -mc all -fc 405
```

#### vhost探索
-fs で除外したいサイズを指定する。サイズがまちまちのときは-fw でワード数除外することも考える。

Command
```sh
ffuf -u http://example.com/ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -c -ic -H 'FUZZ.example.com' -mc all -fs 777
```