<properties 
   pageTitle="Azure Automation Webhook"
   description="HTTP 呼び出しから Azure Automation の Runbook を開始することをクライアントに許可する Webhook。  この記事では、Webhook を作成する方法と、Webhook を呼び出して Runbook を開始する方法について説明します。"
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="bwren;sngun"/>

# Azure Automation Webhook

A *webhook* 、単一の HTTP 要求を通して Azure Automation で特定の runbook を開始することができます。 これにより、Visual Studio Team Services、GitHub などの外部サービス、またはカスタム アプリケーションにおいて、Azure Automation API を使用した完全なソリューションを実装していなくても、Runbook を開始することができます。

![Webhook](media/automation-webhooks/webhooks-overview.png)

Webhook runbook を開始してから、その他の方法を比較する [Azure Automation で runbook の開始](automation-starting-a-runbook.md)

## Webhook の詳細

次のテーブルは、Webhook 用に構成する必要があるプロパティについて説明しています。

| プロパティ | 説明 |
|:---|:---|
|名前 | Webhook に使用する任意の名前を指定できます。これはクライアントには公開されません。  これはユーザーが Azure Automation の Runbook を識別する場合にのみ使用されます。 <br>  ベスト プラクティスとして、webhook にそれを使用するクライアントに関連する名前を付ける必要があります。 |
|URL |Webhook の URL は、クライアントが Webhook にリンクされた Runbook を開始するために HTTP POST で呼び出す一意のアドレスです。  これは、Webhook を作成するときに自動的に生成されます。  カスタム URL を指定することはできません。 <br> <br>  URL には、さらに認証なしのサードパーティ製システムにより呼び出される runbook を許可するセキュリティ トークンが含まれています。 その理由で、これはパスワードと同じように扱う必要があります。  セキュリティ上の理由から、Webhook の作成時に Azure プレビュー ポータルで表示できるのは URL だけです。 将来の使用に備えて、URL を安全な場所にメモしてください。   |
|有効期限 | 証明書のように、各 Webhook には有効期限があり、それ以降は使用できなくなります。  Webhook の作成後に、この有効期限を変更することはできません。また、有効期限に達した後に、Webhook を再度有効にすることもできません。  この場合、別の Webhook を作成して現在の Webhook を置き換え、クライアントを更新して新しい Webhook を使用する必要があります。 |
| 有効 | 既定では、Webhook は作成時に有効になります。  Disabled に設定した場合、クライアントはそれを使用できなくなります。  設定することができます、 **有効** webhook またはその後はいつでも作成する場合のプロパティを作成します。 |


### パラメーター
Webhook は、Runbook がその Webhook によって開始されたときに使用される Runbook のパラメーターの値を定義できます。 Webhook には、Runbook の任意の必須パラメーターの値を含める必要があり、省略可能なパラメーターの値を含めることもできます。  1 つの Runbook にリンクされている複数の Webhook は、それぞれ異なるパラメーター値を使用することができます。

Webhook を使用して Runbook を開始した場合、クライアントは Webhook で定義されているパラメーターの値を上書きできません。  クライアントからデータを受信するには、runbook と呼ばれる 1 つのパラメーターを受け入れることができます **$WebhookData** [object] 型のクライアントが POST 要求にデータを格納するのです。 

![Webhookdata](media/automation-webhooks/webhookdata.png)

 **$WebhookData** オブジェクトには次のプロパティがあります。

| プロパティ | 説明 |
|:--- |:---|
| WebhookName | Webhook の名前。  |
| RequestHeader | 受信 POST 要求のヘッダーを含むハッシュ テーブル。 |
| RequestBody | 受信 POST 要求の本文。  文字列、JSON、XML、フォームのエンコード済みデータなどの書式設定が保持されます。 想定されるデータ形式を操作するには、Runbook を記述する必要があります。|


サポートするために必要な webhook の構成がない、 **$WebhookData** パラメーター、および runbook が、そのまま使用する必要はありません。  Runbook がパラメーターを定義していない場合は、クライアントから送信された要求の詳細は無視されます。

Webhook の作成時に $WebhookData の値を指定した場合、クライアントの要求本文にデータが含まれていなくても、クライアントの POST 要求からのデータで Webhook が Runbook を開始した時点でその値はオーバーライドされます。  Webhook 以外の方法を使用して $WebhookData が含まれる Runbook を開始する場合、Runbook で認識される $Webhookdata の値を指定することができます。  この値は、同じオブジェクト [プロパティ](#details-of-a-webhook) $Webhookdata として、webhook で渡される実際の WebhookData を扱っていたかのように runbook を正しく処理できるようにします。

たとえば、Azure ポータルから次の Runbook を開始するときにテスト用のサンプル WebhookData を渡す場合、WebhookData はオブジェクトであるため、UI に JSON として渡す必要があります。

![UI からの WebhookData パラメーター](media/automation-webhooks/WebhookData-parameter-from-UI.png)

上記の Runbook で、WebhookData パラメーターに次のプロパティがあるとします。

1. WebhookName: *MyWebhook*
2. RequestHeader: *= テストのユーザーから*
3. RequestBody: *["VM1","VM2"]*

この場合は、次の JSON 値を WebhookData パラメーター用の UI に渡します。  

* {"WebhookName":"MyWebhook"、"RequestHeader": {「から」:「ユーザーのテスト」}、"RequestBody":"[\"VM1\",\"VM2\"]"}

![UI から WebhookData パラメーターを開始する](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)


>[AZURE.NOTE] すべての入力パラメーターの値は、runbook のジョブに記録されます。  つまり、Webhook の要求でクライアントから提供された入力はすべて記録され、Automation ジョブにアクセスできるすべてのユーザーが使用できます。  このため、Webhook の呼び出しに機密情報を含める場合には注意する必要があります。

## セキュリティ

Webhook のセキュリティは、呼び出しを許可するセキュリティ トークンが含まれる URL のプライバシーに依存します。 正しい URL に対して要求がなされる場合、Azure Automation はその要求に対してどの認証も実行しません。 このため、要求を検証する他の方法を使用しない場合は、機密性の高い機能を実行する Runbook で Webhook を使用しないでください。

チェックして、webhook で呼び出されたことを決定する runbook 内にロジックを含めることができます、 **WebhookName** 、$WebhookData パラメーターのプロパティです。 Runbook がさらにして検証を実行の特定の情報を探す、 **RequestHeader** または **RequestBody** プロパティです。

別の方法として、Webhook 要求の受信時に外部条件のいくつかの検証を Runbook に実行させることです。  たとえば、GitHub リポジトリに新しいコミットがある場合は常に GitHub によって呼び出される Runbook について考えてみましょう。  Runbook は、新しいコミットが実際に発生したばかりであることを検証するために、GitHub に接続してから、処理を続行することができます。

## Webhook の作成

次の手順を使用して、Runbook にリンクされた新しい Webhook を Azure プレビュー ポータルに作成します。

1.  **Runbook ブレード** Azure プレビュー ポータルで、webhook がその詳細ブレードの表示を開始する runbook をクリックします。 
3. クリックして **Webhook** を開くには、ブレードの上部にある、 **Webhook の追加** ブレードです。 <br>
![Webhook ボタン](media/automation-webhooks/webhooks-button.png)
4. クリックして **新しい webhook を作成** を開くには、 **webhook の作成] ブレード**します。
5. 指定、 **名前**, 、**有効期限の日付** webhook と有効かどうか。 参照してください [webhook の詳細](#details-of-a-webhook) 詳細についてはこれらのプロパティです。
6. コピー アイコンをクリックし、Ctrl + C キーを押して Webhook の URL をコピーします。  次に、それを安全な場所に記録します。  **Webhook を作成した後は、URL を再度取得することはできません。** <br>
![Webhook の URL](media/automation-webhooks/copy-webhook-url.png)
3. クリックして **パラメーター** runbook パラメーターの値を指定します。  Runbook に必須のパラメーターがある場合、値を指定しない限り Webhook を作成できません。
1. クリックして **作成** webhook を作成します。


## Webhook の使用

Webhook を作成後に使用する場合、クライアント アプリケーションは Webhook の URL で HTTP POST を発行する必要があります。  Webhook の構文は、次の形式になります。

    http://<Webhook Server>/token?=<Token Value>

クライアントは、POST 要求から次のリターン コードのいずれかを受け取ります。  

| コード | テキスト | 説明 |
|:---|:----|:---|
| 202 | 承認済み | 要求が承認され、Runbook が正常にキューに入れられました。 |
| 400 | 正しくない要求 | 次のいずれかの理由で要求が受け入れられませんでした。 <ul> <li>Webhook の有効期限が切れています。</li> <li>Webhook は無効です。</li> <li>URL 内のトークンが正しくありません。</li>  </ul>|
| 404 | 見つかりません |  次のいずれかの理由で要求が受け入れられませんでした。 <ul> <li>Webhook が見つかりませんでした。</li> <li>Runbook が見つかりませんでした。</li> <li>アカウントが見つかりませんでした。</li>  </ul> |
| 500 | 内部サーバー エラー | URL は有効ですが、エラーが発生しました。  要求を再送信してください。  |

要求が成功したと仮定した場合、Webhook の応答には、次のような JSON 形式のジョブ ID が含まれています。 ここに含まれるジョブ ID は 1 つですが、JSON 形式は将来拡張できるようになっています。

    {"JobIds":["<JobId>"]}  

Runbook ジョブの完了時期と Webhook からの完了状態は、クライアントからは判別できません。  などの方法でジョブ id を使用してこの情報を決定できる [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) または [Azure Automation API](https://msdn.microsoft.com/library/azure/mt163826.aspx)します。

### 例

次の例では、Windows PowerShell を使用して Webhook で Runbook を開始します。  Webhook は、HTTP 要求を実行できる任意の言語で使用できます。ここでは、例として Windows PowerShell を使用します。

Runbook では、要求の本文に JSON 形式の仮想マシン一覧が必要です。 この例ではほかにも、要求のヘッダーに Runbook を開始したユーザーや日時などの情報を含めます。      

    $uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
    $headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}
    
    $vms  = @([pscustomobject]@{Name="vm01";ServiceName="vm01"})
    $vms += @([pscustomobject]@{Name="vm02";ServiceName="vm02"})
    $body = ConvertTo-Json -InputObject $vms 

    $response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
    $jobid = ConvertFrom-Json $response 


次の図は、ヘッダー情報を示します (を使用して、 [Fiddler](http://www.telerik.com/fiddler) トレース) この要求からします。 この中には、追加したカスタムの Date ヘッダーや From ヘッダー以外に、HTTP 要求の標準ヘッダーが含まれます。  これらの各値は、runbook で使用可能な **RequestHeaders** の **WebhookData**します。 

![Webhook ボタン](media/automation-webhooks/webhook-request-headers.png)

次の図は、要求の本文 (を使用して、 [Fiddler](http://www.telerik.com/fiddler) トレース) は runbook で利用できる、 **RequestBody** の **WebhookData**します。 要求の本文に含まれていた形式が JSON であったため、この形式は JSON になります。     

![Webhook ボタン](media/automation-webhooks/webhook-request-body.png)

次の図に、Windows PowerShell から送信される要求と、結果の応答を示します。  ジョブ ID が応答から抽出され、文字列に変換されます。

![Webhook ボタン](media/automation-webhooks/webhook-request-response.png)

次の例の Runbook では、前の例の要求を受け入れ、要求の本文で指定された仮想マシンを起動します。

    workflow Test-StartVirtualMachinesFromWebhook
    {
        param ( 
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            
            # Collect properties of WebhookData
            $WebhookName    =   $WebhookData.WebhookName
            $WebhookHeaders =   $WebhookData.RequestHeader
            $WebhookBody    =   $WebhookData.RequestBody
            
            # Collect individual headers. VMList converted from JSON.
            $From = $WebhookHeaders.From
            $VMList = ConvertFrom-Json -InputObject $WebhookBody
            Write-Output "Runbook started from webhook $WebhookName by $From."
            
            # Authenticate to Azure resources
            $Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
            Add-AzureAccount -Credential $Cred
            
            # Start each virtual machine
            foreach ($VM in $VMList)
            {
                $VMName = $VM.Name
                Write-Output "Starting $VMName"
                Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
            }
        }
        else {
            Write-Error "Runbook mean to be started only from webhook." 
        } 
    }


## Azure アラートに応じて Runbook を開始する

対応するため、Webhook が有効な runbook を使用できます [Azure アラート](Azure-portal/insights-receive-alert-notifications.md)します。 Azure 内のリソースは、Azure アラートを活用してパフォーマンス、可用性、および利用状況などの統計を収集することで監視できます。 Azure リソースの監視メトリックまたはイベントに基づいて通知を受け取ることができます。現在、Automation アカウントではメトリックしかサポートしていません。 指定したメトリックの値が割り当てられているしきい値を超えたとき、またはアラートを解決するのには、サービス管理者または共同管理者に、通知が送信し、構成されているイベントがトリガーされる場合は、メトリックとイベントの詳細についてを参照してください [Azure アラート](Azure-portal/insights-receive-alert-notifications.md)します。

Azure アラートを通知システムとして使用するだけでなく、アラートに応じて Runbook を開始することもできます。 Azure Automation には、Azure アラートを使用して Webhook 対応の Runbook を開始する機能があります。 メトリックが構成済みのしきい値を超えると、アラート ルールがアクティブになって Automation Webhook がトリガーされ、この Webhook により Runbook が実行されます。

![Webhook](media/automation-webhooks/webhook-alert.jpg)

### アラート コンテキスト

仮想マシンなどの Azure リソースを例に取ると、このマシンの CPU 使用率は重要なパフォーマンス メトリックの 1 つです。 長期間にわたり CPU 使用率が 100% になるか特定の値を超えた場合、仮想マシンを再起動して問題を解決する必要があります。 この問題は、CPU 使用率をメトリックとするアラート ルールを仮想マシンに構成することで解決できます。 ここで、CPU 使用率はほんの一例であり、Azure リソースに構成できるメトリックは他にも多数あります。また、この問題を解決するために行うアクションは仮想マシンの再起動ですが、他のアクションを実行するように Runbook を構成することもできます。

このアラート ルールがアクティブになって Webhook 対応の Runbook をトリガーするときに、ルールから Runbook にアラート コンテキストが送信されます。 [アラートのコンテキスト](Azure-portal/insights-receive-alert-notifications.md) などの詳細が含まれています **SubscriptionID**, 、**ResourceGroupName**, 、**ResourceName**, 、**ResourceType**, 、**ResourceId** と **タイムスタンプ** これらは runbook アクションでを取得してリソースを識別するのに必要です。 アラートのコンテキストがのボディ部に埋め込まれている、 **WebhookData** して runbook に送信されたオブジェクトを使用してアクセスできます **Webhook.RequestBody** プロパティ


### 例

Azure の仮想マシンを作成すると、サブスクリプションと関連付け、 [CPU のパーセンテージ メトリックを監視するアラート](Azure-portal/insights-receive-alert-notifications.md)します。 アラートの作成時には、[Webhook] フィールドに、Webhook の作成中に生成された Webhook の URL を必ず入力してください。

次のサンプル Runbook は、アラート ルールがアクティブになるとトリガーされ、アクション対象のリソースを特定するために Runbook が必要とするアラート コンテキストのパラメーターを収集します。

    workflow Invoke-RunbookUsingAlerts
    {
        param (     
            [object]$WebhookData 
        ) 

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            # Collect properties of WebhookData. 
            $WebhookName    =   $WebhookData.WebhookName 
            $WebhookBody    =   $WebhookData.RequestBody 
            $WebhookHeaders =   $WebhookData.RequestHeader 

            # Outputs information on the webhook name that called This 
            Write-Output "This runbook was started from webhook $WebhookName." 

            
            # Obtain the WebhookBody containing the AlertContext 
            $WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody) 
            Write-Output "`nWEBHOOK BODY" 
            Write-Output "=============" 
            Write-Output $WebhookBody 

            # Obtain the AlertContext     
            $AlertContext = [object]$WebhookBody.context

            # Some selected AlertContext information 
            Write-Output "`nALERT CONTEXT DATA" 
            Write-Output "===================" 
            Write-Output $AlertContext.name 
            Write-Output $AlertContext.subscriptionId 
            Write-Output $AlertContext.resourceGroupName 
            Write-Output $AlertContext.resourceName 
            Write-Output $AlertContext.resourceType 
            Write-Output $AlertContext.resourceId 
            Write-Output $AlertContext.timestamp 

            # Act on the AlertContext data, in our case restarting the VM. 
            # Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine. 
            $cred = Get-AutomationPSCredential -Name "MyAzureCredential" 
            Add-AzureAccount -Credential $cred 
            Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN" 
          
            #Check the status property of the VM
            Write-Output "Status of VM before taking action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
            Write-Output "Restarting VM"

            # Restart the VM by passing VM name and Service name which are same in this case
            Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName 
            Write-Output "Status of VM after alert is active and takes action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
        } 
        else  
        { 
            Write-Error "This runbook is meant to only be started from a webhook."  
        }  
    }

 

## 関連記事:

- [Runbook の開始](automation-starting-a-runbook.md)
- [Runbook ジョブの状態の表示](automation-viewing-the-status-of-a-runbook-job.md)
- [Using Azure Automation to take actions on Azure Alerts (Azure Automation を使用した Azure アラートに対するアクションの実行)](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/)


