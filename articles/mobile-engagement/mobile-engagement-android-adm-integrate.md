<properties
    pageTitle="Azure Mobile Engagement Android SDK の統合"
    description="Android SDK for Azure Mobile Engagement の最新の更新情報と更新手順について"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2015"
    ms.author="piyushjo" />


#ADM を Engagement に統合する方法

> [AZURE.IMPORTANT] このガイドの手順の前に、Android ドキュメントのエンゲージメントを統合する方法で説明する統合手順を従う必要があります。
>
> このドキュメントは、いつでも実施できるキャンペーンをサポートする Reach モジュールを統合した場合にのみ役立ちます。 アプリケーションに Reach キャンペーンを統合するには、Engagement Reach を Android に統合する方法に関するドキュメントを先にお読みください。

##はじめに

ADM を統合すると、Amazon Android デバイスを対象とした場合に、アプリケーションをプッシュできます。

SDK にプッシュされた ADM ペイロードのデータ オブジェクトには常に `azme` キーが含まれています。 そのため、アプリケーションで別の目的で ADM を使用する場合、そのキーに基づいてプッシュをフィルター処理できます。

> [AZURE.IMPORTANT] Amazon Kindle デバイスのみ Android 4.0.3 以降を実行している Amazon Device Messaging; によってサポートされるは上記のただし、その他のデバイスでも安全にこのコードを統合することができます。

##ADM にサインアップする

まだ実行してない場合は、ADM を Amazon アカウントで有効にする必要があります。

この手順の詳細: [<https://developer.amazon.com/sdk/adm/credentials.html>]します。

手順を完了すると、以下が取得されます。

-   Engagement がデバイスにプッシュできるようにする OAuth 資格情報 (クライアント ID とクライアント シークレット)。
-   アプリケーションに統合する必要がある API キー。

##SDK の統合

### デバイス登録の管理

各デバイスは ADM サーバーに登録コマンドを送信する必要があります。そうしないと、デバイスに接続できません。

既に使用している場合、 [ADM client library], 、いてまだ [integrated ADM] に android sdk-adm の受信を直接移動できます。

ADM をまだ統合してしない場合は、Engagement では簡単な方法でアプリケーション内でそれを有効にできます。

`AndroidManifest.xml` ファイルを編集します。

-   Amazon 名前空間を追加します。ファイルは次のように始まります。

        <?xml version="1.0" encoding="utf-8"?>
        <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                  xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   `<application/>` タグの中にこのセクションを追加します。

        <amazon:enable-feature
           android:name="com.amazon.device.messaging"
           android:required="false"/>

        <meta-data android:name="engagement:adm:register" android:value="true" />

-   amazon タグを追加した後、プロジェクトのビルド ターゲットが Android 2.1 より前の場合はビルド エラーが発生することがあります。 使用する必要がある、 **Android 2.1 +** ターゲットをビルドする (心配しないでください。 することができます、 `minSdkVersion` を 4 に設定)。
-   ADM API キーを次をアセットとして統合 [this procedure]します。

その後、次のセクションの指示に従います。

### 登録 ID を Engagement Push サービスに伝達し、通知を受信する

デバイスの登録 ID を Engagement Push サービスに伝達し、通知を受信するには、次を `AndroidManifest.xml` ファイルの `<application/>` タグ内に追加します (Engagement のない ADM を使用している場合でも)。

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

`AndroidManifest.xml` に次のアクセス許可があることを確認します (`</application>` タグの前にあります)。

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##Engagement OAuth 資格情報を与える

OAuth 資格情報 (クライアント ID とクライアント シークレット) を $/\#application/YOUR\_APPID/native-push で送信します。

これで、Reach 通知とポーリングを作成するときに [いつでも] を選択することができます。


[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[ADM client library]:https://developer.amazon.com/sdk/adm/setup.html
[integrated ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[this procedure]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset

