<properties
 pageTitle="Scheduler の概念、用語、およびエンティティ | Microsoft Azure"
 description="Azure Scheduler の概念、用語、およびエンティティ階層構造 (ジョブやジョブ コレクションなど)。  スケジュールされたジョブの全体をまとめた例を示します。"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# Scheduler の概念、用語集、エンティティ階層構造

## Scheduler エンティティの階層構造

次の表では、Scheduler API で公開または使用される主要なリソースについて説明します。

|リソース | 説明 |
|---|---|
|**クラウド サービス**|概念的には、クラウド サービスは、アプリケーションを表します。 サブスクリプションには、いくつかのクラウド サービスが含まれていることがあります。|
|**ジョブ コレクション**|ジョブ コレクションはジョブのグループを含み、コレクション内のジョブで共有される設定、クォータ、調整を保持します。 ジョブ コレクションは、サブスクリプションの所有者によって作成され、使用状況やアプリケーションの境界に基づいてジョブをグループ化します。 ジョブ コレクションは、1 つのリージョンに制限されます。 ジョブ コレクションでは、クォータを適用して、そのコレクション内のすべてのジョブの使用量を制限することもできます。 クォータには、MaxJobs と MaxRecurrence が含まれます。|
|**Job**|ジョブは、単純または複雑な実行方法による単一の反復的な操作を定義します。 操作には、HTTP 要求やストレージ キュー要求を含めることができます。|
|**ジョブ履歴**|ジョブ履歴は、ジョブの実行の詳細を表します。 応答の詳細と、ジョブの実行結果 (成功または失敗) が含まれます。|

## Scheduler エンティティの管理

大まかに言うと、Scheduler およびサービス管理 API は、リソースに対する次の操作を公開します。

|機能|説明と URI アドレス|
|---|---|
|**クラウド サービス管理**|クラウド サービスを作成と変更するための GET、PUT、および DELETE のサポート<p>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}`</p>|
|**ジョブ コレクションの管理**|ジョブ コレクションとジョブ コレクション内のジョブを作成と変更するための GET、PUT、および DELETE のサポート。 ジョブ コレクションはジョブのコンテナーであり、クォータと共有設定にマップされます。 後で説明するクォータの例としては、ジョブの最大数や最小の繰り返し間隔があります。 <p>PUT および DELETE: `https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/jobcollections/{jobCollectionName}`</p><p>GET: `https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}`</p>
|**ジョブの管理**|ジョブを作成と変更するための GET、PUT、POST、PATCH、および DELETE のサポート。 すべてのジョブは、暗黙的な作成はありませんが既に存在するジョブ コレクションに属する必要があります。 <p>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}/jobs/{jobId}`</p>|
|**ジョブ履歴の管理**|ジョブの経過時間やジョブの実行結果など、60 日分のジョブの実行履歴を取得するための GET のサポート。 状態およびステータスに基づいてフィルター処理のクエリ文字列パラメーターのサポートを追加します。 <P>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}/jobs/{jobId}/history`</p>|

## ジョブの種類

ジョブの種類には、HTTP ジョブ (SSL をサポートする HTTPS ジョブを含みます) とストレージ キュー ジョブの 2 つがあります。 HTTP ジョブは、既存のワークロードやサービスのエンドポイントがある場合に最適です。 ストレージ キュー ジョブを使用すればストレージ キューにメッセージを送信できるため、このジョブはストレージ キューを使用するワークロードに最適です。

## "ジョブ" エンティティの詳細

基本的なレベルでは、スケジュールされたジョブは、次のような要素で構成されます。

- ジョブ タイマーが起動するときに実行するアクション  

- (省略可能) ジョブを実行する時間  

- (省略可能) ジョブを繰り返し実行するタイミングと頻度  

- (省略可能) プライマリ アクションが失敗した場合に実行するアクション  

内部的には、スケジュールされたジョブには、システム指定のデータ (次のスケジュールされた実行時間など) も含まれています。

次のコードは、スケジュールされたジョブの全体をまとめた例です。 詳細については、後続のセクションで説明します。

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

上記のスケジュールされたジョブの例に示すように、ジョブ定義はいくつかの要素から構成されます。

- 開始時刻 ("startTime")  

- アクション ("action")。エラー時のアクション ("errorAction") が含まれています。

- 繰り返し ("recurrence")  

- 状態 ("state")  

- ステータス ("status")  

- 再試行ポリシー ("retryPolicy")  

以降では、これらについて詳しく説明します。

## startTime

"StartTime"は、開始時刻はでき、ネットワークにおけるタイム ゾーン オフセットを指定する、呼び出し元 [ISO 8601 形式](http://en.wikipedia.org/wiki/ISO_8601)します。

## action と errorAction

"action" は、それぞれの実行時に呼び出されるアクションであり、サービスの呼び出しの種類を示します。 アクションは、指定されたスケジュールに従って実行されます。 Scheduler では、HTTP アクションとストレージ キュー アクションをサポートしています。

上の例のアクションは HTTP アクションです。 ストレージ キュー アクションの例を次に示します。

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

"errorAction" はエラー ハンドラーであり、プライマリ アクションが失敗したときに呼び出されるアクションを示します。 この変数を使用すると、エラー処理エンドポイントを呼び出したり、ユーザー通知を送信したりできます。 これは、(たとえば、エンドポイントのサイトの障害が原因で) プライマリが使用できない場合にセカンダリ エンドポイントにアクセスしたり、エラー処理エンドポイントに通知したりするために使用できます。 エラー アクションは、プライマリ アクションと同じように、単純なロジックにすることも、他のアクションに基づいた複合型のロジックにすることもできます。 SAS トークンを作成する方法についてを参照してください [を作成し、共有アクセス署名を使用して](https://msdn.microsoft.com/library/azure/jj721951.aspx)します。

## recurrence

recurrence には、次の要素が含まれます。

- frequency: minute、hour、day、week、month、または year。  

- interval: 指定した頻度で繰り返しジョブを実行する間隔。  

- 所定の schedule: 繰り返しジョブを実行する minutes (分)、hours (時)、weekdays (曜日)、months (月)、および monthdays (日にち) を指定します。  

- count: 実行回数。  

- endTime: 指定された終了日時以降、ジョブは実行されません。  

JSON 定義に定期実行オブジェクトが指定されている場合、ジョブは定期的に実行されます。 count と endTime の両方を指定した場合、最初に実行される完了ルールが優先されます。

## state

ジョブの状態は、enabled、disabled、completed、faulted のいずれかになります。 PUT ジョブまたは PATCH ジョブを使用して、ジョブの状態を enabled または disabled に更新できます。 completed または faulted のジョブの状態は、更新できない最終的な状態です (ただし、ジョブを削除することはできます)。 state プロパティの例を次に示します。


        "state": "disabled", // enabled, disabled, completed, or faulted
completed または faulted のジョブは、60 日後に削除されます。

## status

Scheduler ジョブが開始すると、ジョブの現在のステータスに関する情報が返されます。 このオブジェクトは、ユーザーが設定することはできず、システムによって設定されます。 ただし、このオブジェクトは (別個のリンクされたリソースではなく) ジョブ オブジェクトに含まれるため、ジョブのステータスを簡単に取得できます。

ジョブのステータスには、前回の実行の時間 (存在する場合)、次回のスケジュールされた実行の時間 (進行中ジョブの場合)、ジョブの実行回数が含まれます。

## retryPolicy

Scheduler ジョブが失敗した場合、再試行ポリシーを指定して、アクションを再試行するかどうかと再試行の方法を指定できます。 によって決定される、 **retryType** オブジェクト-に設定されている **none** 上記のように、再試行ポリシーがない場合。 設定 **固定** 再試行ポリシーがある場合。

再試行ポリシーを設定するには、2 つの追加設定を指定することがあります: 再試行間隔 (**retryInterval**) と再試行回数 (**retryCount**)。

指定された再試行間隔は、 **retryInterval** オブジェクト、再試行の間隔。 既定値は 1 分です。最小値は 1 分、最大値は 18 か月です。 この値は、ISO 8601 形式で定義します。 再試行の回数の値が指定されている同様に、 **retryCount** オブジェクトです。 再試行を行う回数を超えることができます。 既定値は 5、および、最大値は 20 です。 両方とも **retryInterval** と **retryCount** は省略可能です。 場合に、既定値が与え、 **retryType** に設定されている **固定** 値が明示的に指定しないとします。

## 関連項目

 [Scheduler とは](scheduler-intro.md)
 
 [Azure ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [Azure Scheduler で複雑なスケジュールと高度な定期実行を構築する方法](scheduler-advanced-complexity.md)

 [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/dn528946)

 [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)

 [Azure Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)

 [Azure Scheduler 送信認証](scheduler-outbound-authentication.md)

