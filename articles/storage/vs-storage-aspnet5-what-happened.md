<properties
    pageTitle="ASP.NET 5 プロジェクトの変更点 (Visual Studio 接続済みサービス) | Microsoft Azure Storage"
    description="Visual Studio 接続済みサービスを使用して Visual Studio ASP.NET 5 プロジェクトの Azure ストレージ アカウントに接続した後の変更点について説明します。"
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

# ASP.NET 5 プロジェクトの変更点 (Visual Studio Azure Storage 接続済みサービス)

## リファレンスの追加

Visual Studio プロジェクトに Azure Storage の NuGet パッケージが追加されました。  
このパッケージは、次の .NET 参照を追加します。

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

また、NuGet パッケージ **Microsoft.Framework.Configuration.Json** が追加されました。

## Azure Storage の接続文字列の追加
選択されたストレージ アカウントの接続文字列とキーを使用して、プロジェクトの config.json ファイル内に要素が作成されました。

詳細については、次を参照してください。 [ASP.NET 5](http://www.asp.net/vnext)します。

