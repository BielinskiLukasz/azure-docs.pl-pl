<properties 
   pageTitle="Runbook Output and Messages in Azure Automation | Microsoft Azure"
   description="Azure Automation で Runbook から出力とエラー メッセージを作成および取得する方法を説明します。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/24/2015"
   ms.author="bwren" />


# Azure Automation での Runbook の出力および メッセージ

Azure Automation のほとんどの Runbook では、ユーザーに対するエラー メッセージや別のワークフローで使用するための複合オブジェクトなど、いくつかの出力形式が含まれます。 Windows PowerShell では、 [複数のストリーム](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) スクリプトまたはワークフローから出力を送信します。 Azure Automation はこれらのストリームごとに異なる方法で対応するため、Runbook を作成する場合は、それぞれの使用法のベスト プラクティスに従ってください。

次の表は、公開された runbook を実行している場合との両方の各ストリームと、Azure 管理ポータルでの動作の簡単な説明を示します [runbook をテストする](http://msdn.microsoft.com/library/azure/dn879147.aspx)します。 各ストリームの詳細については、後続のセクションで説明します。

| ストリーム| 説明| 公開先| テスト|
|:---|:---|:---|:---|
| 出力| 他の Runbook によって使用されることを目的とするオブジェクト。| ジョブ履歴に書き込まれます。| [テスト出力] ウィンドウに表示されます。|
| 警告| ユーザー向けの警告メッセージ。| ジョブ履歴に書き込まれます。| [テスト出力] ウィンドウに表示されます。|
| エラー| ユーザー向けのエラー メッセージ例外とは異なり、既定では Runbook はエラー メッセージ発行後に実行を継続します。| ジョブ履歴に書き込まれます。| [テスト出力] ウィンドウに表示されます。|
| 詳細| 全般的なまたはデバッグ情報を提供するメッセージ。| Runbook の詳細ログが有効な場合のみ、ジョブ履歴に書き込まれます。| Runbook で $VerbosePreference が Continue に設定されている場合のみ、[テスト出力] ウィンドウに表示されます。|
| 進捗状況| Runbook の各アクティビティの前後に自動的に生成されるレコード。Runbook では、独自の進行状況レコードを作成しないでください。これらのレコードは対話型ユーザー向けです。| Runbook の進行状況ログが有効な場合のみ、ジョブ履歴に書き込まれます。| [テスト出力] ウィンドウには表示されません。|
| デバッグ| 対話型ユーザー向けのメッセージ。Runbook では使用しないでください。| ジョブ履歴には書き込まれません。| [テスト出力] ペインには書き込まれません。|

## 出力ストリーム

出力ストリームはスクリプトまたはワークフローが正しく実行された場合に作成されるオブジェクト出力のためのものです。 Azure Automation でのこのストリームは主に使用での使用を目的としたオブジェクト [親の現在の runbook を呼び出す runbook](automation-child-runbooks.md)します。 ときに、 [インラインで runbook を呼び出す](automation-child-runbooks.md/#InlineExecution) 親 runbook からデータが返されます出力ストリームから親にします。 ユーザーに一般情報を返すときは、その Runbook が別の Runbook から呼び出されないことがわかっている場合にのみ、出力ストリームを使用してください。 ベスト プラクティスとしては通常、使用、 [詳細ストリーム](#Verbose) ユーザーに一般情報を通信します。

データを使用して、出力ストリームを書き込むことができます [Write-output](http://technet.microsoft.com/library/hh849921.aspx) か、または runbook では、その行でオブジェクトを配置します。

    #The following lines both write an object to the output stream.
    Write-Output –InputObject $object
    $object

### 関数からの出力

Runbook に含まれている関数から出力ストリームに書き込むと、出力は Runbook に戻されます。 Runbook がその出力を変数に代入する場合、出力は出力ストリームに書き込まれません。 関数内から他のストリームへの書き込みは、Runbook の対応するストリームに書き込まれます。

次のサンプル Runbook で考えてみましょう。

    Workflow Test-Runbook
    {
       Write-Verbose "Verbose outside of function"
       Write-Output "Output outside of function"
       $functionOutput = Test-Function
    
       Function Test-Function
       {
          Write-Verbose "Verbose inside of function"
          Write-Output "Output inside of function"
       }
    }

Runbook ジョブの出力ストリームは次のようになります。

    Output outside of function

Runbook ジョブの詳細ストリームは次のようになります。

    Verbose outside of function
    Verbose inside of function

### 出力のデータ型の宣言

ワークフローを使用してその出力のデータ型を指定できます、 [OutputType 属性](http://technet.microsoft.com/library/hh847785.aspx)します。 この属性は実行時に影響はありませんが、設計時にRunbook 作成者に対して Runbook の予想される出力を通知します。 Runbook 用のツールセットは進化し続けるため、設計時に出力のデータ型を宣言することの重要性は高まり続けます。 その結果、ベスト プラクティスは、作成するすべての Runbook にこの宣言を含めることです。

次のサンプル Runbook は、文字列オブジェクトを出力し、その出力の型宣言が含まれています。 Runbook が特定の型の配列を出力する場合でも、型の配列ではなく、型を指定してください。

    Workflow Test-Runbook
    {
       [OutputType([string])]
    
       $output = "This is some string output."
       Write-Output $output
    }

## メッセージ ストリーム

出力ストリームとは異なり、メッセージ ストリームはユーザーに情報を伝えるためのものです。 さまざまな種類の情報向けに複数のメッセージ ストリームがあり、それぞれが異なる方法で Azure Automation で処理されます。

### 警告およびエラー ストリーム

警告およびエラー ストリームは、Runbook で発生する問題をログ記録するためのものです。 それらは、Runbook が実行されたときにジョブ履歴に書き込まれ、Runbook がテストされたときに Microsoft Azure 管理ポータルの [テスト出力] ウィンドウに表示されます。 既定では、Runbook は警告またはエラーの後も引き続き実行されます。 設定して、警告またはエラー時に、runbook を中断する必要がありますを指定することができます、 [ユーザー設定変数](#PreferenceVariables) メッセージを作成する前に、runbook にします。 たとえば、Runbook を例外の場合と同様、エラーで中断するようにするには、**$ErrorActionPreference** を Stop に設定します。

使用して、警告またはエラー メッセージを作成、 [Write-warning](https://technet.microsoft.com/library/hh849931.aspx) または [Write-error](http://technet.microsoft.com/library/hh849962.aspx) コマンドレットです。 アクティビティによってストリームに書き込むことができる場合もあります。

    #The following lines create a warning message and then an error message that will suspend the runbook.
    
    $ErrorActionPreference = "Stop"
    Write-Warning –Message "This is a warning message."
    Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."

### 詳細ストリーム

詳細メッセージ ストリームは、Runbook の操作に関する一般情報用です。  [デバッグ ストリーム](#Debug) では、runbook では、デバッグ情報の詳細メッセージが使用する必要があります。 既定では、公開された Runbook の詳細メッセージはジョブ履歴に格納されません。 詳細メッセージを格納するには、Microsoft Azure 管理ポータルの Runbook の [構成] タブで、公開された Runbook を詳細レコードを記録するように構成します。 ほとんどの場合、パフォーマンス上の理由から、詳細レコードを記録しないというRunbook の既定の設定を保持する必要があります。 このオプションを有効にするのは、Runbook のトラブルシューティングやデバッグをする場合のみです。

[Runbook をテストする](http://msdn.microsoft.com/library/azure/dn879147.aspx), 、詳細レコードを記録する、runbook が構成されている場合でも、詳細メッセージが表示されません。 中に詳細メッセージを表示する [runbook をテストする](http://msdn.microsoft.com/library/azure/dn879147.aspx), 、続行するか、$VerbosePreference 変数を設定する必要があります。 この変数が設定されると、Azure 管理ポータルの [テスト出力] ペインに詳細メッセージが表示されます。

使用して、詳細メッセージを作成、 [Write-verbose](http://technet.microsoft.com/library/hh849951.aspx) コマンドレットです。

    #The following line creates a verbose message.
    
    Write-Verbose –Message "This is a verbose message."

### デバッグ ストリーム

デバッグ ストリームは対話ユーザー向けに使用するものであるため、Runbook では使用しません。

## 進捗状況レコード

Runbook を (Azure 管理ポータルの Runbook の [構成] タブで) 進捗状況レコードが記録されるように構成すると、各アクティビティの実行前後にレコードがジョブ履歴に書き込まれます。 ほとんどの場合、パフォーマンスを最大化するために、進行状況レコードを記録しないという Runbook の既定の設定を保持する必要があります。 このオプションは、Runbook のトラブルシューティングやデバッグを行うためにのみ有効にします。 Runbook のテスト時には、Runbook が進歩状況レコードを記録するように構成されている場合でも、進歩状況メッセージは表示されません。

[Write-progress](http://technet.microsoft.com/library/hh849902.aspx) ので、この対話ユーザーで使用するためのものは、コマンドレットは、runbook で無効になっています。

## ユーザー設定変数

Windows PowerShell を使用して [ユーザー設定変数](http://technet.microsoft.com/library/hh847796.aspx) 別の出力ストリームに送信されるデータに応答する方法を決定します。 これらの変数を Runbook で設定することで、異なるストリームに送信されるデータへの応答方法を制御できます。

次の表は、Runbook で使用できるユーザー設定変数と、それらの有効値と既定値を示しています。 この表には、Runbook で有効な値のみが含まれることに注意してください。 Windows PowerShell を Azure Automation の外部で使用する場合は、ユーザー設定変数で有効なその他の値があります。

| 変数| 既定値| 有効な値|
|:---|:---|:---|
| WarningPreference| Continue| 停止<br>続行<br>SilentlyContinue|
| ErrorActionPreference| Continue| 停止<br>続行<br>SilentlyContinue|
| VerbosePreference| SilentlyContinue| 停止<br>続行<br>SilentlyContinue|

次の表では、Runbook で有効なユーザー設定変数値の動作を一覧表示します。

| 値| 動作|
|:---|:---|
| Continue| メッセージを記録し、Runbook の実行を続けます。|
| SilentlyContinue| メッセージを記録せずに Runbook の実行を続けます。これはメッセージを無視する効果があります。|
| Stop| メッセージを記録し、Runbook を中断します。|

## Runbook の出力とメッセージの取得

### Azure 管理ポータル

Runbook ジョブの詳細は、Azure 管理ポータルの Runbook の [ジョブ] タブで参照できます。 入力パラメーターをジョブの概要が表示されます、 [出力ストリーム](#Output) だけでなく、ジョブと例外が発生した場合の全般情報。 履歴からのメッセージが表示されます、 [出力ストリーム](#Output) と [警告およびエラー ストリーム](#WarningError) に加え、 [詳細ストリーム](#Verbose) と [進捗状況レコード](#Progress) ログの詳細および進捗状況レコードを runbook が構成されている場合。

### Windows PowerShell

Windows PowerShell で取得できますの出力とメッセージを使用して runbook から、 [Get-azureautomationjoboutput](http://msdn.microsoft.com/library/dn690268.aspx) コマンドレットです。 このコマンドレットにはジョブの ID が必要であり、返すストリームを指定する Stream と呼ばれるパラメーターがあります。 [Any] を指定して、ジョブのすべてのストリームを返すことができます。

次の例は、サンプル Runbook を開始し、完了するまで待機します。 完了すると、その出力ストリームがジョブから収集されます。

    $job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" 
    
    $doLoop = $true
    While ($doLoop) {
       $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
       $status = $job.Status
       $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
    }
    
    Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## 関連記事:

- [Runbook ジョブを追跡します。](automation-runbook-execution.md)
- [子 Runbook には](http://msdn.microsoft.com/library/azure/dn857355.aspx)




