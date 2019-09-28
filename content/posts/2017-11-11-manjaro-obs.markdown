---
layout: post
title: "Manjaro Linux の OBS で日本語が四角で表示されてしまう場合の対処方法"
date: 2017-11-11 9:41:28 +0900
categories: 'programmer'
slug: 'programmer'
aliases: "/manjaro/2017/11/11/manjaro-obs.html"
---

タイトルの通りですが、OBSで放送しようと思った時にTextを表示しようとしたら日本語が豆腐になっていたので解決する。

## 対処方法
日本語フォントをインストールすれば良さそうなので、notoフォントを入れる

```
$ sudo pacman -Sy noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra
```

でインストールして、Select fontで "Noto Snas CJK JP" を指定したら正しく表示された。
