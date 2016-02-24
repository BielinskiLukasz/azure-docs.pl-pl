<properties
    pageTitle="仮想マシンにカスタム データを挿入する | Microsoft Azure"
    description="このトピックでは、Azure の仮想マシンのインスタンスを作成する際に、仮想マシンにカスタム データを挿入する方法のほか、Windows と Linux の場合について、カスタム データの場所を探して利用する方法について説明します。"
    services="virtual-machines"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="rasquill"/>


#Azure の仮想マシンにカスタム データを挿入する

Azure の仮想マシンをプロビジョニングしているときに、スクリプトなどのデータをその中に挿入する場合がよくあります。これは、オペレーティング システムが Windows であっても Linux ディストリビューションであっても同じです。 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。


このトピックでは、次の操作について説明します。

- Azure の仮想マシンをプロビジョニングしているときに、スクリプトやデータを挿入する。

- Windows や Linux 用にデータを取得する。

- いくつかのシステムで利用できる特殊なツールを使用して、自動的にカスタム データを検出し、操作する。

> [AZURE.NOTE] どのカスタム データを説明する Azure サービス管理 API で作成した VM を使用して挿入することができます。 Azure リソース管理 API を使用する方法を確認するには、次を参照してください。 [のサンプル テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata)します。

## Azure 仮想マシンにカスタム データを挿入する

この機能は現在サポートされてでのみ、 [Azure コマンド ライン インターフェイス](https://github.com/Azure/azure-xplat-cli)します。 `azure vm create` コマンドのオプションはすべて使用できますが、以下はきわめて基本的な方法の 1 つです。

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## 仮想マシンでカスタム データを使用する

+ Azure 仮想マシンが Windows ベース仮想マシンの場合、カスタム データのファイルは `%SYSTEMDRIVE%\AzureData\CustomData.bin` に保存されます。 このファイルは、ローカル コンピューターから新しい仮想マシンに転送するために Base64 でエンコードされますが、自動的にデコードされるため、直ちに開いて使用できます。

   > [AZURE.NOTE] ファイルが存在する場合は上書きされます。 設定されているディレクトリのセキュリティ **システム: フル コントロール** と **管理者: フル コントロール**します。

+ Azure 仮想マシンが Linux ベースの仮想マシンの場合、カスタム データのファイルは次の 2 箇所にあります。 データは base64 でエンコードされているので、最初にデータをデコードする必要があります。

    + `/var/lib/waagent/ovf-env.xml`
    + `/var/lib/waagent/CustomData`



## Azure での cloud-Init

Azure 仮想マシンが Ubuntu イメージまたは CoreOS イメージから作成されている場合は、CustomData を使用して cloud-config を cloud-init に送信できます。 また、カスタム データ ファイルがスクリプトの場合は、cloud-init でスクリプトを実行できます。

### Ubuntu Cloud Image

ほとんどの Azure Linux イメージでは、"/etc/waagent.conf" を編集して、一時的なリソース ディスクとスワップ ファイルを構成します。 参照してください [Azure Linux エージェント ユーザー ガイド](virtual-machines-linux-agent-user-guide.md) の詳細。

ただし、Ubuntu Cloud Image では、cloud-init を使用して、リソース ディスク ("一時" ディスク) とスワップ パーティションを構成する必要があります。 詳細については、Ubuntu wiki の次のページを参照してください: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions)します。



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ: cloud-init を使用する

詳細については、次を参照してください。、 [Ubuntu 用 cloud-init documentation ドキュメント](https://help.ubuntu.com/community/CloudInit)します。

<!--Link references-->
[Add Role Service Management REST API Reference](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Azure コマンド ライン インターフェイス](https://github.com/Azure/azure-xplat-cli)

