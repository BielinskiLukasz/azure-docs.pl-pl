<properties 
    pageTitle="Azure Notification Hubs - 診断ガイドライン" 
    description="Azure Notification Hubs に関する一般的な問題を診断する方法のガイドラインです。" 
    services="notification-hubs" 
    documentationCenter="Mobile" 
    authors="wesmc7777" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="NA" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/27/2015" 
    ms.author="wesmc"/>


# Azure Notification Hubs - 診断ガイドライン

## 概要

Azure Notification Hubs ユーザーから最もよく寄せられる質問の 1 つは、アプリケーション バックエンドから送信された通知がクライアント デバイスに表示されない理由を解明する方法です。具体的には、通知が破棄された場所とその原因、およびこの問題を解決する方法があります。 この記事では、通知が破棄されたり、デバイスに届かなかったりするさまざまな理由を説明します。 また、根本的な原因を分析して解明する方法も紹介します。

まず重要なのが、Azure Notification Hubs がデバイスに通知をプッシュする方法を理解することです。
![][0]

一般的な通知の送信フローでは、メッセージは**アプリケーション バックエンド**から **Azure Notification Hubs** に送信されます。Notification Hubs では、すべての登録に対して、構成されているタグとタグ式を考慮して処理を行い、「ターゲット」、つまりプッシュ通知を受信する必要があるすべての登録を特定します。一般的な通知の送信フローでは、メッセージはアプリケーション バックエンドから Azure Notification Hubs に送信されます。Notification Hubs では、すべての登録に対して、構成されているタグとタグ式を考慮して処理を行い、「ターゲット」、つまりプッシュ通知を受信する必要があるすべての登録を特定します。これらの登録は、iOS、Google、Windows、Windows Phone、Kindle、Baidu for China Android など、サポートされているプラットフォームのいずれかまたはすべてにわたる可能性があります。ターゲットが確立されると、次に Notification Hubs は、登録の複数のバッチに分割された通知をデバイス プラットフォーム固有の **Push Notification サービス (PNS)** (例: Apple の APNS、Google の GCM など) にプッシュします。Notification Hubs は、Azure クラシック ポータルの [Notification Hubs の構成] ページで設定した資格情報に基づいて、各 PNS への認証を実行します。その後、PNS は通知を各**クライアント デバイス**に転送します。これは、プッシュ通知を配信するためにプラットフォームで推奨されている方法です。通知の配信の最終段階はプラットフォームの PNS とデバイスの間で実行される点に注目してください。そのため、*クライアント*、*アプリケーション バックエンド*、*Azure Notification Hubs (NH)*、および*プッシュ通知サービス (PNS)* の 4 つの主要なコンポーネントがあり、そのいずれも、通知の脱落の原因となる可能性があります。このアーキテクチャの詳細については、[通知ハブの概要] でご確認いただけます。

初期のテストまたはステージング段階の期間中に通知の配信が失敗する場合は、構成の問題が発生している可能性があります。また、運用環境ですべてまたは一部の通知が破棄される場合は、より深刻なアプリケーションの問題やメッセージング パターンの問題が発生している可能性があります。 以下のセクションでは、一般的なシナリオから比較的珍しいケースまで、さまざまな通知の破棄シナリオについて説明します。中には自明だと思われる原因もありますが、意外と気付かないものも含まれています。

## Azure Notification Hubs の構成ミス

各 PNS に通知を正常に送信できるようにするためには、Azure Notification Hubs を開発者のアプリケーションのコンテキストで認証する必要があります。 これを実行するには、開発者が各プラットフォーム (Google、Apple、Windows など) に開発者アカウントを作成してから、資格情報を取得するアプリケーションを登録します。この資格情報は、ポータルの [Notification Hubs の構成] セクションで構成する必要があります。 通知が届かない場合、まずはプラットフォーム固有の開発者アカウントで作成されたアプリケーションと一致する正しい資格情報が Notification Hubs で構成されていることを確認する必要があります。 [作業の開始のチュートリアル] が表示されますステップ バイ ステップ方式でこの処理を移動すると便利です。 一般的な構成ミスの一部を以下に示します。

1. **全般**

    a) Notification Hubs の名前にタイプミスがなく、以下と一致することを確認します。

    - クライアントから登録する場所
    - バックエンドから通知を送信する場所
    - PNS の資格情報を構成した場所
    - クライアントとバックエンドで構成した SAS 資格情報を持つユーザー

    b) クライアントとアプリケーション バックエンドで正しい SAS 構成文字列を使用していることを確認します。 原則として、クライアントでは **DefaultListenSharedAccessSignature**、アプリケーションのバックエンドでは **DefaultFullSharedAccessSignature** を使用する必要があります (これにより、Notification Hubs に通知を送信できるようになります)。

2. **Apple Push Notification Service (APNS) の構成**

    運用環境とテスト用に 2 つの異なるハブを維持する必要があります。 つまり、サンドボックス環境で使用する証明書と運用環境で使用する証明書をそれぞれ別のハブにアップロードする必要があります。 後から通知エラーが発生する可能性があるため、異なる種類の証明書を同じハブにアップロードしないでください。 誤って異なる種類の証明書を同じハブに既にアップロードしている場合は、ハブを削除し、新たに開始することをお勧めします。 何らかの理由でハブを削除できない場合は、少なくとも、既存のすべての登録をハブから削除する必要があります。

3. **Google Cloud Messaging (GCM) の構成**

    a) クラウド プロジェクトで [Google Cloud Messaging for Android] を有効にしていることを確認します。

    ![][2]

    b) GCM への認証を実行するために Notification Hubs が使用する資格情報を取得するときに、「サーバー キー」を作成します。

    ![][3]

    c) クライアントで「プロジェクト ID」を構成していることを確認します。プロジェクト ID はすべて数値で構成されるエンティティで、ダッシュボードから取得することができます。

    ![][1]

## アプリケーションの問題

1) **タグ/タグ式**

対象ユーザーのセグメント化にタグまたはタグ式を使用している場合は、通知を送信するときに、送信呼び出しで指定したタグまたはタグ式に基づくターゲットが見つからない可能性が常にあります。登録を確認して、通知を送信するときに一致するタグがあることを確認し、それらが登録されているクライアントから受信した通知のみを検証することをお勧めします。たとえば、Notification Hubs へのすべての登録に「Politics」というタグを使用していて、「Sports」というタグを含む通知を送信した場合、この通知はデバイスに送信されません。複雑なケースでは、「Tag A」OR「Tag B」というタグ式のみを登録したにもかかわらず、「Tag A && Tag B」を対象として通知を送信するといった例が挙げられます。以下の「自己診断のヒント」のセクションでは、登録およびそれに含まれるタグを確認できる方法について説明しています。

2) **テンプレートの問題**

場合は、テンプレートを使用し、[テンプレート ガイダンス] に説明されているガイドラインに従っていることを確認します。

3) **無効な登録**

Notification Hubs が正しく構成されており、タグまたはタグ式が正しく使用されていて、通知を送信する必要のある有効なターゲットが見つかった場合、Notification Hubs は複数の処理のバッチを並行して開始し、各バッチが一連の登録にメッセージを送信します。
> [AZURE.NOTE] 並行して処理を行うため、通知が配信される順序は保証されません。 

現行の Azure Notification Hubs は、「最大 1 回の」メッセージ配信モデルに合わせて最適化されています。 つまり、デバイスに同じ通知が複数回配信されないように、重複の除去を行います。 これを確実に実行するために、実際にメッセージを PNS に送信する前に、登録全体を確認し、各デバイス ID にメッセージが 1 回のみ送信されることを確認しています。 各バッチが PNS へ送信され、登録の承認と検証が行われるときに、PNS によってバッチの 1 つまたは複数の登録でエラーが検出され、Azure Notification Hubs にエラーが返されて処理が停止し、そのバッチが完全に破棄される可能性があります。 これは、TCP ストリーム プロトコルを使用する　APNS に特に当てはまります。 最大 1 回の配信に最適化されているため、この失敗したバッチは再試行されません。これは、PNS によってバッチ全体が破棄されたか、部分的に破棄されたかを確実に把握することができないためです。 ただし、PNS はどの登録にエラーが発生したのかを Azure Notification Hubs に通知するので、そのフィードバックに基づいてデータベースからその登録を削除します。 この結果、1 つの登録バッチやそのサブセットが通知を受信しない可能性がありますが、不適切な登録を削除したため、次に送信が試行されるときには、正常に送信される可能性が高くなります。 ターゲット デバイスの数が多くなると (一部のお客様は、数百万台のデバイスに通知を送信しています)、一部で不適切なバッチが破棄されても、通知を受信するデバイスの全体的な割合はそれほど変わりませんが、少数の通知を送信して PNS のエラーが発生した場合は、すべてまたはほとんどの通知が受信されない可能性があります。 この動作が繰り返される場合は、不適切な登録を特定して削除する必要があります。 特に、手動で作成された登録は通知が破棄される最も一般的な原因であるため、必ず削除する必要があります。 テスト環境の場合は、アプリがデバイスで開かれたときに Notification Hubs への再登録を再試行するため、以降に作成されるすべての登録が有効になるように、既存のすべての登録を直接削除してください。

## PNS の問題

各 PNS が通知メッセージを受信した後は、デバイスに通知を配信する責任は PNS にあります。 このとき、Azure Notification Hubs は無関係であり、通知がデバイスに配信されるタイミングや、配信されるかどうかを制御することはできません。 プラットフォーム通知サービスは非常に堅牢なため、通常は PNS からデバイスに数秒で通知が送信されます。 ただし、PNS でスロットルが発生している場合、Azure Notification Hubs は指数バックオフ戦略を適用します。PNS に 30 分間送信できなかった場合は、ポリシーによって、メッセージを有効期限切れにして完全に削除します。

PNS が通知を配信しようとしてデバイスがオフラインである場合、通知は PNS によって一定期間保存され、デバイスにアクセス可能になったときに配信されます。 保存されるのは、特定のアプリにつき 1 つの最新の通知のみです。 デバイスがオフラインの間に複数の通知が送信された場合、新しい通知が配信されるたびに以前の通知が破棄されます。 この最新の通知のみを維持する動作は、APNS では結合通知と呼ばれ、GCM では折りたたみと呼ばれます (これには、折りたたみキーを使用します)。 デバイスが長時間オフラインのままである場合、保存されている通知は破棄されます 
ソースの構成-[APNS のガイダンス] & [GCM のガイダンス]

Azure Notification hubs では、ジェネリックを使用して HTTP ヘッダーを使用して結合キーを渡すことができます `SendNotification` API (例: .NET SDK の `SendNotificationAsync`) によりとして渡される HTTP ヘッダーは、各 PNS をします。

## 自己診断のヒント

このセクションでは、Notification Hubs の問題を診断し、根本原因を究明するためのさまざまな手段を説明します。

### 資格情報の確認

1. **PNS 開発者ポータル**

    各 PNS 開発者ポータル (APNS、GCM、WNS など)、[作業の開始のチュートリアル] を使用することを確認します。

2. **Azure クラシック ポータル**

    [構成] タブに移動して、資格情報を確認し、PNS 開発者ポータルから取得した資格情報と照合します。

    ![][4]

### 登録の確認

1. **Visual Studio**

    開発に Visual Studio を使用している場合は、Microsoft Azure に接続し、[サーバー エクスプローラー] から Notification Hubs を含む多数の Azure サービスを表示、管理することができます。 これは、特に開発/テスト環境で便利です。

    ![][9]

    ハブのすべての登録を表示、管理でき、プラットフォーム、ネイティブまたはテンプレートの登録、タグ、PNS 識別子、登録 ID、有効期限ごとに見やすく分類されています。 また、その場で登録を編集することもできます。これは、タグを編集する場合などに便利です。

    ![][8]
    > [AZURE.NOTE] Visual Studio の登録を編集する機能は、登録の数が限られている開発/テスト期間中にのみ使用してください。 登録を一括で修正する必要がある場合は、エクスポート/インポートを使用することを検討してください登録説明されている機能として、[エクスポート/インポートの登録] (https://msdn.microsoft.com/library/dn790624.aspx)

2. **Service Bus エクスプローラー**

    多くのお客様は、表示と管理、通知ハブの説明は、ここで [service Bus エクスプ ローラー] を使用します。 これは、オープン ソース プロジェクト code.microsoft.com - [service Bus エクスプ ローラーのコード]

### メッセージ通知の確認

1. **Azure クラシック ポータル**

    [デバッグ] タブから、サービス バックエンドを稼働させることなく、クライアントにテスト通知を送信することができます。

    ![][7]

2. **Visual Studio**

    Visual Studio から簡単にテスト通知を送信することもできます。

    ![][10]

    Visual Studio の Azure Notification Hubs エクスプローラーの機能の詳細については、以下を参照してください。

    - [VS サーバー エクスプ ローラーの概要]
    - [VS サーバー エクスプ ローラーに関するブログ記事 - 1]
    - [VS サーバー エクスプ ローラーに関するブログ記事 - 2]

### 失敗した通知のデバッグ/通知の出力の確認

**EnableTestSend プロパティ**

Notification Hubs を使用して通知を送信する場合、通知はまず単にキューに追加され、Notification Hubs がすべてのターゲットを特定する処理を実行してから、最終的に Notification Hubs によって PNS に送信されます。 そのため、REST API やクライアント SDK を使用していて送信呼び出しが正常に返されても、Notification Hubs でメッセージが正常にキューに追加されたということでしかありません。 Notification Hubs が最終的に PNS にメッセージを送信したときに何が起こったのかを確認することはできません。 クライアント デバイスに通知が届かない場合は、Notification Hubs が PNS にメッセージを配信しようとしたときに、エラー (ペイロードのサイズが PNS で許容されている上限を超えている、Notification Hubs で構成されている資格情報が無効である、など) が発生した可能性があります。 
PNS のエラーを把握、[enabletestsend] というプロパティが導入されています。 このプロパティは、ポータルまたは Visual Studio クライアントからテスト メッセージを送信するときに自動的に有効にされ、詳細なデバッグ情報を表示することができます。 これは、現在 .NET SDK などの API で利用可能で、将来的にすべてのクライアント SDK に追加される予定です。 これを REST 呼び出しで使用するには、送信呼び出しの最後に「test」というクエリ文字列パラメーターを追加します。以下がその例です。

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*例 (.NET SDK)*

たとえば、.NET SDK を使用して、ネイティブのトースト通知を送信するとします。

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);

`結果。状態` は単に状態 `エンキュー` 、プッシュに何が起こったのか、実行の最後にします。 
使用すれば、 `EnableTestSend` ブール型プロパティの初期化中に、 `NotificationHubClient` して、通知を送信中に発生した PNS エラーに関する詳細なステータスを取得できます。 このときの送信呼び出しは、Notification Hubs が PNS に通知を配信し、結果が確認された後に返されるため、通常よりも時間がかかります。

    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
    
    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);
    
    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*サンプル出力*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

このメッセージは、Notification Hubs で無効な資格情報が構成されているか、Notification Hubs の登録に問題であることを示しています。この登録を削除して、メッセージを送信する前にクライアントに再作成させることをお勧めします。
> [AZURE.NOTE] このプロパティを使用すると大幅なスロットルが発生するため、登録数の限られた開発/テスト環境でのみ使用してください。 デバッグ通知は 10 のデバイスのみに送信されます。 また、1 分あたりに処理できるデバッグ送信は 10 件に制限されています。 

### 統計情報の確認

1. **Azure クラシック ポータルの使用**

    ポータルでは、Notification Hubs のすべてのアクティビティの概要を簡単に確認できます。

    a) [ダッシュボード] タブでは、登録、通知、プラットフォーム別のエラーの集計データを表示できます。

    ![][5]

    b) また、[モニター] タブからその他多数のプラットフォーム固有のメトリックを追加して、Notification Hubs が PNS に通知を送信しようとしたときに返された PNS 固有のエラーの詳細を確認することもできます。

    ![][6]

    c) PNS 固有のエラーを確認するには、まず**受信メッセージ**、**登録操作**、**正常に送信された通知**を確認してから、プラットフォーム別のタブを確認します。

    d) Notification Hubs で認証設定が正しく構成されていない場合は、PNS 認証エラーが表示されます。 この場合は、PNS の資格情報を確認してください。

2) **プログラムによるアクセス**

詳細については、以下を参照してください。

- [プログラムによる統計情報のアクセス]
- [Api のサンプルを使用したテレメトリ アクセス]

> [AZURE.NOTE] **登録のエクスポート/インポート**や **API による統計情報へのアクセス**といった一部のテレメトリ関連機能は Standard レベルでのみ利用可能です。 Free または Basic レベルでこれらの機能を使用しようとすると、SDK を使用している場合はこの効果に対する例外メッセージが表示され、REST API から直接使用している場合は HTTP 403 (Forbidden) が表示されます。 Azure クラシック ポータルから Standard レベルに移行してください。  




[0]: ./media/notification-hubs-diagnosing/Architecture.png 
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png 
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png 
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png 
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png 
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png 
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png 
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png 
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png 
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png 
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png 
[notification hubs overview]: notification-hubs-overview.md 
[getting started tutorials]: notification-hubs-windows-store-dotnet-get-started.md 
[template guidance]: https://msdn.microsoft.com/library/dn530748.aspx 
[apns guidance]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4 
[gcm guidance]: http://developer.android.com/google/gcm/adv.html 
[export/import registrations]: http://msdn.microsoft.com/library/dn790624.aspx 
[servicebus explorer]: http://msdn.microsoft.com/library/dn530751.aspx 
[servicebus explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a 
[vs server explorer overview]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[vs server explorer blog post - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[vs server explorer blog post - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[enabletestsend feature]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx 
[programmatic telemetry access]: http://msdn.microsoft.com/library/azure/dn458823.aspx 
[telemetry access via apis sample]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel 

