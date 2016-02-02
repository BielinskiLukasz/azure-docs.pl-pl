<properties
    pageTitle="Azure Media Indexer によるメディア ファイルのインデックス作成"
    description="Azure Media Indexer を使用すると、メディア ファイルのコンテンツを検索対応にしたり、字幕やキーワード用にフルテキストのトランスクリプトを生成したりできます。このトピックでは、Media Indexer の使用方法について説明します。"
    services="media-services"
    documentationCenter=""
    authors="Juliako,Asolanki,johndeu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/15/2015"   
    ms.author="juliako"/>



# Azure Media Indexer によるメディア ファイルのインデックス作成

> [AZURE.SELECTOR]
- [Portal](media-services-manage-content.md#index)
- [.NET](media-services-index-content.md)



Azure Media Indexer を使用すると、メディア ファイルのコンテンツを検索対応にしたり、字幕やキーワード用にフルテキストのトランスクリプトを生成したりできます。 バッチ内の 1 つのメディア ファイルまたは複数のメディア ファイルを処理できます。
>[AZURE.IMPORTANT] コンテンツのインデックスを作成する場合は、クリアな (バック グラウンド ミュージック、ノイズ、特殊効果、またはマイク ヒスノイズなどがない) 音声機能を持つメディア ファイルを使用してください。 適切なコンテンツの例としては、記録された会議、講義またはプレゼンテーションなどがあります。 ムービー、テレビ番組、混合音声とサウンド効果を含むもの、バックグラウンド ノイズ (ヒスノイズ) を含む記録状態が良好でないコンテンツは、インデックス作成に適しません。


インデックス作成ジョブは次の出力を生成できます。

- 次の形式のクローズド キャプション ファイル: **SAMI**、**TTML**、および **WebVTT**。

    クローズド キャプション ファイルには、Recognizability と呼ばれるタグが含まれています。これは、ソース ビデオ内の音声がどれくらい認識可能であるかに基づいて、インデックス作成ジョブを評価します。 見分けの値を使用して、出力ファイルの使いやすさを検査します。 低いスコアは、オーディオの品質が良好ではないために、インデックスの作成結果が良好ではないという意味です。
- キーワード ファイル (XML)。
- SQL サーバーで使用する Audio Indexing BLOB (AIB) ファイル。

    詳細については、次を参照してください。 [AIB ファイルを Azure Media Indexer および SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)します。


このトピックでは、ジョブのインデックスを作成して、**アセットのインデックスを作成**、**複数のファイルのインデックスを作成**する方法について説明します。

Azure Media Indexer の最新の更新を参照してください。 [Media Services のブログ](#preset)します。

## タスクのインデックスを作成するための構成とマニフェスト ファイルの使用

タスクの構成を使用して、インデックス作成のタスクの詳細を指定できます。 たとえば、メディア ファイルに使用するメタデータを指定できます。 このメタデータは、言語エンジンによって、そのボキャブラリの展開に使用され、音声認識の精度が大幅に向上します。 希望の出力ファイルを指定することもできます。

マニフェスト ファイルを使用して一度に複数のメディア ファイルを処理できます。

詳細については、次を参照してください。 [Azure Media Indexer 用のタスク プリセット](#)します。

## アセットのインデックス作成

次のメソッドは、アセットとしてメディア ファイルをアップロードし、ジョブを作成してアセットのインデックスを作成します。

構成ファイルが指定されていない場合、メディア ファイルは既定のすべての設定を使用してインデックスが作成されます。

    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);
    
        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");
    
        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer",
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
    
        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);
    
        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);
    
        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);
    
        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
    
        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
    
        // Launch the job.
        job.Submit();
    
        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();
    
        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }
    
        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);
    
        return true;
    }
    
    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);
    
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);
    
        return asset;
    }
    
    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }
    
    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();
    
        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));
    
        return processor;
    }  

### <a id="output_files"></a>出力ファイル

既定では、ジョブのインデックスを作成すると、次の出力ファイルが生成されます。 ファイルは、最初の出力アセットに格納されます。

複数の入力メディア ファイルがある場合、インデクサーはジョブの出力に対して「JobResult.txt」という名前のマニフェスト ファイルを生成します。 それぞれの入力メディア ファイル、結果として得られる AIB、SAMI、TTML、WebVTT、およびキーワード ファイルが順番に番号が付けられ、「エイリアス」を使用してという名前です。

 ファイル名| 説明
----------|------------
 __InputFileName.aib__| オーディオ インデックス BLOB ファイル。<br /><br /> オーディオ インデックス Blob (AIB) ファイルとは、フルテキスト検索を使用して Microsoft SQL server で検索できるバイナリ ファイルです。AIB ファイルは、各単語に豊富な代替候補を含み、より充実した検索を実現しているため、単純なキャプション ファイルよりも強力です。<br/> <br/>マシン実行されている Microsoft SQL server 2008 以降に Indexer SQL アドオンのインストールが必要です。Microsoft SQL サーバーのフル テキスト検索を使用して、AIB を検索すると、WAMI によって生成されたクローズド キャプション ファイルを検索するよりも正確な検索結果が得られます。これは、クローズド キャプション ファイルがオーディオの各セグメントに信頼度が最上位の単語を含んでいるのに対し、AIB には代替候補として似ているサウンドの単語が含まれているためです。場合は、話された単語の検索が最も重要な Microsoft SQL Server と AIB を組み合わせて使用することをお勧めします。<br/><br/>アドオンをダウンロードする] をクリックして <a href="http://aka.ms/indexersql">Azure Media Indexer SQL アドオン</a>します。<br/><br/>その他の検索エンジン Apache Lucene/solr など、クローズド キャプションとキーワードの XML ファイルに基づいたビデオをインデックスを利用することもが、この正確さに欠けますの検索結果になります。
 __InputFileName.smi__<br />__InputFileName.ttml__<br />__InputFileName.vtt__| SAMI、TTML、および WebVTT 形式のクローズド キャプション (CC) ファイルです。<br/><br/>オーディオさせるでき、ビデオ ファイルを聴覚障がいを持つユーザーにアクセスできる<br/>。<br/>クローズド キャプション ファイルと呼ばれるタグが含まれます。 <b>見分け</b> 、ソース ビデオ内の音声の認識性がに基づいてインデックス作成ジョブを評価します。値を使用して <b>見分け</b> 出力ファイルの使いやすさにします。低いスコアは、オーディオの品質が良好ではないために、インデックスの作成結果が良好ではないという意味です。
 __InputFileName.kw.xml<br />InputFileName.info__| キーワードと情報ファイル。<br/><br/>キーワードのファイルは、頻度とオフセットの情報、音声コンテンツから抽出されたキーワードを含む XML ファイルです。<br/><br/>情報ファイルは、各用語の認識について詳細な情報を含むプレーン テキスト ファイルです。最初の行は、特別な見分けスコアが含まれます。後続の各行は、次のデータのタブ区切りの一覧:、時刻、終了時刻、単語や語句の信頼度を開始します。時間は秒単位で、信頼度は 0 ～ 1 の数値として指定されます。<br/><br/>行の例:"1.20 1.45 word 0.67" <br/><br/>これらのファイルを音声分析を実行するなど、さまざまな目的で、使用または関連性の高い広告を配信するために使用見つけやすくしたり、メディア ファイルを作成するには、Bing、Google または Microsoft SharePoint などの検索エンジンに公開されていることができます。
 __JobResult.txt__| Output manifest, present only when indexing multiple files, containing the following information:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Error</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/>



すべての入力メディアのインデックスが正常に作成されない場合は、インデックス作成ジョブはエラー コード 400 を表示して失敗します。 詳細については、次を参照してください。 [エラーコード](#error_codes)します。

## 複数のファイルのインデックス作成

次のメソッドは、アセットとして複数のメディア ファイルをアップロードし、ジョブを作成してバッチ内のすべてのファイルのインデックスを作成します。

.Lst 拡張子のマニフェスト ファイルが作成され、アセットにアップロードします。 マニフェスト ファイルには、すべてのアセットファイルの一覧が含まれています。 詳細については、次を参照してください。 [Azure Media Indexer 用のタスク プリセット](https://msdn.microsoft.com/library/azure/dn783454.aspx)します。

    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);
    
        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);
    
        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");
    
        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
    
        // Read configuration.
        string configuration = File.ReadAllText("batch.config");
    
        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);
    
        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);
    
        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);
    
        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
    
        // Launch the job.
        job.Submit();
    
        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();
    
        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }
    
        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);
    
        return true;
    }
    
    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);
    
        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }
    
        return asset;
    }

### 部分的に成功したジョブ

すべての入力メディアのインデックスが正常に作成されない場合は、インデックス作成ジョブはエラー コード 400 を表示して失敗します。 詳細については、次を参照してください。 [エラーコード](#error_codes)します。


(成功したジョブの場合) と同じ出力が生成されます。 エラー列の値に基づいて、出力マニフェスト ファイルで、入力ファイルが失敗したかどうかを参照します。 失敗した入力ファイルでは、結果として得られる AIB、SAMI、TTML、WebVTT、およびキーワード ファイルは生成されません。

### <a id="preset"></a> Azure Media Indexer のタスク プリセット

Azure Media Indexer からの処理は、オプションのタスク プリセットをタスクと共に指定することでカスタマイズできます。 次の表は、この configuration xml の形式の説明です。

 名前| 必要な| 説明
----|----|---
 __入力__| false| インデックスを作成する資産ファイル。</p><p>Azure Media Indexer は、次のメディア ファイル形式をサポートしています: MP4、WMV、MP3、M4A、WMA、AAC、WAV</p>。<p>でファイル名 (s) を指定できます、 **名** または **リスト** の属性、 **入力** 要素 (下図)。インデックスにする資産ファイルを指定しない場合、プライマリ ファイルを選択します。プライマリ資産ファイルが設定されていない場合、入力資産の最初のファイルのインデックスが作成されます。</p><p>アセット ファイルの名前を明示的に指定するには:<br />`< 入力 name="TestFile.wmv">`<br /><br />も複数の資産ファイルを一度に (最大 10 個のファイル) をインデックスを指定できます。これを行う:<br /><br /><ol class="ordered"><li><p>テキスト ファイル (マニフェスト ファイル) を作成し、拡張子 .lst を付けます。</p></li><li><p>このマニフェスト ファイルを入力資産のすべての資産ファイル名の一覧を追加します。</p></li><li><p>(アップロード) マニフェスト ファイルをアセットに追加します。</p></li><li><p>Input の list 属性でマニフェスト ファイルの名前を指定します。<br />`< 入力 list="input.lst">`</li></ol><br /><br />注: 10 個を超えるファイルをマニフェスト ファイルを追加する場合、インデックス作成ジョブは失敗し、2006年エラー コード。
 __metadata__| false| 語彙アダプテーション用に指定するアセット ファイルのメタデータ。名詞などの非標準の語彙を認識するためのインデクサーを準備すると便利です。<br />`< メタデータ キー =「...」value="..."/>` <br /><br />を指定できます __値__ 定義済みの __キー__します。次のキーが現在サポートされて:<br /><br />、ジョブのモデルし、音声認識の精度を向上させる"title"と"description"- ボキャブラリの学習機能の言語を調整するために使用します。値は、インターネット検索のインデックス作成タスクの期間の内部辞書を強化する内容を使用して、コンテキストに関連するテキスト ドキュメントを検索するシードします。<br />`< メタデータ キー"title"の値を = ="[、メディア ファイルのタイトル]"/>`<br />`< メタデータ キー「説明」の値を = ="[メディア ファイルの説明]/>"`
 __機能__ <br /><br /> バージョン 1.2 に追加します。現時点でサポートされている機能は、音声認識 ("ASR") のみです。| false| 音声認識機能が次の設定キー:<table><tr><th><p>キー</p></th>     <th><p>説明</p></th><th><p>値の例</p></th></tr><tr><td><p>言語</p></td><td><p>マルチ メディア ファイルで認識される自然言語</p>。</td><td><p>English, Spanish</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>a semicolon-separated list of the desired output caption formats (if any)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>AIB ファイルが (SQL Server とお客様の Indexer IFilter で使用する) ために必要かどうかを指定するブール型のフラグ。     詳細については、次を参照してください <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">AIB ファイルを Azure Media Indexer および SQL Server</a>。</p>。</td><td><p>True;False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>キーワードの XML ファイルが必要かどうかを指定するブール型のフラグ</p>。</td><td><p>True;False を指定します。</p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>(信頼レベル) に関係なくキャプションがフルを強制するかどうかを指定するブール型のフラグ。</p><p>単語や語句が 50% の信頼度未満の最終的なキャプションの出力から省略すると、(「...」) の省略記号で置き換えられます場合、既定値は false に設定します。省略記号は、キャプション品質管理および監査に役立ちます。</p></td><td><p>True;False を指定します。 </p></td></tr></table>

### <a id="error_codes"></a>エラー コード

エラーが発生した場合、Azure Media Indexer は、次のいずれかのエラー コードを返します。

 コード| 名前| 考えられる原因
-----|------|------------------
 2000| 構成が無効です| 構成が無効です
 2001| 無効な入力アセット| 入力アセット、または空のアセットがありません。
 2002| マニフェストが無効です| マニフェストが空か、マニフェストに無効な項目が含まれています。
 2003| メディア ファイルのダウンロードに失敗しました| マニフェスト ファイルでの URL が無効です。
 2004| サポートされていないプロトコル| メディア URL のプロトコルはサポートされません。
 2005| サポートされていないファイルの種類| 入力メディア ファイルの種類がサポートされていません。
 2006| 入力のファイルが多すぎます| 入力のマニフェストに 10 個を超えるファイルがあります。
 3000| メディア ファイルのデコードに失敗しました| サポートされていないメディア コーデック <br/>または<br/> 破損しているメディア ファイル <br/>または<br/> 入力メディアのオーディオ ストリームがありません。
 4000| バッチのインデックス作成一部成功しました| 一部の入力メディア ファイルは、インデックスを付けるできませんでした。詳細については、次を参照してください。 <a href="output_files">出力ファイル</a>します。
 その他の| 内部エラー| サポート チームにお問い合わせください。indexer@microsoft.com


## <a id="supported_languages"></a>サポートされている言語

現時点では、英語とスペイン語の言語がサポートされています。 詳細については、次を参照してください。 [v1.2 リリース ブログの投稿](http://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)します。


## Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



## 関連リンク

[Azure Media Indexer および SQL Server で AIB ファイルを使用します。](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)











