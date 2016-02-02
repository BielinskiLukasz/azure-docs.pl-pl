<properties
    pageTitle="Azure Media Hyperlapse を使用する Hyperlapse メディア ファイル"
    description="Azure Media Hyperlapse では、ファースト パーソンまたはアクション カメラのコンテンツからスムーズの経過時間のビデオを作成します。このトピックでは、Media Indexer の使用方法について説明します。"
    services="media-services"
    documentationCenter=""
    authors="asolanki"
    manager="johndeu"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/19/2015"   
    ms.author="adsolank"/>



# Azure Media Hyperlapse を使用する Hyperlapse メディア ファイル

Azure Media Hyperlapse は、最初のユーザーまたはアクション カメラのコンテンツから滑らかな低速度撮影ビデオを作成するメディア プロセッサ (MP) です。 クラウド ベースの兄弟を [Microsoft Research のデスクトップの Hyperlapse Pro と電話による Hyperlapse Mobile](http://aka.ms/hyperlapse), 、Microsoft Hyperlapse の Azure Media Services Azure メディア サービス メディアの処理プラットフォームで数を増やし、並列化の大規模なスケールを利用して一括 Hyperlapse を処理します。
>[AZURE.IMPORTANT] Azure Media Services 向けの Microsoft Hyperlapse は、無料のパブリック プレビュー段階です。 ジョブは、入力アセットの最初の 10,000 フレームに制限されます。


> Microsoft Hyperlapse は、移動カメラを使用する最初のユーザー コンテンツで最適に機能するように設計されています。 静止カメラの映像は引き続き動作しますが、Azure Media Hyperlapse メディア プロセッサのパフォーマンスと品質は他の種類のコンテンツに対して保証できません。 Azure Media Services の Microsoft Hyperlapse の詳細について、いくつかの例のビデオを参照して、チェック アウト、 [入門のブログの投稿](http://aka.ms/azurehyperlapseblog) パブリック プレビューからです。

Azure Media Hyperlapse ジョブでは、低速度撮影のビデオ フレームとその速度 (たとえば、最初の 10,000 フレームは 2 倍速) を指定する構成ファイルと共に、MP4、MOV、または WMV ファイルを入力として使用します。 出力は、入力ビデオの安定した低速度撮影画像となります。

Azure Media Hyperlapse の最新の更新を参照してください。 [Media Services のブログ](http://azure.microsoft.com/blog/topics/media-services/)します。

## Hyperlapse におけるアセット

最初に、必要な入力ファイルを Azure Media Services にアップロードする必要があります。 アップロードして、コンテンツの管理に関連する概念に関する詳細についてを参照してください、 [コンテンツ管理の記事](media-services-manage-content.md#upload)します。

### <a id="configuration"></a>Hyperlapse の構成のプリセット

コンテンツを Media Services アカウントにアップロードしたら、構成プリセットを作成する必要があります。 次の表では、ユーザー指定のフィールドについて説明します。

 フィールド| 説明
-------|-------------
 StartFrame| Microsoft Hyperlapse 処理を開始する必要があるフレーム。
 NumFrames| 処理するフレームの数。
 速度| 入力ビデオの高速化に使用する係数。

XML と JSON で準拠する構成ファイルの例を以下に示します。

**XML プリセット:**

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>

**JSON プリセット:**

    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }

### <a id="sample_code"></a> Microsoft Hyperlapse AMS .NET SDK の使用

次のメソッドは、アセットとしてメディア ファイルをアップロードし、Azure Media Hyperlapse メディア プロセッサを搭載したジョブを作成します。
> [AZURE.NOTE] このコードを動作させるには、スコープ内に "context" という名前の CloudMediaContext が既に存在している必要があります。 これに関する詳細についてを参照してください、 [コンテンツ管理の記事](media-services-manage-content.md)します。

> [AZURE.NOTE] 上記の JSON または XML の準拠する構成プリセットには、"hyperConfig" という文字列引数が必要です。

    static bool RunHyperlapseJob(string input, string output, string hyperConfig)
    {
        // create asset with input file
        IAsset asset = context
                       .Assets
                       .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);
    
        // grab instances of Azure Media Hyperlapse MP
        IMediaProcessor mp = context
                             .MediaProcessors
                             .GetLatestMediaProcessorByName("Azure Media Hyperlapse");
    
        // create Job with Hyperlapse task
        IJob job = context
                   .Jobs
                   .Create(String.Format("Hyperlapse {0}", input));
    
        if (String.IsNullOrEmpty(hyperConfig))
        {
            // config cannot be empty
            return false;
        }
    
        hyperConfig = File.ReadAllText(hyperConfig);
    
        ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task",
                                                mp,
                                                hyperConfig,
                                                TaskOptions.None);
        hyperlapseTask.InputAssets.Add(asset);
        hyperlapseTask.OutputAssets.AddNew("Hyperlapse output",
                                            AssetCreationOptions.None);
    
    
        job.Submit();
    
        // Create progress printing and querying tasks
            Task progressPrintTask = new Task(() =>
            {
    
                IJob jobQuery = null;
                do
                {
                    var progressContext = context;
                    jobQuery = progressContext.Jobs
                                              .Where(j => j.Id == job.Id)
                                              .First();
                    Console.WriteLine(string.Format("{0}\t{1}\t{2}",
                                      DateTime.Now,
                                      jobQuery.State,
                                      jobQuery.Tasks[0].Progress));
                    Thread.Sleep(10000);
                }
                while (jobQuery.State != JobState.Finished &&
                       jobQuery.State != JobState.Error &&
                       jobQuery.State != JobState.Canceled);
            });
            progressPrintTask.Start();
    
            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
            progressJobTask.Wait();
    
            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                                error.Code,
                                                error.Message));  
                return false;                  
            }
    
        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }
    
    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }
    
    
    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);
    
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);
    
        return asset;
    }
    
    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();
    
        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));
    
        return processor;
    }

### <a id="file_types"></a>サポートされているファイルの種類

- MP4
- MOV
- WMV



## Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]















