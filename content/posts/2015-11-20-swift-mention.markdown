---
layout: post
title: "チャットのメンションなどで使われる@で始まる部分にマッチする正規表現"
date: 2015-11-20 9:41:28 +0900
categories: programmer
slug: programmer
tags:
  - swift
aliases: '/swift/2015/11/20/swift-mention.html'
---

よくチャットなどで `@regonn` のようにメンションで使ったりしますが、Swift で正規表現(NSRegularExpression)を使って取得したい。

例えば次のような String の場合

`"@banana @ tomato @orange regonn@sonicgarden.jp "`

マッチして欲しいのは [ ] で囲んだ所(今回は`@`のみもマッチする)

`"[@banana] [@] tomato [@orange] regonn@sonicgarden.jp "`

そこで次のようなコードになりました。

### mentionMatch.swift

```swift
import UIKit

let string = "@banana @ tomato @orange regonn@sonicgarden.jp "

let regrex = try? NSRegularExpression(pattern: "(?<=^|\\s)(@\\w*)", options: NSRegularExpressionOptions.CaseInsensitive)

let matches = regrex!.matchesInString(string, options: [], range: NSMakeRange(0, string.characters.count))

for match in matches {
    print(match.range)
}

=> (0,7)
(8,1)
(17,7)
```

ちゃんと 3 か所にマッチできていることが確認できます。

今回の正規表現は

`(?<=^|\\s)(@\\w*)`

となりました(もし`@`単体はマッチさせたくないのなら最後を`w+`に変更)

`(?<=pattern)`は肯定後読み(positive lookbehind)という書き方らしい。知らなかった。。。

図にしてみるとこんな感じになります。

<amp-img src="https://blog.regonn.tokyo/images/2015-11-20.png" alt="" width="768" height="266" layout="responsive" ></amp-img>
