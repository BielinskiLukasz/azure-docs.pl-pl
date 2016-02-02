<properties 
   pageTitle="Azure Data Lake Store の概要 | Azure" 
   description="Azure Data Lake Store とは何か、Azure Data Lake Store が他のデータ ストアで提供する値はどのようなものか、を理解する" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2015"
   ms.author="nitinme"/>


# Azure Data Lake Store の概要

Azure Data Lake Store は、ビッグ データの分析ワークロードに対応するエンタープライズ規模のハイパースケール リポジトリです。 Azure Data Lake を使用すると、運用分析や調査分析を目的として任意のサイズ、種類、および取り込み速度のデータを 1 か所でキャプチャすることができます。
> [AZURE.TIP] 使用して、 [湖のデータ ストアのラーニング パス](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) ものの、Azure Data Lake ストア サービスを開始します。

Azure Data Lake Store には、Hadoop (HDInsight クラスターで使用可能) から、WebHDFS 互換の REST API を使用してアクセスできます。 Azure Data Lake Store は、格納されたデータを分析できるように特別に設計されており、データ分析シナリオに合わせてパフォーマンスの調整が行われます。 これには、企業における実際のユース ケースで不可欠なエンタープライズ レベルのすべての機能 (セキュリティ、管理の容易性、スケーラビリティ、信頼性、および可用性) が既定で組み込まれています。


![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Azure Data Lake の主要な機能のいくつかを以下に示します。

### Hadoop 用に構築

Azure Data Lake Store は、Hadoop 分散ファイル システム (HDFS) と互換性のある Apache Hadoop ファイル システムであり、Hadoop エコシステムと連携して動作します。 WebHDFS API を使用する既存の HDInsight アプリケーションまたはサービス は、Data Lake Store と容易に統合することができます。 Data Lake Store では、アプリケーション向けの WebHDFS と互換性のある REST インターフェイスを公開しています。

Data Lake Store に格納されたデータは、MapReduce または Hive などの Hadoop 分析フレームワークを使用して簡単に分析することができます。 Data Lake Store に格納されたデータに直接アクセスするように Microsoft Azure HDInsight クラスターをプロビジョニングおよび構成することができます。

### 無制限のストレージ、ペタバイト ファイル

Azure Data Lake Store では無制限のストレージを実現しています。このため、分析を目的としてさまざまなデータを格納するのに適しています。 Data Lake に格納できるアカウント サイズ、ファイル サイズ、またはデータ量に関する制限は設定されていません。 対応可能な個々のファイルのサイズはキロバイトからペタバイトの範囲にわたり、任意の種類のデータを自由に格納することができます。 データは複数のコピーを作成して格納されるため障害が発生しても保護されます。Data Lake でのデータの格納期間に制限はありません。

### ビッグ データを分析するためのパフォーマンス チューニング

Azure Data Lake Store は、大量のデータのクエリと分析のために非常に高いスループットを必要とする、大規模な分析システムを実行するために構築されています。 Data Lake では、ファイル内のデータを複数の異なる記憶域サーバーに分散します。 これにより、ファイルを並列に読み取ってデータ分析を実行する場合の読み取りスループットが向上します。


### エンタープライズ対応: 高い可用性とセキュリティ保護

Azure Data Lake Store では、業界標準の可用性と信頼性を提供します。 データ資産は、冗長なコピーを作成して格納されるので、予期せぬ障害が発生しても保護されます。 企業では、実際のソリューションの中で既存のデータ プラットフォームの重要な部分として Azure Data Lake を使用できます。

Data Lake Store では、格納されたデータに対してエンタープライズ レベルのセキュリティも提供します。 詳細については、次を参照してください。 [Azure Data Lake ストア内のデータをセキュリティで保護する](#DataLakeStoreSecurity)します。


### すべてのデータ

Azure Data Lake Store では、任意のデータをネイティブ形式でそのまま格納することができ、事前の変換は必要ありません。 Data Lake Store では、データを読み込む前にスキーマを定義する必要はなく、分析時にデータを解釈しスキーマを定義するかどうかは個々の分析フレームワークに任されます。 任意のサイズおよび形式のファイルを格納できるようにすると、Data Lake Store は構造化データ、半構造化データ、および非構造化データを処理できるようになります。


## <a name="DataLakeStoreSecurity"></a>Azure Data Lake ストアでデータを保護します。

Azure Data Lake Store では、Azure Active Directory を使用し、認証およびアクセス制御リスト (ACL) によってデータへのアクセスを管理します。

| 機能| 説明|
|-----------------------------------------|------------------------------------------|
| 認証| Azure Data Lake Store は、Azure Active Directory (AAD) と統合することで、Azure Data Lake Store に格納されたすべてのデータの ID 管理とアクセス管理を行います。この統合によって、Azure Data Lake Store は、AAD のあらゆる機能 (たとえば、多要素認証、条件付きアクセス、ロール ベースのアクセス制御、アプリケーション使用状況の監視、セキュリティの監視とアラート通知など) から恩恵を受けます。Azure Data Lake Store では、REST インターフェイスでの認証に対応する OAuth 2.0 プロトコルをサポートしています。|
| Access control| Azure Data Lake Store では、WebHDFS プロトコルで公開された POSIX 形式のアクセス許可をサポートすることにより、アクセス制御を実現しています。現在のリリースでは、アクセス許可は Data Lake のレベルで指定され、Data Lake 内のすべてのファイルおよびフォルダーに適用されます。今後の更新では、個々のファイルおよびフォルダーに対してアクセス許可を指定できるようにすることで、詳細なアクセス制御を可能にします。|

Data Lake ストア内のデータを保護する方法については、次を参照してください。 [Azure Data Lake ストア内のデータをセキュリティで保護する](data-lake-store-secure-data.md)します。

## Azure Data Lake Store と互換性のあるアプリケーション

参照してください [アプリケーションとサービスの Azure Data Lake と互換性のある](data-lake-store-compatible-oss-other-applications.md) なオープン ソース アプリケーションを Azure Data Lake ストアと相互運用可能な一覧についてです。 参照してください [他の Azure サービスとの統合](data-lake-store-integrate-with-other-services.md) 広範なシナリオを有効にするデータ湖ストアを他の Azure サービスで使用する方法を理解します。

## Azure Data Lake ストアのファイル システムとは (含む://) ですか?

Data Lake ストアは、新しいファイル システム、AzureDataLakeFilesystem を介してアクセスできます (含む://)、(HDInsight クラスターで使用可能) の Hadoop 環境でします。 adl:// を使用するアプリケーションとサービスでは、WebHDFS で現在まだサポートされていない、より有効なパフォーマンスの最適化を利用できるようになります。 結果として、Data Lake Store では、adl:// を使用する推奨オプションで最適なパフォーマンスを利用することも、引き続き WebHDFS API を直接使用することにより既存のコードを維持することもできるという柔軟性が得られます。 Azure HDInsight は、AzureDataLakeFilesystem をフルに活用して Data Lake Store で最適なパフォーマンスを実現します。

湖のデータ ストアを使用して、データにアクセスすることができます `含む://< data_lake_store_name >. azuredatalakestore.net`します。Data Lake ストア内のデータにアクセスする方法の詳細については、を参照してください [、格納されたデータのプロパティを表示](data-lake-store-get-started-portal.md#properties)。

## Azure Data Lake Store の使用を開始するにはどうすればよいですか?

参照してください [を使ってみる湖のデータ ストア Azure ポータルを使用して](data-lake-store-get-started-portal.md), 、Azure ポータルを使用してデータ湖ストアをプロビジョニングする方法についてです。 Azure Data Lake をプロビジョニングしたら、ビッグ データ ソリューション (Azure Data Lake Analytics、または Azure HDInsight と Data Lake Store の組み合わせ) を使用する方法を確認できます。 Azure Data Lake Store のアカウントを作成し操作 (データのアップロードやデータのダウンロードなど) を実行するための .NET アプリケーションを作成することもできます。

- [Azure Data Lake 分析を使ってみる](data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Data Lake ストアと Azure HDInsight を使用してください。](data-lake-store-hdinsight-hadoop-use-portal.md)
- [.NET SDK を使用して Azure Data Lake ストアを使ってみる](data-lake-store-get-started-net-sdk.md)






