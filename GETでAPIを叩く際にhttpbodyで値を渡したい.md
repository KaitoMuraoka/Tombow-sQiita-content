<!--
title: 【Swift】GETリクエストでAPIを活用し、パラメーターを送信する方法
tags:  Swift, API
-->

# 記事の背景
 APIを利用する際に、GETリクストで値を送信しようとしたところエラーが発生しました。
 そこで、この記事では、GETリクエストで値を送信するとなぜエラーが起こるのか、そしてGETリクエストで正しく値を送信する方法について説明します。

## 開発環境
- Swift
- Xcode 14.2

## エラーコード
`GET method must not have a body`
# 本論
## GETで値を渡すとエラーになる理由
エラーコードにも書いてある通り、基本的にGETメソッドではリクエストボディを指定してはいけません。

理由は主に2つあり、

- GETリクエストの目的に違反する
- HTTP/1.1の仕様

です。

### GETリクエストの目的 
[GETリクスト](https://www.rfc-editor.org/rfc/rfc2616#section-9.3)は、リソースを取得するために存在します。
リクエストボディは、リソースを取得するためのものです。
リクエストボディを使用すると、それが変更や作成を意味するPOSTやPUTリクエストと混同される危険性があります。
<!-- そのため、GETリクエストでは、リクエストボディを使用せず、クエリパラメータなどを利用して情報を送信する -->

### HTTP/1.1の仕様
[HTTP/1.1の仕様の4.3節](https://www.rfc-editor.org/rfc/rfc2616#section-4.3)において、GETリクエストはリクエストボディを持つべきではないとされています。
もし、GETリクエストボディを持たせると、サーバーやプロキシなどの中継機器がリクエストボディを無視する可能性があり、期待した結果が得られない場合があります。

以上の理由から、GETリクエストでリクエストボディを指定することは避けるべきです。

## GETリクエストで正しく値を送信する方法
上記の理由でGETリクエストでは、リクエストボディの指定ができないため、適切にデータを送信するには**クエリパラメータ**を活用しましょう。これにより、簡単かつ正確に値を伝えることができます。

クエリパラメータとは、WEBブラウザや他のアプリケーションがWEBサーバにデータを送信する際、送信先のURLの末尾に特定の形式で表記される情報です。

<!-- しかし、アプリによってはGETリクエスト以外にもPOSTやPUTなど他のリクエストも使用する場合があります。

そこで、Swiftの構造体メソッドを作成して、GETリクエストの場合とその他の場合で分けて処理をさせる良いです。 -->

しかし、アプリケーションによってはGETリクエストだけでなく、POSTやPUTなどの他のリクストも使われることがあります。

そこで、Swiftの構造体を活用し、GETリクエストとそれ以外のリクエストに分けて処理を行う方法が効果的です。

実際に、コードを見てみると、以下のようになります[^1]。

```Swift:Swift
enum HttpMethod: String {
    case get = "GET"
    case post = "POST"
    case put = "PUT"
    case delete = "DELETE"
    case patch = "PATCH"

    func urlRequest(url: URL, data: Data?) throws -> URLRequest? {
        var request = URLRequest(url: url)
        switch self {
        case .get:
            guard let data = data else {
                request.httpMethod = rawValue
                return request
            }

            guard var components = URLComponents(url: url, resolvingAgainstBaseURL: true),
                let dictionary = try JSONSerialization.jsonObject(with: data, options: []) as? [String: Any] else { return nil }

            components.queryItems = dictionary.map { URLQueryItem(name: $0.key, value: "\($0.value)") }
            guard let getUrl = components.url else { return nil }
            var request = URLRequest(url: getUrl)
            request.httpMethod = rawValue
            return request

        case .post, .put, .delete, .patch:
            request.httpMethod = rawValue
            request.httpBody = data
            return request
        }
    }
}
```
[^1]:このコードは[ある方のQiita記事](https://qiita.com/toya108/items/a74a6165f923f9cb0871#%E3%82%AF%E3%83%A9%E3%82%A4%E3%82%A2%E3%83%B3%E3%83%88%E6%9C%AC%E4%BD%93)を参考にさせていただきました。


# まとめ

 APIを活用する際、GETリクエストで値を送信しようとしてエラーが発生することがあります。
 これはGETリクエストの目的とHTTP/1.1の仕様および推奨事項に反しているためです。
 そのため、GETリクエストでリクエストボディを使用するのは避けるべきです。
 代わりに、値を送信する際にクエリパラメータを利用するのが効果的です。