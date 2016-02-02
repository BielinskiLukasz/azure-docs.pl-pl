<properties 
    pageTitle="タスク プリセットのカスタマイズによるエンコード タスクの操作" 
    description="Azure Media Services Encoder では、カスタムのプリセット ファイルを Azure Media Encoder に渡すことができます。このトピックでは、次のタスクを実現するためにプリセット ファイルをカスタマイズする方法を示します。既存のビデオでイメージをオーバーレイする、エンコーダーが生成する出力ファイル名を制御する、ビデオを結合する。 " 
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


# タスク プリセットのカスタマイズによるエンコード タスクの操作

Azure Media Services Encoder では、カスタムのプリセット ファイルを Azure Media Encoder に渡すことができます。 このトピックでは、次のタスクを実現するためにプリセット ファイルをカスタマイズする方法を示します。

- 既存のビデオにイメージをオーバーレイする
- エンコーダーが生成する出力ファイル名を制御する
- ビデオを結合する
- ほとんどが話し声のプレゼンテーションをエンコードする

## Azure Media Encoder の出力ファイル名の制御

Azure Media Encoder の出力ファイル名は、エンコーディング プロセスと入力資産の各種属性を組み合わせて自動的に作成されます。 各属性の識別には、以下に示したマクロが使用されます。

出力ファイルの命名に使用されるマクロの完全な一覧を次に示します。
Kbps で指定された audio Bitrate - オーディオをエンコードするときに使用されるビットレート

- Audio Codec - オーディオのエンコードに使用するコーデック。有効な値は AAC、WMA、DDP です。
- Channel Count - エンコードするオーディオ チャンネルの数。有効な値は 1、2、6 です。
- Default extension – 既定のファイル拡張子。
- Language - オーディオに使用される言語を表す BCP-47 言語コード。 現在は、"und" が既定で使用されます。
- Original File Name - Azure Storage にアップロードされたファイルの名前。
- StreamId – の streamID 属性で定義されたストリーム ID、 <StreamInfo> プリセット ファイル内の要素
- Video Codec - エンコードに使用するコーデック。有効な値は H264 と VC1 です。
- Video Bitrate - ビデオをエンコードするときに使用されるビットレート (kbps で指定)

Media Services Encoder によって生成されるファイルの名前は、以上のマクロを任意の順序で組み合わせることによって制御できます。 たとえば、既定の名前付け規則は次のようになっています。

    {Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

ファイルの名前付け規則がの DefaultMediaOutputFileName 属性で指定されている、 [プリセット](https://msdn.microsoft.com/library/azure/dn554334.aspx) 要素。 次に例を示します。

    <Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
      Version="5.0">
    <MediaFile …>
       <OutputFormat>
          <MP4OutputFormat StreamCompatibility="Standard">
             <VideoProfile>
                <MainH264VideoProfile … >
                   <Streams  AutoSize="False"
                             FreezeSort="False">
                      <StreamInfo StreamId="1"
                                  Size="1280, 720">
                         <Bitrate>
                           <ConstantBitrate Bitrate="3400"
                                            IsTwoPass="False"
                                            BufferWindow="00:00:05" />
                         </Bitrate>
                       </StreamInfo>
                      </Streams>
                   </MainH264VideoProfile>
                </VideoProfile>
             </MP4OutputFormat>
       </OutputFormat>
    </MediaFile>

マクロ間にはアンダースコアが挿入されます。たとえば、この構成で生成されるファイル名は、MyVideo_H264_4500kpbs_AAC_und_ch2_128kbps.mp4 のようになります。


## オーバーレイの作成

Azure Media Services Encoder では、イメージ (jpg、bmp、gif、tif)、ビデオ、またはオーディオ トラック (*.wma、*.mp3、*.wav) を既存のビデオにオーバーレイすることができます この機能は、Expression Encoder 4 (Service Pack 2) の機能によく似ています。

### Media Services Encoder でのオーバーレイ

オーバーレイを表示するタイミング、オーバーレイを表示する時間、およびイメージ/ビデオのオーバーレイでは画面にオーバーレイを表示する位置を指定することができます。オーバーレイをフェードイン/フェードアウトすることもできます。オーバーレイするオーディオ/ビデオ ファイルは、複数の資産にも 1 つの資産にも含めることができます。オーバーレイは、エンコーダーに渡すプリセット XML で制御します。プリセット スキーマの詳細については、Azure Media Encoder のスキーマを参照してください。オーバーレイで指定されます、 <MediaFile> 要素の次のプリセット スニペットに示すようにします。

    <MediaFile
        ...
        OverlayFileName="%1%"
        OverlayRect="257, 144, 255, 144"
        OverlayOpacity="0.9"
        OverlayFadeInDuration="00:00:02"
        OverlayFadeOutDuration="00:00:02"
        OverlayLayoutMode="Custom"
        OverlayStartTime="00:00:05"
        OverlayEndTime="00:00:10.2120000"
    
        AudioOverlayFileName="%2%"
        AudioOverlayLoop="True"
        AudioOverlayLoopingGap="00:00:00"
        AudioOverlayLayoutMode="WholeSequence"
        AudioOverlayGainLevel="2.2"
        AudioOverlayFadeInDuration="00:00:00"
        AudioOverlayFadeOutDuration="00:00:00">
        ...
    </MediaFile>

### ビデオまたはイメージ オーバーレイのプリセット

オーバーレイは、1 つまたは複数の資産から指定できます。 複数の資産を使用してビデオ オーバーレイを作成する場合は、オーバーレイのファイル名を %n% 構文を使用して OverlayFileName 属性で指定します。ここで n はエンコード タスクの入力資産の 0 から始まるインデックスです。 1 つの資産を使用してビデオ オーバーレイを作成する場合は、次のプリセット スニペットに示すように、オーバーレイのファイル名は OverlayFileName 属性に直接指定します。

例 1:

    
    <MediaFile
        ...
        OverlayFileName="%1%"
        OverlayRect="257, 144, 255, 144"
        OverlayOpacity="0.9"
        OverlayFadeInDuration="00:00:02"
        OverlayFadeOutDuration="00:00:02"
        OverlayLayoutMode="Custom"
        OverlayStartTime="00:00:05"
        OverlayEndTime="00:00:10.2120000">

例 2:

    
    <MediaFile
        ...
        OverlayFileName="videoOverlay.mp4"
        OverlayRect="257, 144, 255, 144"
        OverlayOpacity="0.9"
        OverlayFadeInDuration="00:00:02"
        OverlayFadeOutDuration="00:00:02"
        OverlayLayoutMode="Custom"
        OverlayStartTime="00:00:05"
        OverlayEndTime="00:00:10.2120000">

ビデオ オーバーレイのサイズと位置は、OverlayRect 属性で制御します。 オーバーレイ対象のコンテンツは、この四角形に合わせてサイズ変更されます。 不透明度は、OverlayOpacity 属性で制御します。 有効な値は 0.0 ～ 1.0 で、1.0 は不透明度 100% です。 オーバーレイは OverlayStartTime 属性で指定した時間に表示され、OverlayEndTime 属性で指定した時間に終了します。 OverlayStartTime と OverlayEndTime は、ソース ビデオのタイムライン内に収まるようにします。 オーバーレイ固有の各属性の詳細については、Azure Media Encoder のスキーマを参照してください。

### オーディオ オーバーレイのプリセット

オーディオ オーバーレイは、サポートされているオーディオ ファイル形式のいずれかにすることができます。サポートされているオーディオ ファイル形式の詳しい一覧については、Media Services Encoder でサポートされる形式を参照してください。オーディオ オーバーレイは指定も、 <MediaFile> 要素の次のプリセット スニペットに示すようにします。

    <MediaFile
        ...
        AudioOverlayFileName="%1%" 
        AudioOverlayLoop="True"
        AudioOverlayLoopingGap="00:00:00"
        AudioOverlayLayoutMode="Custom"
        AudioOverlayStartTime="00:05:00"
        AudioOverlayEndTime="00:10:00"
        AudioOverlayGainLevel="2.2"
        AudioOverlayFadeInDuration="00:00:00"
        AudioOverlayFadeOutDuration="00:00:00">

複数の資産に格納されているオーディオ オーバーレイの場合は、オーディオ オーバーレイのファイル名を %n% 構文を使用して AudioOverlayFileName 属性で指定します。ここで n はエンコード タスクの入力資産のコレクションの 0 から始まるインデックスです。 1 つの資産に格納されているオーディオ オーバーレイの場合は、オーバーレイのファイル名は AudioOverlayFileName 属性で直接指定します。 AudioOverlayLayoutMode で、オーディオ オーバーレイを表示するタイミングを指定します。 "WholeSequence" に設定すると、ビデオの時間中ずっとオーディオ トラックが表示されます。 "Custom" に設定すると、AudioOverlayStartTime 属性と AudioOverlayEndTime 属性でオーディオ オーバーレイの開始時間と終了時間を指定します。 OverlayStartTime と OverlayEndTime は、ソース ビデオのタイムライン内に収まるようにします。 オーディオ オーバーレイのすべての属性の詳細については、Azure Media Encoder のスキーマを参照してください。 オーディオ オーバーレイは、次のプリセット スニペットに示すようにビデオ オーバーレイと組み合わせることができます。

    <MediaFile
        DeinterlaceMode="AutoPixelAdaptive"
        ResizeQuality="Super"
        AudioGainLevel="1"
        VideoResizeMode="Stretch"
        OverlayFileName="%1%"
        OverlayRect="257, 144, 255, 144"
        OverlayOpacity="0.9"
        OverlayFadeInDuration="00:00:02"
        OverlayFadeOutDuration="00:00:02"
        OverlayLayoutMode="Custom"
        OverlayStartTime="00:00:05"
        OverlayEndTime="00:00:10.2120000"
        AudioOverlayFileName="%2%"
        AudioOverlayLoop="True"
        AudioOverlayLoopingGap="00:00:00"
        AudioOverlayLayoutMode="Custom"
        AudioOverlayStartTime="00:05:00"
        AudioOverlayEndTime="00:10:00"
        AudioOverlayGainLevel="2.2"
        AudioOverlayFadeInDuration="00:00:00"
        AudioOverlayFadeOutDuration="00:00:00">

### オーバーレイ付きのタスクの送信

プリセット ファイルを作成した後は、次の処理を行う必要があります。

- 資産をアップロードする
- プリセットの構成を読み込む (注: 以下のコードでは上記のプリセットを前提としています)
- ジョブを作成する
- Media Services Encoder への参照を取得する
- 入力資産、プリセットの構成、メディア エンコーダー、および出力資産のコレクションを指定するタスクを作成する
- ジョブを送信する

これらの処理を実行する方法を次のコード スニペットに示します。

    static public void CreateOverlayJob()
    {
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);
    
    
        // Upload assets to overlay
        IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4); // this is the input mezzanine
        IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);// this will be used as a video overlay
        IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv); // this will be used as an audio overlay
    
        // Load the preset configuration
        string presetFileName = "OverlayPreset.xml";
        string configuration = File.ReadAllText(presetFileName);
    
        // Create a job
        IJob job = _context.Jobs.Create("A AME overlay job, using " + presetFileName);
    
        // Get a reference to the media services encoder   
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
    
        // Create a task    
        ITask task = job.Tasks.AddNew("Encode Task for overlay, using " + presetFileName, processor, configuration, TaskOptions.None);
    
        // Add the input assets
        task.InputAssets.Add(inputAsset1);
        task.InputAssets.Add(inputAsset2);
        task.InputAssets.Add(inputAsset3);
    
        // Add the output asset
        task.OutputAssets.AddNew("Result of an overlay job, using " + presetFileName, AssetCreationOptions.None);
    
        // Submit the job
        job.Submit();
    }

>[AZURE.NOTE]このスニペットは、わかりやすくするために順番に各資産を読み込みます。 運用環境では、資産は一括で読み込まれます。 複数のアセットの一括アップロードする方法の詳細を参照してください。 [アセットの Media Services sdk for .NET の一括取り込み](media-services-dotnet-upload-files.md#ingest_in_bulk)します。

完全なサンプル コードを参照してください。 [Media Services Encoder でのオーバーレイの作成](https://code.msdn.microsoft.com/Creating-Audio-and-Video-c2942c47)します。

### エラー条件

プリセット文字列を編集するときは、次のことを確認する必要があります。

- ビデオ/イメージ オーバーレイの場合は、オーバーレイの四角形全体をソース ビデオのサイズ内に収める必要があります。
- オーバーレイの開始時間と終了時間を、ソース ビデオのタイムライン内にする必要があります。
- プリセット XML に ?OverlayFileName=”%n%” への参照が含まれている場合は、タスクの InputAssets コレクションに n + 1 以上の資産を含める必要があります。



## ビデオのセグメントの結合

Media Services Encoder は、一連のビデオの結合をサポートします。 ビデオの端と端を結合できます。また、結合するビデオの一方または両方の部分を指定することができます。 結合の結果は、入力資産の指定されたビデオを含む 1 つの出力資産になります。 結合するビデオは、複数の資産にも 1 つの資産にも含めることができます。 結合は、エンコーダーに渡すプリセット XML によって制御します。 プリセット スキーマの詳細については、次を参照してください。 [Azure メディア エンコーダーのスキーマ](https://msdn.microsoft.com/library/azure/dn584702.aspx)します。

### Media Services Encoder での結合

内で合成の制御、 <MediaFile> 、次のプリセットに示すようにします。

    <MediaFile
        DeinterlaceMode="AutoPixelAdaptive"
        ResizeQuality="Super"
        AudioGainLevel="1"
        VideoResizeMode="Stretch">
        <Sources>
          <Source
            AudioStreamIndex="0">
            <Clips>
              <Clip
                StartTime="00:00:00"
                EndTime="00:00:05" />
            </Clips>
          </Source>
          <Source
            ResizeMode="Letterbox"
            ApplyCrop="False"
            AudioStreamIndex="0"
            MediaFile="%1%">
            <Clips>
              <Clip
                StartTime="00:00:00"
                EndTime="00:00:05" />
            </Clips>
          </Source>
          <Source
            ResizeMode="Letterbox"
            ApplyCrop="False"
            AudioStreamIndex="0"
            MediaFile="%2%">
            <Clips>
              <Clip
                StartTime="00:00:00"
                EndTime="00:00:05" />
            </Clips>
          </Source>
         </Sources>
    </MediaFile>

結合するには、各ビデオについて、 <Source> に要素を追加、 <Sources> 要素。各 <Source> 要素を含む、 <Clips> 要素。各 <Clips> 要素は、1 つまたは複数含まれています。 <Clip> 開始と終了時刻を指定してに、ビデオの長さを指定する要素は、出力アセットに結合します。 <Source> 要素が機能するアセットを参照します。参照の形式は、結合するビデオが別々の資産と 1 つの資産のどちらに含まれているかよって異なります。ビデオ全体を結合する場合は省略するだけで、 <Clips> 要素。

### 複数の資産からのビデオの結合

MediaFile 属性に、0 から始まるインデックスを使用する複数の資産からのビデオを結合するとき、 <Source> アセットを指定する要素、 <Source> 要素に対応します。ゼロから始まるインデックスが指定されていない、 <Source> MediaFile 属性で指定されていない要素が最初の入力アセットを参照します。他のすべての <Source> 要素は、ここで n は入力アセットの 0 から始まるインデックスを %n% 構文を使用して参照する入力アセットの 0 から始まるインデックスを指定する必要があります。前の例では、1 つ目で <Source> 要素は、最初の入力アセットを指定します。 もう 1 つ <Source> 要素を指定しており、2 番目の入力アセット。参照する入力資産の順序などの要件はありません。

    <MediaFile
        DeinterlaceMode="AutoPixelAdaptive"
        ResizeQuality="Super"
        AudioGainLevel="1"
        VideoResizeMode="Stretch">
        <Sources>
          <Source
            AudioStreamIndex="0"
            MediaFile="%1%">
            <Clips>
              <Clip EndTime="00:00:05" 
                    StartTime="00:00:00" />
            </Clips>
    
            </Source>
          <Source
           AudioStreamIndex="0">
            <Clips>
              <Clip
                StartTime="00:00:00"
                EndTime="00:00:05" />
            </Clips>
          </Source>
          <Source
              AudioStreamIndex="0"
             MediaFile="%2%">
            <Clips>
              <Clip
                StartTime="00:00:00"
                EndTime="00:00:05" />
            </Clips>
          </Source>
         </Sources>
    </MediaFile>

この例では、2 番目、1 番目、3 番目の入力資産の一部を結合します。 各ビデオは 0 から始まるインデックスによって参照されるため、同じ名前を持つ 2 つのビデオを結合できることに注意してください。 プリセット ファイルを作成した後は、次の処理を行う必要があります。

- 資産をアップロードする
- プリセットの構成を読み込む
- ジョブを作成する
- Media Services Encoder への参照を取得する
- 入力資産、プリセットの構成、メディア エンコーダー、および出力資産を指定するタスクを作成する
- ジョブを送信する

これらの処理を実行する方法を次のコード スニペットに示します。

    static public void StitchWithMultipleAssets()
    {
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);
    
        // Upload assets to stitch
        IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4);
        IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);
        IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv);
    
        // Load the preset configuration
        string presetFileName = "StitchingWithMultipleAssets.xml";
        string configuration = File.ReadAllText(presetFileName);
    
        // Create a job
        IJob job = _context.Jobs.Create("A AME stitching job, using " + presetFileName);
    
        // Get a reference to the media services encoder   
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
    
        // Create a task    
        ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
    
        // Add the input assets
        task.InputAssets.Add(inputAsset1);
        task.InputAssets.Add(inputAsset2);
        task.InputAssets.Add(inputAsset3);
    
        // Add the output asset
        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);
    
        // Submit the job
        job.Submit();
        } 

このスニペットは、わかりやすくするために順番に各資産を読み込みます。 運用環境では、資産は一括で読み込まれます。 複数のアセットの一括アップロードする方法の詳細を参照してください。 [アセットの Media Services sdk for .NET の一括取り込み](media-services-dotnet-upload-files.md#ingest_in_bulk)します。 完全なサンプル コードを参照してください。 [Media Services Encoder での合成](https://code.msdn.microsoft.com/Stitching-with-Media-8fd5f203)します。

### 1 つの資産からのビデオの結合

1 つの資産内のビデオを結合するときは、各ビデオの名前が一意である必要があります。 ビデオは、ファイル名を属性の値として、MediaFile 属性を使用して指定します。 次に例を示します。

    <MediaFile
        DeinterlaceMode="AutoPixelAdaptive"
        ResizeQuality="Super"
        AudioGainLevel="1"
        VideoResizeMode="Stretch">
        <Sources>
          <Source
            AudioStreamIndex="0"
            MediaFile="video1.mp4">
            <Clips>
              <Clip
                StartTime="00:00:00"
                EndTime="00:00:05" />
            </Clips>
          </Source>
          <Source
           AudioStreamIndex="0"
           MediaFile="video2.wmv">
    
            <Clips>
              <Clip
                StartTime="00:00:00"
                EndTime="00:00:05" />
            </Clips>
          </Source>
          <Source
              AudioStreamIndex="0"
             MediaFile="video3.wmv">
            <Clips>
              <Clip
                StartTime="00:00:00"
                EndTime="00:00:05" />
            </Clips>
          </Source>
         </Sources>
    </MediaFile>

このプリセットでは、video1.mp4、video2.wmv、および video3.wmv の一部を結合して出力資産にします。
ジョブとタスクの作成は、複数の資産からのビデオの結合と同じです。次のコードに示すように、1 つの資産をアップロードするだけです。

    // Creates a stitching job that uses a single asset 
    static public void StitchWithASingleAsset()
    {
        string presetFileName = "StitchingWithASingleAsset.xml";
        string configuration = File.ReadAllText(presetFileName);
    
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);
    
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
        IJob job = _context.Jobs.Create("A AME stitching job, using " + presetFileName);
        IAsset asset = CreateAssetAndUploadMultipleFiles(AssetCreationOptions.None, _stitchingFiles);
    
        ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);
    
        job.Submit();
    }

## プレゼンテーション (ほとんどが話し声のオーディオ ストリーム) のエンコード

オーディオ トラックの内容のほとんどが話し声であるビデオをエンコードするときに既定のエンコーダー プリセットを使用すると、バックグラウンド ノイズが発生し、エンコード先の資産で増幅されることがあります。 この現象は、NormalizeAudio 属性が true に設定されていると発生します。

### ほとんどが話し声であるプレゼンテーションのエンコード

バックグラウンド ノイズの増幅をを回避するのには、次の手順を実行します。

1. 使用するエンコーダー プリセットの内容を XML ファイルにコピーします。 エンコーダー プリセットは、Azure Media Encoder のスキーマにあります。
1. NormalizeAudio 属性を削除、プリセット ファイルの先頭付近にある検出された、 <MediaFile> 要素。

    < MediaFile
         DeinterlaceMode ="AutoPixelAdaptive"
         ResizeQuality =「スーパー」
         NormalizeAudio ="True"
         AudioGainLevel =「1」
         VideoResizeMode ="Stretch">

1. 変更したプリセット ファイルをローカル ハード ドライブに保存し、次のようなコードを使用してこのカスタム プリセットでエンコードします。

     // Upload file and create asset
     IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, @"C:\TEMP\Original.mp4");
    
     string inputPresetFile = @"C:\TEMP\H264 Broadband 720p NoAudioNorm.xml";
     string presetName = Path.GetFileNameWithoutExtension(inputPresetFile);
    
     IJob job = _context.Jobs.Create("Encode Job for " + asset.Name + ", encoded using " +  presetName);
    
     Console.WriteLine("Encode Job for " + asset.Name + ", encoded using " + presetName);
    
     // Get a media processor reference, and pass to it the name of the processor to use for the specific task.
     IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
     Console.WriteLine("Got MP " + processor.Name + ", ID : " + processor.Id + ", version: " + processor.Version);
    
     // Read the configuration data into a string. 
     string configuration = File.ReadAllText(inputPresetFile);
    
     // Create a task with the encoding details, using a string preset.
     ITask task = job.Tasks.AddNew("Encode Task for " + asset.Name + ", encoded using " + presetName, processor, configuration,
                     Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
    
     // Specify the input asset to be encoded.
     task.InputAssets.Add(asset);
    
     // Add an output asset to contain the results of the job.
     task.OutputAssets.AddNew("Output asset for " + asset.Name + ", encoded using " + presetName, AssetCreationOptions.None);
    
     // Launch the job. 
     job.Submit();



## Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## 関連項目

[Azure メディア エンコーダーの XML スキーマ](https://msdn.microsoft.com/library/azure/dn584702.aspx)




