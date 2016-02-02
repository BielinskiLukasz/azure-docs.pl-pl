<properties
    pageTitle="Virtual Network での HBase クラスターのプロビジョニング | Microsoft Azure"
    description="Azure HDInsight での HBase の使用の概要Azure Virtual Network での HDInsight HBase クラスターの作成方法について説明します。"
    keywords=""
    services="hdinsight,virtual-network"
    documentationCenter=""
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/02/2015"
   ms.author="jgao"/>


# Azure Virtual Network での HBase クラスターのプロビジョニング

Azure HDInsight HBase クラスターを作成する方法について、 [Azure 仮想ネットワーク ][1]します。

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Azure Virtual Network での HBase クラスターをプロビジョニングします。](hdinsight-hbase-provision-vnet.md)

アプリケーションが HBase と直接通信できるように、仮想ネットワーク統合を使用して、HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。 次の利点があります。

- Web アプリケーションと HBase クラスターのノード間の直接接続により、HBase Java リモート プロシージャ コール (RPC) API を使用した通信が可能になります。
- トラフィックが複数のゲートウェイやロード バランサーを経由しないためパフォーマンスが向上します。
- 機密情報は、パブリック エンドポイントに公開されることなく、より安全な方法で処理できます。

## 前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。 参照してください [取得 Azure 無料試用版](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)します。

- **Azure PowerShell を実行できるワークステーション**。 参照してください [のインストールおよび使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)します。 手順については、次を参照してください。 [をインストールし、Azure PowerShell を構成](../install-configure-powershell.md)します。 PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを *RemoteSigned* に設定する必要があります。 参照してください [Set-executionpolicy コマンドレット ][2]します。

    Azure PowerShell スクリプトを実行する前に、次のコマンドレットを使用して Azure サブスクリプションに接続されていることを確認します。

        Add-AzureAccount

    Azure サブスクリプションが複数ある場合は、次のコマンドレットを使用して、現在のサブスクリプションを設定します。

        Select-AzureSubscription <AzureSubscriptionName>



## HBase クラスターを仮想ネットワークへプロビジョニングする

HBase クラスターをプロビジョニングする前に、Azure 仮想ネットワークが必要になります。

**Azure クラシック ポータルを使用して仮想ネットワークを作成するには**

1. サインイン、 [Azure Classic Portal ][azure-portal]します。
2. 左下隅にある **[新規]** をクリックし、**[Network Services]**、**[Virtual Network]**、**[簡易作成]** の順にクリックします。
3. 次の値を入力または選択します。

    - **名前**: 仮想ネットワークの名前。
    - **アドレス空間** -アドレス空間に、クラスター内のすべてのノードのアドレスを提供するのに十分な仮想ネットワークを選択します。 そうでないと、プロビジョニングは失敗します。 このチュートリアルを読み進めるには、3 つの中から任意のアドレス空間を選択できます。
    - **最大 VM 数**: 仮想マシン (VM) の最大数のいずれかを選択します。 この値は、アドレス空間内に作成できる潜在的なホスト (VM) の数を決定します。 このチュートリアルを進めるには、**4096 [CIDR: /20]** で十分です。
    - **場所**: 場所は作成する HBase クラスターと同じである必要があります。
    - **DNS サーバー**: このチュートリアルでは、Azure が提供する内部のドメイン ネーム システム (DNS) サーバーを使用するため **[なし]** を選択できます。 カスタム DNS サーバーを使用した、より高度なネットワーク構成もサポートされています。 詳細については、次を参照してください。 [名前解決 (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)します。
4. 右下にある **[仮想ネットワークの作成]** をクリックします。 新しい仮想ネットワーク名が一覧に表示されます。 [ステータス] 列に **[作成済み]** が表示されるまで待機します。
5. メイン ウィンドウで、作成した仮想ネットワークをクリックします。
6. ページの上部にある **[ダッシュボード]** をクリックします。
7. **[概要]** の下の [仮想ネットワーク ID] を書き留めます。 この ID は、HBase クラスターのプロビジョニング時に必要です。
8. ページの上部にある **[構成]** をクリックします。
9. ページの下部に示される既定のサブネット名は、**Subnet-1** です。 必要に応じて、HBase クラスターのサブネット名を変更したり、新しいサブネットを追加したりできます。 サブネット名を書き留めます。これは、クラスターのプロビジョニング時に必要となります。
10. クラスター用に使用するサブネットの **[CIDR (アドレス数)]** を確認します。 アドレス数は、ワーカー ノード数に 7 (ゲートウェイ: 2、ヘッドノード: 2、Zookeeper: 3) を加えた合計よりも多くする必要があります。 たとえば、10 ノードの HBase クラスターが必要な場合、サブネットのアドレス数は、17 (10+7) を超えている必要があります。 17 以下の場合、デプロイは失敗します。
11. サブネット値を更新した場合は、ページ下部の **[保存]** をクリックします。


**DNS サーバーの仮想マシンを仮想ネットワークに追加するには**

DNS サーバーはオプションですが、場合によっては必要になります。 手順が記載されて [2 つの Azure 仮想ネットワーク ][hdinsight-hbase-replication-dns]します。 基本的には次の手順を実行する必要があります。

1. Azure 仮想マシンを仮想ネットワークに追加する
2. 仮想マシンに静的 IP アドレスを設定する
3. 仮想マシンの DNS サーバー ロールを追加する
4. 仮想ネットワークへ DNS サーバーを割り当てる


**Azure ストレージ アカウントと BLOB ストレージ コンテナーを作成してクラスターで使用するには**
> [AZURE.NOTE] HDInsight クラスターは、データ保存するために Azure BLOB ストレージを使用します。 詳細については、次を参照してください。 [Azure Blob ストレージの使用 HDInsight での Hadoop](../hdinsight-use-blob-storage.md)します。 ストレージ アカウントと BLOB ストレージ コンテナーが必要です。 ストレージ アカウントの場所は、仮想ネットワークの場所およびクラスターの場所と一致している必要があります。

その他の HDInsight クラスターのように HBase クラスターにも既定のファイル システムとして Azure ストレージ アカウントと BLOB ストレージ コンテナーが必要になります。 ストレージ アカウントの場所は、仮想ネットワークの場所およびクラスターの場所と一致している必要があります。 詳細については、次を参照してください。 [[hdinsight のストレージ] の HDInsight での Hadoop での使用 Azure Blob ストレージ][hdinsight-storage]します。 HBase クラスターをプロビジョニングする場合、新規作成するか既存のものを使用するオプションがあります。 この手順では、Azure クラシック ポータルを使用したストレージ アカウントと BLOB ストレージ コンテナーを作成する方法を説明します。

1. サインイン、 [Azure Classic Portal ][azure-portal]します。
2. 左下隅にある **[新規]** をクリックし、**[Data Services]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

3. 次の値を入力または選択します。

    - **URL**: ストレージ アカウントの名前。
    - **場所**: ストレージ アカウントの場所。 仮想ネットワークの場所と一致していることを確認します。 アフィニティ グループはサポートされていません。
    - **レプリケーション**: テストの目的には、**[ローカル冗長]** を使用してコストを削減します。

4. **[ストレージ アカウントの作成]** をクリックします。 新しいストレージ アカウントがストレージ一覧に表示されます。
5. 新しいストレージ アカウントの **[状態]** 列が **[オンライン]** になるまで待ちます。
6. 一覧の新しいストレージ アカウントをクリックして選択します。
7. ページの下部にある **[アクセス キーの管理]** をクリックします。
8. ストレージ アカウント名とプライマリ アクセス キーやセカンダリ アクセス キー (動作しているいずれかのキー) の値を書き留めます。 この情報は後で必要になります。
9. ページの上部にある **[コンテナー]** をクリックします。
10. ページの下部にある **[追加]** をクリックします。
11. コンテナーの名前を入力します。 このコンテナーは、HBase クラスターの既定のコンテナーとして使用されます。 既定では、既定のコンテナー名はクラスター名に一致します。 **[アクセス]** フィールドは **[プライベート]** のままにします。
12. チェックマークをクリックしてコンテナーを作成します。

**Azure クラシック ポータルを使用して HBase クラスターをプロビジョニングするには**
> [AZURE.NOTE] Azure PowerShell を使用して、新しい HBase クラスターのプロビジョニング方法の詳細については、次を参照してください。 [Azure PowerShell を使用して HBase クラスターのプロビジョニング](#powershell)します。

1. サインイン、 [Azure Classic Portal ][azure-portal]します。

2. 左下にある **[新規]** をクリックし、**[Data Services]**、**[HDINSIGHT]** の順にポイントして、**[カスタム作成]** をクリックします。

3. クラスターの名前を入力して、クラスターの種類として HBase を選択し、Windows Server 2012 オペレーティング システムを選択します。その後 HDInsight のバージョンを選択して右側のボタンをクリックします。

    ![Provide details for the HBase cluster][img-provision-cluster-page1]

    > [AZURE.NOTE] HBase クラスターの場合、使用可能な OS オプションは Windows Server のみになります。

4. **[クラスターの構成]** ページで、次を入力するか選択します。

    ![Provide details for the HBase cluster](./media/hdinsight-hbase-provision-vnet/hbasewizard2.png)

   <table border="1">
        <tr><th>プロパティ</th><th>値</th></tr>
        <tr><td>データ ノード</td><td>デプロイするデータ ノードの数を選択します。テストでは、単一ノード クラスターを作成します。 <br />クラスター サイズの制限は、Azure サブスクリプションによって変わります。制限値を上げるには、Azure の課金サポートにお問い合わせください。</td></tr>
        <tr><td>リージョン/仮想ネットワーク</td><td><p>リージョン、またはすでに作成済みの場合は Azure 仮想ネットワークを選択します。このチュートリアルでは、以前に作成したネットワークの選択し、対応するサブネットを選択します。既定の名前は <b>サブネット 1</b>.</p></td></tr>
        <tr><td>ヘッド ノード サイズ</td><td><p>ヘッド ノードの VM サイズを選択します。</p></td></tr>
        <tr><td>データ ノード サイズ</td><td><p>データ ノードの VM サイズを選択します。</p></td></tr>
        <tr><td>Zookeeper サイズ</td><td><p>Zookeeper ノードの VM サイズを選択します。</p></td></tr>
    </table>
    >[AZURE.NOTE] VM の選択に基づき、料金が異なる場合があります。 HDInsight は、クラスター ノードにすべて Standard レベルの VM を使用します。 VM のサイズに応じた料金の方法については、次を参照してください。 <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 料金</a>します。

    右側のボタンをクリックします。

5. このクラスターで使用する Hadoop ユーザーの [ユーザー名] と [パスワード] を入力してから、右側のボタンをクリックします。

    ![Provide Storage account for Hadoop HDInsight cluster](./media/hdinsight-hbase-provision-vnet/hbasewizard3.png)

   <table border="1">
        <tr><th>プロパティ</th><th>値</th></tr>
        <tr><td>HTTP ユーザー名</td>
            <td>HDInsight クラスターのユーザー名を指定します。</td></tr>
        <tr><td>HTTP パスワード / パスワードの確認</td>
            <td>HDInsight クラスターのユーザー パスワードを指定します。</td></tr>
        <tr><td>Enable remote desktop for cluster (クラスターのリモート デスクトップ アクセスを有効にする)</td>
            <td>このチェック ボックスをオンにして、プロビジョニングされるとクラスター ノードにリモートにできるリモート デスクトップ ユーザーのユーザー名、パスワード、有効期限を指定します。また、クラスターがプロビジョニングされた後にリモート デスクトップを有効にすることもできます。手順については、 <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP を使用した HDInsight クラスターへの接続</a>.</td></tr>
    </table>

6. **[ストレージ アカウント]** ページで、次の値を指定します。

    ![Provide Storage account for Hadoop HDInsight cluster](./media/hdinsight-hbase-provision-vnet/hbasewizard4.png)

   <table border="1">
        <tr><th>プロパティ</th><th>値</th></tr>
        <tr><td>ストレージ アカウント</td>
            <td>HDInsight クラスターの既定のファイル システムとして使用する Azure ストレージ アカウントを指定します。次の 3 つのオプションのいずれかを選択できます。
            <ul>
                <li><strong>既存のストレージを使用する</strong></li>
                <li><strong>新しいストレージを作成する</strong></li>
                <li><strong>別のサブスクリプションのストレージを使用する</strong></li>
            </ul>
            </td></tr>
        <tr><td>アカウント名</td>
            <td><ul>
                <li>既存のストレージを使用する場合は、[ <strong>アカウント名</strong>、既存のストレージ アカウントを選択します。ドロップダウンの一覧には、クラスターをプロビジョニングする対象として選択したデータ センターにあるストレージ アカウントのみが表示されます。</li>
                <li>[ <strong>新しいストレージを作成する</strong> または <strong>別のサブスクリプションのストレージを使用する</strong> オプションでは、ストレージ アカウント名を指定する必要があります。</li>
            </ul></td></tr>
        <tr><td>アカウント キー</td>
            <td>[ <strong>別のサブスクリプションのストレージを使用する</strong> オプションで、そのストレージ アカウントのアカウント キーを指定します。</td></tr>
        <tr><td>既定のコンテナー</td>
            <td><p>ストレージ アカウントの既定のコンテナーを指定します。既定のコンテナーは、HDInsight クラスターの既定のファイル システムとして使用されます。[ <strong>既存のストレージを使用する</strong> を指定して <strong>ストレージ アカウント</strong> フィールドが既定では、クラスター名と同じ名前のコンテナーは、そのアカウントに既存のコンテナーが作成されません。クラスター名と同じ名前のコンテナーが既に存在する場合は、コンテナー名に連番が付加されます。たとえば、mycontainer1、mycontainer2、などとなります。ただし、既存のストレージ アカウントに指定したクラスター名と異なる名前のコンテナーがある場合は、そのコンテナーを使用できます。</p>
            <p>新しいストレージの作成または別の Azure サブスクリプションのストレージの使用を選択した場合は、既定のコンテナー名を指定する必要があります。</p>
        </td></tr>
        <tr><td>追加のストレージ アカウント</td>
            <td>必要に応じてクラスターに追加するストレージ アカウントを指定します。HDInsight は複数のストレージ アカウントをサポートします。クラスターで使用できる追加ストレージ アカウントの数に制限はありません。ただし、Azure クラシック ポータルを使用してクラスターを作成する場合は、UI の制約により 7 が上限になります。各追加のストレージ アカウントを指定する追加を追加します。 <strong>ストレージ アカウント</strong> ページがウィザードをアカウント情報を指定することができます。たとえば、上のスクリーンショットでは、追加のストレージ アカウントが選択されていないため、ページがウィザードに追加されることはありません。</td></tr>
    </table>

    右矢印をクリックします。

7. **[Script Actions]** ページで、右下にあるチェック マークを選択します。 このチュートリアルではクラスターの設定をカスタマイズする必要はないため、**[スクリプト アクションの追加]** はクリックしないでください。

    ![Configure Script Action to customize an HDInsight HBase cluster][img-provision-cluster-page5]
    > [AZURE.NOTE] このページは、セットアップ時のクラスターのカスタマイズで使用できます。 詳細については、次を参照してください。 [をカスタマイズする HDInsight クラスターの Script Action を使って](hdinsight-hadoop-customize-cluster.md)します。

新しい HBase クラスターの使用を開始するには」に記載の手順を使用することができます [HBase を HDInsight での Hadoop を使い始める](../hdinsight-hbase-get-started.md)します。

## HBase Java RPC API を使用して、仮想ネットワーム内でプロビジョニングされた HBase クラスターへ接続する

1.  サービスとしてのインフラストラクチャ (IaaS) 仮想マシンを同じ Azure 仮想ネットワークと同じサブネットに対してプロビジョニングします。 それにより、仮想マシンと HBase クラスターはどちらも同じ内部 DNS サーバーを使用してホスト名を解決します。 そのためには、**[ギャラリーから]** を選択し、データ センターの代わりに仮想ネットワークを選択する必要があります。 手順については、次を参照してください。 [Windows Server を実行している仮想マシンを作成する](../virtual-machines-windows-tutorial.md)します。 標準の Windows Server 2012 イメージとサイズの小さい VM で十分です。

2.  Java アプリケーションを使用して HBase にリモートで接続する場合は、完全修飾ドメイン名 (FQDN) を使用する必要があります。 これを確認するには、HBase クラスターの接続固有の DNS サフィックスを取得する必要があります。 それには、Curl を使用して Ambari を照会するか、リモート デスクトップを使用してクラスターに接続します。

* **Curl**: 次のコマンドを実行します。

        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

    返された JavaScript Object Notation (JSON) データで、"host_name" エントリを見つけます。 これには、クラスターのノードの FQDN が含まれています。 次に例を示します。

        ...
        "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
        ...

    クラスター名で始まるドメイン名の部分は、DNS サフィックスです。 たとえば、mycluster.b1.cloudapp.net です。

* **Azure PowerShell** - 次の Azure PowerShell スクリプトを使用して、DNS サフィックスを返すことができる **Get-ClusterDetail** 関数を登録します。

      function Get-ClusterDetail(
          [String]
          [Parameter( Position=0, Mandatory=$true )]
          $ClusterDnsName,
          [String]
          [Parameter( Position=1, Mandatory=$true )]
          $Username,
          [String]
          [Parameter( Position=2, Mandatory=$true )]
          $Password,
          [String]
          [Parameter( Position=3, Mandatory=$true )]
          $PropertyName
          )
      {
      <#
          .SYNOPSIS
           Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
          .Description
           This command shows the following 4 properties of an HDInsight cluster:
           1. ZookeeperQuorum (supports only HBase type cluster)
              Shows the value of HBase property "hbase.zookeeper.quorum".
           2. ZookeeperClientPort (supports only HBase type cluster)
              Shows the value of HBase property "hbase.zookeeper.property.clientPort".
           3. HBaseRestServers (supports only HBase type cluster)
              Shows a list of host FQDNs that run the HBase REST server.
           4. FQDNSuffix (supports all cluster types)
              Shows the FQDN suffix of hosts in the cluster.
          .EXAMPLE
           Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
           This command shows the value of HBase property "hbase.zookeeper.quorum".
          .EXAMPLE
           Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
           This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
          .EXAMPLE
           Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
           This command shows a list of host FQDNs that run the HBase REST server.
          .EXAMPLE
           Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
           This command shows the FQDN suffix of hosts in the cluster.
      #>
    
          $DnsSuffix = ".azurehdinsight.net"
    
          $ClusterFQDN = $ClusterDnsName + $DnsSuffix
          $webclient = new-object System.Net.WebClient
          $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
    
          if($PropertyName -eq "ZookeeperQuorum")
          {
              $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
              $Response = $webclient.DownloadString($Url)
              $JsonObject = $Response | ConvertFrom-Json
              Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
          }
          if($PropertyName -eq "ZookeeperClientPort")
          {
              $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
              $Response = $webclient.DownloadString($Url)
              $JsonObject = $Response | ConvertFrom-Json
              Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
          }
          if($PropertyName -eq "HBaseRestServers")
          {
              $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
              $Response1 = $webclient.DownloadString($Url1)
              $JsonObject1 = $Response1 | ConvertFrom-Json
              $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'
    
              $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
              $Response2 = $webclient.DownloadString($Url2)
              $JsonObject2 = $Response2 | ConvertFrom-Json
              foreach ($host_component in $JsonObject2.host_components)
              {
                  $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                  Write-host $ConnectionString
              }
          }
          if($PropertyName -eq "FQDNSuffix")
          {
              $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
              $Response = $webclient.DownloadString($Url)
              $JsonObject = $Response | ConvertFrom-Json
              $FQDN = $JsonObject.host_components[0].HostRoles.host_name
              $pos = $FQDN.IndexOf(".")
              $Suffix = $FQDN.Substring($pos + 1)
              Write-host $Suffix
          }
      }

  Azure PowerShell スクリプトを実行した後、次のコマンドで **Get-ClusterDetail** 関数を使用して DNS サフィックスを返します。 このコマンドを使用する場合は、HDInsight HBase のクラスター名、管理者名、および管理者のパスワードを指定します。

      Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

  これは、DNS サフィックスを返します。 たとえば、**yourclustername.b4.internal.cloudapp.net** です。

> [AZURE.NOTE] リモート デスクトップを使用して HBase クラスターに接続し (ヘッド ノードに接続されます)、コマンド プロンプトから **ipconfig** を実行して DNS サフィックスを取得することもできます。 リモート デスクトップ プロトコル (RDP) を有効にする RDP を使用して、クラスターに接続する手順については、次を参照してください。 [Azure Classic Portal ][hdinsight-admin-portal]します。
>
> ![hdinsight.hbase.dns.surffix][img-dns-surffix]




仮想マシンが HBase クラスターと通信できることを確認するには、コマンドを使用 `ping headnode0. < dns サフィックス >` バーチャル マシンからです。たとえば、「ping headnode0.mycluster.b1.cloudapp.net」のように入力します。

この情報を使用して、Java アプリケーションで、」の手順を行うことができる [を Maven で HBase を HDInsight (Hadoop) で使用する Java アプリケーションを構築](hdinsight-hbase-build-java-maven.md) 、アプリケーションを作成します。 アプリケーションをリモート HBase サーバーに接続するには、Zookeeper の FQDN を使用するように、この例の **hbase-site.xml** ファイルを変更します。 次に例を示します。

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [AZURE.NOTE] Azure での名前解決の詳細については、独自の DNS サーバーを使用する方法などの仮想ネットワークを参照してください [名前解決 (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)します。

## Azure PowerShell を使用して HBase クラスターをプロビジョニングする

**Azure PowerShell を使用して HBase クラスターをプロビジョニングするには**

1. Azure PowerShell Integrated Scripting Environment (ISE) を開きます。
2. 次のコマンドをコピーしてスクリプト ウィンドウに貼り付けます。

     $hbaseClusterName = "<HBaseClusterName>"
     $hadoopUserName = "<HBaseClusterUsername>"
     $hadoopUserPassword = "<HBaseClusterUserPassword>"
     $location = "<HBaseClusterLocation>"  #i.e. "West US"
     $clusterSize = <HBaseClusterSize>  
     $vnetID = "<AzureVirtualNetworkID>"
     $subNetName = "<AzureVirtualNetworkSubNetName>"
     $storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
     $storageAccountKey = "<AzureStorageAccountKey>"
     $storageContainerName = "<AzureBlobStorageContainer>"
    
     $password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
     $creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)
    
     New-AzureHDInsightCluster -Name $hbaseClusterName `
                               -ClusterType HBase `
                               -Version 3.1 `
                               -Location $location `
                               -ClusterSizeInNodes $clusterSize `
                               -Credential $creds `
                               -VirtualNetworkId $vnetID `
                               -SubnetName $subNetName `
                               -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                               -DefaultStorageAccountKey $storageAccountKey `
                               -DefaultStorageContainerName $storageContainerName

3. **[スクリプトの実行]** をクリックするか、**F5** キーを押します。
4. クラスターを検証するために、Azure クラシック ポータルからクラスターを確認するか、下部のウィンドウから次の Azure PowerShell コマンドレットを実行することができます。

    Get-AzureHDInsightCluster

## 次のステップ

このチュートリアルでは、HBase クラスターのプロビジョニング方法を学習しました。 詳細については、次を参照してください。

- [HDInsight を概要します。](../hdinsight-get-started.md)
- [HDInsight での HBase レプリケーションを構成します。](hdinsight-hbase-geo-replication.md)
- [HDInsight で Hadoop クラスターをプロビジョニングします。](hdinsight-provision-clusters.md)
- [HDInsight での Hadoop で HBase を使用します。](../hdinsight-hbase-get-started.md)
- [HDInsight での HBase で Twitter のセンチメントを分析します。](../hdinsight-hbase-twitter-sentiment.md)
- [仮想ネットワークの概要 ][vnet-overview]



[1]: http://azure.microsoft.com/services/virtual-network/ 
[2]: http://technet.microsoft.com/library/ee176961.aspx 
[3]: http://technet.microsoft.com/library/hh847889.aspx 
[hbase-get-started]: ../hdinsight-hbase-get-started.md 
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md 
[vnet-overview]: ../virtual-network/virtual-networks-overview.md 
[vm-create]: ../virtual-machines-windows-tutorial.md 
[azure-portal]: https://management.windowsazure.com 
[azure-create-storageaccount]: ../storage-create-storage-account.md 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/ 
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/ 
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/ 
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md 
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp 
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx 
[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis 
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter 
[powershell-install]: ../install-configure-powershell.md 
[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md 
[hdinsight-provision]: hdinsight-provision-clusters.md 
[hdinsight-get-started]: ../hdinsight-get-started.md 
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell 
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md 
[hdinsight-storage]: ../hdinsight-use-blob-storage.md 
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md 
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md 
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md 
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md 
[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png 
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png 
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Provision details for the new HBase cluster"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Use Script Action to customize an HBase cluster"

