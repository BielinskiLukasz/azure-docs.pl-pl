<properties 
   pageTitle="PowerShell ワークフローについての説明"
   description="Azure Automation の Runbook は、Windows PowerShell ワークフローが基になっています。この記事では、PowerShell に慣れている作成者を対象に、PowerShell と PowerShell ワークフローの具体的な違いについて簡単に説明します。"
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
   ms.date="10/01/2015"
   ms.author="bwren" />


# Windows PowerShell ワークフローについて

Azure Automation の Runbook は Windows PowerShell ワークフローとして実装されています。 Windows PowerShell ワークフローは Windows PowerShell スクリプトと似ていますが、新規ユーザーにはわかりにくい大きな違いがいくつかあります。 この記事は既に PowerShell に慣れているユーザーを対象としており、PowerShell スクリプトを PowerShell ワークフローに変換する場合に必要となる考え方について説明します。

A workflow is a sequence of programmed, connected steps that perform long-running tasks or require the coordination of multiple steps across multiple devices or managed nodes. The benefits of a workflow over a normal script include the ability to simultaneously perform an action against multiple devices and the ability to automatically recover from failures. A Windows PowerShell Workflow is a Windows PowerShell script that leverages Windows Workflow Foundation. ワークフローは Windows PowerShell の構文で記述され、Windows PowerShell によって起動されますが、Windows Workflow Foundation によって処理されます。

詳細については、この記事のトピックは、次を参照してください。 [Windows PowerShell ワークフローの概要](http://technet.microsoft.com/library/jj134242.aspx)します。

## Runbook の種類

Azure Automation の Runbook には、*テキスト*と*グラフィカル*の 2 種類があります。 Runbook を作成するときに Runbook の種類を定義し、作成した後で種類を変えることはできません。

テキスト形式の runbook では、Azure Automation のテキスト エディターまたは PowerShell ISE などのオフライン エディターを使用する PowerShell ワークフロー コードを直接操作を好むユーザー用です。 テキストの runbook を作成する場合は、この記事の情報を理解する必要があります。

グラフィカルな Runbook ではグラフィカル インターフェイスを使用するため、基になる PowerShell ワークフローの複雑性を意識することなく、同じアクティビティとコマンドレットを使用して Runbook を作成することができます。 チェックポイントや並列実行などのこの記事で紹介する概念は、グラフィカルな Runbook にも当てはまりますが、詳細な構文を気にする必要はありません。

## ワークフローの基本構造

PowerShell スクリプトを PowerShell ワークフローに変換する最初のステップは、スクリプトを **Workflow** キーワードで囲むことです。 ワークフローは、**Workflow** キーワードで始まり、中かっこで囲まれたスクリプトの本文が後に続きます。 次の構文に示すように、ワークフローの名前は **Workflow** キーワードの後に続きます。

    Workflow Test-Workflow
    {
       <Commands>
    }

ワークフローの名前は、Automation の Runbook の名前と一致する必要があります。 Runbook がインポートされている場合、ファイル名はワークフロー名と一致していなければならず、末尾は .ps1 でなければなりません。

ワークフローにパラメーターを追加するには、スクリプトの場合と同様に **Param** キーワードを使用します。

## コードの変更

PowerShell ワークフローのコードは PowerShell スクリプト コードとほぼ同じですが、いくつかの重要な変更点があります。 次のセクションでは、ワークフローで実行するための PowerShell スクリプトに対して行う必要がある変更について説明します。

### アクティビティ

アクティビティとは、ワークフロー内の特定のタスクです。 スクリプトが 1 つ以上のコマンドで構成されているのと同様に、ワークフローも順番に実行される 1 つ以上のアクティビティで構成されます。 Windows PowerShell ワークフローでは、ワークフローの実行時に Windows PowerShell コマンドレットの多くが自動でアクティビティに変換されます。 Runbook でこれらのコマンドレットのいずれかを指定すると、対応するアクティビティは、実際には Windows Workflow Foundation で実行されます。 対応する活動がないコマンドレットの場合、Windows PowerShell ワークフローを自動的に実行内でそのコマンドレット、 [InlineScript](#inlinescript) アクティビティ。 InlineScript ブロックに明示的に含めないと除外され、ワークフローでは使用できない一連のコマンドレットがあります。 これらの概念の詳細については、次を参照してください。 [スクリプト ワークフローでアクティビティを使用して](http://technet.microsoft.com/library/jj574194.aspx)します。

ワークフロー アクティビティは、操作を構成するための一連の共通パラメーターを共有します。 詳細については、ワークフロー共通パラメーターは、次を参照してください。 [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx)します。

### 位置指定パラメーター

ワークフローのアクティビティおよびコマンドレットでは、位置指定パラメーターを使用できません。 このため、パラメーターを使用する必要があります。

For example, consider the following code that gets all running services.

     Get-Service | Where-Object {$_.Status -eq "Running"}

同じコードをワークフローで実行しようとすると、「指定された名前のパラメーターを使用してパラメーター セットを解決できません。」というメッセージが表示されます。 これを修正するには、次のようにパラメーター名を指定します。

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### 逆シリアル化されたオブジェクト

ワークフロー内のオブジェクトは逆シリアル化されます。 This means that their properties are still available, but not their methods. 例として、サービス オブジェクトの Stop メソッドを使用してサービスを停止する次の PowerShell コードを考えます。

    $Service = Get-Service -Name MyService
    $Service.Stop()

ワークフローでこれを実行しようとすると、「Windows PowerShell ワークフローでは、メソッド呼び出しはサポートされていません」というエラー メッセージが表示されます。

1 つのオプションは、次の 2 行のコードをラップする、 [InlineScript](#InlineScript) 場合 $Service はブロック内のサービス オブジェクトになるをブロックします。

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    } 

もう 1 つの方法は、メソッドとして同じ機能を実行する別のコマンドレットがある場合は、それを使用することです。 このサンプルの場合、Stop-Service コマンドレットは Stop メソッドと同じ機能を提供し、ワークフローでは次のように使用できます。

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }

## InlineScript

**InlineScript** アクティビティは、PowerShell ワークフローではなく従来の PowerShell スクリプトとして 1 つまたは複数のコマンドを実行する必要がある場合に便利です。 ワークフロー内のコマンドは Windows Workflow Foundation に送信されて処理され、InlineScript ブロック内のコマンドは Windows PowerShell によって処理されます。

InlineScript uses the syntax shown below.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

InlineScript の出力を変数に割り当てることで、出力を返すことができます。 次の例では、サービスを停止した後、サービス名を出力しています。

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }
    
        $Output.Name
    }

InlineScript ブロックに値を渡すことはできますが、**$Using** スコープ修飾子を使用する必要があります。 次の例は前の例と同じですが、サービス名を変数によって渡しています。

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"
    
        $Output = InlineScript {
            $Service = Get-Service -Name $Using:MyService
            $Service.Stop()
            $Service
        }
    
        $Output.Name
    }

InlineScript アクティビティは特定のワークフローにとって重要ですが、ワークフロー コンストラクトをサポートせず、次の理由により、必要な場合にのみ使用する必要があります。

- 使用することはできません [チェックポイント](#Checkpoints) InlineScript ブロックの内部でします。 ブロック内でエラーが発生した場合は、ブロックの先頭から再開する必要があります。
- 使用することはできません [並列実行](#parallel-execution) Inlinescript ブロックの内部です。
- InlineScript は、InlineScript ブロックの長さ全体について Windows PowerShell セッションを保持するため、ワークフローの拡張性に影響します。

InlineScript の使用の詳細については、次を参照してください。 [ワークフローを使用した Windows PowerShell コマンドを実行している](http://technet.microsoft.com/library/jj574197.aspx) と [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx)します。


## 並列処理

Windows PowerShell ワークフローの利点の 1 つは、一般的なスクリプトのように順番に実行するのでなく、一連のコマンドを並行して実行できることです。

**Parallel** キーワードを使用して、同時に実行される複数のコマンドを含むスクリプト ブロックを作成できます。 これには、次に示す構文を使用します。 この場合、Activity1 と Activity2 は同時に開始されます。 Activity3 は、Activity1 と Activity2 の両方が完了した後にのみ開始されます。

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

例として、複数のファイルをネットワーク上にコピーする次の PowerShell コマンドを考えます。 These commands are run sequentially so that one file must finish copying before the next is started.

    $Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    $Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    $Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

次のワークフローでは、すべてのコピーが同時に開始されるように、同じコマンドが並列実行されます。 すべてが完全にコピーされた後でのみ、完了メッセージが表示されます。

    Workflow Copy-Files
    {
        Parallel 
        {
            $Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }
    
        Write-Output "Files copied."
    }

**ForEach -Parallel** の構文を使用することにより、コレクション内の各項目のコマンドを同時に処理できます。 コレクション内の項目は並行して処理され、スクリプト ブロック内のコマンドは順番に実行されます。 This uses the syntax shown below. この場合、Activity1 は、コレクション内のすべての項目に対して同時に開始されます。 For each item, Activity2 will start after Activity1 is complete. Activity3 は、すべての項目における Activity1 と Activity2 の両方が完了した後にのみ開始されます。

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

次の例は、並列でファイルのコピーを行う前の例と似ています。 In this case, a message is displayed for each file after it copies. Only after they are all completely copied is the final completion message displayed.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")
    
        ForEach -Parallel ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }
    
        Write-Output "All files copied."
    }

> [AZURE.NOTE]  We do not recommend running child runbooks in parallel since this has been shown to give unreliable results. 子 Runbook からの出力が表示されないことがあり、1 つの子 Runbook での設定が並列に実行されている他の子 Runbook に影響を与える可能性があります。 


## チェックポイント

*チェックポイント* は、変数の現在の値と、そのポイントに生成された出力を含むワークフローの現在の状態のスナップショットです。 ワークフローがエラーで終了、または [中断](suspending-a-workflow), 、次回の実行、ワークフローの先頭ではなく、最後のチェックポイントから開始されます。 **Checkpoint-Workflow** アクティビティを使用してワークフローにチェックポイントを設定できます。

次のサンプル コードでは、Activity2 の後に例外が発生し、ワークフローが終了します。 ワークフローを再実行すると、設定された最後のチェックポイントの直後に Activity2 があるため、まず Activity2 が実行されます。

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

例外を引き起こす可能性があり、ワークフローが再開された場合は繰り返す必要のないアクティビティの後に、ワークフローのチェックポイントを設定する必要があります。 For example, your workflow may create a virtual machine. チェックポイントをコマンドの前後に設定して、仮想マシンを作成できます。 If the creation fails, then the commands would be repeated if the workflow is started again. 作成の成功後にワークフローが失敗した場合、ワークフローが再開されても仮想マシンが再び作成されることはありません。

次の例では、ネットワーク上の場所に複数のファイルをコピーし、各ファイルの後にチェックポイントを設定します。 If the network location is lost, then the workflow will end in error. ワークフローを再び開始すると、最後のチェックポイントで再開するので、既にコピーされているファイルだけがスキップされます。

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")
    
        ForEach ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }
    
        Write-Output "All files copied."
    }

チェックポイントの詳細については、次を参照してください。 [スクリプト ワークフローへのチェックポイントの追加](http://technet.microsoft.com/library/jj574114.aspx)します。



## 関連記事:

- [Windows PowerShell ワークフローの概要](http://technet.microsoft.com/library/jj134242.aspx)




