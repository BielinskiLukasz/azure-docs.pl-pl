<properties
    pageTitle="Azure Notification Hubs - よく寄せられる質問 (FAQ)"
    description="Notification Hubs におけるソリューションの設計と実装についての FAQ"
    services="notification-hubs"
    documentationCenter="mobile"
    authors="wesmc7777"
    manager="dwrede"
    editor="" />

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="11/25/2015" 
    ms.author="wesmc" />


# Azure Notification Hubs - よく寄せられる質問 (FAQ)

## 全般

### 1. Notification Hubs の料金を教えてください。

Notification Hubs は *Free*、*Basic*、*Standard* の 3 つのレベルで提供されます。[Notification Hubs の料金] の詳細については、ここで説明します。料金はサブスクリプション レベルで課金され、名前空間または通知ハブの数に関係なく、プッシュの数に基づいています。
Free レベルは、開発目的のために提供されており、SLA の保証はありません。Basic と Standard レベルは本番環境用に提供されています。次の主要機能は Standard レベルのみで有効になります。

- *リッチ テレメトリ* - Basic レベルではテレメトリ データや登録データのエクスポートは行えません。 オフライン表示と分析のためにテレメトリ データをエクスポートする必要がある場合、Standard レベルに移行する必要があります。
- *マルチ テナント機能* - Notification Hubs を使用してモバイル アプリを作成し、複数のテナントをサポートする場合、Standard レベルへの移行を検討する必要があります。これにより、アプリの通知ハブの名前空間レベルでプッシュ通知サービス (PNS) の資格情報を設定して、この共通の名前空間でテナントに個別のハブを提供し、分離ができます。これにより、テナント間の重複を確実に避けるために各テナント用に分離した通知ハブから通知を送受信する SAS キーを保持しつつ、簡単にメンテナンスをすることができます。
- *プッシュのスケジュール* - プッシュ通知がキューに配置され送信されるようにスケジュールすることができます。
- *一括インポート* - 登録を一括インポートすることができます。

### 2. SLA とは何ですか。

Basic と Standard の通知ハブ レベルでは、Basic や Standard の通知ハブ レベル内で導入した通知ハブに関しては少なくとも 99.9%、適切に構成されたアプリケーションが通知を送信したり、登録管理操作を実行できるように保証します。 SLA の詳細については、SLA を参照してください] ページは、ここでの [Notification Hubs の SLA] です。 Notification Hubs は外部のプラットフォーム プロバイダーに依存してデバイスに通知を送信するため、プラットフォーム通知サービスとデバイス間の区間における SLA の保証はありません。

### 3. Notification Hubs を使用している顧客を教えてください。

大勢のお客様が Notification Hubs をご使用になっています。次にいくつかご紹介します。

* ソチ 2014 年冬季オリンピック – 数百におよぶ利益団体、300 万台以上のデバイス、2 週間でディスパッチした通知 150 万件以上 [CaseStudy - ソチ]
* Skanska - [CaseStudy - Skanska]
* シアトル タイムズ - [CaseStudy - シアトル タイムズ]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Bing アプリ – 数千万台のデバイス、1 日あたり 300 万件の通知を送信

### 4.Notification Hubs をアップグレードまたはダウングレー年、サービス層を変更する方法は?

[Azure クラシック ポータル] に移動、Service Bus 名前空間で、通知ハブに] をクリックします。 [スケール] タブの下で、Notification Hubs サービス層を変更できます。

## 設計と開発

### 1. サポートするサービス側のプラットフォームはどれですか。

SDK と、.NET、Java、PHP、Python、Node.js のサンプルを提供しています。アプリのバックエンドを設定して、こうしたプラットフォームを使い Notification Hubs に通信できるようにします。 Notification Hubs の API は REST インターフェイスに基づいているので、これらと直接対話ができます。 詳しくは [NH - REST Api]

### 2. どのデバイスのプラットフォームをサポートしていますか。

Apple iOS、Android、Windows ユニバーサル、Windows Phone、Kindle、Android China (Baidu 経由)、Xamarin (iOS と Android)、Chrome アプリ プラットフォームへの通知の送信をサポートしています。はじめに」チュートリアルについてこれらのプラットフォームは、使用可能な次の手順を [NH - 使用] を「ステップ

### 3. SMS/メール/Web 通知はサポートしていますか。

Notification Hubs は、主に、上記のプラットフォームを使用してモバイル アプリに通知を送信することを目的としています。 メールや SMS への送信機能は提供していませんが、こうした機能を提供するサード パーティ製のプラットフォームを Notification Hubs に統合させて、Azure Mobile Services を使いネイティブのプッシュ通知を送信ができます。 また、すぐに使えるブラウザー内プッシュ通知も提供していません。 お客様は SignalR でこれを実装することもできます。 また、Google Chrome ブラウザーで作動する Chrome アプリへのプッシュ通知の送信についてのチュートリアルも提供しています。 この [Chrome アプリ チュートリアル]」を参照してください。

### 4. Azure Mobile Services と Azure Notification Hubs との関係について教えてください。いつ、どれを使えばいいのですか。

モバイル アプリのバックエンドが既にあり、プッシュ通知の送信機能だけを追加する場合は、Azure Notification Hubs を使う必要があります。 モバイル アプリのバックエンドを最初から設定する場合は、Azure Mobile Services の使用を検討する必要があります。 Azure モバイル サービスでは通知ハブを自動的にプロビジョニングするため、モバイル アプリのバックエンドから簡単にプッシュ通知を送信できます。 Azure Mobile Services の料金には、通知ハブの基本料金が含まれており、それに含まれるプッシュ数を超過する場合のみ料金を支払います。 詳しくは [Mobile Services の料金]

### 5. サポートできるデバイス数を教えてください。

Basic と Standard レベルでは、通知を受信できるアクティブなデバイスの数に制限はありません。詳細情報を参照してください: [Notification Hubs の料金]

### 6. 送信できるプッシュ通知の数を教えてください。

お客様は Azure Notification Hubs を使って毎日数百万のプッシュ通知を送信しています。 Notification Hubs の数を増やすためにお客様がすることは何もありません。 システムで流動する通知の数を基に、こちらで自動的に増やします。 送信するプッシュ通知に基づいて料金が左右することに注意してください。

### 7. 通知が自分のデバイスに到達するまでどのくらいの時間がかかりますか。

Azure Notification Hubs では、着信負荷が一定で急増することのない通常の使用シナリオにおいては、1 分間に少なくとも 100 万件の送信を処理できます。この数はタグの数や着信するメッセージなどの性質によって異なります。この間に、登録したタグやタグ式を基に、プラットフォームあたりのターゲットを計測し、各プッシュ通知サービスへメッセージを送信します。ここからデバイスに通知を送信するのはプッシュ通知サービス (PNS) の責任になります。PNS では、通知の送信に対する SLA を保証しませんが、通常ほとんどのメッセージはこちらのプラットフォームに送信された時から数分以内 (10 分以内) にデバイスに送信されます。中にはもう少し時間がかかるものもあります。また、Azure Notification Hubs には 30 分以内に PNS に送信できない通知を停止するポリシーがあります。こうした遅延にはさまざまな理由が考えられますが、最も一般的な理由は PNS がアプリケーションをスロットルするためです。

### 8. 遅延に対する保証はありますか。

プッシュ通知は外部プラットフォーム専用のプッシュ通知サービスによって送信されるという性質であるため、遅延に対する保証はありません。 通常、ほとんどの通知は数分以内に送信されます。

### 9. 名前空間と Notification Hubs を使用するソリューションを設計する際に考慮する必要がある点を教えてください。

*モバイル アプリ/環境:*
1 つの環境、1 つのモバイル アプリに対して通知ハブは 1 つにする必要があります。 複数テナントのシナリオでは、各テナントで個別のハブが必要になります。
この通知を送信する問題を引き起こす可能性は、テストおよび運用環境で同じ通知ハブを共有する必要があることはありません。 例: Apple では、それぞれ個別の資格情報と Sandbox と Production Push エンドポイントを提供します。 ハブがもともと Apple Sandbox の証明書で構成されており、後から Apple の本番用の証明書を使用するように再構成される場合、古いデバイスのトークンは新しい証明書で無効になり、プッシュが失敗する原因になります。 本番環境とテスト環境は別にし、個別の環境にはそれぞれのハブを使用することをお勧めします。

*PNS の資格情報:*
モバイル アプリがプラットフォームの開発者ポータル (Apple や Google など) で登録される場合、アプリの識別子とセキュリティ トークンを取得します。これはデバイスにプッシュ通知を送信できるようにするために、アプリのバックエンドがプラットフォームのプッシュ通知サービスに提供するものです。 セキュリティ トークンは、Notification Hubs で構成する必要がある証明書 (Apple iOS、Windows Phone など) やセキュリティ キー (Google Android、Windows など) の形式にできます。 これは通常、通知ハブ レベルで行われますが、マルチ テナントのシナリオでは名前空間レベルで行うこともできます。

*名前空間:*
名前空間は展開のグループ化にも使用できます。 また、マルチ テナントのシナリオで同じアプリのすべてのテナントにすべての通知ハブを表すことにも使用できます。

*地理的分散:*
地理的分散は常にプッシュ通知の場合に重要ではありません。 最終的にプッシュ通知をデバイスに送信するさまざまなプッシュ通知サービス (APNS、GCM など) が均等に分散されていないことに注意してください。 ただし、世界中で使われているアプリケーションがある場合は、別の名前空間で複数のハブを作成し、全世界のさまざまな Azure リージョンで Notification Hubs サービスの可用性を活用できます。 これは、特に登録に関する管理コストが増加するため、お勧めできません。本当に必要な場合のみ行ってください。

### 10.登録はアプリのバックエンドから行うべきですか。デバイスから直接行うべきですか。

アプリのバックエンドからの登録は、登録を作成する前にクライアント認証を実行する必要がある場合、アプリのロジックに基づいてアプリのバックエンドで作成もしくは変更する必要のあるタグがある場合に役立ちます。他のガイダンスが [登録の管理] こちらで入手できます (&) [バックエンド登録のガイダンス - 2] には

### 11.セキュリティ モデルとは何ですか。

Azure Notification Hubs では Shared Access Signature (SAS) ベースのセキュリティ モデルを使用しています。 SAS トークンはルートの名前空間レベルや詳細な通知ハブ レベルで使用できます。 こうした SAS トークンは、送信メッセージの許可やリッスン通知の許可などさまざまな承認規則で設定できます。詳しくは [NH セキュリティ モデル]

### 12.通知ではどのようにデリケートなペイロード情報が扱われますか。

すべての通知は、プラットフォームのプッシュ通知サービス (PNS) によってデバイスに送信されます。 送信者が Azure Notification Hubs に通知を送信すると、通知は処理されてそれぞれの PNS に渡されます。 送信者から Azure Notification Hubs に接続され、さらに HTTPS を使用する PNS へと繋がります。 Azure Notification Hubs でメッセージのペイロードが記録されることは決してありません。
ただし、デリケートなペイロードの送信には、セキュリティで保護されたプッシュのパターンをお勧めします。送信者はデリケートなのペイロードなしで、メッセージの識別子が付いた ping 通知をデバイスに送信できます。デバイスのアプリでこのペイロードを受信すると、セキュリティ保護されたアプリのバックエンド API を直接呼び出し、メッセージの詳細をフェッチします。 パターンを実装するチュートリアルは、[NH - 安全なプッシュのチュートリアル] では、

## 操作

### 1. 災害復旧 (DR) ストーリーとは何ですか。

Microsoft ではメタデータの災害復旧 (通知ハブの名前、接続文字列など) を提供します。 DR が発生する場合、消失するのは登録データですので、このデータを新しいハブに再入力するソリューションが必要になります。

- *ステップ 1* - 別の DC でセカンダリ通知ハブを作成します。 これは DR 発生時にすぐに作成できます。または最初から作成しておくこともできます。 NH プロビジョニングは数秒でできる高速プロセスですので、どちらでもあまり関係ありませんが、 最初から作成しておくことによって、DR 発生時の管理機能への影響からも保護できるので、こちらをお勧めします。

- *ステップ 2* - プライマリの通知ハブからの登録をセカンダリの通知ハブに利用します。 登録は入ってくるものであり、PNS により期限切れになる性質により正しく作動しないので、両方のハブで登録を管理し、最新に保つことはお勧めしません。 Notification Hubs では、期限が切れたり、無効な登録についての PNS のフィードバックを受信しながら、そうした登録をクリーンアップします。

次のいずれかのアプリのバックエンドを使用することをお勧めします。

- 一連の登録を管理し、DR 発生時にセカンダリの通知ハブに一括挿入できる

- バックアップとしてプライマリのハブから登録のダンプを定期的に取得し、セカンダリ NH に一括挿入する

(Standard レベルで利用可能な登録のエクスポート/インポート機能は次に示します [登録のエクスポート/インポート])。

バックエンドがない場合、アプリがデバイスで起動すると、セカンダリ NH で新規登録を実行します。セカンダリ NH には最終的に登録済みのアクティブなデバイスのすべてがあることになりますが、デバイスでアプリを起動しない場合に、通知を受信しない期間があるのが欠点です。

### 2. 監査ログ機能はありますか。

すべての通知ハブ管理操作は、[Azure クラシック ポータル] で公開されている操作ログに移動します。

## 監視とトラブルシューティング

### 1. どのようなトラブルシューティングの機能がありますか。

Azure Notification Hubs では、特にドロップされた通知に関する最も一般的なシナリオに共通するトラブルシューティングを行うための機能をいくつか提供しています。 このトラブルシューティングのホワイト ペーパーで詳細を参照してください [NH - トラブルシューティング]

### 2. どのようなテレメトリ機能がありますか。

Azure 通知ハブを利用 [Azure クラシック ポータル] で製品利用統計情報を表示します。 使用可能なメトリックの詳細については、使用可能な [メトリック] です。
通知の成功とは、外部の Push Notification サービス (Apple の APNS、Google の GCM など) に通知が送信されたことを意味します。通知をデバイスに送信するのは PNS 次第で、PNS ではこうしたメトリックを Microsoft に公表しておりません。  
また、プログラムによってテレメトリをエクスポートする機能も提供しています (Standard レベル)。 このサンプルの詳細 - [NH - メトリックのサンプル] を参照してください。


[azure classic portal]: https://manage.windowsazure.com 
[notification hubs pricing]: http://azure.microsoft.com/pricing/details/notification-hubs/ 
[notification hubs sla]: http://azure.microsoft.com/support/legal/sla/ 
[casestudy - sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942 
[casestudy - skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847 
[casestudy - seattle times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354 
[casestudy - mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592 
[casestudy - 7digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684 
[nh - rest apis]: https://msdn.microsoft.com/library/azure/dn530746.aspx 
[nh - getting started tutorials]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/ 
[chrome apps tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/ 
[mobile services pricing]: http://azure.microsoft.com/pricing/details/mobile-services/ 
[backend registration guidance]: https://msdn.microsoft.com/library/azure/dn743807.aspx 
[backend registration guidance - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx 
[nh security model]: https://msdn.microsoft.com/library/azure/dn495373.aspx 
[nh - secure push tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/ 
[nh - troubleshooting]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/ 
[nh - metrics]: https://msdn.microsoft.com/library/dn458822.aspx 
[nh - metrics sample]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel 
[registrations export/import]: https://msdn.microsoft.com/library/dn790624.aspx 

