
<properties 
  pageTitle="Azure Storage のオブジェクトのプロパティおよびメタデータの設定と取得 | Microsoft Azure" 
  description="Azure Storage のオブジェクトにカスタム メタデータを格納して、システムのプロパティを設定、取得します。" 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="10/26/2015" 
  ms.author="tamram"/>


# プロパティおよびメタデータの設定と取得 #

## 概要

Azure Storage のオブジェクトは、含まれるデータのほかにもシステムのプロパティとユーザー定義のメタデータをサポートします。

*   **システムのプロパティ。**システムのプロパティは、各ストレージ リソースに存在します。 このようなプロパティには、読み取りまたは設定可能なものもありますが、読み取り専用のものもあります。 実際には、システムのプロパティの一部は、特定の標準 HTTP ヘッダーに対応しています。 これらは Azure ストレージ クライアント ライブラリで管理されます。  

*   **ユーザー定義のメタデータ。**ユーザー定義のメタデータは、名前と値のペアの形式で、特定のリソースで指定したメタデータです。 メタデータを使用すると、ストレージ リソースで追加の値を格納できます。つまり、これらの値は、自身の目的のためだけに存在するため、リソースの動作には影響しません。  

ストレージ リソースのプロパティとメタデータの値を取得するには、2 つの手順が必要です。 これらの値を参照する前にする必要があります明示的に取得して呼び出すことによって、 **FetchAttributes** メソッドです。

> [AZURE.IMPORTANT] いずれかを呼び出さない限り、ストレージ リソースのプロパティとメタデータ値が反映されず、 **FetchAttributes** メソッドです。 

## プロパティの設定と取得

プロパティの値を取得する、 **FetchAttributes** blob またはコンテナーのプロパティを設定するメソッドが後に、値を読み取ります。

オブジェクトのプロパティを設定するには、プロパティを指定しを呼び出す、 **SetProperties** メソッドです。

次のコード例では、コンテナーを作成し、プロパティ値の一部をコンソール ウィンドウに書き込みます。

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    //Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## メタデータの設定と取得

メタデータは、BLOB またはコンテナーのリソースで 1 つ以上の名前と値のペアとして指定できます。 メタデータを設定する名前と値のペアを追加、 **メタデータ** し、リソースのコレクションを呼び出す、 **SetMetadata** 値をサービスに保存します。

> [AZURE.NOTE]: メタデータの名前は、c# 識別子の名前付け規則に従う必要があります。
 
次のコード例では、コンテナーでメタデータを設定します。 1 つの値は、コレクションを使用して設定 **追加** メソッドです。 もう一方の値は、暗黙的なキーと値の構文を使用して設定されます。 どちらも有効です。

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

メタデータを取得する、 **FetchAttributes** blob またはコンテナーを作成する方法、 **メタデータ** コレクションは、次の例で示すようにし、値を読み取ります。

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## 関連項目  

- [.NET 用 Azure Storage クライアント ライブラリ リファレンス](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [.NET 用 Azure Storage クライアント ライブラリ パッケージ](https://www.nuget.org/packages/WindowsAzure.Storage/) 
