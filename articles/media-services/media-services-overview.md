<properties 
    pageTitle="Azure Media Services の概要と一般的なシナリオ" 
    description="このトピックでは、Azure Media Services の概要を説明します。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako,anilmur" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/15/2015"
    ms.author="juliako"/>


# Azure Media Services の概要と一般的なシナリオ

Microsoft Azure Media Services は拡張可能なクラウド ベースのプラットフォームです。これにより、開発者はスケーラブルなメディア管理の構築、アプリケーションの配信を実行できます。 Media Services は、オンデマンド、また各種クライアント (TV、PC、モバイル デバイスなど) へのライブ ストリーム配信の両方でのビデオやオーディオの安全なアップロード、格納、エンコード、パッケージ化を可能にする REST API に基づいています。

Media Services を使いこなして、エンド ツー エンドのワークフローを構築できます。 ワークフローの一部にサード パーティのコンポーネントを使用することもできます。 たとえば、サード パーティのエンコーダーを使用してエンコードしてから、 Media Services を使用してアップロード、保護、パッケージ化、配信などを行うことができます。

コンテンツをライブ ストリーム配信したり、オンデマンドで配信したりできます。 このトピックは、コンテンツの配信の一般的なシナリオを示しています。 [live](media-services-overview.md#live_scenarios) または [オンデマンド](media-services-overview.md#vod_scenarios)します。 このトピックでは、その他の関連トピックのリンクも提供します。

## SDK とツール

Media Services ソリューションを構築するために、以下を使用できます。

- [Media Services REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- 使用可能なクライアント Sdk のいずれか: [Azure Media Services SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services), 、[Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java), 、[Azure Media Services for Node.js](https://github.com/fritzy/node-azure-media), 、[Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
- 既存のツール: [Azure Classic Portal](http://manage.windowsazure.com/) または [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)します。


## Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS ライブ ストリーミングのワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [オンデマンド ストリーミングのワークフローで AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## ポスター

[ここ](http://azure.microsoft.com/documentation/infographics/media-services/) メディアの作成から使用まで、AMS ワークフローを示しています。 Azure Media Services ポスターを表示することができます。

## 前提条件

Azure Media Services を使用するには、次が必要です。

3. Azure アカウント。 アカウントがない場合は、無料試用版アカウントを数分で作成することができます。 詳細については、「 [Azure 無料試用版](azure.microsoft.com)します。
2. Azure Media Services アカウントを作成します。 Azure Media Services アカウントを作成するには、Azure クラシック ポータル、.NET、REST API を使用します。 詳細については、次を参照してください。 [アカウントの作成](media-services-create-account.md)します。
3. (省略可能) 開発環境をセットアップします。 開発環境の .NET または REST API を選択します。 詳細については、次を参照してください。 [環境を設定する](media-services-dotnet-how-to-use.md)します。

    プログラムで接続する方法についても、説明 [接続](media-services-dotnet-connect_programmatically.md)します。
4. (推奨) 1 つまたは複数のスケール単位を割り当てます。 実稼働環境でのアプリケーションの 1 つ以上のスケール単位を割り当てることをお勧めします。 詳細については、次を参照してください。 [ストリーミング エンドポイントを管理する](media-services-manage-origins.md)します。

## 概念

詳細については、次を参照してください。 [概念](media-services-concepts.md)します。


## <a id="vod_scenarios"></a>Azure Media Services でオンデマンドでメディアを配信する: 一般的なシナリオとタスク

このセクションでは、一般的なシナリオと関連するトピックへのリンクを提供します。 次の図は、コンテンツのオンデマンド配信に関連する Media Services プラットフォームの主要な部分を示しています。

![VoD ワークフロー][vod-overview]


### ストレージのコンテンツを保護し、ストリーミング メディアを平文 (暗号化されていない) で配信する

1. 高品質な中間ファイルを資産にアップロードします。

    ストレージ暗号化オプションを資産に適用し、アップロード中のコンテンツとストレージ内のコンテンツを保護することをお勧めします。

1. アダプティブ ビットレート MP4 ファイルのセットにエンコードする。

    ストレージ暗号化オプションを出力資産に適用し、保存されているコンテンツを保護することをお勧めします。

1. 資産配信ポリシーを構成します (動的パッケージで使用)。

    アセットがストレージで暗号化されている場合は、アセット配信ポリシーを構成する**必要があります**。

1. OnDemand ロケーターを作成して資産を発行します。

    コンテンツをストリームするストリーミング エンドポイントに少なくとも 1 つのストリーミング予約ユニットがあることを確認します。

1. 公開済みコンテンツをストリーミングします。

### ストレージ内のコンテンツを保護し、動的に暗号化されたストリーミング メディアを配信する

動的暗号化を使用するには、暗号化されたコンテンツのストリーミング元となるストリーミング エンドポイントに、少なくとも 1 つのストリーミング予約ユニットが必要です。

1. 高品質な中間ファイルを資産にアップロードします。 ストレージ暗号化オプションを資産に適用します。
1. アダプティブ ビットレート MP4 ファイルのセットにエンコードする。 ストレージ暗号化オプションを出力資産に適用します。
1. 再生中に動的に暗号化する資産の、暗号化コンテンツ キーを作成します。
2. コンテンツ キー承認ポリシーを構成します。
1. 資産配信ポリシーを構成します (動的パッケージと動的暗号化で使用)。
1. OnDemand ロケーターを作成して資産を発行します。
1. 公開済みコンテンツをストリーミングします。

### コンテンツのインデックス作成

1. 高品質な中間ファイルを資産にアップロードします。
1. コンテンツのインデックスを作成します。

    インデックス作成ジョブは、ビデオの再生でクローズド キャプション (CC) として使用できるファイルを生成します。 また、ビデオ内を検索したり、正確なビデオの位置へ移動したりできるようにするファイルを生成します。

1. インデックス付けされたコンテンツを使用します。


### プログレッシブ ダウンロードの提供

1. 高品質な中間ファイルを資産にアップロードします。
1. 単一 MP4 ファイルにエンコードする。
1. OnDemand または SAS ロケーターを作成して資産を発行します。

    OnDemand ロケーターを使用する場合、コンテンツを徐々にダウンロードするストリーミング エンドポイントに、少なくとも 1 つのストリーミング予約ユニットがあることをご確認ください。

    SAS ロケーターを使用する場合、コンテンツは Azure BLOB ストレージからダウンロードされます。 この場合、ストリーミング予約ユニットは必要ありません。

1. コンテンツを徐々にダウンロードします。

### 関連トピック

- [コンテンツをアップロードする方法](media-services-manage-content.md#upload)
- [メディア プロセッサを取得する方法](media-services-get-media-processor.md)
- [コンテンツをエンコードする方法](media-services-manage-content.md#encode)
- [ジョブを監視する方法](media-services-portal-check-job-progress.md)
- [コンテンツ インデックスを作成する方法](media-services-manage-content.md#index)
- [コンテンツを保護する方法](media-services-manage-content.md#encrypt)
- [保護する方法を公開](media-services-manage-content.md#publish)
- [エンコードを拡張する方法](media-services-portal-encoding-units.md)

## <a id="live_scenarios"></a>Azure Media Services によるライブ ストリーミング イベントを配信します。

ライブ ストリーミングを使用する場合は、通常、次のコンポーネントが関連しています。

- イベントのブロードキャストに使用されるカメラ。
- カメラからの信号をライブ ストリーミング サービスに送信されるストリームに変換するライブ ビデオ エンコーダー。

    必要に応じて、複数のライブ エンコーダー。 非常に高い可用性と高品質が要求される重要なライブ イベントでは、アクティブ/アクティブの冗長エンコーダーを使用して、データを失わないシームレスなフェールオーバーを実現することをお勧めします。
- 次の操作を実行できるライブ ストリーミング サービス。
    - さまざまなライブ ストリーミング プロトコル (RTMP、スムーズ ストリーミングなど) を使用してライブ コンテンツを取り込む。
    - ストリームをアダプティブ ビットレート ストリームにエンコードする。
    - ライブ ストリームをプレビューする。
    - 後でストリーミングするために、取り込んだコンテンツを保存する (ビデオ オン デマンド)。
    - コンテンツを一般的なストリーミング プロトコル (MPEG DASH、Smooth、HLS、HDS など) を使用して顧客に配信したり、再配布のための Content Delivery Network (CDN) に直接配信する。


**Microsoft Azure Media Services** (AMS) は、取り込み、エンコード、プレビュー、保存、およびライブ ストリーミング コンテンツを配信する機能を提供します。

コンテンツを顧客に配信する場合、その目標は、異なるネットワーク条件におけるさまざまなデバイスに高品質のビデオを配信することにあります。 品質とネットワーク条件に対応するには、ライブ エンコーダーを使用して、ストリームをマルチビットレート (アダプティブ ビットレート) ビデオ ストリームにエンコードします。 さまざまなデバイスでのストリーミングに対応をするには、Media Services を使用して [動的パッケージング](media-services-dynamic-packaging-overview.md) 、ストリームをさまざまなプロトコルを動的に再パッケージ化します。 Media Services で配信がサポートされるアダプティブ ビットレート ストリーミング テクノロジは、HTTP ライブ ストリーミング (HLS)、Smooth Streaming、MPEG DASH、HDS (Adobe PrimeTime/Access のライセンスが必要) です。

Azure Media Services、**チャネル**、**プログラム**、**ストリーミング エンドポイント**で、インジェスト、書式設定、DVR、セキュリティ、スケーラビリティ、冗長性などのすべてのライブ ストリーミングの機能を処理します。

**チャネル**は、ライブ ストリーミング コンテンツを処理するためのパイプラインを表します。 現時点では、チャネルは次の方法でライブ入力ストリームを受信できます。


- オンプレミスのライブ エンコーダーは、RTP (MPEG-TS)、RTMP、スムーズ ストリーミング (Fragmented MP4) のいずれかの形式で、シングル ビットレート ストリームを Media Services によるLive Encoding が有効なチャネルに送信します。 次に、受信したシングル ビットレート ストリームのマルチ ビットレート (アダプティブ) ビデオ ストリームへのライブ エンコードがチャネルで実行されます。 Media Services は、要求に応じて、ストリームを顧客に配信します。

    Media Services でのライブ ストリームのエンコードは、**プレビュー**になっています。
- オンプレミスのライブ エンコーダーは、マルチ ビットレート **RTMP** または **スムーズ ストリーミング** (Fragmented MP4) をチャネルに送信します。 マルチ ビットレートのスムーズ ストリーミングを出力するライブ エンコーダーとして、Elemental、Envivio、Cisco を使用できます。 Adobe Flash Live、Telestream Wirecast、Tricaster トランスコーダーは、RTMP を出力するライブ エンコーダーです。 取り込んだストリームは、追加の処理なしで**チャネル**を通過します。 ライブ エンコーダーは、ライブ エンコードが有効になっていないチャネルにシングル ビットレート ストリームも送信できますが、これはお勧めしません。 Media Services は、要求に応じて、ストリームを顧客に配信します。


### Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する

次の図は、Media Services による Live Encoding の実行が有効なチャネルのライブ ストリーミング ワークフローに関連する AMS プラットフォームの主要な部分を示しています。

![ライブ ワークフロー][live-overview1]

詳細については、次を参照してください。 [Azure Media Services によるライブ エンコードの実行を有効になっているチャネルを操作](media-services-manage-live-encoder-enabled-channels.md)します。


### オンプレミスのエンコーダーからマルチ ビットレートのライブ ストリームを受信するチャネルを操作する

次の図は、ライブ ストリーミング ワークフローに関連する AMS プラットフォームの主要な部分を示しています。

![ライブ ワークフロー][live-overview2]

詳細については、次を参照してください [その受信マルチ チャネルを操作の内部設置型のエンコーダーからビットレートのライブ ストリーム。
(メディア-サービスの管理のチャネルの overview.md)。

## コンテンツの使用

Azure Media Services には、ほとんどのプラットフォーム (iOS デバイス、Android デバイス、Windows、Windows Phone、Xbox、セットトップ ボックスなど) 向けのリッチで動的なクライアント再生アプリケーションの作成に必要なツールが用意されています。 次のトピックには、Media Services からのストリーミング メディアを使用する独自のクライアント アプリケーションの開発に使用できる、SDK とプレーヤー フレームワークへのリンクがあります。

[ビデオ プレーヤー アプリケーションの開発](media-services-develop-video-players.md)

## Azure CDN を有効にする

Media Services では、Azure CDN との統合をサポートしています。 Azure CDN を有効にする方法については、次を参照してください。 [Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-manage-origins.md#enable_cdn)します。

## Media Services アカウントのスケーリング

**Media Services** は、アカウントのプロビジョニングに使用する **[ストリーミング占有ユニット]** と **[エンコード占有ユニット]** の数を指定することで規模を設定できます。

ストレージ アカウントを追加して、Media Services アカウントの規模を設定することもできます。 各ストレージ アカウントの上限は 500 TB (テラバイト) です。 既定の上限を超えるストレージ容量を設定するために、複数のストレージ アカウントを単一の Media Services アカウントにアタッチすることを選択できます。

[この](media-services-how-to-scale.md) トピックは関連するトピックにリンクします。

## サポート

[Azure サポート](http://azure.microsoft.com/support/options/) Media Services を含む Azure のサポート オプションを提供します。

## パターンとプラクティスのガイダンス

[パターンとプラクティスのガイダンス](https://wamsg.codeplex.com/)
[オンライン ドキュメント](https://msdn.microsoft.com/library/dn735912.aspx)
[ダウンロード可能な電子ブック](https://www.microsoft.com/download/details.aspx?id=42629)


## フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## サービス レベル アグリーメント (SLA)

- Media Services Encoding では、REST API トランザクションの可用性が 99.9% 保証されます。
- ストリーミングでは、1 つ以上のストリーミング ユニットが購入された場合、既存のメディア コンテンツに対して 99.9% の可用性で要求が適切に処理されます。
- ライブ チャンネルでは、実行中のチャンネルが 99.9% 以上の時間において外部に接続されることが保証されます。
- Content Protection では、99.9% 以上の時間においてキー要求が適切に処理されることが保証されます。
- インデクサーでは、エンコード予約ユニットの 99.9% の時間においてインデクサー タスクの要求が適切に処理されます。

    詳細については、次を参照してください。 [Microsoft Azure SLA](http://azure.microsoft.com/support/legal/sla/)します。



[overview]: ./media/media-services-overview/media-services-overview.png 
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png 
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png 
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png 

