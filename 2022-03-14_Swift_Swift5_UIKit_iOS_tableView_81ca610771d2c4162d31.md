<!--
title:   【iOS】TableViewのCell をスワイプして削除する
tags:    Swift,Swift5,UIKit,iOS,tableView
id:      81ca610771d2c4162d31
private: false
-->
# はじめに
これは半分メモ書きのような記事です。そこまで内容は深くありませんのでご了承のほどよろしくお願いいたします。

# 前提内容
画像のような簡単なToDoアプリを作成中です。タップしたらチェックマークが付き、再度タップするとチェックマークが外れる。iOSアプリ開発で入門的存在といて扱われるようなアプリです。
![Simulator Screen Shot - iPhone 11 - 2022-03-14 at 21.59.33.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/707293/ed2a8233-c749-278d-6758-b5aab3927da5.png)

# 目標
画像のように、削除用のボタンを左にスライドさせると出現します。
タップもしくはそのまま左にスライドさせるとCellが削除されます。
![Simulator Screen Shot - iPhone 11 - 2022-03-14 at 22.20.03.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/707293/1dd52103-259e-0d90-19df-3fe39cd8b5f9.png)

# やり方
方法はとてもシンプルです。``ViewController.swift``の中に次の方法を追加します。
`swift
override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
        // 1
        items.remove(at: indexPath.row)

        // 2
        let indexPaths = [indexPath]
        tableView.deleteRows(at: indexPaths, with: .automatic)
    }

`items`は配列です。この`items`の中に`TableView`のCellにある`Walk the dog`、`Brush my teeth`などの文字列やチェックマークについての情報が入っています。

## 説明
簡単に説明します。
まず、1の`items.remove(at: indexPath.row)`で配列からアイテムを削除します。

次に、2の`let indexPaths = [indexPath]`と`tableView.deleteRows(at: indexPaths, with: .automatic)`で、`TableView`から対応する行を削除します。

# 注意
`items.remove(at:)`を呼び出すと配列から取り出すだけでなく、オブジェクトへの参照がなくなるため、永久的に破棄されます。

# 最後に
今回は簡単なメモ投稿になります。最後まで読んでいただきありがとうございます。