---
layout: post
title: "MacOSX で fork: Resource temporarily unavailable エラーになる場合の対処法"
date: 2017-02-03 9:41:28 +0900
categories: programmer
slug: programmer
tags:
  - macos
aliases:
  - '/macos/2017/02/03/maxos.html'
  - '/programmer/2017-02-03-maxos/'
---

最近 Rails 開発で puma-dev とかを入れていると、fsevent_watch プロセスが量産されて、プロセス上限数に達し

```
fork: Resource temporarily unavailable
```

というエラーが出て開発が中断される。

色々なところで議論もされていて、まだ根本的な解決はされていない様子

- [fork: Resource temporarily unavailable occurring since installing · Issue \#56 · puma/puma\-dev](https://github.com/puma/puma-dev/issues/56)
- [The listen gem breaks my laptop · Issue \#26158 · rails/rails](https://github.com/rails/rails/issues/26158)

`listen` gem を削除したり `development.rb` を修正する必要があるみたいだけど、Railsアプリのコードをいじってしまうと他の人の開発環境にも影響が出るので、自分の Mac のプロセス上限を上げることに。

以前の MacOSX だと、 `/etc/launchd.conf` をいじれば良さそうだったけど、Sierraではそんなファイルが存在していなく、調べると

[Open Files Limit](https://docs.basho.com/riak/kv/2.2.0/using/performance/open-files-limit/#mac-os-x)

という記事で、次の2つのファイルを作ることで対応できるみたい。

### /Library/LaunchDaemons/limit.maxfiles.plist
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>limit.maxfiles</string>
    <key>ProgramArguments</key>
    <array>
      <string>launchctl</string>
      <string>limit</string>
      <string>maxfiles</string>
      <string>200000</string>
      <string>200000</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>ServiceIPC</key>
    <false/>
  </dict>
</plist>
```

### /Library/LaunchDaemons/limit.maxproc.plist
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple/DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>limit.maxproc</string>
    <key>ProgramArguments</key>
    <array>
      <string>launchctl</string>
      <string>limit</string>
      <string>maxproc</string>
      <string>2048</string>
      <string>2048</string>
    </array>
    <key>RunAtLoad</key>
    <true />
    <key>ServiceIPC</key>
      <false />
  </dict>
</plist>
```

再起動して、 `$ sudo launchctl limit` コマンドで maxproc と maxfiles が増えていればOK
