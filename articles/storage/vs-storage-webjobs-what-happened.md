<properties
    pageTitle="Web ジョブ プロジェクトの変更点 (Visual Studio Azure Storage 接続済みサービス) | Microsoft Azure"
    description="Visual Studio 接続済みサービスを使用してストレージ アカウントに接続した後の、Azure Web ジョブ プロジェクトの変更点について説明します。"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/16/2015"
    ms.author="tarcher"/>

# Web ジョブ プロジェクトの変更点 (Visual Studio Azure Storage 接続済みサービス)

## リファレンスの追加

Visual Studio プロジェクトで Azure Storage の NuGet パッケージが追加または更新されました。  
このパッケージは、次の .NET 参照を追加します。

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Azure Storage の接続文字列の追加
プロジェクトの App.config ファイルで、 **AzureWebJobsStorage** と **AzureWebJobsDashboard** を選択したストレージ アカウントの接続文字列とキーのエントリが更新されました。

詳細については、次を参照してください。 [Azure WebJobs の推奨リソース](http://go.microsoft.com/fwlink/?linkid=390226)します。

