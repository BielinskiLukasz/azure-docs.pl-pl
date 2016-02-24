<properties 
    pageTitle="顧客へのコンテンツの配信に関する概要" 
    description="このトピックでは、Azure Media Services を使用したコンテンツの配信に関する概要を説明します。" 
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
    ms.date="09/27/2015" 
    ms.author="juliako"/>


#顧客へのコンテンツの配信に関する概要

##概要

コンテンツ (ストリーミング ライブ イベントまたはビデオ オン デマンド) を顧客に配信する場合、その目標は、異なるネットワーク条件におけるさまざまなデバイスに高品質のビデオを配信することにあります。 

この目標を実現するには:

- ストリームをマルチビットレート (アダプティブ ビットレート) ビデオ ストリームにエンコードします (これにより品質とネットワーク条件に対応)。 
- Media Services を使用して [Dynamic Packaging](media-services-dynamic-packaging-overview.md) に動的に再パッケージ、ストリーム化 (これは対処さまざまなデバイスでのストリーミング) のさまざまなプロトコルです。 Media Services で配信がサポートされるアダプティブ ビットレート ストリーミング テクノロジは、HTTP ライブ ストリーミング (HLS)、Smooth Streaming、MPEG DASH、HDS (Adobe PrimeTime/Access のライセンスが必要) です。

このトピックでは、コンテンツ配信の重要な概念の概要を説明します。


##動的パッケージ

コンテンツの配信には、動的パッケージの使用をお勧めします。 詳細については、次を参照してください。 [動的パッケージング](media-services-dynamic-packaging-overview.md)します。  

動的パッケージを活用するには、コンテンツの配信元となるストリーミング エンドポイントのオンデマンド ストリーミング ユニットを 1 つ以上取得する 必要があります。 詳細については、次を参照してください。 [Media services の規模変更方法](media-services-manage-origins.md#scale_streaming_endpoints)します。

##フィルターと動的マニフェスト

Media Services では、アセットにフィルターを定義できます。 これらのフィルターは、ビデオの 1 つのセクションのみの再生や (ビデオ全体を再生するのではなく)、顧客のデバイスが処理できるオーディオ サブセットとビデオ演奏のみの再生 (資産に関連付けられているすべての演奏ではなく) などを顧客が選択できるようにする、サーバー側のルールです。 このアセットのフィルター処理を通じた **動的マニフェスト**指定したフィルターに基づいてビデオをストリームする顧客のリクエストに応じて作成されます。

詳細については、次を参照してください。 [フィルターと動的マニフェスト](media-services-dynamic-manifest-overview.md)します。

##ロケーター

ストリーミングかダウンロードに使用できる URL を提供するには、まず、ロケーターを作成してアセットを "発行" する必要があります。  ロケーターは、アセットに含まれているファイルにアクセスするためのエントリ ポイントになります。 Media Services では、2 種類のロケーターがサポートされています。 

- **OnDemandOrigin** ロケーターはメディア (たとえば、MPEG DASH、HLS、またはスムーズ ストリーミング) ストリームまたはファイルのプログレッシブ ダウンロードに使用します。
-  **SAS** (アクセス署名) URL ロケーターはメディア ファイルをローカル コンピューターにダウンロードするために使用します。 

 **アクセス ポリシー** (読み取り、書き込み、一覧など) のアクセス許可と、クライアントに特定のアセットへのアクセスができる期間を定義するために使用します。 一覧表示のアクセス許可 (AccessPermissions.List) は、OrDemandOrigin ロケーターを作成するときには使用しないでください。

ロケーターには、有効期限があります。 ポータルを使用して資産を発行すると、有効期限が 100 年のロケーターが作成されます。 

>[AZURE.NOTE] 2015 年 3 月前にロケーターを作成するポータルを使用する場合は、2 年間有効期限の日付がロケーターが作成します。  

ロケーターの有効期限を更新する [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) または [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) Api です。 SAS ロケーターの有効期限を更新すると、URL が変更されることにご注意ください。 
 
ロケーターは、ユーザーごとのアクセス制御を管理するためのものではありません。 個々のユーザーに異なるアクセス権限を付与するには、デジタル著作権管理 (DRM) ソリューションを使用します。 詳細については、次を参照してください。 [メディアの保護](http://msdn.microsoft.com/library/azure/dn282272.aspx)します。

ロケーターを作成する際、Azure Storage に必要な記憶域や伝達プロセスの関係上 30 秒の遅延が生じる場合があります。


##アダプティブ ストリーミング 

アダプティブ ビットレート テクノロジでは、ビデオ再生アプリケージョンでネットワークの状態を特定し、複数のビットレートの中から選択できます。 ネットワーク通信のパフォーマンスが低下した場合、クライアントはそれより低いビットレートを選択して、より低い画質レベルでビデオの再生をプレーヤーで継続できます。 ネットワークの状態が改善したら、顧客はより高画質なより高いビットレートに切り替えることができます。 Azure Media Services でサポートされるアダプティブ ビットレート テクノロジは、HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH、HDS です。

ユーザーにストリーミング URL を提供するには、最初に OnDemandOrigin ロケーターを作成する必要があります。 ロケーターを作成すると、ストリーミングするコンテンツが含まれているアセットの基本パスが提供されます。 ただし、このコンテンツをストリーミングするためには、このパスをさらに変更する必要があります。 ストリーミング マニフェスト ファイルの完全な URL を構築するには、ロケーターの Path の値とマニフェスト (filename.ism) ファイルの名前を連結する必要があります。 その後、/Manifest と適切な形式 (必要な場合) をロケーターのパスに付加します。 

>[AZURE.NOTE]SSL 接続経由でコンテンツをストリーミングすることもできます。 そのためには、ストリーミング URL の先頭が HTTPS になっていることをご確認ください。 

SSL 経由でのストリーミングを実行できるのは、コンテンツの配信元となるストリーミング エンドポイントが 2014 年 9 月 10 日より後に作成されている場合のみです。 ストリーミング URL の基になるストリーミング エンドポイントの作成日が 9 月 10 日より後である場合、URL に "streaming.mediaservices.windows.net" (新形式) が含まれています。 "origin.mediaservices.windows.net" (旧形式) を含んだストリーミング URL では、SSL がサポートされません。 URL が旧形式である場合、SSL ストリーミングに対応するためには、新しいストリーミング エンドポイントを作成してください。 SSL でコンテンツをストリーミングするには、新しいストリーミング エンドポイントに基づいて作成された URL を使用する必要があります。 


##ストリーミング URL の形式:

**MPEG DASH 形式**

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=mpd-time-csf) 

例

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



**Apple HTTP Live Streaming (HLS) V4 形式**

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=m3u8-aapl)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

**Apple HTTP Live Streaming (HLS) V3 形式**

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=m3u8-aapl-v3)
    
    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

**Apple HTTP Live Streaming (HLS) 形式 (オーディオ専用フィルター付き)**

既定では、HLS マニフェストにはオーディオ専用トラックのみが含まれます。 これは、Apple ストアのセルラー ネットワーク用の認定で必要です。 この場合、クライアントの帯域幅が十分でないか、2G 接続を超えて接続された場合、クライアントはオーディオ専用再生に切り替わります。 これにより進行中のストリーミングはバッファリングなしで維持されますが、ビデオは再生されないという短所があります。 ただし、シナリオによっては、オーディオ専用よりもプレーヤーをバッファリングするほうが好ましい場合があります。 オーディオ専用トラックを削除する場合は、(audio-only=false) を URL に追加することで削除できます。

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

詳細については、次を参照してください。 [この](http://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support) ブログです。


**ストリーミング URL の形式**

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest

例:

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

**Smooth Streaming 2.0 マニフェスト (旧マニフェスト)**

既定では、スムーズ ストリーミングのマニフェスト形式には、繰り返しタグ (r タグ) が含まれています。 ただし、一部のプレーヤーは、r タグをサポートしていません。 このようなクライアントは、r タグを無効にする形式を使用できます。

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

**HDS (Adobe PrimeTime/Access ライセンスのみ)**

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=f4m-f4f)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)


##動的パッケージ

Media Services には動的パッケージ化機能があり、アダプティブ ビットレート MP4 またはスムーズ ストリーミングでエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、スムーズ ストリーミング、HDS) でそのまま配信することができます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。 

動的パッケージ化機能を利用するには、次の作業が必要となります。

- mezzanine (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードまたはトランスコードする (エンコーディングの手順は後述)。アダプティブ ビットレート MP4 ファイルやアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードする。
- コンテンツに配信するストリーミング エンドポイントの 1 つ以上のオンデマンド ストリーミング ユニットを取得します。 詳細については、次を参照してください。 [スケール、オンデマンド ストリーミングの予約ユニット方法](media-services-manage-origins.md#scale_streaming_endpoints)します。 

動的パッケージ化機能を使用した場合、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて適切な応答を構築して返します。 

動的パッケージ化機能を使用できることに加え、オンデマンド ストリーミング予約ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できます。 既定では、オンデマンド ストリーミングは、サーバー リソース (コンピューティング、送信容量など) を他のユーザーと共有する共有インスタンス モデルとして構成されます。 オンデマンド ストリーミングのスループットを高めるために、オンデマンド ストリーミング占有ユニットの購入をお勧めします。

##プログレッシブ ダウンロード 

プログレッシブ ダウンロードでは、ファイル全体がダウンロードされる前に、メディアの再生を開始できます。 .ism* (ismv, isma, ismt, ismc) ファイルのプログレッシブ ダウンロードはできません。 

コンテンツをプログレッシブ ダウンロードするには、OnDemandOrigin のロケーター型を使用します。 次の例は、OnDemandOrigin のロケーター型に基づいた URL を示しています。

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

次の考慮事項が適用されます。

- プログレッシブ ダウンロードで元のサービスからストリームするには、ストレージで暗号化されたアセットの暗号化を解除する必要があります。


##ダウンロード

クライアント デバイスにコンテンツをダウンロードするには、SAS ロケーターを作成する必要があります。 SAS ロケーターでは、ファイルが配置されている Azure ストレージ コンテナーにアクセスできます。 ダウンロード URL を作成するには、ホストと SAS 署名の間にファイル名を埋め込む必要があります。 

次の例は、SAS ロケーターに基づいている URL を示しています。

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

次の考慮事項が適用されます。

- プログレッシブ ダウンロードで元のサービスからストリームするには、ストレージで暗号化されたアセットの暗号化を解除する必要があります。
- 12 時間以内に完了しないダウンロードは失敗します。



##ストリーミング エンドポイント

A **ストリーミング エンドポイント** ストリーミング サービスや、コンテンツ配信ネットワーク (CDN) に再配布のため、クライアント プレーヤー アプリケーションに直接コンテンツを配信することを表します。 ストリーミング エンドポイント サービスからの送信ストリームには、Media Services アカウントのライブ ストリームやオンデマンド ビデオ アセットがあります。 さらに、ストリーミング占有ユニットを調整することで、ストリーミング エンドポイント サービスの容量を制御し、帯域幅の増化ニーズに対応できます。 運用環境のアプリケーションに、1 つ以上の予約ユニットを割り当てる必要があります。 詳細については、次を参照してください。 [メディア サービスの規模変更方法](media-services-manage-origins.md#scale_streaming_endpoints)します。



##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##関連トピック

[ストレージ キーの展開後に Media Services ロケーターを更新する](media-services-roll-storage-access-keys.md)
 
