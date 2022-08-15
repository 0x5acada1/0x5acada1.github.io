---
layout: post
title: "Forensics"
published : true
tags: CTF Common cheatsheet forensics
---

## 画像ファイルが与えられたとき
### 画像ファイルが壊れているとき
- xxdで中身を見て治す
- cyberchefで開けるかどうか確かめる(!?)[Render image]

## なにかのデータファイルが与えられたとき
- file *
- binwalk
- strings
- xxd
- etc...

## PCAPファイルが与えられたとき
- Protocol Hierarchy で中身をざっと確認
- ファイルエクスポートしてみる
- 文字列検索してみる
