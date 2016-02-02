<properties 
    pageTitle="メディア アセットのダウンロード" 
    description="コンピューターへのアセットのダウンロードについて説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/05/2015" 
    ms.author="juliako"/>


# 方法: ダウンロードによってアセットを配信する

このトピックでは、メディア サービスにアップロードされたメディア アセットを配信するためのオプションについて説明します。 メディア サービスのコンテンツ配信には、さまざまな方法を適用できます。 メディア アセットをダウンロードするか、ロケーターを使用してアクセスすることができます。 メディア コンテンツは、別のアプリケーションまたは別のコンテンツ プロバイダーに送ることができます。 パフォーマンスとスケーラビリティを高めるために、Content Delivery Network (CDN) を使用してコンテンツを配信することもできます。

この例では、メディア アセットを Media Services からローカル コンピューターにダウンロードする方法を紹介しています。 このコードは、ジョブ ID によってメディア サービス アカウントに関連付けられたジョブのクエリを実行し、**OutputMediaAssets** コレクション (ジョブの実行の結果である 1 つまたは複数の出力メディア アセットのセット) にアクセスします。 この例は、ジョブから出力メディア アセットをダウンロードする方法を示していますが、その他のアセットをダウンロードする同じアプローチを適用することができます。


    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 
    
        // Get a reference to the job. 
        IJob job = GetJob(jobId);
    
        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);
    
        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };
    
        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;
    
            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);
    
            Console.WriteLine("File download path:  " + localDownloadPath);
    
            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));
    
            outputFile.DownloadProgressChanged -= DownloadProgress;
        }
    
        Task.WaitAll(downloadTasks.ToArray());
    
        return outputAsset;
    }
    
    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }

## Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## 関連項目

[ストリーミング コンテンツを配信します。](media-services-deliver-streaming-content.md)






