<!--
title:   【iOS】新型コロナ(COVID19)についてのアプリは作ってはいけないって話
tags:    AppStore,apple,iOS
id:      fbd79f65b1e25dfc17d4
private: false
-->
今年の年末年始にアプリを作ってAppStoreに公開しようとしたら見事に玉砕したので供養として、またiOSアプリ開発ビギナーが同じ目にあって欲しくないと思い、投稿します。

# はじめに
今年の年末年始に新型コロナウイルスについての簡単なアプリを作りました。どのようなアプリかというと、累計感染者数と重症者数、死亡者数等を画面に表示。体調管理や都道府県ごとの感染者数を調べてみることができるというアプリでした。
作成後、AppStoreに提出。次の日にAppStoreConnectで確認するとリジェクトされていました。
![スクリーンショット 2022-01-15 17.16.58.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/707293/4f09951a-7da1-5a5f-3f32-67f3a0417100.png)


# リジェクトとその内容
リジェクトされるのは、よくあること(?)なので特に驚きはしませんでした。なんなら、「お！やっぱ来たか」くらい。

問題はその内容

>We found in our review that your app provides services or requires sensitive user information related to the COVID-19 pandemic. Since the COVID-19 pandemic is a public health crisis, services and information related to it are considered to be part of the healthcare industry. In addition, the seller and company names associated with your app are not from a recognized institution, such as a governmental entity, hospital, insurance company, non-governmental organization, or university.

>Per section 5.1.1 (ix) of the App Store Review Guidelines, apps that provide services or collect sensitive user information in highly-regulated fields, such as healthcare, should be submitted by a legal entity that provides these services, and not by an individual developer.

>Next Steps

>To resolve this issue, your app must be published under a seller and company name of a recognized institution. If you have developed this app on behalf of such an institution, please advise your client to add you to the development team of their Apple Developer account. If your client does not yet have an Apple Developer account, they can enroll for one as an organization through the Apple Developer website.

>Resources

>For additional details, please refer to the update on the Apple Developer website about Ensuring the Credibility of Health & Safety Information.

日本語にすると、以下のような文になります。

>私たちは、あなたのアプリがCOVID-19パンデミックに関連するサービスを提供したり、機密性の高いユーザー情報を必要とすることをレビューで発見しました。COVID-19パンデミックは公衆衛生上の危機であるため、それに関連するサービスや情報はヘルスケア産業の一部であると考えられます。また、アプリに関連する販売者や企業名は、政府機関、病院、保険会社、非政府組織、大学など、公認された機関のものではありません。

>App Store審査ガイドラインのセクション5.1.1 (ix)により、ヘルスケアなどの規制の厳しい分野でサービスを提供したり、ユーザーの機密情報を収集するアプリは、個人の開発者ではなく、これらのサービスを提供する法人によって提出されるべきとされています。

>次のステップ

>この問題を解決するには、アプリを認知された機関の販売者名と会社名で公開する必要があります。このアプリをそのような機関に代わって開発した場合、クライアントのApple Developerアカウントの開発チームにあなたを追加するよう、クライアントに助言してください。あなたのクライアントがまだApple Developerアカウントをお持ちでない場合は、Apple Developerウェブサイトから組織として登録することができます。

>リソース

>詳しくは、Apple Developerサイトの「健康と安全に関する情報の信頼性を確保するために」をご参照ください。

はえ〜知らなかった💦

つまり、「COVID19についてのアプリは**公認されている機関**ではないとダメ」ということ。個人での開発では問答無用でOUTってことになります。

確かに、ご存じの通り新型コロナのパンデミックは緊急性の高いものなので、個人でバンバン開発できたら根拠のない情報に溢れてしまいますしね。

## ガイドラインではどう書かれているか
では、リジェクト内容の"Per section 5.1.1 (ix) of the App Store Review Guidelines"の中身を確認してみると、以下のように書かれてました

https://developer.apple.com/app-store/review/guidelines/#data-collection-and-storage

>(ix) Apps that provide services in highly-regulated fields (such as banking and financial services, healthcare, gambling, legal cannabis use, and air travel) or that require sensitive user information should be submitted by a legal entity that provides the services, and not by an individual developer. Apps that facilitate the legal sale of cannabis must be geo-restricted to the corresponding legal jurisdiction.

一応、日本語に訳すと以下のように
>(ix) 規制の厳しい分野（銀行・金融サービス、医療、ギャンブル、合法大麻の使用、航空旅行など）のサービスを提供するアプリや、ユーザーの機密情報を必要とするアプリは、開発者個人ではなく、サービスを提供する法人が提出する必要があります。大麻の合法的な販売を促進するアプリは、対応する法的管轄区域に地理的に制限されなければなりません。

今回、この「規制の厳しい分野」に新型コロナが含まれていることになります。
「規制の厳しい分野」ってなかなかアバウトな表現でわかりにくい気がしますが・・・

# では、今後どうするのか？
今回の場合だと、完璧に個人開発なので、何も改善できない事になると思います。お蔵入りです。
4.2 Minimum Functionalityより詰んでます。

https://kanasys.com/tech/669

もし、会社等で代わりにリリースできるのならなんとかなるかも？そこについては、ご自身の環境などによって変わるので、ご自身の判断に任せます。

# 最後に
今回、作ったアプリが水の泡にならないように、この記事を書かせていただきました。

結果、私の作ったアプリはお蔵入りという形になりましたが、色々と得るものは技術的にも知識的にも多かったので、個人開発というのは大事であることが痛感させられました。また、ガイドラインはちゃんと読むことが大切であることがよくわかりました。

読みにくい文章でありながら、最後まで読んでいただきありがとうございます。