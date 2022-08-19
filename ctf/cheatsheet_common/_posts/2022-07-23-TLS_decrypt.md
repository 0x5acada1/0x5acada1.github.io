---
layout: post
title: "TLS通信の復号"
published : true
permalink: /ctf/cheatsheets/common/tls_decrypt
tags: [CTF, Common, cheatsheet, network, TLS, decrypt, 復号]
---

## TLS通信の復号
前提：復号用の鍵を持っていること。
- Wiresharkを起動する
- [ Edit - Preferences - Protocols - TLS] を開く
- [RSA key list - Edit] より、Key fileを追加する。(IPアドレスなどの項目は空欄で良い)
- OK でパケット一覧の画面に戻ると暗号化されていた通信が復号されている。
