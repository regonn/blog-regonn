---
layout: post
title: "山括弧　＜　＞　(小なり,大なり)　が正規表現でマッチしなかった場合"
date: 2014-07-06 9:41:28 +0900
categories: programmer
slug: programmer
tags:
  - javascript
aliases: '/javascript/2014/07/06/match-lt-gt.html'
---

JavaScriptで文章中のタグに使われている山括弧 < > を消したいために `replace(/>/g,' ')` と書いてもマッチしてくれなかった。
`replace(/\>/g,' ')` でエスケープしてもだめだった。

検索で調べても山括弧って言葉自体はじめて知ったし。Googleの検索とかは記号は弾かれてしまって解決に苦労した。

悪意のあるコード等を入力フォームに入力されても、実行されないようにホームページではサニタイズされていて< >は`&lt; &gt;`に変換されていたみたい。

`replace(/&gt;/g,' ')` でマッチしてくれた。
