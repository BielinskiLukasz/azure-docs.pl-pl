<properties
    pageTitle="BLOB ストレージと Visual Studio 接続済みサービスの概要 (クラウド サービス) | Microsoft Azure"
    description="Visual Studio 接続済みサービスを使用してストレージ アカウントに接続した後、Visual Studio のクラウド サービス プロジェクトで Azure BLOB ストレージの使用を開始する方法について説明します。"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/16/2015"
    ms.author="tarcher"/>


# Azure BLOB ストレージと Visual Studio 接続済みサービスの概要 (クラウド サービス プロジェクト)

## 概要

この記事では、Visual Studio クラウド サービス オブジェクトの **[接続済みサービスの追加]** ダイアログを使用して Azure ストレージ アカウントを作成または参照した後に、Azure BLOB ストレージの使用を開始する方法について説明します。 BLOB コンテナーへのアクセス方法や作成方法、BLOB のアップロード、一覧表示、ダウンロードなどの一般的なタスクの実行方法について説明します。 サンプルは c \# および使用で記述され、 [.NET 用 Azure ストレージ クライアント ライブラリ](https://msdn.microsoft.com/library/azure/dn261237.aspx)します。

Azure BLOB ストレージは、大量の非構造化データを格納して HTTP または HTTPS を介して世界中のどこからでもアクセスできるようにするサービスです。 1 つの BLOB は任意のサイズにできます。 BLOB として扱えるのは、画像、オーディオ ファイル、ビデオ ファイル、生データ、およびドキュメント ファイルのようなデータです。

ファイルがフォルダーに格納されるのと同様に、ストレージ BLOB はコンテナーに格納されます。 ストレージを作成した後、その内部に 1 つまたは複数のコンテナーを作成します。 たとえば、"Scrapbook" という名前のストレージに、写真を格納するための "images" という名前のコンテナーと、音声ファイルを格納するための "audio" という名前のコンテナーを作成できます。 コンテナーを作成すると、個々の BLOB ファイルをコンテナーにアップロードできるようになります。

- プログラムによる blob の操作の詳細については、次を参照してください。 [.NET から blob ストレージを使用する方法](storage-dotnet-how-to-use-blobs.md)します。
- Azure Storage の詳細については、次を参照してください。 [Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)します。
- Azure クラウド サービスの詳細については、次を参照してください。 [クラウド サービスのドキュメント](http://azure.microsoft.com/documentation/services/cloud-services/)します。
- ASP.NET アプリケーションのプログラミングの詳細については、次を参照してください。 [ASP.NET](http://www.asp.net)します。

## コードで BLOB コンテナーにアクセスする

クラウド サービス プロジェクトでプログラムを使用して BLOB にアクセスするには、次の項目を追加する必要があります (存在しない場合)。

1. プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. ストレージ アカウント内の既存のコンテナーを参照する **CloudBlobClient** オブジェクトを取得します。

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

4. 特定の BLOB コンテナーを参照する **CloudBlobContainer** オブジェクトを取得します。

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");


> [AZURE.NOTE] 前の手順で示されているコードはすべて、以下のセクションで示されているコードの前に使用してください。

## コードでコンテナーを作成する

> [AZURE.NOTE] ASP.NET で Azure Storage への呼び出しを実行する API の一部は非同期です。 参照してください [Async および Await における非同期プログラミング](http://msdn.microsoft.com/library/hh191443.aspx) の詳細。 次の例のコードでは、非同期のプログラミング方法を使用していることを前提としています。

ストレージ アカウント内にコンテナーを作成するために必要な作業は、次のコードのように、**CreateIfNotExistsAsync** へ呼び出しを追加することだけです。

    // Get a reference to a CloudBlobContainer with the variable name 'container'
    // as described in the "Access blob containers in code" section.
    
    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();

コンテナー内のファイルをだれでも利用できるようにするには、次のコードを使ってコンテナーをパブリックに設定できます。

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

パブリック コンテナー内の BLOB は、インターネットに接続しているすべてのユーザーが
表示できますが、変更または削除できるのは、適切なアクセス キーを持っているユーザーだけです。

## コンテナーに BLOB をアップロードする

Azure Storage では、ブロック BLOB とページ BLOB がサポートされています。 ほとんどの場合は、ブロック BLOB を使用することをお勧めします。

ファイルをブロック blob にアップロードするには、コンテナーの参照を取得し、それを使用してブロック blob の参照を取得します。 BLOB の参照を取得したら、**UploadFromStream** メソッドを呼び出すことによって、データの任意のストリームを BLOB にアップロードできます。 この操作により、BLOB がまだ存在しない場合は作成され、存在する場合は上書きされます。 次の例は、BLOB をコンテナーにアップロードする方法を示しています。この例では、既にコンテナーが作成されていることを前提としています。

    // Get a reference to a CloudBlobContainer with the variable name 'container' as described in
    // the "Access blob containers in code" section.
    
    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");
    
    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、まず、コンテナーの参照を取得します。 次に、コンテナーの **ListBlobs** メソッドを使用して、その中の BLOB やディレクトリを取得できます。 プロパティとメソッドが返されたの豊富なセットにアクセスする **IListBlobItem**, にキャストする必要があります、 **CloudBlockBlob**, 、**CloudPageBlob**, 、または **CloudBlobDirectory** オブジェクトです。 型がわからない場合は、型チェックを使うとどれにキャストすればよいかがわかります。 次のコードは、**photos** コンテナー内の各アイテムの URI を取得して出力する方法を示しています。

    // Get a reference to a CloudBlobContainer with the variable name 'container' as described in
    // the "Access blob containers in code" section.
    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
    
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
    
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;
    
            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
    
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;
    
            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

前のコード例からわかるように、BLOB サービスにはコンテナー内のディレクトリの概念も含まれています。 これは、BLOB をよりフォルダーに近い構造で整理できるようにするためです。 たとえば、**photos** というコンテナーに次のブロック BLOB があるとします。

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

呼び出すと **ListBlobs** (つまり前の例では) コンテナーで、コレクションが返されます
含む **CloudBlobDirectory** と **CloudBlockBlob** ディレクトリおよび blob の最上位レベルに含まれるを表すオブジェクト。 結果の出力は次のようになります。

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

必要に応じて設定、 **UseFlatBlobListing** のパラメーター、 **ListBlobs** メソッド
**true**. これで、ディレクトリに関係なく、すべての BLOB が **CloudBlockBlob** として返されるようになります。 次に、**ListBlobs** の呼び出しを示します。

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

結果は次のとおりです。

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

詳細については、次を参照してください。 [cloudblobcontainer.listblobs に関するページの []][]します。

## BLOB をダウンロードする

BLOB をダウンロードするには、まず BLOB の参照を取得し、次に **DownloadToStream** メソッドを呼び出します。 次の
例では、 **DownloadToStream** blob を転送する方法
ローカル ファイルに保存できるストリーム オブジェクトに BLOB の内容を転送します。

    // Get a reference to a CloudBlobContainer with the variable name 'container' as described in
    // the "Access blob containers in code" section.
    
    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");
    
    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

**DownloadToStream** メソッドを使用して BLOB の内容をテキスト文字列としてダウンロードすることもできます。

    // Get a reference to a CloudBlobContainer with the variable name 'container' as described in
    // the "Access blob containers in code" section.
    
    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");
    
    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## BLOB を削除する

BLOB を削除するには、まず BLOB の参照を取得し、次にその
**削除** メソッドです。

    // Get a reference to a CloudBlobContainer with the variable name 'container' as described in
    // the "Access blob containers in code" section.
    
    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");
    
    // Delete the blob.
    blockBlob.Delete();

## BLOB をページで非同期に一覧表示する

多数の BLOB を一覧表示する場合や、1 回の一覧表示操作で返される結果の数を制御する場合には、BLOB の一覧を結果のページで表示できます。 この例は、大きな結果のセットを返すために待機している間に実行がブロックされないように、結果をページで非同期に返す方法を示しています。

この例では、BLOB をフラットな一覧で表示しますが、**ListBlobsSegmentedAsync** メソッドの **useFlatBlobListing** パラメーターを **false** に設定することで、階層化された一覧で表示することもできます。

サンプル メソッドは非同期メソッドを呼び出すため、先頭に **async** キーワードを付ける必要があり、また、**Task** オブジェクトを返す必要があります。 **ListBlobsSegmentedAsync** メソッドに対して指定された await キーワードは、一覧表示タスクが完了するまで、サンプル メソッドの実行を中断します。

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");
    
        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;
    
        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();
    
            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## 次のステップ

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]





