<!--
title:   【Swift5】キーボードを閉じる方法
tags:    Keyboard,Swift,Swift5,iOS,iPhone
id:      ea77e40ca9f5cf69a6de
private: false
-->
# はじめに
この記事は、アプリを作っている際に幾度も「なんだっけ？」を解決するために作成した記事です。記事というか公開ノートのようなものです。

# 前提
classに`UITextFieldDelegate`を追加します。

```swift
import UIKit

class ViewController: UIViewController, UITextFieldDele{

    @IBOutlet weak var TextField: UITextField!

    override func viewDidLoad() {
        super.viewDidLoad()

        TextField.delegate = self
    }

```

`TextField`は任意のtextFieldです。

# タッチでキーボードを閉じる
キーボード以外の場所をタップするとキーボードが閉じます。

```swift
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {

        view.endEditing(true) //編集を終了するかを聞く=>true
    }

```

# リターンキーでキーボードを閉じる
キーボードのReturnをタップするとキーボードが閉じます。

```swift
    func textFieldShouldReturn(_ textField: UITextField) -> Bool {

        textField.resignFirstResponder() //キーボードを閉じる

        return true
    }

```


#最後に
別の方法があったら追記していきたいと思います。