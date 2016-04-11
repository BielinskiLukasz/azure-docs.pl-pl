<properties
    pageTitle="Azure クラシック ポータルを使用した HDInsight での Hadoop クラスターの管理 | Microsoft Azure"
    description="HDInsight サービスを管理する方法を学習します。 HDInsight クラスターを作成し、対話型 JavaScript コンソールを開いて、Hadoop コマンド コンソールを開きます。"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/04/2015"
    ms.author="jgao"/>

# Azure クラシック ポータルを使用した HDInsight での Hadoop クラスターの管理

使用して、 [Azure Classic Portal](https://manage.windowsazure.com), 、Azure HDInsight で Hadoop クラスターのプロビジョニング、Hadoop ユーザーのパスワードを変更して、クラスターで Hadoop コマンド コンソールにアクセスできるように、リモート デスクトップ プロトコル (RDP) を有効にすることができます。

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Azure ポータルを使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-management-portal.md)

## HDInsight を管理するためのその他のツール
Azure クラシック ポータル以外にも、HDInsight を管理するツールが用意されています。

- Azure PowerShell を使用して HDInsight を管理する方法の詳細については、次を参照してください。 [Azure PowerShell を使用して HDInsight を管理](hdinsight-administer-use-powershell.md)します。

- Azure CLI を使用して HDInsight を管理する方法の詳細については、次を参照してください。 [Azure CLI を使用して HDInsight を管理](hdinsight-administer-use-command-line.md)します。

##前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**します。 参照してください [取得 Azure 無料試用版](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)します。
- **Azure ストレージ アカウント** -HDInsight クラスターは既定のファイル システムとして Azure Blob ストレージ コンテナーを使用します。 Azure Blob ストレージが HDInsight クラスターにシームレスなエクスペリエンスを提供する方法の詳細については、次を参照してください。 [HDInsight で Azure Blob ストレージの使用](../hdinsight-use-blob-storage.md)します。 Azure ストレージ アカウントの作成の詳細については、「 [をストレージ アカウントを作成する方法](../storage-create-storage-account.md)します。


##HDInsight クラスターのプロビジョニング

Azure クラシック ポータルから [簡易作成] または [カスタム作成] オプションを使用して、HDInsight クラスターをプロビジョニングできます。 手順については、次の各リンクを参照してください。

- [簡易作成を使用したクラスターのプロビジョニング](../hdinsight-get-started.md#provision)
- [カスタム作成を使用したクラスターのプロビジョニング](hdinsight-provision-clusters.md#portal)

[AZURE.INCLUDE [data center list](../../includes/hdinsight-pricing-data-centers-clusters.md)]


##HDInsight クラスターのカスタマイズ

HDInsight は、広範囲の Hadoop コンポーネントで動作します。 検証されサポートされているコンポーネントの一覧を参照してください。 [Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)です。 次のいずれかのオプションを使用して、HDInsight をカスタマイズできます。

- Script Action を使用してカスタム スクリプトを実行します。これによってクラスターをカスタマイズして、クラスター構成を変更することや、Giraph や Solr などのカスタム コンポーネントをインストールすることができます。 詳細については、次を参照してください。 [HDInsight クラスターのカスタマイズ Script Action を使って](hdinsight-hadoop-customize-cluster.md)します。
- クラスターのプロビジョニング中に、HDInsight .NET SDK または Azure PowerShell でクラスター カスタマイズ パラメーターを使用します。 そうすることで、クラスターの有効期間中はこれらの構成変更が保持され、Azure プラットフォームが保守のために定期的に実行するクラスター ノードの再イメージ化の影響も受けません。 クラスター カスタマイズ パラメーターの使用に関する詳細については、次を参照してください。 [HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)します。
- Mahout や Cascading などの一部のネイティブ Java コンポーネントは、JAR ファイルとしてクラスター上で実行できます。 これらの JAR ファイルは、Azure BLOB ストレージに分配し、Hadoop ジョブ送信メカニズムによって HDInsight クラスターに送信できます。 詳細については、次を参照してください。 [プログラムによる Hadoop の送信ジョブ](hdinsight-submit-hadoop-jobs-programmatically.md)します。


    >[AZURE.NOTE] HDInsight クラスターへの JAR ファイルの配置に関する問題がある場合は、HDInsight クラスターでの JAR ファイルを呼び出すことにお問い合わせください [Microsoft サポート](http://azure.microsoft.com/support/options/)します。

    > Cascading is not supported by HDInsight, and is not eligible for Microsoft Support. For lists of supported components, see [What's new in the cluster versions provided by HDInsight?](hdinsight-component-versioning.md).


リモート デスクトップ接続を使用したクラスターへのカスタム ソフトウェアのインストールはサポートされていません。 クラスターの再作成が必要な場合にファイルが失われるため、ヘッド ノードのドライブにはファイルを保存しないでください。 Azure BLOB ストレージにファイルを保存することをお勧めします。 BLOB ストレージは永続的です。

##HDInsight クラスターのユーザー名とパスワードの変更
HDInsight クラスターは、2 つのユーザー アカウントを持つことができます。 HDInsight クラスターのユーザー アカウントは、プロビジョニング処理中に作成されます。 RDP を使用してクラスターにアクセスするために、RDP ユーザー アカウントを作成することもできます。 参照してください [リモート デスクトップを有効にする](#connect-to-hdinsight-clusters-by-using-rdp)です。

**HDInsight クラスターのユーザー名とパスワードを変更するには**

1. サインイン、 [Azure クラシック ポータル](https://manage.windowsazure.com/)します。
2. クリックして **HDINSIGHT** 左側のウィンドウでします。 デプロイした HDInsight クラスターが一覧表示されます。
3. ユーザー名とパスワードをリセットする HDInsight クラスターをクリックします。
4. ページの上部で、次のようにクリックします。 **構成**します。
5. クリックして **OFF** ] の横に **HADOOP サービス**します。
6. クリックして **保存** ページ、および、無効化を完了を待ちますの下部にあります。
7. クリックして、サービスが無効に **ON** ] の横に **HADOOP サービス**します。
8.  **ユーザー名** と **新しいパスワード**, 、クラスターの新しいユーザー名とパスワードを (それぞれ) を入力します。
8. クリックして **保存**します。


##RDP を使用した HDInsight クラスターへの接続

クラスターの作成時に指定したクラスターの資格情報を使用するとクラスター上のサービスにアクセスできますが、リモート デスクトップを介してクラスター自体にアクセスすることはできません。 リモート デスクトップ アクセスは既定でオフに設定されているため、リモート デスクトップを使用してクラスターに直接アクセスするには、クラスターの作成後に追加の構成をする必要があります。

**リモート デスクトップを有効にするには**

1. サインイン、 [Azure クラシック ポータル](https://manage.windowsazure.com/)します。
2. クリックして **HDINSIGHT** 左側のウィンドウでします。 デプロイした HDInsight クラスターが一覧表示されます。
3. 接続する HDInsight クラスターをクリックします。
4. ページの上部で、次のようにクリックします。 **構成**します。
5. クリックして、ページの下部にある、 **リモートの有効化**します。
6.  **リモート デスクトップの構成** ウィザードで、リモート デスクトップのユーザー名とパスワードを入力します。 ユーザー名が別のクラスターを作成するために使用する必要があります (**admin** デフォルトでは、[簡易作成] オプション)。 有効期限の日付を入力、 **期限** ボックス。 有効期限は、今日から 90 日以内の将来の日付である必要があります。 有効期限の時刻は、既定で、指定した日付の真夜中と想定されます。 チェック マーク アイコンをクリックします。

    ![HDI.CreateRDPUser][image-hdi-create-rpd-user]


> [AZURE.NOTE] HDInsight .NET SDK を使用して、クラスターにリモート デスクトップを有効にすることができますもします。 使用して、 **EnableRdp** 次のように HDInsight クライアント オブジェクトでメソッド: **クライアントです。EnableRdp (clustername、location,"rdpuser","rdppassword", DateTime.Now.AddDays(6))**します。 同様に、クラスターにリモート デスクトップを無効にして **クライアントです。DisableRdp, location)**します。 これらの方法の詳細については、次を参照してください。 [HDInsight .NET SDK のリファレンス](http://go.microsoft.com/fwlink/?LinkId=529017)します。 この方法は、Windows で実行されている HDInsight クラスターにのみ適用できます。



> [AZURE.NOTE] クラスターの RDP が有効にすると、クラスターに接続するには、ページを更新する必要があります。

**RDP を使用してクラスターに接続するには**

1. サインイン、 [Azure クラシック ポータル](https://manage.windowsazure.com/)します。
2. クリックして **HDINSIGHT** 左側のウィンドウでします。 デプロイした HDInsight クラスターが一覧表示されます。
3. 接続する HDInsight クラスターをクリックします。
4. ページの上部で、次のようにクリックします。 **構成**します。
5. クリックして **接続**, 、指示に従います。

##自己署名証明書の作成

クラスターに対し、.NET SDK を使用して何らかの操作を実行するには、ワークステーションで自己署名証明書を作成したうえで、ご利用の Azure サブスクリプションにその証明書をアップロードする必要があります。 これは 1 回限りの作業です。 証明書が有効である限り、同じ証明書を他のコンピューターにインストールすることができます。

**自己署名証明書を取得するには**

1. 要求を認証するために使用する自己署名証明書を作成します。 インターネット インフォメーション サービス (IIS) を使用するか、 [makecert]( http://go.microsoft.com/fwlink/?LinkId=534000) 証明書を作成します。

2. 証明書の場所を参照、証明書を右クリックし、クリックして **証明書のインストール**, 、コンピューターの個人用ストアに証明書をインストールします。 証明書のプロパティを編集し、フレンドリ名を割り当てます。

3. Azure クラシック ポータルに証明書をインポートします。 ポータルで、 **設定** クリックして、ページの左下にある [ **管理証明書**します。 クリックして、ページの下部にある、 **アップロード** 前の手順で作成した .cer ファイルをアップロードして、指示に従います。

    ![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##HTTP サービスのアクセス許可の付与/取り消し

HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

既定では、これらのサービスへのアクセス許可が付与されます。 アクセス権の取り消しまたは付与は、Azure クラシック ポータルから実行できます。

>[AZURE.NOTE] アクセス権を付与/取り消しでは、クラスターのユーザー名とパスワードがリセットされます。

**HTTP Web サービスのアクセス許可を付与する/取り消すには**

1. サインイン、 [Azure クラシック ポータル](https://manage.windowsazure.com/)します。
2. クリックして **HDINSIGHT** 左側のウィンドウでします。 デプロイした HDInsight クラスターが一覧表示されます。
3. 構成する HDInsight クラスターをクリックします。
4. ページの上部で、次のようにクリックします。 **構成**します。
5. クリックして **ON** または **OFF** ] の横に **HADOOP サービス**します。
6.  **ユーザー名** と **新しいパスワード**, 、クラスターの新しいユーザー名とパスワードを (それぞれ) を入力します。
7. クリックして **保存**します。

参照してください [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)します。

##Hadoop コマンド ラインを開く

リモート デスクトップを使用してクラスターに接続して Hadoop コマンド ラインを使用するには、まず、先のセクションで説明したように、クラスターに対するリモート デスクトップ アクセスを有効にする必要があります。

**Hadoop コマンド ラインを開くには**

1. サインイン、 [Azure クラシック ポータル](https://manage.windowsazure.com/)します。
2. クリックして **HDINSIGHT** 左側のウィンドウでします。 デプロイした Hadoop クラスターが一覧表示されます。
3. 接続する HDInsight クラスターをクリックします。
3. クリックして **構成** 、ページ上部にあります。
4. クリックして **接続** 、ページの下部にあります。
5. クリックして **開く**します。
6. 資格情報を入力し、クリックして **OK**します。 クラスターの作成時に構成したユーザー名とパスワードを使用してください。
7. クリックして **はい**します。
8. ダブルクリックして、デスクトップから **Hadoop コマンド ライン**します。

    ![HDI.HadoopCommandLine][image-hadoopcommandline]


    For more information on Hadoop commands, see [Hadoop commands reference](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

先のスクリーンショットで、フォルダー名には Hadoop のバージョン番号が埋め込まれています。 バージョン番号は、クラスターにインストールされている Hadoop コンポーネントのバージョンに基づいて変更できます。 Hadoop 環境変数を使用して、これらのフォルダーを参照できます。 次に例を示します。

    cd %hadoop_home%
    cd %hive_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

##クラスターのスケール
参照してください [スケール Hadoop クラスターの HDInsight](hdinsight-hadoop-cluster-scaling.md)します。

##次のステップ
この記事では、Azure クラシック ポータルを使用して HDInsight クラスターを作成する方法、および Hadoop コマンド ライン ツールを開く方法について説明しました。 詳細については、次の記事を参照してください。

* [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)
* [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md)
* [HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)
* [プログラムによる Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight の概要](../hdinsight-get-started.md)
* [Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)

[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal-v1/hdi.createrdpuser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal-v1/hdinsight-hadoop-command-line.png "Hadoop command line"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal-v1/hdi.clustercreate.uploadcert.png


