<properties 
   pageTitle="リソース マネージャーでのテンプレートを使用した内部ロード バランサーの作成 | Microsoft Azure"
   description="リソース マネージャーでテンプレートを使用して、内部ロード バランサーを作成する方法について説明します"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/09/2015"
   ms.author="joaoma" />


# テンプレートを使用した内部ロード バランサーの作成の概要

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [従来のデプロイ モデル](load-balancer-get-started-ilb-classic-ps.md)します。

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## [クリックしてデプロイ] を使用してテンプレートをデプロイする

パブリック リポジトリで使用できるサンプル テンプレートは、上記のシナリオの生成に使用した既定値を含むパラメーター ファイルを使用します。 クリックを使用して展開するこのテンプレートを展開するには、[次のリンク] を進める ((https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer) をクリックして **Deploy to Azure**, 、必要に応じて、既定のパラメーター値を置き換えるし、ポータルの指示に従います。

## PowerShell を使用してテンプレートをデプロイする

PowerShell を使用してダウンロードしたテンプレートをデプロイするには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、次を参照してください。 [インストールおよび Azure PowerShell の構成方法](powershell-install-configure.md) Azure にサインインし、サブスクリプションを選択最後までの指示に従います。
2. 次に示すように、**Switch-AzureMode** コマンドレットを実行して、リソース マネージャー モードに切り替えます。

        Switch-AzureMode AzureResourceManager

    上記のコマンドで想定される出力を次に示します。

        WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.


>[AZURE.WARNING] Switch-AzureMode コマンドレットは間もなく廃止予定です。 これらが廃止された場合は、すべてのリソース マネージャー コマンドレットの名前が変更されます。

3.ダウンロード、 [パラメーター](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.parameters.json) ファイルをローカル ディスクにします。<BR>
4. ファイルを編集し、保存します。<BR>
5. テンプレートを使用してリソース グループを作成するには、**New-AzureResourceGroup** コマンドレットを実行します。

        New-AzureResourceGroup -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'




## Azure CLI を使用してテンプレートをデプロイする

Azure CLI を使用してテンプレートをデプロイするには、次の手順に従います。

1. Azure CLI を初めて使用する場合は、次を参照してください。 [のインストールと Azure CLI の構成](xplat-cli.md) Azure アカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、**azure config mode** コマンドを実行してリソース マネージャー モードに切り替えます。

        azure config mode arm

    上記のコマンドで想定される出力を次に示します。

        info:    New mode is arm

3. 開いている、 [パラメーター ファイル](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.parameters.json), 、その内容を選択して、コンピューターのファイルに保存します。 この例では、パラメーター ファイルを *parameters.json* に保存しました。

4. 上記でダウンロードして変更したテンプレート ファイルとパラメーター ファイルを使用して、**azure group deployment create** コマンドレットを実行して新しい VNet をデプロイします。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。

        azure group create -n TestRG -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json -e parameters.json




## 次のステップ

[ソース IP アフィニティを使用してロード バランサー分散モードを構成します。](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定を構成します。](load-balancer-tcp-idle-timeout.md)








