<properties
    pageTitle="Ambari API を使用した HDInsight の Hadoop クラスターの監視 | Microsoft Azure"
    description="Apache Ambari API は、Hadoop クラスターのプロビジョニング、管理、および監視を目的として使用します。 直観的なオペレーター ツールと API によって、Hadoop の複雑さに悩まされずに作業を進められます。"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    editor="cgronlun"
    manager="paulettm"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/11/2015"
    ms.author="jgao"/>

# Ambari API を使用した HDInsight の Hadoop クラスターの監視

Ambari API を使用して HDInsight クラスター バージョン 3.1 および 2.1 を監視する方法について説明します。

> [AZURE.NOTE] この記事の情報では、主に Windows ベースの HDInsight クラスターは、Ambari REST API の読み取り専用バージョンを提供します。 Linux ベースのクラスターでは、次を参照してください。 [管理 Hadoop クラスターの Ambari を使用した](hdinsight-hadoop-manage-ambari.md)します。

## Ambari とは

[Apache Ambari][ambari-home] のプロビジョニング、管理、および Apache Hadoop クラスターを監視するために使用します。 演算子ツールの直観的なコレクションおよび Hadoop の複雑さが見えないようにする信頼性の高い、一連の API が含まれており、クラスターの操作を単純化しています。 Api の詳細については、次を参照してください。 [Ambari API リファレンス][ambari-api-reference]します。


HDInsight は現在、Ambari の監視機能のみをサポートしています。 Ambari API 1.0 は HDInsight バージョン 3.0 と 2.1 のクラスターによってサポートされています。 この記事では、HDInsight バージョン 3.1 と 2.1 のクラスターでの Ambari API へのアクセスを取り上げます。 この 2 つの重要な相違点は、新しい機能 (ジョブ履歴サーバーなど) の導入により、一部のコンポーネントに変更が加えられた点です。


**前提条件**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure PowerShell を実行できるワークステーション**します。 参照してください [Azure PowerShell 1.0 をインストールし、大きい](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater)します。


- (省略可能) [cURL][curl]します。 これをインストールするには、「 [cURL のリリースとダウンロード][curl-download]します。

    >[AZURE.NOTE] CURL コマンドを使用して、Windows では、オプションの値の単一引用符の代わりに二重引用符記号を使用します。

- **Azure HDInsight クラスター**します。 クラスターのプロビジョニング方法については、「 [HDInsight の概要][hdinsight-get-started] または [HDInsight クラスターのプロビジョニング][hdinsight-provision]します。 このチュートリアルを読み進めるには、次のデータが必要です。

クラスター プロパティ|Azure PowerShell 変数名|値|説明
---|---|---|---
HDInsight クラスター名|$clusterName||HDInsight クラスターの名前です。
クラスター ユーザー名|$clusterUsername||プロビジョニングで指定されたクラスター ユーザー名です。
クラスター パスワード|$clusterPassword||クラスター ユーザー パスワードです。

    > [AZURE.NOTE] Fill-in the values in the table. This will be helpful for going through this tutorial.



## ジャンプ スタート

いくつかの方法で、Ambari を使用して HDInsight クラスターを監視することができます。

**Azure PowerShell の使用**

MapReduce job tracker 情報を取得する Azure PowerShell スクリプトを次に示します *HDInsight 3.1 クラスターにします。*ここで重要な相違点は、(MapReduce ではなく) YARN サービスから詳細をプルする点です。

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

MapReduce job tracker 情報を取得するための Azure PowerShell スクリプトを次に示します *、HDInsight 2.1 クラスターで*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

出力は次のようになります。

![Jobtracker の出力][img-jobtracker-output]

**cURL の使用**

cURL を使用してクラスター情報を取得する例を以下に示します。

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

出力は次のようになります。

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**2014 年 10 月 8 日にリリースの**:

Ambari エンドポイントを使用するときに"https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}"を *host_name* フィールドは、ホスト名ではなくノードの完全修飾ドメイン名 (FQDN) を返します。 この例を単純に戻す前に、2014 年 10 月 8 日リリースでは、"**headnode0**"です。 2014 年 10 月 8 日のリリース後に、FQDN を取得する"**headnode0 {。.Azurehdinsight.net** .net**"、前の例で示すようにします。 この変更は、1 つの仮想ネットワーク (VNET) に HBase や Hadoop などの複数のクラスターの種類をデプロイできるシナリオの実現を容易にするために必須でした。 このシナリオは、Hadoop のバックエンド プラットフォームとして HBase を使用する場合などが該当します。

## Ambari での API の監視

以下のテーブルに最も一般的な Ambari での API 呼び出しの監視の一部を示します。 API の詳細については、次を参照してください。 [Ambari API リファレンス][ambari-api-reference]します。

API 呼び出しの監視|URI|説明
---|---|---
クラスターの取得|`/api/v1/clusters`|
クラスター情報の取得|`/api/v1/clusters/<ClusterName>.azurehdinsight.net`|クラスター、サービス、ホスト
サービスの取得|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`|含まれるサービス: hdfs、mapreduce
サービス情報の取得|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`|
サービス コンポーネントの取得|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`|HDFS: namenode、datanode<br/>MapReduce: jobtracker、tasktracker
コンポーネント情報の取得|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`|ServiceComponentInfo、host-components、metrics
ホストの取得|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`|headnode0、workernode0
ホスト情報の取得|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>`|
ホスト コンポーネントの取得|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`|namenode、resourcemanager
ホスト コンポーネント情報の取得|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`|HostRoles、component、host、metrics
構成の取得|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations`|構成の種類: core-site、hdfs-site、mapred-site、hive-site
構成情報の取得|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>`|構成の種類: core-site、hdfs-site、mapred-site、hive-site


##次のステップ

ここでは Ambari での API 呼び出しの監視の使用方法を説明しました。 詳細については、次を参照してください。

- [Azure ポータルを使用した HDInsight クラスターの管理][hdinsight-admin-portal]
- [Azure PowerShell を使用した HDInsight クラスターの管理][hdinsight-admin-powershell]
- [コマンド ライン インターフェイスを使用した HDInsight クラスターの管理][hdinsight-admin-cli]
- [HDInsight のドキュメント][hdinsight-documentation]
- [HDInsight の使用][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png

