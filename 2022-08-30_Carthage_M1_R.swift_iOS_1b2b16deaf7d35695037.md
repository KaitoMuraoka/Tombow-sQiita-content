<!--
title:   [M1Mac]Carthageを用いてR.swiftを使う方法
tags:    Carthage,M1,R.swift,iOS
id:      1b2b16deaf7d35695037
private: false
-->
# はじめに
Carthageでライブラリを入れて開発をしているのですが、R.swiftを初めて使う際に機能しなくて困っていました。
色々調べていく際に、M1Macの「Homebrewの場所がIntel製と異なる」という特徴が原因であると発覚したので、報告＆改善策としてまとめます。

# 開発環境

- M1 macOS Monterey
- Swift5
- Xcode: 13.4.1
- R.swift: 6.1.0(13F100)

# R.swiftとは
Swiftプロジェクトで画像、フォント、ストーリーボードなどのリソースを簡単に取得できるようにするライブラリです。

https://github.com/mac-cain13/R.swift

これを導入するメリットは、

- リソースをキャストせずに使える
- コンパイル時のチェックにより、誤った文字列でアプリがクラッシュすることがなくなる
- リソース名が自動補完される

といった点があります。

具体的にSwiftコードで見てみると、

`swift:R.swiftなし
let icon = UIImage(named: "settings-icon")
let font = UIFont(name: "San Francisco", size: 42)
let color = UIColor(named: "indicator highlight")
let viewController = CustomViewController(nibName: "CustomView", bundle: nil)
let string = String(format: NSLocalizedString("welcome.withName", comment: ""), locale: NSLocale.current, "Arthur Dent")
`swift:R.swiftあり
let icon = R.image.settingsIcon()
let font = R.font.sanFrancisco(size: 42)
let color = R.color.indicatorHighlight()
let viewController = CustomViewController(nib: R.nib.customView)
let string = R.string.localizable.welcomeWithName("Arthur Dent")
`
のようにR．swiftで記述した方が、簡潔にリソースを取得することができます。

# R.swiftの導入方法
ここから本題で、Carthageを利用してR.swiftを導入していきます。
Carthageのインストール方法に関しては、[CarthageのGitHub](https://github.com/Carthage/Carthage#installing-carthage)か[コチラの記事](https://qiita.com/KaitoMuraoka/items/4904f67c58d93c677667#homebrew%E3%82%92%E5%88%A9%E7%94%A8%E3%81%97%E3%81%9F%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)をご参照ください。

手順は主に3つあります：

1. `rswift`の自動生成プログラムをインストール
1. CarthageでR.swiftライブラリを導入
1. Xcodeへ移り、[公式のGitHubのManuallyの2番](https://github.com/mac-cain13/R.swift#manually)以降の通りに設定をしていく

以下では、これらを順番に説明していきたいと思います。

## `rswift`の自動生成プログラムをインストール
Homebrewから`rswift`の自動生成プログラムをインストールしてきます。
　Brewfileを利用している方は`brew "rswift"`の行をBrewfileに追加、Homebrewを使用している方は、Terminalから`brew install rswift`でインストールできます。

## CarthageでR.swiftライブラリを導入
Cartfileに
`:Cartfile
github "mac-cain13/R.swift.Library"
`
を追加して、`carthage update --use-xcframeworks　--platform iOS`を実行します。（開発がiOSであると仮定して、`--platform iOS`にしました。）すると、`.xcframework`を作成することができます。
そして、Xcode内のGeneralから"Frameworks, Libraries, and Embedded Content"セクションに、`.xcframework`を追加します。
これで、CarthageでR．swiftライブラリを導入することができました。
しかし、このままではR.swift本来の力を発揮することができないので、R．swiftの設定をしていきます。

## 公式のGitHubのManuallyの2番以降の通りに設定をしていく
ここからは、[公式のGitHubのManuallyの2番](https://github.com/mac-cain13/R.swift#manually)を参考に導入していきます。

Xcodeにて、ファイルリストでプロジェクトをクリック。その後、"TARGETS"で　"Build Phases"タブをクリックして、左上の＋から、**New Run Script Phase**を追加します。
<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/707293/de4bda33-2877-1b06-0935-c104dcd846fe.png" width=75%>
次に、**Run Script**を**Compile Sources**の上にドラッグします。

<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/707293/e1f52cdb-d123-4317-11e2-4e88066e5dbf.png" width=75%>

そして、以下のスクリプトを貼り付けます。
`swift:Run Script
if test -d /opt/homebrew/bin; then
    export PATH=$PATH:/opt/homebrew/bin:/opt/homebrew/sbin
fi
rswift generate "$SRCROOT/R.generated.swift"
`
:::note warn
Point!
GitHub上では、`"$SRCROOT/rswift" generate "$SRCROOT/R.generated.swift"`だけなのに、なぜ↑のような書き方をするのか？
:::
`"$SRCROOT/rswift"`ではなく、`rswift`である理由は、先ほどすでにHomebrewで`rswift`を導入しているからです。そのため、`"$SRCROOT/rswift"`で呼び出さなくても`rswift`で呼ぶ出すことが可能です。
また、M1 MacはHomebrewを`/opt/homebrew`で保存しています。一方、Intel Macは`/usr/local`に保存されています。現在のXcode13は、Run Scriptを実行した際に`/usr/local`を参照してしまうため、`/opt/homebrew`を参照するよう、
`swift
if test -d /opt/homebrew/bin; then
    export PATH=$PATH:/opt/homebrew/bin:/opt/homebrew/sbin
fi
`
を最初の行に追加する必要があります。

最後に、Build Phaseの**Output Files**に、`$SRCROOT/R.generated.swift`を追加し、**Based on dependency analysis**のチェックを外します。

<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/707293/f7968832-7874-8065-6f49-cf78afc27e75.png" width=50%%>

↑のような形になっていればビルド毎にR.swiftを実行することができます。

# まとめ
M1 MacでR.swiftを利用する際は、**Run Script**にhomebrewの場所を参照するよう追加のコードを記述する必要がある。
今回はCarthageを用いたR.swiftの導入でしたが、R.swift自体はCarthageでの導入を推奨しておらず、Cocoa Podsでの導入を推奨しているため、CocoaPodsで開発ができるのであれば、そちらをオススメします。

# 参考文献
https://qiita.com/uhooi/items/82fbdd94bdc467a22422

https://qiita.com/lovee/items/5617cdaa28a470b141c2

# おまけ：セットアップ方法
R.swiftで文字列を使用する方法をここで解説します。

プロジェクトディレクトリの配下に`.strings`拡張子のファイルを配置します。Xcodeでは、右クリック→`New file...`から追加することができます。

デフォルトの`AppDelegate.swift`の同層配下であればディレクトリがネストしていても取得可能です。
`:message.strings
"one" = "hoge";
"helloSwift" = "Hello Swift!";
`
一度、`command+B`でビルドした後、これをプロジェクト内で使うと
`swift
let hogeText = R.string.messages.one()  // hoge
let helloText = R.string.messages.helloSwift() // Hello Swift!
`
となります。