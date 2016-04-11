<properties
   pageTitle="Azure PowerShell を使用した仮想マシンの管理 | Microsoft Azure"
   description="仮想マシンの管理タスクを自動化するのに使用できるコマンドについて説明します。"
   services="virtual-machines"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="kasing"/>

# Azure PowerShell を使用した仮想マシンの管理

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。


Azure PowerShell コマンドレットを使用すると、VM を管理するために日常的に行う多くのタスクを自動化できます。 この記事では、比較的単純なタスクにはコマンド例を示し、より複雑なタスクには、コマンドが記載されている記事へのリンクを示します。

>[AZURE.NOTE] アーティクルで指示を入手できますまだインストールして、Azure PowerShell を構成していない場合は [をインストールして、Azure PowerShell を構成する方法](../install-configure-powershell.md)します。

## サンプル コマンドを使用する方法
コマンド内のテキストの一部を、使用する環境に適した別のテキストに置き換える必要があります。 < と > 記号に置き換える必要があるテキストを表します。 テキストを置き換える場合、記号は削除し、引用符はそのままの位置に残します。

## VM の取得
これは頻繁に使用することになる基本的なタスクです。 VM に関する情報の取得、VM でのタスクの実行、変数に格納する出力の取得に使用します。

VM に関する情報を取得するには、このコマンドを含む引用符内のすべてを実行、< と > 文字。

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

出力を $vm 変数に格納するには、次のように実行します。

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Windows ベースの VM へのログオン

次の各コマンドを実行します。

>[AZURE.NOTE] 表示から仮想マシンとクラウド サービス名を取得できます、 **Get-azurevm** コマンドです。
>
    $svcName="<cloud service name>"
    $vmName="<virtual machine name>"
    $localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
    $localFile=$localPath + "\" + $vmname + ".rdp"
    Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## VM の停止

次のコマンドを実行します。

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT] ある場合に、そのクラウド サービス内の最後の VM クラウド サービスの仮想 IP (VIP) を保持するのにには、このパラメーターを使用します。 <br><br> StayProvisioned パラメーターを使用する場合、VM のする引き続き課金されます。

## VM の起動

次のコマンドを実行します。

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## データ ディスクの接続
このタスクには、いくつかの手順が必要です。 まず、****Add-AzureDataDisk**** コマンドレットを使用して、$vm オブジェクトにディスクを追加します。 次を使用する **Update-azurevm** コマンドレット、仮想マシンの構成を更新します。

新しいディスクとデータを含むディスクのどちらを接続するかについても決める必要があります。 新しいディスクの場合、コマンドによって .vhd ファイルが作成、接続されます。

新しいディスクを接続するには、次のコマンドを実行します。

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM <$vm> `
              | Update-AzureVM

既存のデータ ディスクを接続するには、次のコマンドを実行します。

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> `
              | Update-AzureVM

BLOB ストレージにある既存の .vhd ファイルからデータ ディスクを接続するには、次のコマンドを実行します。

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> `
              | Update-AzureVM

## Windows ベースの VM の作成

Azure で新しい Windows ベースのバーチャル マシンを作成するには、の手順に従います
[Azure PowerShell を使用して作成し、Windows ベースの仮想マシンを事前に構成](virtual-machines-ps-create-preconfigure-windows-vms.md)します。 このトピックでは、次の項目を事前構成することができる Windows ベースの VM を作成するための Azure PowerShell コマンド セットを作成する手順を説明します。

- Active Directory ドメイン メンバーシップ。
- 追加のディスク。
- 既存の負荷分散セットのメンバーとしての設定。
- 静的 IP アドレス。

## Linux ベースの VM の作成

」の指示に従って [の作成と Azure Powershell を使用して Linux 仮想マシンを事前構成する](virtual-machines-ps-create-preconfigure-linux-vms.md) が事前に Azure で新しい Linux ベースのバーチャル マシンを作成します。

- 追加のディスク。
- 既存の負荷分散セットのメンバーとしての設定。
- 静的 IP アドレス。


