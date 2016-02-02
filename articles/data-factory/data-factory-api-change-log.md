<properties 
    pageTitle="Data Factory - .NET API の変更ログ | Microsoft Azure" 
    description="Azure Data Factory.の特定のバージョンの .NET API における重大な変更、機能の追加、バグ修正などについて説明します。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/10/2015" 
    ms.author="spelluru"/>


# Azure Data Factory - .NET SDK の変更ログ

この記事では、特定のバージョンの Azure Data Factory SDK の変更に関する情報を提供します。 Azure Data Factoryの最新の Nuget パッケージを検索する [ここ](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## バージョン 4.2.0

リリース日: 2015年-11-10

### 機能の追加

- 次の新しいアクティビティの種類が追加されました: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx)します。 利用状況に関する詳細については、「 [リソースの更新プログラム アクティビティを使用して、Azure ML の更新モデル](https://azure.microsoft.com/documentation/articles/data-factory-azure-ml-batch-execution-activity/#updating-azure-ml-models-using-the-update-resource-activity)します。
- 省略可能なプロパティを新しい [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) に追加されて、 [AzureMLLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)します。
- [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) と [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) プロパティに追加されて、 [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) クラスです。
- Data Factory サービスへのクライアントの呼び出しのタイムアウトの構成が許可されました。


## バージョン 4.1.0

リリース日: 2015年-10-28

### 機能の追加

* 次のリンクされたサービスの種類が追加されています。
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* 次の活動の種類が追加されました。
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* 次の種類のデータセットが追加されました。
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* 次のコピー アクティビティ用のソースとシンクの種類が追加されています。
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## バージョン 4.0.1

リリース日: 2015 年 10 月 13 日

### 重大な変更

次のクラスの名前が変更されました。新しい名前は、4.0.0 リリースより前の、クラスの元の名前です。

 4.0.0 での名前| 4.0.1 での名前
:------------ | :------------
 AzureSqlDataWarehouseDataset| [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
 AzureSqlDataset| [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
 AzureDataset| [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
 OracleDataset| [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
 RelationalDataset| [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
 SqlServerDataset| [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## バージョン 4.0.0

リリース日: 2015 年 10 月 2 日

### 重大な変更

- 次のクラスとインターフェイスの名前が変更されました。

| 以前の名前| 新しい名前|
| :-------- | :-------- |
| ITableOperations| [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx)|
| テーブル| [データセット](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx)|
| TableProperties| [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx)|
| TableTypeProprerties| [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx)|
| TableCreateOrUpdateParameters| [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx)|
| TableCreateOrUpdateResponse| [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx)|
| TableGetResponse| [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx)|
| TableListResponse| [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx)|
| CreateOrUpdateWithRawJsonContentParameters| [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx)|

- このリリースの **API バージョンは** **2015-10-01** です。

- **List** メソッドでページングされた結果が返されるようになりました。 応答に空でない **NextLink** プロパティが含まれている場合、クライアント アプリケーションは、すべてのページが返されるまで次のページを取得し続ける必要があります。 たとえば次のようになります。

      PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
      var pipelines = new List<Pipeline>(response.Pipelines);
    
      string nextLink = response.NextLink;
      while (string.IsNullOrEmpty(response.NextLink))
      {
          PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
          pipelines.AddRange(nextResponse.Pipelines);
    
          nextLink = nextResponse.NextLink;
      }

- **List** パイプライン API では、全詳細ではなく、パイプラインの概要だけが返されます。 たとえば、パイプライン概要のアクティビティには、名前と種類だけが含まれます。

### 機能の追加

- [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) クラスは、2 つの新しいプロパティをサポートしている **SliceIdentifierColumnName** と **SqlWriterCleanupScript**, 、Azure SQL Data Warehouse にアイデムポ テントなコピーをサポートします。 参照してください、 [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) 記事、具体的には、 [メカニズムの 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) と [メカニズム 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) これらのプロパティの詳細セクションです。

- コピー アクティビティの一環として、Azure SQL Database ソースと Azure SQL Data Warehouse ソースに対するストアド プロシージャの実行がサポートされるようになりました。  [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) と [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) クラスは、これをサポートするために次のプロパティを持つ: **SqlReaderStoredProcedureName** と **StoredProcedureParameters**します。 参照してください、 [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) と [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) Azure.com でこれらのプロパティの詳細についての記事です。




