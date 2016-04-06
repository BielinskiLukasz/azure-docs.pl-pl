<properties
 pageTitle="Excel と SOA 用の HPC Pack クラスター | Microsoft Azure"
 description="リソース マネージャーのデプロイ モデルを使用して HPC Pack クラスターを開始して Excel と SOA ワークロードを実行します。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="11/11/2015"
 ms.author="danlep"/>

# Azure で HPC Pack クラスターを開始して Excel と SOA ワークロードを実行する

この記事では、Azure クイックスタート テンプレートまたは Azure PowerShell デプロイ スクリプトを使用して Azure インフラストラクチャ サービス (IaaS) に HPC Pack クラスターをデプロイする方法を示します。 HPC Pack で Microsoft Excel またはサービス指向アーキテクチャ (SOA) のワークロードを実行するように設計されている Azure Marketplace VM イメージを使用します。 クラスターを使用して、オンプレミスのクライアント コンピューターから簡単な Excel HPC サービスおよび SOA サービスを実行できます。 Excel の HPC サービスには、Excel ブックのオフロードと Excel ユーザー定義関数、または UDF が含まれます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 従来のデプロイ モデルです。


これから作成する HPC Pack クラスターの概要は次の図のようになります。

![Excel ワークロードを実行するノードを含む HPC クラスター][scenario]

## 前提条件

* **クライアント コンピューター** -Windows ベースのクライアント コンピューター (その展開方法を選択する) 場合は、Azure PowerShell のクラスター デプロイメント スクリプトを実行して、クラスターに、サンプルの Excel と SOA ジョブを送信する必要があります。

* **Azure サブスクリプション** のアカウントを持っていない場合は、ほんの数分で無料の試用アカウントを作成することができます。 詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)をご覧ください。

* **コア クォータ** 、特に、マルチコア VM サイズでいくつかのクラスター ノードを展開する場合、コアのクォータを増やす必要があります。 Azure クイックスタート テンプレートを使用する場合、リソース マネージャーのコア クォータは Azure リージョンごとであることに注意してください。特定のリージョンのクォータを増やす必要がある可能性があります。 参照してください [Azure サブスクリプションの制限、クォータ、および制約](../azure-subscription-service-limits.md)します。 クォータを増やすをするには [オンライン カスタマー サポートに申請](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) 無償でします。


## 手順 1. Azure で HPC Pack クラスターをセットアップする

クラスターをセットアップする 2 つの方法を説明します。1 つめは Azure クイックスタート テンプレートと Azure ポータルを使用する方法で、2 つめは Azure PowerShell デプロイ スクリプトを使用する方法です。


### クイックスタート テンプレートを使用する
Azure クイックスタート テンプレートを使用すると、Azure ポータルで HPC Pack クラスターをすばやく簡単にデプロイできます。 プレビュー ポータルでテンプレートを開くと表示される簡単な UI で、クラスターの設定を入力します。 手順は次のようになります。

1. 参照してください、 [GitHub の HPC クラスターの作成テンプレート ページ](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)します。 必要な場合は、テンプレートとソース コードに関する情報を確認します。

2. クリックして **Deploy to Azure** Azure ポータルでテンプレートを使用して展開を開始します。

    ![テンプレートを Azure にデプロイする][github]

3. プレビュー ポータルで以下の手順に従って、HPC クラスター テンプレートのパラメーターを入力します。

    a.  **テンプレートの編集** ] ページで [ **保存**します。

    ![テンプレートを保存する][template]

    b.  **パラメーター** ] ページで、テンプレート パラメーターの値を入力します。 (各設定の隣のアイコンをクリックするとヘルプ情報が表示されます)。次の画面に示されているのはサンプルの値です。 この例はという名前の新しい HPC Pack クラスターを作成 *hpc01* で、 *hpc.local* ドメイン ヘッド ノードと 2 で構成される計算ノードです。 コンピューティング ノードは、Microsoft Excel を含む HPC Pack VM イメージから作成されます。

    ![パラメーターを入力する][parameters]

    >[AZURE.NOTE]ヘッド ノードから VM が自動的に作成される、 [最新の Marketplace イメージ](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) の Windows Server 2012 R2 上の HPC Pack 2012 R2。 現時点では、イメージは HPC Pack 2012 R2 Update 3 に基づいています。
    >
    >コンピューティング ノードの VM は、選択したコンピューティング ノード ファミリの最新のイメージから作成されます。 選択、 **ComputeNode** 最新の HPC Pack 2012 R2 更新プログラム 3 のオプションは、汎用目的でイメージを計算します。 選択 **ComputeNodeWithExcel** 適しています。 最新の HPC Pack コンピューティング ノード イメージを Microsoft Excel Professional Plus 2013 の評価版が含まれています。 一般的な SOA セッション用または Excel UDF オフロード用にクラスターを展開する場合は、選択、 **ComputeNode** (Excel がインストールされている) なしのオプションです。
    >
    >使用する場合  **ComputeNodeWithExcel** 実稼働ワークロードでは、計算ノード上に Excel をアクティブにする有効な Excel ライセンスを提供する必要があります。 そうしないと、Excel の評価版は 30 日間の有効期限であり、Excel ブックを実行すると COMExeption (0x800AC472) で常に失敗します。 このような場合は、ヘッド ノードにログオンして HPC Cluster Manager コンソールから “%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe” をすべての Excel コンピューティング ノードで clusrun 実行し、さらにExcel の 30 日間の評価日数を再実装します。 猶予期間の再実装の最大回数は 2 回なので、それを過ぎると有効な Excel ライセンスを実装する必要があります。

    c. サブスクリプションを選択します。

    d. クラスターの場合、新しいリソース グループを作成し *hpc01RG*します。

    e. リソース グループの場所を選択します (米国東部など)。

    f.  **法律条項** ] ページで、条項を確認します。 同意する場合はクリックして **購入**します。

    g. テンプレートの値の設定が完了したら、クリックして **作成** します。

    ![クラスターを作成する][create]

3.  デプロイが完了したら (通常約 30 分かかります)、クラスターのヘッド ノードからクラスターの証明書ファイルをエクスポートします。 後の手順でこのパブリック証明書をクライアント コンピューターにインポートし、セキュリティで保護された HTTP バインディングのサーバー側認証を提供します。

    a. Azure ポータルからリモート デスクトップでヘッド ノードに接続します。

     ![ヘッド ノードに接続する][connect]

    b. 標準的な手順で証明書マネージャーを使用して、秘密キーを含まないヘッド ノード証明書 (Cert:\LocalMachine\My の下にあります) をエクスポートします。 この例では、エクスポート *CN = hpc01.eastus.cloudapp.azure.com*します。

    ![証明書をエクスポートする][cert]

### HPC Pack IaaS デプロイ スクリプトを使用する

HPC Pack IaaS デプロイ スクリプトは、HPC Pack クラスターをデプロイするためのもう 1 つの汎用性の高い方法です。 スクリプトは Azure サービス管理 (ASM) モードで実行しますが、テンプレートは Azure リソース マネージャー (ARM) モードで実行します。 また、スクリプトは Azure Global サービスまたは Azure China サービスのサブスクリプションと互換性があります。

**追加の前提条件**

* **Azure PowerShell** - [をインストールし、Azure PowerShell を構成](../powershell-install-configure.md) (バージョン 0.8.10 以降)、クライアント コンピューターにします。

* **HPC Pack IaaS デプロイメント スクリプト** - をダウンロードしてから、スクリプトの最新バージョンを開梱、 [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=44949)します。 `New-HPCIaaSCluster.ps1 –Version` を実行して、スクリプトのバージョンを確認します。 この記事はバージョン 4.5.0 以降のスクリプトに基づきます。

**構成ファイルを作成する**

 HPC Pack IaaS デプロイ スクリプトは HPC クラスターのインフラストラクチャについて記載された XML 構成ファイルを入力として使用します。 Microsoft Excel を含むコンピューティング ノード イメージから作成された 1 個のヘッド ノードと 18 個のコンピューティング ノードで構成されるクラスターをデプロイするには、次のサンプル構成ファイルを実際の環境の値に置き換えます。 詳細については、構成ファイル、スクリプト フォルダーにある Manual.rtf ファイルを参照してくださいと [HPC Pack IaaS デプロイメント スクリプトで HPC クラスターを作成](virtual-machines-hpcpack-cluster-powershell-script.md)します。

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**構成ファイルに関する注意事項**

*  **VMName** ヘッド ノードの **必要があります** と同じでなければ、 **ServiceName**, 、またはを実行すると、SOA ジョブは失敗します。

* 指定するかどうかを確認 **EnableWebPortal** ヘッド ノード証明書が生成され、エクスポートされるようにします。

* 構成後 PowerShell スクリプト PostConfig.ps1 では、Azure ストレージ接続文字列の設定、ヘッド ノードからのコンピューティング ノード ロールの削除、デプロイ後の全ノードのオンライン化など、ヘッド ノードでの特定の設定が構成されます。 スクリプトのサンプルを次に示します。

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**スクリプトを実行する**

1. 管理者としてクライアント コンピューターで PowerShell を開きます。

2. ディレクトリをスクリプト フォルダー (この例では、E:\IaaSClusterScript) に変更します。

    ```
    cd E:\IaaSClusterScript
```

4. 下のコマンドを実行し、HPC Pack クラスターをデプロイします。 この例では、構成ファイルは E:\HPCDemoConfig.xml にあるものと想定しています。

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
```

HPC Pack デプロイ スクリプトの実行には少し時間がかかります。 スクリプトでの処理の 1 つとして、クラスター証明書がエクスポートされてダウンロードされ、クライアント コンピューター上の現在のユーザーの Documents フォルダーに保存されます。 スクリプトでは、次のようなメッセージが生成されます。 次の手順では、適切な証明書ストアに証明書をインポートします。

```
You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
 C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_2015070716
2011.cer
```

## 手順 2. Excel ブックをオフロードし、オンプレミスのクライアントから UDF を実行する

### Excel ブックをオフロードする

以下の手順に従って、Azure の HPC Pack クラスターで実行するように Excel ブックをオフロードします。 そのためには、Excel 2010 または 2013 がクライアント コンピューターに既にインストールされている必要があります。

1. 手順 1 のいずれかの方法を使用して、Excel コンピューティング ノード イメージを含む HPC Pack クラスターをデプロイします。 クラスター証明書ファイル (.cer) およびクラスターのユーザー名とパスワードを取得します。

2. クライアント コンピューターで、クラスター証明書を Cert:\CurrentUser\Root にインポートします。

3. Excel がインストールされていることを確認します。 次のような内容の Excel.exe.config ファイルを作成し、クライアント コンピューター上の Excel.exe と同じフォルダーに保存します。 これにより、HPC Pack 2012 R2 の Excel COM アドインが正常に読み込まれます。

    ```
<?xml version="1.0"?>
<configuration>
    <startup useLegacyV2RuntimeActivationPolicy="true">
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
    </startup>
</configuration>
```
4.  ダウンロード [HPC Pack 2012 R2 Update 3 インストール](http://www.microsoft.com/download/details.aspx?id=49922) HPC Pack クライアントをインストール
ダウンロードしてインストール、 [HPC Pack 2012 R2 更新プログラム 3 クライアント ユーティリティ](https://www.microsoft.com/download/details.aspx?id=49923) し、適切な Visual C 2010、コンピューターの再頒布可能パッケージ ([x64](http://www.microsoft.com/download/details.aspx?id=14632), 、[x86](https://www.microsoft.com/download/details.aspx?id=5555))。

5.  この例で使用できる ConvertiblePricing_Complete.xlsb という名前のダウンロード、サンプルの Excel ブック [ここ](https://www.microsoft.com/en-us/download/details.aspx?id=2939)します。

6.  Excel ブックを D:\Excel\Run などの作業フォルダーにコピーします。

7.  Excel ブックを開きます。  **開発** リボンで、をクリックして **COM アドイン** し、HPC Pack Excel COM アドインが読み込まれている、次に示すように正常に確認します。

    ![HPC Pack 用の Excel アドイン][addin]

8.  VBA マクロ HPCControlMacros を Excel で編集し、次のスクリプトで示されているようにコメント行を変更します。 実際の環境に合わせて適切な値に置き換えます。

    ![HPC Pack 用の Excel マクロ][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.  VBA マクロの HPC_DependsFiles 定数で指定されている D:\Excel\Upload などの upload ディレクトリに、Excel ブックをコピーします。

10. クリックして、 **クラスター** Azure IaaS クラスターでブックを実行するワークシートでボタンをクリックします。

### Excel UDF を実行する

Excel の UDF を実行するには、前記の手順 1 ～ 3 に従ってクライアント コンピューターを設定します。 Excel UDF の場合、Excel アプリケーションがコンピューティング ノードにインストールされている必要はないので、手順 1 では、Excel を含むコンピューティング ノード イメージではなく、通常のコンピューティング ノード イメージを選択できます。

>[AZURE.NOTE] Excel 2010 では 34 文字の制限と 2013年クラスター コネクタ] ダイアログ ボックスがあります。 完全なクラスター名が長い場合は (例: hpcexcelhn01.southeastasia.cloudapp.azure.com)、ダイアログ ボックスに収まりません。 マシン全体の変数の例: 設定を回避するには *CCP_IAASHN* 長いクラスター名と入力の値を持つ *CCP_IAASHN %* はクラスターのヘッド ノード名] ダイアログ ボックス。 更新プログラム 2 の QFE KB3085833 を必要と更新プログラム 2 クラスターの場合、注意してください (ダウンロード [ここ](http://www.microsoft.com/en-us/download/details.aspx?id=48725)) この回避策をサポートするために、クライアント コンピューターに SOA Session API のです。

クラスターが正常にデプロイされた後、引き続き以下の手順に従って、サンプルの組み込み Excel UDF を実行します。 カスタマイズされた Excel udf を参照してください [リソース](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) を Xll を作成し、IaaS クラスターで展開します。

1.  新しい Excel ブックを開きます。  **開発** リボンで、をクリックして **アドイン**します。 次に、ダイアログ ボックスで次のようにクリックします。 **参照**, 、%CCP_HOME%Bin\XLL32 フォルダーに移動し、サンプルの ClusterUDF32.xll を選択します。 ClusterUDF32 がクライアント コンピューターに存在しない場合は、ヘッド ノードの %CCP_HOME%Bin\XLL32 フォルダーからコピーすることができます。

    ![UDF を選択する][udf]

2.  クリックして **ファイル** > **オプション** > **高度な**です。  **数式** チェック **コンピューティング クラスターを実行するユーザー定義の XLL 関数を許可する**です。 クリックし、 **オプション** に完全なクラスター名を入力し、 **クラスター ヘッド ノード名**します。 (説明したように、この入力ボックスには 34 文字の制限があり、長いクラスター名が入らない可能性があります。 ここでは、長いクラスター名にマシン全体の変数を使用できます。)

    ![UDF を構成する][options]

3.  値が =XllGetComputerNameC() であるセルをクリックし、Enter キーを押して、IaaS クラスターで UDF の計算を実行します。 この関数は、UDF が実行しているコンピューティング ノードの名前を取得するだけです。 初めて実行したときは、IaaS クラスターに接続するためのユーザー名とパスワードの入力を求める資格情報ダイアログ ボックスが表示されます。

    ![UDF を実行する][run]

    計算するセルが多い場合は、Alt + Shift + Ctrl + F9 キーを押してすべてのセルに対して計算を実行します。

## 手順 3. オンプレミスのクライアントから SOA ワークロードを実行する

一般的な SOA アプリケーションを HPC Pack IaaS クラスターで実行するには、まず、手順 1 のいずれかの方法に従い、汎用コンピューティング ノード イメージを使用して IaaS クラスターをデプロイします (コンピューティング ノードでは Excel が必要ないため)。 次に、以下の手順に従います。

1. クラスター証明書を取得した後、クライアント コンピューターで Cert:\CurrentUser\Root にインポートします。

2. インストール、 [HPC Pack 2012 R2 更新プログラム 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) と [HPC Pack 2012 R2 更新プログラム 3 クライアント ユーティリティ](https://www.microsoft.com/download/details.aspx?id=49923) を開発および SOA クライアント アプリケーションを実行できるようにします。

3. ダウンロード、HellowWorldR2 [のサンプル コードは](https://www.microsoft.com/download/details.aspx?id=41633)です。 Visual Studio 2010 または 2012 で HelloWorldR2.sln を開きます。

4. 最初に EchoService プロジェクトをビルドし、オンプレミスのクラスターにデプロイするのと同じ方法で、IaaS クラスターにサービスをデプロイします。 詳細な手順については、HelloWordR2 の Readme.doc を参照してください。 以下で説明するように HellowWorldR2 および他のプロジェクトを変更してビルドし、オンプレミスのクライアント コンピューターから Azure IaaS クラスター上で実行する SOA クライアント アプリケーションを生成します。

### Azure ストレージ キューありで Http バインディングを使用する

Azure ストレージ キューで Http バインディングを使用するには、サンプル コードにいくつかの変更を加えます。

* クラスター名を更新します。

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* 必要に応じて、SessionStartInfo の既定の TransportScheme を使用するか、または Http に明示的に設定します。

```
    info.TransportScheme = TransportScheme.Http;
```

* BrokerClient に既定のバインディングを使用します。

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    または、basicHttpBinding の使用を明示的に設定します。

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* 必要に応じて、SessionStartInfo で UseAzureQueue フラグを true に設定します。 これを設定しないと、クラスター名に Azure ドメイン サフィックスが含まれ、TransportScheme が Http の場合は、既定で true に設定されます。

    ```
    info.UseAzureQueue = true;
```

###Azure ストレージ キューなしで Http バインディングを使用する

そのためには、SessionStartInfo で UseAzureQueue フラグを明示的に false に設定します。

```
    info.UseAzureQueue = false;
```

### NetTcp バインディングを使用する

NetTcp バインディングを使用するための構成は、オンプレミスのクラスターに接続する場合と似ています。 ヘッド ノード VM でいくつかのエンドポイントを開く必要があります。 Azure クラシック ポータルで次のようにします。


1. VM を停止します。

2. セッション用、ブローカー用、ブローカー ワーカー用、Data Services 用に、それぞれ TCP ポート 9090、9087、9091、9094 を追加します。

    ![エンドポイントを構成する][endpoint]

3. VM を起動します。

SOA クライアント アプリケーションでは、IaaS クラスターの完全な名前にヘッド名を変更する以外の変更は不要です。

## 次のステップ

* 参照してください [これらのリソース](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) 詳細については、HPC pack Excel ワークロードを実行します。

* 参照してください [Microsoft HPC pack SOA サービスを管理する](https://technet.microsoft.com/library/ff919412.aspx) の詳細については、HPC Pack での SOA サービス展開、管理します。

<!--Image references-->
[scenario]: ./media/virtual-machines-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-excel-cluster-hpcpack/udf.png


