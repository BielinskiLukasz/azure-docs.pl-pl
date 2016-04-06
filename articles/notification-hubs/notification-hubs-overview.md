<properties
    pageTitle="Azure Notification Hubs"
    description="Azure でのプッシュ通知の使用方法について説明します。 コード サンプルは .NET API を使用して C# で記述されています。"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="wesmc"/>


#Azure Notification Hubs

##概要

Azure Notification Hubs は、任意のバックエンド (クラウドまたはオンプレミス内) から任意のモバイル プラットフォームにモバイル プッシュ通知を送信できるようにする、使いやすい、マルチプラットフォームのスケールアウトされたプッシュ インフラストラクチャです。

Notification Hubs を使用すると、異なるプラットフォーム通知システム (PNS: Platform Notification System) の詳細を抽象化して、クロスプラットフォームの個人用プッシュ通知を簡単に送信できます。 単一の API 呼び出しで、すべてのデバイスの個々のユーザー、または数百万のユーザーを含むセグメント全体を対象にすることができます。

Notification Hubs は、エンタープライズ向けシナリオとコンシューマー向けシナリオの両方で使用できます。 次に例を示します。

- 短い待機時間でニュース速報通知を数百万人に送信する (Notification Hubs は、すべての Windows および Windows Phone デバイスにプリインストールされた Bing アプリケーションの機能を強化します)。
- 場所に基づくクーポンをユーザー セグメントに送信する。
- イベント通知をスポーツ/金融/ゲーム アプリケーションのユーザーまたはグループに送信する。
- 新着メッセージ/メールやセールス リードなどのイベントをエンタープライズのユーザーに通知する。
- 多要素認証に必要なワンタイム パスワードを送信する。



##プッシュ通知とは?

スマートフォンやタブレットでは、イベントの発生時にそれをユーザーに "知らせる" 機能があります。 これらの通知はさまざまな形式を取ることができます。

Windows ストアと Windows Phone アプリケーションでは、通知の形式で指定できます、 _トースト_: 新しい通知を通知するため、効果音のモードレス ウィンドウが表示されます。 などの他の通知がサポートされます _タイル_, 、_生_, 、および _バッジ_ 通知します。 詳細については、Windows デバイスを参照してくださいでサポートされる通知の種類 [タイル、バッジ、および通知](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)します。

Apple iOS デバイスでは、プッシュはダイアログ ボックスを使用して通知され、ユーザーに通知を表示するか閉じるかを要求します。 クリックすると **ビュー** がメッセージを受信するアプリケーションを起動します。 IOS 通知の参照の詳細については [iOS 通知](http://go.microsoft.com/fwlink/?LinkId=615245)します。

プッシュ通知は、モバイル デバイスの電力消費を抑えながら、最新情報を表示するために有効な機能です。 通知は、デバイスの対応するアプリがアクティブになっていないときでも、バックエンド システムによってモバイル デバイスに送信できます。 プッシュ通知はコンシューマー アプリケーションの必須コンポーネントです。プッシュ通知を利用して、ユーザーをアプリケーションに惹き付け、使用率を向上させるためです。 エンタープライズ アプリケーションでも、通知は役立ちます。従業員に最新情報を通知することで、ビジネス イベントに対する即応性を向上することができます。

次に、モバイル デバイス関連のシナリオの具体例を示します。

1.  Windows 8 または Windows Phone のタイルに最新の財務情報を表示する。
2.  ワークフロー ベースのエンタープライズ アプリケーションで、ユーザーに作業項目が割り当てられたときに、トーストでユーザーに警告を表示する。
3.  CRM アプリケーション (Microsoft Dynamics CRM など) で、現在の潜在顧客の数を示すバッジを表示する。

##プッシュ通知の動作

プッシュ通知と呼ばれるプラットフォーム固有のインフラストラクチャを通じて配信 _プラットフォーム通知システム_ (PNS)。 PNS の機能は最小限であり、ブロードキャストや個人向け設定などはサポートされず、共通インターフェイスも用意されていません。 たとえば、Windows ストア アプリに通知を送信するには、WNS (Windows Notification Service) にアクセスする必要があります。また、iOS デバイスに通知を送信するには、まず APNS (Apple Push Notification サービス) にアクセスした後で、メッセージを送信する必要があります。 Azure Notification hubs は、共通のインターフェイスとその他の機能によって、プラットフォーム間でのプッシュ通知をサポートします。

ただし、大まかに言えば、すべてのプラットフォームの通知システムが同じパターンに従って動作します。

1.  クライアント アプリが PNS にアクセスを取得、 _処理_します。 ハンドルの種類はシステムにより異なります。 WNS の場合、ハンドルは URI または "通知チャネル" です。 APNS の場合、ハンドルはトークンです。
2.  クライアント アプリは、アプリケーションでこのハンドル _バック エンド_ 後で使用します。 WNS の場合、一般的にバックエンドはクラウド サービスになります。 Apple の場合、システムと呼ばれる、 _プロバイダー_します。
3.  プッシュ通知を送信する際には、アプリケーションのバックエンドがハンドルを使用して PNS にアクセスし、特定のクライアント アプリケーション インスタンスを対象として指定します。
4.  PNS はハンドルで指定されたデバイスに通知を転送します。

![][0]

##プッシュ通知の課題

これらのシステムは高度な機能を備えていますが、ブロードキャストやセグメント化されたユーザーへのプッシュ通知の送信などの一般的なプッシュ通知シナリオを実装するだけでも、アプリケーション開発者はさらに多くの課題を克服する必要があります。

プッシュ通知は、モバイル アプリケーション向けのクラウド サービスで最も需要の高い機能の 1 つです。 それは、プッシュ通知を機能させるために必要なインフラストラクチャが非常に複雑であることに加え、プッシュ通知の機能がアプリケーションの主要なビジネス ロジックと無関係であることが多いためです。 オンデマンド プッシュ インフラストラクチャを構築する際に課題となるのは、次のような点です。

- **プラットフォーム依存。** さまざまなプラットフォームのデバイスに通知を送信するには、複数のインターフェイスに対応するコードをバックエンドに用意する必要があります。 詳細な仕様が異なるだけでなく、通知の表示方法 (タイル、トースト、バッジなど) もプラットフォームに依存します。 このため、バックエンド コードは複雑化し、保守が困難になります。

- **拡張性。** このインフラストラクチャの拡張に関しては、次の 2 つの問題点があります。
    + PNS のガイドラインに従って、アプリが起動するたびにデバイス トークンを更新する必要があります。 そのため、デバイス トークンを最新の状態に維持するためだけに膨大なトラフィック (およびそれに伴うデータベース アクセス) が発生します。 デバイスの数が増加すると (数百万台に達する可能性があります)、このインフラストラクチャの作成と保守にかかるコストが無視できないものになります。

    + ほとんどの PNS は、複数のデバイスに対するブロードキャストをサポートしていません。 そのため、数百万台のデバイスに対してブロードキャストを実行すると、PNS に対して数百万回の呼び出しを実行することになります。 このように要求が増大する可能性があることは、重大な問題です。一般的に、アプリケーション開発者は合計遅延時間をできるだけ少なくしようとするためです (たとえば、最終的にメッセージを受信するデバイスが通知の送信後 30 分経ってもメッセージを受信しなければ、ほとんどの場合、プッシュ通知を使用する意味がなくなります)。
- **ルーティング。** PNS は、デバイスにメッセージを送信する機能を備えています。 しかし、ほとんどのアプリケーションでは通知の対象がユーザーまたは特定のグループです (特定の顧客アカウントに割り当てられた全従業員など)。 そのため、通知を適切なデバイスにルーティングするには、特定のグループにデバイス トークンを関連付けるためのレジストリをアプリケーションのバックエンドで維持する必要があります。 この対策を行うことで、市場投入までの開発期間が長引き、アプリケーションの保守コストも増大します。

##Notification Hubs を使用する理由は?

Notification Hubs は、複雑さを排除します。プッシュ通知の数々の課題を管理する必要はありません。 代わりに、通知ハブを使用できます。 Notification Hubs はマルチプラットフォームに完全対応する、スケールアウトされたプッシュ通知インフラストラクチャを使用しており、アプリケーションのバックエンドで実行するプッシュ通知用コードを大幅に減らすことができます。 Notification Hubs は、プッシュ インフラストラクチャのすべての機能を備えています。 次の図に示すように、デバイス側の処理は PNS ハンドルを登録するだけであり、バックエンドによってプラットフォームに依存しないメッセージがユーザーまたは対象グループに送信されます。

![][1]


通知ハブはすぐに利用できるプッシュ通知インフラストラクチャであり、次のメリットがあります。

- **複数のプラットフォーム。**
    +  すべての主要なモバイル プラットフォームをサポートします。 通知ハブからプッシュ通知を送信できるのは、Windows ストア アプリケーション、iOS アプリケーション、Android アプリケーション、および Windows Phone アプリケーションです。

    +  通知ハブは、サポート対象のすべてのプラットフォームに通知を送信する共通インターフェイスを備えています。 プラットフォーム固有のプロトコルは必要ありません。 アプリケーションのバックエンドから、通知をプラットフォームの独自形式で送信することも、プラットフォームに依存しない形式で送信することもできます。 アプリケーションは、Notification Hubs とのみ通信します。

    +  デバイス ハンドル管理。 Notification Hubs はハンドル レジストリと PNS からのフィードバックを管理します。

- **任意のバックエンドが連携する**: クラウドまたは内部設置型、.NET、PHP、Java、ノードなどです。

- **拡張性。** 通知ハブは再設計やシャーディングを行わなくても、数百万のデバイスに対応できます。


- **豊富な配信パターン**:

    - *ブロードキャスト*: により、単一の API 呼び出しで多くのデバイスにブロードキャストをほぼ同時に発生します。

    - *ユニキャスト/マルチキャスト*: 自分のデバイスのすべてを含む個々 のユーザーを表すタグへのプッシュまたは広範囲なグループに、たとえば、別々 のフォーム ファクター (タブレットとスマート フォン)。

    - *セグメント化*: タグ式 (たとえば、ヤンキースをフォロー ニューヨークのデバイス) によって定義された複雑なセグメントにプッシュします。

    各デバイスは、通知ハブにハンドルを送信する際に、1 つ以上を指定できる _タグ_します。 詳細については [tags]します。 タグは事前に定義したり、破棄したりする必要はありません。 タグを使用して、通知を簡単にユーザーや対象グループに送信できます。 タグにはアプリケーション独自の識別子 (ユーザー ID、グループ ID など) を含めることができるため、タグを使用することで、アプリケーションのバックエンドでデバイス ハンドルの格納と管理の処理を行う必要がなくなります。

- **パーソナル化**: 各デバイスは、バックエンド コードに影響を与えずにデバイスごとのローカライゼーションと個人用設定を実現するために、1 つまたは複数のテンプレートを持つことができます。

- **セキュリティ**: 共有アクセス シークレット (SAS) またはフェデレーション認証。

- **リッチ テレメトリ**: 利用可能なポータルでプログラム的です。


##App Service Mobile Apps との統合

Facililate シームレスな Azure サービス間でのエクスペリエンスを統合することを [App Service Mobile Apps] Notification Hubs を使用したプッシュ通知用の組み込みサポートを備えています。 [App Service Mobile Apps] 企業の開発者やシステム インテグレーター向けのモバイル開発者に豊富な機能を提供するには、拡張性の高い、グローバルに利用可能なモバイル アプリケーション開発プラットフォームを提供します。

Mobile Apps の開発者は、次のワークフローで Notification Hubs を使用します。

1. デバイスの PNS ハンドルを取得する
2. デバイスを登録し、 [templates] 便利なモバイル アプリ クライアント SDK で Notification Hubs の登録 API
    + Mobile Apps ではセキュリティの目的で登録時にすべてのタグを除去することに注意してください。 タグをデバイスに関連付けるには、Notification Hubs をバックエンドから直接操作します。
3. Notification Hubs を使用してアプリのバックエンドから通知を送信する

この統合は、開発者にいくつかの便利な機能を提供します。
- **Mobile Apps クライアント SDK。** これらの複数プラットフォームの SDK は登録用のシンプルな API を提供し、モバイル アプリに自動的にリンクされている通知ハブと対話します。 開発者は Notification Hubs の資格情報を掘り下げて追加のサービスを操作する必要はありません。
    + SDK は自動的に指定のデバイスを Mobile Apps の認証済みユーザー ID にタグ付けしてユーザー シナリオへのプッシュを有効にします。
    + SDK は自動的に Mobile Apps のインストール ID を Notification Hubs に登録する GUID として使用し、開発者が複数サービスの GUID を維持する手間を省きます。
- **インストール モデル。** Mobile Apps は Notification Hubs の最新のプッシュ モデルと簡単に連携し、プッシュ通知サービスと連携する JSON インストール内のデバイスに関連付けられたすべてのプッシュ プロパティを表します。
- **柔軟性。** 開発者は統合中でも常に Notification Hubs を直接操作することを選択できます。
- **統合された環境で [Azure Portal]します。** 機能としてのプッシュが Mobile Apps に視覚的に表示され、開発者は Mobile Apps を通じて関連付けられた通知ハブを簡単に操作できます。



##次のステップ

Notification Hubs の詳細については、次のトピックを参照してください。

+ **[ユーザーは Notification Hubs をどのように使用しているか]**

+ **[Notification Hubs のチュートリアルとガイド]**

+ **Notification Hubs の使用チュートリアル** ([iOS], 、[Android], 、[Windows Universal], 、[Windows Phone], 、[Kindle], 、[Xamarin.iOS], 、[Xamarin.Android])

プッシュ通知の関連する .NET マネージ API リファレンスについては、次を参照してください。

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [How customers are using Notification Hubs]: http://azure.microsoft.com/services/notification-hubs
  [Notification Hubs tutorials and guides]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [App Service Mobile Apps]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [templates]: notification-hubs-templates.md
  [Azure Portal]: https://portal.azure.com
  [tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)


