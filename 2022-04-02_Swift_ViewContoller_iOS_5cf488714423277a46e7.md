<!--
title:   【iOS】画面遷移時にキーボードを表示させる方法
tags:    Swift,ViewContoller,iOS
id:      5cf488714423277a46e7
private: false
-->
# はじめに
キーボードを起動するためにテキストフィールドを毎回タップするのがめんどくさいなぁと感じたため、画面が開いたときにキーボードが自動的に表示される方が便利と思いました。そのため、画面遷移をした後にキーボードを自動的に表示させる方法を書いていきます。

また、これは画面遷移が完全に終わる前にキーボードを事前に表示しておく方法です。画面遷移が完全に終了した後の処理はこの後出てくる`viewWillAppear`を`viewDidAppear`にすると可能です。

# やり方
これを実現するには簡単で、新しいメソッドを`ViewController`内に以下のコードを追加します。

```swift
override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
    textField.becomeFirstResponder()
}
```

# 詳細説明
## `override func viewWillAppear(_ animated: Bool) {`とは？
画面に表示される直前に呼ばれるメソッドです。初期表示に必要な`viewDidLoad()`とは異なり、毎回呼び出されます。

UIViewControllerのライフサイクルについての詳細は以下の記事をご参照ください

https://qiita.com/entaku0818/items/87d4e7225e23db0b01ff

https://ios-docs.dev/life-cycle/

## `textField.becomeFirstResponder()`
UITextFieldはファーストレスポンダにすることによりキーボードを表示することができます。逆にファーストレスポンダをやめるとキーボードを非表示にすることができます。

そのため、`textField.becomeFirstResponder()`とすることにより、画面を読み込むたびにキーボードを表示することができます。

### 「ファーストレスポンダ」とは？
iOSでは「レスポンダチェーン」と呼ばれる仕組みが用意されています。一連のレスポンダオブジェクトの連なりで、イベントを処理できるオブジェクトを決定するために使用されます。

レスポンダオブジェクトは基本的にView階層のかいに位置されるオブジェクトから順に評価されるため、最初にイベントを受け取るレスポンダオブジェクトを「ファーストレスポンダ」と呼びます。