---
layout: post
title: "Forensicsの基本"
published : true
permalink: /ctf/cheatsheets/forensics/common
tags: [CTF, cheatsheet, forensics]
---
## なにかのデータファイルが与えられたとき
- file *
- binwalk
- strings
- xxd
- etc...

## 画像ファイルが与えられたとき
- file, binwalk, strings, xxd
- exiftool
- 画像検索で元画像を探してみる。見つかったら比較する。
- 同じような画像が複数与えられたなら、それらを比較する。(別ページにまとめたい)

### JPEGファイルが与えられたとき
- steghideでファイル抽出
- stegoveritas

### PNGファイルが与えられたとき
- stegoveritas
- stegosolveでLSB抽出

### 画像ファイルが壊れているとき
- xxdで中身を見て治す
- GIMPで開いてみる
- CRCが改ざんされ、そこにフラグがある可能性がある。スクリプトを書いてCRCを抽出する(別ページにまとめたい)
- cyberchefで開けるかどうか確かめる(!?)[Render image]

## 音声ファイルが与えられたとき
- Sonic Visualiserで開き、スペクトログラムを確認する。
- DeepSoundを使う

## Zipファイルが与えられたとき
- zipcheckで確認してみる
- unzipで解凍できないときは"7zip e"してみる

### パスワードがかかっているとき
- ZipCryptoに脆弱性あり。bkcrackツールでクラックを試みる(別ページにまとめたい)
- 簡単なパスワードなら、zip2johnからのjohnでクラック可能。

## PDFファイルが与えられたとき
- peepdfで調べてみる
- LibreOffice Drawで開く

## Officeファイルが与えられたとき
- LibreOffice Drawで開く
- oletoolsで調査

## PCAPファイルが与えられたとき
- Protocol Hierarchy で中身をざっと確認
- ファイルエクスポートしてみる
- 文字列検索してみる

## Firefoxのファイル群が与えられたとき
- firefox_decrypt.pyを使ってパスワードを解析してみる
```sh
$ firefox_decrypt Mozilla/Firefox 
Select the Mozilla profile you wish to decrypt
1 -> Profiles/yodxf5e0.default
2 -> Profiles/2542z9mo.default-release
2

Website:   http://acc01:8080
Username: 'admin'
Password: 'HTB{ur_8RoW53R_H157Ory}'
```