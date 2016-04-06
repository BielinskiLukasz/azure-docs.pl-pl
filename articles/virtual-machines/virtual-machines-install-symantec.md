<properties
    pageTitle="VM に Symantec Endpoint Protection をインストールする | Microsoft Azure"
    description="クラシック デプロイ モデルで作成された新しいまたは既存の Azure VM に Symantec Endpoint Protection のセキュリティ拡張機能をインストールして構成する方法を説明します。"
    services="virtual-machines"
    documentationCenter=""
    authors="dsk-2015"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2015"
    ms.author="dkshir"/>

# Windows VM に Symantec Endpoint Protection をインストールし、構成する方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。


この記事では、Windows Server を実行している既存の仮想マシン (VM) または新しい VM に Symantec Endpoint Protection クライアントをインストールして構成する方法を説明します。 このクライアントには、ウイルスおよびスパイウェア対策、ファイアウォール、侵入防止などのサービスが含まれています。

このクライアントは、VM エージェントを使用することにより、セキュリティ拡張機能としてインストールできます。 新しい仮想マシンの場合は、Endpoint Protection クライアントと同時に VM エージェントをインストールします。 VM エージェントがない既存の仮想マシンの場合には、最初に VM エージェントをダウンロードしてインストールする必要があります。 この記事では、この両方の場合について説明します。

Symantec のオンプレミス ソリューション用サブスクリプションが既にある場合には、それを使用して Azure の仮想マシンを保護できます。 サブスクリプションがない場合には、サインアップして試用サブスクリプションを利用できます。 このソリューションの詳細については、次を参照してください。 [Microsoft の Azure platform 上の Symantec Endpoint Protection][Symantec]します。 このページでは、既に Symantec の顧客である場合は、クライアントをインストールするためライセンス情報と手順へのリンクもあります。

## 新しい仮想マシンに Symantec Endpoint Protection をインストールする

 [Azure クラシック ポータル][Portal] を使用する場合に、VM エージェントと Symantec のセキュリティ拡張機能をインストールすることができます、 **ギャラリーから** 仮想マシンを作成するにはオプションです。 仮想マシンを 1 つだけ作成する場合には、この方法が Symantec の保護機能を追加する最も簡単な方法です。

これは、 **ギャラリーから** オプションは、仮想マシンを設定するを支援するウィザードを開きます。 ウィザードの最後のページで、VM エージェントと Symantec のセキュリティ拡張機能をインストールします。

一般的な手順については、次を参照してください。 [Windows Server を実行している仮想マシンを作成する][Create]です。 ウィザードの最後のページで、次の手順を実行します。

1.  [VM エージェント] **VM エージェントをインストール** 既にチェックする必要があります。

2.  [セキュリティ拡張機能を確認 **Symantec Endpoint Protection**します。


    ![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.  ページの下部にあるチェック マークをオンにして、仮想マシンを保存します。

## 既存の仮想マシンに Symantec Endpoint Protection をインストールする

開始する前に、次の条件が必要です。

- Azure PowerShell モジュール Version 0.8.2 以降がコンピューター上に存在すること。 インストールされている Azure PowerShell のバージョンを確認することができます、 **Get-module azure | format-table バージョン** コマンドです。 手順と最新バージョンへのリンクでは、次を参照してください。 [インストールおよび Azure PowerShell の構成方法][PS]します。 Azure サブスクリプションへのログインを確認してください。

- Azure 仮想マシンで実行されている VM エージェント。

最初に、VM エージェントが仮想マシンに既にインストールされていることを確認します。 クラウド サービス名と仮想マシン名を入力して、管理者レベルの Azure PowerShell のコマンド プロンプトで、次のコマンドを実行します。 置換を含む引用符内のすべての < と > 文字です。

> [AZURE.TIP] クラウド サービスと仮想マシン名がわからない場合は、実行 **Get-azurevm** 、現在のサブスクリプション内のすべての仮想マシンの名前を一覧表示します。

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

場合、 **の write-host** コマンドが表示されます **True**, 、VM エージェントがインストールされています。 表示される場合 **False**, 、指示し、Azure ブログの記事のダウンロードへのリンクを参照してください [VM エージェントと拡張機能 - パート 2][Agent]します。

VM エージェントがインストールされている場合、これらのコマンドを実行して Symantec Endpoint Protection エージェントをインストールします。

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Symantec のセキュリティ拡張機能がインストールされ、最新の状態であることを確認するには:

1.  仮想マシンにログオンします。 手順については、次を参照してください。 [Windows Server を実行している仮想マシンにログオンする方法][Logon]します。
2.  Windows Server 2008 R2 では、クリックして **開始 > Symantec Endpoint Protection**します。 Windows Server 2012 または Windows Server 2012 R2、スタート画面から「 **Symantec**, 、クリックしてして **Symantec Endpoint Protection**します。
3.   **ステータス** のタブ、 **状態: Symantec Endpoint Protection** ウィンドウで、更新プログラムを適用するかが必要な場合に再起動します。

## その他のリソース

[Windows Server が実行されている仮想マシンにログオンする方法][Logon]

[Azure VM 拡張機能と機能][Ext]


<!--Link references-->
[Symantec]: http://go.microsoft.com/fwlink/p/?LinkId=403942

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-tutorial-classic-portal.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-log-on-windows-server.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493


