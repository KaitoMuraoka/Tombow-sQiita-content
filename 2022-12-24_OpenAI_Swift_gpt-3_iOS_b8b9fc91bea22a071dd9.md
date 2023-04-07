<!--
title:   【Swift】OpenAIのGPT-3をSwiftで統合する方法
tags:    OpenAI,Swift,gpt-3,iOS
id:      b8b9fc91bea22a071dd9
private: false
-->
Swift/Kotlin愛好会 Advent Calendar 2022の18日の記事です。
Swift/Kotlin愛好会の皆様、とんとんぼと申します。Advent Calendarそのものが初参加です。よろしくお願いいたします🙇‍♂️

# はじめに

この記事では、まず初めにOpenAIとGPT-3について簡単に解説し、GPT-3モデルをiOSアプリに組み込む方法を紹介します。


# OpenAIとは
OpenAIとは、AIの研究・開発企業です。人類全体に利益をもたらす形で友好的なAIを普及・発展させることを目標に掲げています。
2015年にサム・アルトマン、イーロン・マスクらによってサンフランシスコで設立されました。

主なアプリケーションは、自然言語処理と画像生成モデル(VAE Clip)を組み合わせた**DALL-E**、**DALL-E2**、
強化学習ライブラリの**Gym**,
教師なし変換言語モデルの**GPT-3**
などがあります。


https://openai.com/


# GPT-3とは
事前学習による自然言語技術処理モデルの1つです。
大きな特徴は、まるで人が書いたように自然な文章を作成することができる点です。

GPT-3は、Web等から収集した45TBもの膨大なテキストデータのうち、いくつかの前処理を施した570GBのデータセットを学習に用いたデータセットに対して、 1,750億個のパラメータを持つ自己回帰型言語モデル（ある単語の次に出てくる単語を予測するモデル）を学習することで、 今まであった「BERT」や「GPT-2」のデータ学習量を遥かに超えた、巨大な言語モデルを形成しています。

広告やキャッチコピーやニュース記事作成など、今までのAIモデルや手法よりはるかに人間らしい文章を作成できるようになったと言われています。


https://www.data-artist.com/contents/gpt-3.html

# 本題：作成方法
新しいSwiftファイルを作成し、以下のコードを作成します。

```swift:OpenAIConnector.swift
import Foundation

public class OpenAIConnector {
    let openAIURL = URL(string: "")
    var openAIKey: String {
        return ""
    }

    private func executeRequest(request: URLRequest, withSessionConfig sessionConfig: URLSessionConfiguration?) -> Data? {
        let semaphore = DispatchSemaphore(value: 0)
        let session: URLSession
        if (sessionConfig != nil) {
            session = URLSession(configuration: sessionConfig!)
        } else {
            session = URLSession.shared
        }
        var requestData: Data?
        let task = session.dataTask(with: request as URLRequest, completionHandler:{ (data: Data?, response: URLResponse?, error: Error?) -> Void in
            if error != nil {
                print("error: \(error!.localizedDescription): \(error!.localizedDescription)")
            } else if data != nil {
                requestData = data
            }

            print("Semaphore signalled")
            semaphore.signal()
        })
        task.resume()

        // semaphoresで非同期処理。最大10秒の待ち時間
        let timeout = DispatchTime.now() + .seconds(20)
        print("Waiting for semaphore signal")
        let retVal = semaphore.wait(timeout: timeout)
        print("Done waiting, obtained - \(retVal)")
        return requestData
    }

    public func processPrompt(
        prompt: String
    ) -> Optional<String> {

        var request = URLRequest(url: self.openAIURL!)
        request.httpMethod = "POST"
        request.addValue("application/json", forHTTPHeaderField: "Content-Type")
        request.addValue("Bearer \(self.openAIKey)", forHTTPHeaderField: "Authorization")
        let httpBody: [String: Any] = [
            "prompt" : prompt,
            "max_tokens" : 100
        ]

        var httpBodyJson: Data

        do {
            httpBodyJson = try JSONSerialization.data(withJSONObject: httpBody, options: .prettyPrinted)
        } catch {
            print("Unable to convert to JSON \(error)")
            return nil
        }
        request.httpBody = httpBodyJson
        if let requestData = executeRequest(request: request, withSessionConfig: nil) {
            let jsonStr = String(data: requestData, encoding: String.Encoding(rawValue: String.Encoding.utf8.rawValue))!
            print(jsonStr)

            //MARK: - 以下、エラーが出ていますが、記事の後半で修正しますので、変更しないように注意してください
            let responseHandler = OpenAIResponseHandler()

            return responseHandler.decodeJson(jsonString: jsonStr)?.choices[0].text

        }
        return nil
    }
}
```

ここでエラーが発生すると思いますが、後に解決するので気にしないでください。

## API Keyの受け取り

さて、次にOpenAIのサイトへ行き、API　Keyを受け取ります。

右上にあるアカウントのアイコンをクリックし、`View API Keys`をクリックすると、自分のkeyが全て表示されたページに移動します。

:::note warn
ソースコードを公開する場合は、APIkeyを隠す必要があります。
:::

API　Keyを入手したら。`class OpenAIConnector`の`openAIKey`に貼り付けます。

```diff_swift:OpenAIConnector.swift
import Foundation

public class OpenAIConnector {
    let openAIURL = URL(string: "")
    var openAIKey: String {
+        return "sk-samplehogehogehogehogehogehogehogehogehoge"
    }

    private func executeRequest(request: URLRequest, withSessionConfig sessionConfig: URLSessionConfiguration?) -> Data? {
        let semaphore = DispatchSemaphore(value: 0)
        let session: URLSession
        if (sessionConfig != nil) {
            session = URLSession(configuration: sessionConfig!)
        } else {
            session = URLSession.shared
        }
        var requestData: Data?
        let task = session.dataTask(with: request as URLRequest, completionHandler:{ (data: Data?, response: URLResponse?, error: Error?) -> Void in
            if error != nil {
                print("error: \(error!.localizedDescription): \(error!.localizedDescription)")
            } else if data != nil {
                requestData = data
            }

            print("Semaphore signalled")
            semaphore.signal()
        })
        task.resume()

        // semaphoresで非同期処理。最大10秒の待ち時間
        let timeout = DispatchTime.now() + .seconds(20)
        print("Waiting for semaphore signal")
        let retVal = semaphore.wait(timeout: timeout)
        print("Done waiting, obtained - \(retVal)")
        return requestData
    }

    public func processPrompt(
        prompt: String
    ) -> Optional<String> {

        var request = URLRequest(url: self.openAIURL!)
        request.httpMethod = "POST"
        request.addValue("application/json", forHTTPHeaderField: "Content-Type")
        request.addValue("Bearer \(self.openAIKey)", forHTTPHeaderField: "Authorization")
        let httpBody: [String: Any] = [
            "prompt" : prompt,
            "max_tokens" : 100
        ]

        var httpBodyJson: Data

        do {
            httpBodyJson = try JSONSerialization.data(withJSONObject: httpBody, options: .prettyPrinted)
        } catch {
            print("Unable to convert to JSON \(error)")
            return nil
        }
        request.httpBody = httpBodyJson
        if let requestData = executeRequest(request: request, withSessionConfig: nil) {
            let jsonStr = String(data: requestData, encoding: String.Encoding(rawValue: String.Encoding.utf8.rawValue))!
            print(jsonStr)

            //MARK: - 以下、エラーが出ていますが、記事の後半で修正しますので、変更しないように注意してください
            let responseHandler = OpenAIResponseHandler()

            return responseHandler.decodeJson(jsonString: jsonStr)?.choices[0].text

        }

        return nil
    }
}
```

## モデルの選択

次に、どのモデルを使用するか選択します。OpenAIには4つのモデルがあり、以下の中からURLをコピーしてください。

|  モデル名  |  説明  |  URL  |
| ---- | ---- | ---- |
|  Davinci  |  どのモデルよりも強力で、どのモデルよりも高価です  | https://api.openai.com/v1/engines/text-davinci-002/completions |
|  Curie  |  Davinciよりも高速で安価ですが、若干パワーが劣ります  |　https://api.openai.com/v1/engines/text-davinci-002/completions　 |
|  Babbage  |  標準的なタスクが可能で、それほど強力ではないが、より速く、より安いです   |  https://api.openai.com/v1/engines/text-davinci-002/completions  |
|  Ada  |  他のものよりもずっと性能は劣りますが、最も速く、最も安価です  |  https://api.openai.com/v1/engines/text-davinci-002/completions  |

モデルの詳細な説明は以下のリンクにあります。

https://beta.openai.com/docs/models/gpt-3

モデルを選択し、URLをコピーしたら、`class OpenAIConnector`に戻り、`openAIURL`に貼り付けます。（サンプルコードでは、Adaを選択しました。）

```diff_swift:OpenAIConnector.swift
import Foundation

public class OpenAIConnector {
+    let openAIURL = URL(string: "https://api.openai.com/v1/engines/text-davinci-002/completions")
    var openAIKey: String {
        return "sk-samplehogehogehogehogehogehogehogehogehoge"
    }

    private func executeRequest(request: URLRequest, withSessionConfig sessionConfig: URLSessionConfiguration?) -> Data? {
        let semaphore = DispatchSemaphore(value: 0)
        let session: URLSession
        if (sessionConfig != nil) {
            session = URLSession(configuration: sessionConfig!)
        } else {
            session = URLSession.shared
        }
        var requestData: Data?
        let task = session.dataTask(with: request as URLRequest, completionHandler:{ (data: Data?, response: URLResponse?, error: Error?) -> Void in
            if error != nil {
                print("error: \(error!.localizedDescription): \(error!.localizedDescription)")
            } else if data != nil {
                requestData = data
            }

            print("Semaphore signalled")
            semaphore.signal()
        })
        task.resume()

        // semaphoresで非同期処理。最大10秒の待ち時間
        let timeout = DispatchTime.now() + .seconds(20)
        print("Waiting for semaphore signal")
        let retVal = semaphore.wait(timeout: timeout)
        print("Done waiting, obtained - \(retVal)")
        return requestData
    }

    public func processPrompt(
        prompt: String
    ) -> Optional<String> {

        var request = URLRequest(url: self.openAIURL!)
        request.httpMethod = "POST"
        request.addValue("application/json", forHTTPHeaderField: "Content-Type")
        request.addValue("Bearer \(self.openAIKey)", forHTTPHeaderField: "Authorization")
        let httpBody: [String: Any] = [
            "prompt" : prompt,
            "max_tokens" : 100
        ]

        var httpBodyJson: Data

        do {
            httpBodyJson = try JSONSerialization.data(withJSONObject: httpBody, options: .prettyPrinted)
        } catch {
            print("Unable to convert to JSON \(error)")
            return nil
        }
        request.httpBody = httpBodyJson
        if let requestData = executeRequest(request: request, withSessionConfig: nil) {
            let jsonStr = String(data: requestData, encoding: String.Encoding(rawValue: String.Encoding.utf8.rawValue))!
            print(jsonStr)

            //MARK: - 以下、エラーが出ていますが、記事の後半で修正しますので、変更しないように注意してください
            let responseHandler = OpenAIResponseHandler()

            return responseHandler.decodeJson(jsonString: jsonStr)?.choices[0].text

        }

        return nil
    }
}
```

最後に、`OpenAIResponseHandler.swift`ファイルを作成して、以下のコードを作成します。

```swift:OpenAIResponseHandler.swift
struct OpenAIResponseHandler {
    func decodeJson(jsonString: String) -> OpenAIResponse? {
        let json = jsonString.data(using: .utf8)!

        let decoder = JSONDecoder()
        do {
            let product = try decoder.decode(OpenAIResponse.self, from: json)
            return product

        } catch {
            print("Error decoding OpenAI API Response")
        }

        return nil
    }
}

struct OpenAIResponse: Codable {
    var id: String
    var object: String
    var created: Int
    var model: String
    var choices: [Choice]
}

struct Choice: Codable {
    var text: String
    var index: Int
    var logprobs: String?
    var finish_reason: String
}
```

以上で準備が完了しました。後は自分の好きな場所で`OpenAIConnector().processPrompt(prompt: )`という関数を呼び出すだけで、完了です。

# 最後に
OpenAIには他にも自然言語からプログラミング言語へ変換するCodexシリーズというサービスもあります。
気になるかたはぜひ参照してみてください

https://beta.openai.com/docs/models/codex