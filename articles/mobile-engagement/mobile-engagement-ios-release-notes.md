<properties
    pageTitle="Azure Mobile Engagement iOS SDK リリース ノート"
    description="Azure Mobile Engagement 用 iOS SDK の最新の更新プログラムと手順"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="MehrdadMzfr"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="08/05/2015"
    ms.author="MehrdadMzfr" />


# リリース ノート

## 3.2.1 (12/11/2015)

-   新しいアプリケーション インスタンスがディープ リンクを持つ通知によってトリガーされたときの遅延を修正。

## 3.2.0 (10/08/2015)

-   **Xcode 7** と連携できるように、SDK のビットコードを有効化。
-   アプリ内通知に関連するバグを修正。
-   バッテリ低下のような状況でのアプリ内通知の信頼性を向上。
-   サード パーティ製のライブラリによって生成される余分なコンソール ログの削除。

## 3.1.0 (2015 年 8 月 26 日)

-   サードパーティ ライブラリと iOS 9 の互換性バグを修正。 ポーリングの結果、アプリケーション情報、追加のデータの送信時にクラッシュを引き起こしていました。

## 3.0.0 (06/19/2015)

-   Mobile Engagement では、サイレント プッシュ通知が使用されます。
-   iOS 4.X のサポートが終了。 このバージョンから、アプリケーションのデプロイ ターゲットは iOS 6 以降である必要があります。

## 2.2.0 (05/21/2015)

-   iOS 6 より前のデバイスでは、Mobile Engagement のデバイス ID がインストール時に生成された GUID に基づくようになりました。

## 2.1.0 (04/24/2015)

-   Swift 互換性を追加しました。
-   通知をクリックすると、アプリケーションの起動直後にアクション URL が実行されるようになりました。
-   SDK パッケージに足りないヘッダー ファイルを追加しました。
-   Mobile Engagement のクラッシュ レポートが無効になる問題を修正しました。

## 2.0.0 (02/17/2015)

-   Azure Mobile Engagement の最初のリリース
-   appId/sdkKey 構成は、接続文字列の構成で置き換えられます。
-   任意の XMPP エンティティから任意の XMPP メッセージを送受信する API が削除されました。
-   デバイス間でメッセージを送受信する API が削除されました。
-   セキュリティの強化。
-   SmartAd の追跡が削除されました。





