<properties 
    pageTitle="よく寄せられる質問" 
    description="よく寄せられる質問 (FAQ)" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
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



# よく寄せられる質問

## 概要

Q: インデックス作成の規模はどのように設定できますか?

A: 予約ユニットは、エンコード タスクでもインデックス作成タスクでも同じです。 手順に従って [エンコード占有ユニットのスケール方法](media-services-how-to-scale.md)します。 Indexer のパフォーマンスは、予約ユニットの種類に左右されないことに**注意してください**。

Q: ビデオをアップロード、エンコード、公開しました。 ビデオをストリームしようとしても再生できない場合、どんな原因が考えられますか。

可能性が高い原因は、再生しようとしているストリーミング エンドポイントに、1 つ以上の予約済みストリーミング エンドポイント ユニットが割り当てられていないことです。 手順に従って [ストリーミング予約ユニットのスケール方法](media-services-how-to-scale.md)します。

Q: ライブ ストリームで合成はできますか。

A: 現時点では、Azure Media Services でのライブ ストリームの合成はできないため、コンピューター上で事前に作成する必要があります。

Q: ライブ ストリーミングを Azure CDN で使用できますか。

A: Media Services は、Azure CDN との統合をサポートしています (詳細については、次を参照してください。 [Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-manage-origins.md#enable_cdn))します。 CDN でライブ ストリーミングを使用できます。 Azure Media Services は、スムーズ ストリーミング、HLS、MPEG-DASH の出力を提供します。 これらのすべての形式では、データの転送に HTTP を使用し、HTTP キャッシュを活用します。 ライブ ストリーミングでは実際のビデオ/オーディオ データがフラグメントに分割され、この個別のフラグメントが CDN にキャッシュされます。 更新する必要があるデータは、マニフェスト データのみです。 CDN はマニフェスト データを定期的に更新します。

Q: Azure Media services では画像の保存はサポートされますか。

A: JPEG や PNG 画像を保存するだけの場合は、Azure Blob Storage を使用してください。 これらをビデオやオーディオ アセットに関連付けることがない限り、 Media Services アカウントに保存するメリットはありません。 または、ビデオ エンコーダーで画像をオーバーレイとして使用する必要がある場合、 Media Services エンコーダーではビデオの上に画像をオーバーレイさせることができ、JPEG や PNG が入力形式としてサポートされています。 詳細については、次を参照してください。 [オーバーレイの作成](https://msdn.microsoft.com/library/azure/dn640496.aspx)します。

Q: 1 つの Media Services アカウントから他のアカウントにアセットをコピーする方法を教えてください。

A: 資産を別の 1 つの Media Services アカウントにコピーするには、使用 [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) 拡張メソッドで使用できる、 [Azure Media Services .NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/) リポジトリです。 詳細については、次を参照してください。 [この](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) フォーラムのスレッド。

Q: エンコーディング プロセスでビデオをローテーションする方法を教えてください。

A:、 [メディア エンコーダー スタンダード](media-services-dotnet-encode-with-media-encoder-standard.md) 90/180/270 の角度で回転をサポートしています。 既定の動作 "自動" により、受信 MP4/MOV ファイルのローテーション メタデータの検出、およびこの補正が試行されます。 以下のもの **ソース** 要素を定義する json プリセットの 1 つ [ここ](http://msdn.microsoft.com/library/azure/mt269960.aspx):

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...

## Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





