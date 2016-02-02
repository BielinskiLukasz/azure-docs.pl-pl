<properties 
    pageTitle="Azure Automation でのテキスト形式の Runbook の編集"
    description="この記事では、テキスト エディターを使用して Azure Automation で PowerShell Runbook および PowerShell ワークフロー Runbook の処理を行うためのさまざまな手順について説明します。"
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
    ms.date="09/23/2015"
    ms.author="bwren" />


# Azure Automation でのテキスト形式の Runbook の編集

Azure Automation のテキスト エディターは編集に使用することができます [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) と [PowerShell ワークフロー runbook](automation-runbook-types.md#powershell-workflow-runbooks)します。 これは、runbook に共通のリソースにアクセスするための intellisense およびその他の特定の機能を備えたコードの色分けなど他のコード エディターの一般的な機能です。 この記事では、このエディターのさまざまな機能を実行する詳細な手順を説明します。

このテキスト エディターには、アクティビティ、資産、および子 Runbook のコードを Runbook に挿入するための機能が含まれます。 コードを自分で入力しなくても、使用可能なリソースの一覧から選択して、適切なコードを Runbook に挿入できます。

Azure Automation の各 Runbook には、ドラフトと発行の 2 つのバージョンがあります。 ワークフローのドラフト バージョンを編集し、実行できるように発行します。 発行されたバージョンを編集することはできません。 参照してください [runbook を公開する](automation-creating-importing-runbook.md#publishing-a-runbook) の詳細。

使用する [グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks), を参照してください [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)します。

## Azure プレビュー ポータルで Runbook を編集するには

編集するためにテキスト エディターで Runbook を開くには、次の手順を使用します。

1. Azure ポータルで Automation アカウントを選択します。
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. 編集する Runbook の名前をクリックして、**[編集]** ボタンをクリックします。
6. 必要な編集を実行します。
7. 編集が完了したら、**[保存]** をクリックします。
8. 最新のドラフト バージョンの Runbook を発行する場合は、**[発行]** をクリックします。

### コマンドレットを Runbook に挿入するには

2. テキスト エディターのキャンバスで、コマンドレットを配置する場所にカーソルを置きます。
3. ライブラリ コントロールで **[コマンドレット]** ノードを展開します。
3. 使用するコマンドレットを含むモジュールを展開します。
4. 挿入するコマンドレットを右クリックして、**[キャンバスに追加]** を選択します。 コマンドレットに複数のパラメーター セットがある場合は、既定のセットが追加されます。 コマンドレットを展開して別のパラメーター セットを選択することもできます。
4. コマンドレットのコードがパラメーターのリスト全体と共に挿入されます。
5. 必須パラメーターの山括弧 <> で囲まれているデータ型の代わりに適切な値を入力します。必要のないパラメーターを削除します。

### 子 Runbook のコードを Runbook に挿入するには

2. テキスト エディターのキャンバス、カーソルのコードを配置する、 [子 runbook](automation-child-runbooks.md)します。
3. ライブラリ コントロールで **[Runbook]** ノードを展開します。
3. 挿入する Runbook を右クリックして、**[キャンバスに追加]** を選択します。
4. 子 Runbook のコードがすべての Runbook パラメーターのプレースホルダーと共に挿入されます。
5. 各パラメーターのプレースホルダーを適切な値に置き換えます。

### 資産を Runbook に挿入するには

2. テキスト エディターのキャンバスで、子 Runbook のコードを配置する場所にカーソルを置きます。
3. ライブラリ コントロールで **[アセット]** ノードを展開します。
4. 必要な資産の種類のノードを展開します。
3. 挿入する資産を右クリックして、**[キャンバスに追加]** を選択します。  [変数アセット](variable-assets.md), 、いずれかを選択 **"取得"キャンバスへの変数を追加** または **"設定"キャンバスへの変数を追加** を取得または変数を設定するかどうかによって異なります。
4. 資産のコードが Runbook に挿入されます。



## Azure ポータルで Runbook を編集するには

編集するためにテキスト エディターで Runbook を開くには、次の手順を使用します。

1. Azure ポータルで、**[Automation]** を選択し、次に Automation アカウントの名前をクリックします。
2. **[Runbook]** タブを選択します。
3. 編集する Runbook の名前をクリックして、**[作成]** タブを選択します。
5. 画面の下部にある **[編集]** ボタンをクリックします。
6. 必要な編集を実行します。
7. 編集が完了したら、**[保存]** をクリックします。
8. 最新のドラフト バージョンの Runbook を発行する場合は、**[発行]** をクリックします。

### アクティビティを Runbook に挿入するには

1. テキスト エディターのキャンバスで、アクティビティを配置する場所にカーソルを置きます。
1. 画面の下部にある **[挿入]** をクリックし、**[アクティビティ]** をクリックします。
1. **[統合モジュール]** 列で、アクティビティを含むモジュールを選択します。
1. **[アクティビティ]** ウィンドウでアクティビティを選択します。
1. **[説明]** 列で、アクティビティの説明を確認します。 必要に応じて、[詳細なヘルプの表示] をクリックしてアクティビティのヘルプをブラウザーに表示できます。
1. 右矢印をクリックします。 アクティビティにパラメーターがある場合は一覧表示されます。
1. チェック マークのボタンをクリックします。 アクティビティを実行するためのコードが、Runbook に挿入されます。
1. アクティビティにパラメーターが必要な場合は、山括弧 <> で囲まれているデータ型の代わりに適切な値を入力します。

### 子 Runbook のコードを Runbook に挿入するには

1. テキスト エディターのキャンバスにカーソルを配置する、 [子 runbook](automation-child-runbooks.md)します。
2. 画面の下部にある **[挿入]** をクリックし、**[Runbook]** をクリックします。
3. 中央の列から挿入する Runbook を選択し、右矢印をクリックします。
4. Runbook にパラメーターがある場合は一覧表示されます。
5. チェック マークのボタンをクリックします。 選択した Runbook を実行するためのコードが、現在の Runbook に挿入されます。
7. Runbook にパラメーターが必要な場合は、山括弧 <> で囲まれているデータ型の代わりに適切な値を入力します。

### 資産を Runbook に挿入するには

1. テキスト エディターのキャンバスで、資産を取得するアクティビティを配置する場所にカーソルを置きます。
1. 画面の下部にある **[挿入]** をクリックし、**[設定]** をクリックします。
1. **[設定アクション]** 列で、目的のアクションを選択します。
1. 中央の列の使用できる資産から選択します。
1. チェック マークのボタンをクリックします。 資産を取得または設定するコードが Runbook に挿入されます。



## Windows PowerShell を使用して Azure Automation の Runbook を編集するには

Windows PowerShell を使用して Runbook を編集するには、任意のエディターを使用してワークフローを編集し、.ps1 ファイルに保存します。 使用することができます、 [Get-azureautomationrunbookdefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) コマンドレットを runbook の内容を取得し、 [AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) コマンドレットを既存のドラフト ワークフローを変更したものです。

### Windows PowerShell を使用して Runbook の内容を取得するには

次のサンプル コマンドでは、Runbook のスクリプトを取得し、スクリプト ファイルに保存する方法を示します。 この例では、ドラフト バージョンを取得します。 Runbook の発行済みバージョンを取得することもできますが、このバージョンを変更することはできません。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content
    
    Out-File -InputObject $runbookContent -FilePath $scriptPath

### Windows PowerShell を使用して Runbook の内容を変更するには

次のサンプル コマンドでは、Runbook の既存の内容を、ワークフローを含むスクリプト ファイルの内容に置き換える方法を示します。 これと同じサンプル プロシージャは、 [Windows PowerShell を使用したスクリプト ファイルから runbook をインポートする](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS)します。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## 関連記事:

- [作成または Azure Automation で runbook をインポートします。](automation-creatnig-importing-runbook.md)
- [PowerShell ワークフローについて](automation-powershell-workflow.md)
- [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)
- [証明書](automation-certificates.md)
- [接続](automation-connections.md)
- [資格情報](automation-credentials.md)
- [スケジュール](automation-schedules.md)
- [変数](automation-variables.md)




