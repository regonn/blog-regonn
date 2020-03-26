---
title: "最近Flutterで作ってるもの"
date: 2019-12-01 9:00:00 +0900
categories: "flutter"
slug: "flutter"
tags:
  - "flutter"
desc: "Flutter Web対応も発表されたので、いくつかアプリを作って公開している話"
keywords: "Flutter"
---

今まで Flutter、Dart 関連はちょくちょく触ってましたが、ちゃんとアプリとして公開等したことがなく、Flutter Web 対応も発表されたので、もうちょっと深く触っていきました。

## 自分のレベル感

- 普段業務: Web プログラマ(Rails) 5 年程度
- iOS と Android も一度は開発の経験有り

## 作ってるアプリ概要

- Bouyomisan Flutter
  - 以前 Dart で作ったサービスのリプレイス
  - Flutter web
- Regonn&Curry.fm
  - ポッドキャストを聞くためのスマホアプリ

CI やデプロイは[Codemagic](https://codemagic.io/)を利用しているので、そのあたりも所感に含めて書いていきます。

## Bouyomisan Flutter

以前、YouTubeLive コメント読み上げさんという Web サイトを、Dart1.0 時代に作ってました。
[YouTubeLive のコメントを読み上げてくれるツールを Dart で書いてみた](https://blog.regonn.tokyo/programmer/2017-08-27-dart-speech/)
[YouTubeLive コメント読み上げさん](https://bouyomisan.regonn.tokyo/)
しかし、一度作って放置していたら、Dart1.0 の環境構築が難しくなりメンテナンスが出来ない状態になっていました。
Dart でのコードが残っているので、そのまま Flutter でできないかリプレイスに挑戦してみました。

[Bouyomisan Flutter](https://bouyomisan.codemagic.app/#/)

### 所感

#### `dart:html` が使えて、ブラウザの文章読み上げ機能が使えた。

リプレイス前が Dart のアプリで、更に文章をブラウザで読み上げるために Web Speech API の [SpeechSynthesisUtterance](https://developer.mozilla.org/ja/docs/Web/API/SpeechSynthesisUtterance)を利用していたので、HTML で記述したい部分がありました。Flutter だと、Flutter UI で画面を構築して、web 版も全体を覆う感じにスタイルがなっているので、HTML とかを扱うのが難しいかなと思っていたんですが、 `dart:html` が呼べたので、

```dart

    var u = new SpeechSynthesisUtterance();
    u.text = text;
    u.lang = 'ja-JP';
    u.pitch = _pitchValue;
    u.rate = _rateValue;
    u.volume = _volumeValue;
    window.speechSynthesis.speak(u);

```

みたいにすることで、実際にブラウザで読み上げることができました。
ただし、 `dart:html` が import されていると、スマホアプリのビルドは失敗するみたいです。

#### Adsense はまだ使えなさそう

Flutter だったら、もし広告を配置する場合は Admob とかを使うと思いますが、web 版だったら Adsense を貼りたいですよね。
けど、まだ、ここらへんは対応していないみたいで、Flutter の Issue になっています。

[Support Google AdSense in Flutter Web applications · Issue \#40376 · flutter/flutter](https://github.com/flutter/flutter/issues/40376)(ただ、Issue の中身をみると、なぜかコードが nend のものだったりして、どれだけ Adsense 対応に前向きなのかはわかりません)

#### Codemagic を利用したら、web サイトはすぐ公開できるので良かった

Dart でのサービスのときは AWS S3 にファイル置いてありましたが、Codemagic には、codemagic 専用の公開 URL も用意されているので、CI が通ったら公開されるようにしてありました。

#### Web は最初のアクセスで読み込むためラグが発生する

実際に Web アプリにアクセスしてもらったら、わかりますが、アクセス時には Flutter のロード時間が 1~2 秒発生してしまいます。
何も設定しないと真っ白な画面が出てしまうので、ユーザ的には一瞬固まったと感じてしまいそうなので、現在は「読み込み中」と書き込んでありますが、文字を出すと今度は、Flutter が読み込まれる前と後でスタイルも変わってしまうので、本番で利用するにはどうしたらいいのかなと悩んでます。

## Regonn&Curry.fm

普段は、データサイエンス系の話題を取り上げている、Podcast をやっています。
専用の Podcast を聞くアプリとして、将来的にはアプリ限定のコンテンツ等も出せたりすると面白いのかなと思ってます。
まだ現状は聞くだけしかできませんが、とりあえず Android 版の公開までできました。

[Regonn&Curry\.fm \- Google Play のアプリ](https://play.google.com/store/apps/details?id=art.haizine.regonncurry&hl=ja)

### 利用しているライブラリ

- [webfeed \| Dart Package](https://pub.dev/packages/webfeed)
  - Podcast の RSS 読み取りはこれでできました
- [audioplayers \| Flutter Package](https://pub.dev/packages/audioplayers)
  - サーバー上の Podcast の音声を読み込んで再生もできました

### 所感

#### 結局リリース周りのネイティブアプリ知識は必要

iOS と Android もネイティブアプリも開発とリリース経験はあったので、codemagic を使ってデプロイ等をしていますが、結局 iOS や Android アプリとしてリリースする際には、証明書関係が必要で、そこらへんの知識がないと厳しいなと思いました。

#### Windows 開発機だと、少し辛い

私は普段、VR コンテンツとかデータサイエンス関連で GPU を使う機会も多いので、Windows 環境がメインです。
Flutter だと iOS アプリも作れますが、結局 pod をインストールしたり、開発版ビルドは Mac でするため、いちいち Mac 環境での作業が入るのは少し面倒(Mac 使いだったら、Android もビルドできるので良さそう)。
Codemagic を利用すれば、リリースビルドの作成とかはクラウド上でできて、そのまま iTunes Connect にアップロードもできるので楽だった。

## 結論

- Codemagic で、Web とスマホアプリのリリースビルドとデプロイまでできるのが素晴らしい
- Web はもうちょっと、開発が進むのを待ちたい
