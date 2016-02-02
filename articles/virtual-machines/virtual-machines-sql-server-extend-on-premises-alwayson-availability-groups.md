<properties 
    pageTitle="オンプレミスの AlwaysOn 可用性グループの Azure への拡張 | Microsoft Azure"
    description="このチュートリアルでは、クラシック デプロイ モデルで作成されたリソースを使用し、SQL Server Management Studio (SSMS) のレプリカの追加ウィザードを使用して、Azure に AlwaysOn 可用性グループ レプリカを追加する方法について説明します。"
    services="virtual-machines"
    documentationCenter="na"
    authors="rothja"
    manager="jeffreyg"
    editor="monicar"
    tags="azure-service-management"/>

<tags 
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="11/13/2015"
    ms.author="jroth" />


# オンプレミスの AlwaysOn 可用性グループの Azure への拡張

AlwaysOn 可用性グループは、セカンダリ レプリカを追加することで、データベースのグループの高可用性を実現します。 これらのレプリカを使用すると、障害発生時にデータベースをフェールオーバーできます。 また、読み取りワークロードやバックアップ タスクをオフロードすることもできます。

SQL Server を含む 1 つまたは複数の Azure VM をプロビジョニングし、その VM をレプリカとしてオンプレミスの可用性グループに追加することで、オンプレミスの可用性グループを Microsoft Azure に拡張できます。

このチュートリアルでは、以下があることを前提としています。

- 有効な Azure サブスクリプション 実行できます [無料評価版にサインアップ](http://azure.microsoft.com/pricing/free-trial)します。

- 既存のオンプレミスの AlwaysOn 可用性グループ。 可用性グループの詳細については、次を参照してください。 [AlwaysOn 可用性グループ](https://msdn.microsoft.com/library/hh510230.aspx)します。

- オンプレミスのネットワークと、Azure Virtual Network 間の接続。 この仮想ネットワークの作成の詳細については、次を参照してください。 [Azure クラシック ポータルでサイト間 VPN の構成](../vpn-gateway/vpn-gateway-site-to-site-create.md)します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。

## Azure レプリカ ウィザードの追加

このセクションでは、**Azure レプリカの追加ウィザード**を使用して、AlwaysOn 可用性グループ ソリューションを拡張し、Azure レプリカを含める方法について説明します。

1. SQL Server Management Studio で、**[AlwaysOn 高可用性]**、**[可用性グループ]**、**[<可用性グループの名前>]** の順に展開します。

1. **[可用性レプリカ]** を右クリックし、**[レプリカの追加]** をクリックします。

1. 既定では、**可用性グループへのレプリカの追加ウィザード**が表示されます。 **[次へ]** をクリックします。 このウィザードを以前に起動したときにページの下部にある **[次回からこのページを表示しない]** チェック ボックスをオンにした場合は、この画面は表示されません。

    ![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742861.png)

1. 既存のすべてのセカンダリ レプリカに接続する必要があります。 各レプリカの横にある **[接続]** をクリックするか、画面の下部にある **[すべての接続]** をクリックできます。 認証後、**[次へ]** をクリックして、次の画面に進みます。

1. **[レプリカの指定]** ページの上部には、いくつかのタブ (**[レプリカ]**、**[エンドポイント]**、**[バックアップの設定]**、および **[リスナー]**) が表示されます。 **[レプリカ]** タブの **[Azure レプリカの追加]** をクリックして、Azure レプリカの追加ウィザードを起動します。

    ![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742863.png)

1. 以前に Azure 管理証明書をインストールしている場合は、ローカルの Windows 証明書ストアから既存の Azure 管理証明書を選択します。 以前に Azure サブスクリプションを利用したことがある場合は、Azure サブスクリプションの ID を選択するか入力します。 [ダウンロード] をクリックすると、Azure 管理証明書をダウンロードしてインストールし、Azure アカウントを使用しているサブスクリプションの一覧をダウンロードすることができます。

    ![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742864.png)

1. ページの各フィールドに、レプリカをホストする Azure 仮想マシン (VM) を作成するために使用する値を入力します。

   | 設定| 説明|
|---|---|
| **イメージ**| OS と SQL Server の目的の組み合わせを選択します。|
| **VM サイズ**| ビジネス ニーズに最も適した VM のサイズを選択します。|
| **VM 名**| 新しい VM の一意の名前を指定します。名前は、3 ～ 15 文字で指定する必要があります。文字、数字、およびハイフンのみ使用でき、文字で始まり、文字または数字で終わる必要があります。|
| **VM ユーザー名**| VM の管理者アカウントになるユーザー名を指定します。|
| **VM 管理者パスワード**| 新しいアカウントのパスワードを指定します。|
| **パスワードの確認**| 確認のためにもう一度新しいアカウントのパスワードを入力します。|
| **Virtual Network**| 新しい VM で使用する Azure Virtual Network を指定します。仮想ネットワークの詳細については、次を参照してください。 [仮想ネットワークの概要](..\virtual-network\virtual-networks-overview.md)します。|
| **Virtual Network サブネット**| 新しい VM で使用する仮想ネットワーク サブネットを指定します。|
| **ドメイン**| あらかじめ入力されているドメインの値が正しいことを確認します。|
| **ドメイン ユーザー名**| ローカル クラスター ノード上のローカルの Administrators グループに属しているアカウントを指定します。|
| **パスワード**| ドメイン ユーザー名のパスワードを指定します。|

1. **[OK]** をクリックして、デプロイ設定を検証します。

1. 次に、法律条項が表示されます。 これらの条項を読み、同意する場合は **[OK]** をクリックします。

1. **[レプリカの指定]** ページが再び表示されます。 **[レプリカ]**、**[エンドポイント]**、および **[バックアップの設定]** の各タブで新しい Azure レプリカの設定を確認します。 ビジネス要件に合わせて設定を変更します。 これらのタブに表示されるパラメーターの詳細については、次を参照してください。 [レプリカの指定] ページ (新しい可用性グループ ウィザード/追加レプリカ ウィザード)](https://msdn.microsoft.com/library/hh213088.aspx)します。[リスナー] タブを使用して、Azure のレプリカが含まれている可用性グループ リスナーを作成できないことに注意してください。 また、ウィザードを起動する前にリスナーを既に作成している場合は、そのリスナーが Azure ではサポートされないことを示すメッセージが表示されます。 リスナーを作成する方法については、「**可用性グループ リスナーの作成**」セクションで説明します。

    ![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742865.png)

1. **[次へ]** をクリックします。

1. **[最初のデータの同期を選択]** ページで、使用するデータの同期方法を選択し、**[次へ]** をクリックします。 ほとんどのシナリオでは、**[完全データ同期]** を選択します。 データの同期方法の詳細については、次を参照してください。 [選択初期データの同期] ページ (AlwaysOn 可用性グループ ウィザード)](https://msdn.microsoft.com/library/hh231021.aspx)します。

1. **[検証]** ページで結果を確認します。 未解決の問題を修正し、必要に応じて、検証を再実行します。 **[次へ]** をクリックします。

    ![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742866.png)

1. **[概要]** ページで設定を確認し、**[完了]** をクリックします。

1. プロビジョニング プロセスが開始されます。 ウィザードが正常に完了したら、**[閉じる]** をクリックして、ウィザードを終了します。

>[AZURE.NOTE] Azure のレプリカの追加ウィザード内のログ ファイルを作成する <Users>\<user name>\appdata\local\sql します。このログ ファイルは、障害が発生した Azure レプリカのデプロイのトラブルシューティングに使用できます。ウィザードで操作の実行に失敗すると、プロビジョニングされた VM が削除されるなど、前のすべての操作がロールバックされます。

## 可用性グループ リスナーの作成

可用性グループを作成したら、レプリカに接続するクライアントのリスナーを作成する必要があります。 リスナーは、着信接続をプライマリ レプリカまたは読み取り専用のセカンダリ レプリカに転送します。 リスナーの詳細については、次を参照してください。 [Azure で AlwaysOn 可用性グループ用の ILB リスナーを構成する](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)します。

## 次のステップ

**Azure レプリカの追加ウィザード**を使用して AlwaysOn 可用性グループを Azure に拡張するだけでなく、一部の SQL Server ワークロードを完全に Azure に移行することもできます。 最初に、次を参照してください。 [Azure で SQL Server 仮想マシンをプロビジョニング](virtual-machines-provision-sql-server.md)します。

Azure Vm での SQL Server の実行に関連するその他のトピックを参照してください。 [Azure 仮想マシン上の SQL Server](virtual-machines-sql-server-infrastructure-services.md)します。





