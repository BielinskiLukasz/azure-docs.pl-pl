<properties
    pageTitle="Azure RemoteApp コレクション用に仮想ネットワークを計画する方法 | Microsoft Azure"
    description="Azure RemoteApp コレクション用に仮想ネットワークを計画する方法について説明します。"
    services="remoteapp"
    documentationCenter="" 
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/11/2015"
    ms.author="elizapo" />


# Azure RemoteApp の仮想ネットワークを計画する方法

このドキュメントでは、Azure RemoteApp 用に Azure の仮想ネットワーク (VNET) とサブネットを設定する方法について説明します。 Azure の仮想ネットワークを熟知していない場合、この機能は、クラウドにネットワーク インフラストラクチャを仮想化し、Azure とオンプレミスのリソースを使用したハイブリッド ソリューションを作成するのに役立ちます。 詳細について [ここ](virtual-networks-overview.md)します。

Azure RemoteApp をデプロイする仮想ネットワーク内の送受信トラフィックについてセキュリティ ポリシーを定義する場合は、Azure RemoteApp 用には、Azure の仮想ネットワーク内の他のデプロイとは別にサブネットを作成することを強くお勧めします。 Azure の仮想ネットワーク サブネット上のセキュリティ ポリシーを定義する方法の詳細については、を参照してください [ネットワーク セキュリティ グループ (NSG) は何ですか?](virtual-networks-nsg.md).

## Azure の仮想ネットワークを使用した Azure RemoteApp コレクションの種類

次の図は、仮想ネットワークを使用する場合に選択できる 2 つの異なるコレクションを示しています。

### VNET を使用した Azure RemoteApp クラウド コレクション

 ![Azure RemoteApp - VNET を使用したクラウド コレクション](./media/remoteapp-planvpn/ra-cloudvpn.png)

この Azure RemoteApp コレクションでは、RemoteApp セッション ホストがアクセスする必要があるすべてのリソースが Azure にデプロイされています。 これらは Azure 内の RemoteApp VNET と同じ VNET にも、別の VNET にもデプロイできます。

### VNET を使用した Azure RemoteApp ハイブリッド コレクション

![Azure RemoteApp - VNET を使用したハイブリッド コレクション](./media/remoteapp-planvpn/ra-hybridvpn.png)

この Azure RemoteApp コレクションでは、RemoteApp セッション ホストがアクセスする必要がある一部のリソースがオンプレミスでデプロイされています。 RemoteApp VNET は、サイト間 VPN または Express Route のような Azure のハイブリッド テクノロジを使用して、オンプレミスのネットワークにリンクされます。


## システムの動作

内部では、Azure RemoteApp は、プロビジョニング中に選択した仮想ネットワークのサブネットに、(アップロードされたイメージを使用して) Azure 仮想マシンをデプロイします。 ハイブリッド コレクションを選択した場合は、仮想ネットワークで提供される DNS サーバーのプロビジョニング ワークフローで入力したドメイン コントローラーの FQDN の解決が試行されます。  
既存の仮想ネットワークに接続する場合は、Azure RemoteApp のサブネットでネットワーク セキュリティ グループ内の必要なポートを公開してください。

使用することをお勧めする [Azure RemoteApp の十分な大きさのサブネット](remoteapp-vnetsizing.md)します。 Azure Virtual Network でサポートされている最大数は /8 です (CIDR のサブネット定義を使用)。 サブネットは、スケール アップ時に Azure RemoteApp のすべての VM を収容するのに十分な規模にし、より多くのユーザーがアプリにアクセスできるようにする必要があります。

次は、仮想ネットワークのサブネット上で有効にする必要がある機能を示しています。

2.  サブネットからの送信トラフィックは、内部の Azure RemoteApp サービスのいずれかと通信するために、ポート範囲 10101 ～ 10175 で許可されている必要があります。
3.  ポート 443 で、Azure Storage に接続するサブネットからの送信トラフィックを許可する必要があります。
4.  Active Directory を Azure でホストしている場合は、Azure RemoteApp 用の仮想ネットワークのサブネットで、どの VM もドメイン コントローラーに接続できることを確認します。 仮想ネットワークの DNS によって、このドメイン コントローラーの FQDN を解決できる必要があります。


## 強制トンネリングを使用した仮想ネットワーク

[強制トンネリング](vpn-gateway-about-forced-tunneling.md) すべての新しい Azure RemoteApp コレクションのサポートが追加されました。 現在、既存のコレクションを移行して、強制トンネリングをサポートすることはできません。 コレクションで強制トンネリングを有効にするには、Azure RemoteApp にリンクしている VNET を使用する既存のコレクションをすべて削除し、新しいコレクションを作成する必要があります。





