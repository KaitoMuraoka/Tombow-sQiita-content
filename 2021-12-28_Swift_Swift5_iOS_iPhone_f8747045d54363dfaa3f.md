<!--
title:   【Swift5】アラートを表示
tags:    Swift,Swift5,iOS,iPhone
id:      f8747045d54363dfaa3f
private: false
-->
# はじめに
ボタンを押した時の表示されるアラートについて、基本的なことをまとめた技術メモです。
ご指摘等ございましたら、コメント、編集リクエストなどで教えていただけると幸いです。

# 開発環境
- iOS15
- Swift5
- Xcode 13


# アラートを表示(Alert/ActionSheet)
## Alert
ダイアログ風のアラートが表示される
![Simulator Screen Shot - iPhone 11 - 2021-12-28 at 15.44.40.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/707293/6b2c4a8f-c59e-cb0f-c208-e689f2dce652.png)

サンプルコード

```swift
let alert: UIAlertController = UIAlertController(title: "Alert表示", message: "hoge", preferredStyle: .alert)

        //OKボタン
        let defaultAction: UIAlertAction = UIAlertAction(title: "OK", style: .default, handler: {
            //ボタンが押された時の処理
            (action: UIAlertAction) -> Void in
            print("OK")
        })
        //キャンセルボタン
        let cancelAction: UIAlertAction = UIAlertAction(title: "キャンセル", style: .default, handler: {
            //ボタンが押された時の処理
            (action: UIAlertAction) -> Void in
            print("キャンセル")
        })

        //UIAlertControllerにActionを追加
        alert.addAction(defaultAction)
        alert.addAction(cancelAction)

        //Alertを表示
        present(alert, animated: true, completion: nil)
```

## ActionSheet
![Simulator Screen Shot - iPhone 11 - 2021-12-28 at 13.33.29.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/707293/cd4fa82e-a366-ce6c-9fe7-50ddd4e66b58.png)

```swift
let alert: UIAlertController = UIAlertController(title: "Alert表示", message: "hoge", preferredStyle: .alert)
```
を`actionSheet`に変更するだけです。
そして、置き換えたのが、以下のようなコードになります。

```swift
let alert: UIAlertController = UIAlertController(title: "Alert表示", message: "hoge", preferredStyle: UIAlertController.Style.actionSheet)

                //OKボタン
                let defaultAction: UIAlertAction = UIAlertAction(title: "OK", style: .default, handler: {
                    //ボタンが押された時の処理
                    (action: UIAlertAction) -> Void in
                    print("OK")
                })
                //キャンセルボタン
                let cancelAction: UIAlertAction = UIAlertAction(title: "キャンセル", style: .default, handler: {
                    //ボタンが押された時の処理
                    (action: UIAlertAction) -> Void in
                    print("キャンセル")
                })

                //UIAlertControllerにActionを追加
                alert.addAction(defaultAction)
                alert.addAction(cancelAction)

                //Alertを表示
                present(alert, animated: true, completion: nil)
```

## ボタンスタイル

表示については、以下の種類を選択することができます。

| UIAlertActionStyle | 説明 |
|:-:|:-:|
| default  |  文字：標準／複数指定可 |
|  cancel |  文字：ボールド／複数指定不可／最下部に位置固定 |
| destructive  |  文字：赤文字／複数指定可|

### Alert
![スクリーンショット 2021-12-28 16.38.37.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/707293/1209df25-4e1f-1401-2c1e-5e8ef8aa22c9.png)

### ActionSheet

![スクリーンショット 2021-12-28 16.42.56.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/707293/c5c3ce9c-69db-16bd-88d6-b7bc7ff07411.png)

以下は、Alertのサンプルコード

```swift
let alert: UIAlertController = UIAlertController(title: "Alert表示", message: "hoge", preferredStyle: .alert)
        //defaultボタン
        let defaultAction: UIAlertAction = UIAlertAction(title: "OK", style: .default, handler: {
            //ボタンが押された時の処理
            (action: UIAlertAction) -> Void in
            print("default")

        })
        //cancelボタン
        let cancelAction: UIAlertAction = UIAlertAction(title: "キャンセル", style: .cancel, handler: {
            //ボタンが押された時の処理
            (action: UIAlertAction) -> Void in
            print("cancel")

        })

        //destructiveボタン
        let destructiveAction: UIAlertAction = UIAlertAction(title: "キャンセル", style: .destructive, handler: {
            //ボタンが押された時の処理
            (action: UIAlertAction) -> Void in
            print("destructive")
        })

        //UIAlertControllerにActionを追加
        alert.addAction(defaultAction)
        alert.addAction(cancelAction)
        alert.addAction(destructiveAction)

        //Alertを表示
        present(alert, animated: true, completion: nil)
```

# 参考文献

https://qiita.com/funacchi/items/b76e62eb82fc8d788da5