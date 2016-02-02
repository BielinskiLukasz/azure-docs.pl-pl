<properties 
    pageTitle="Elemental Live エンコーダーを構成して、単一ビットレートのライブ ストリームを送信する" 
    description="このトピックでは、Elemental Live エンコーダーを構成して、ライブ エンコードで有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako,cenkdin,anilmur" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/15/2015"  
    ms.author="juliako"/>


# Elemental Live エンコーダーを使用して、単一ビットレートのライブ ストリームを送信する

> [AZURE.SELECTOR]
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)


このトピックは、構成する方法を示しています、 [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live) チャンネルを AMS に単一ビットレートのストリームを送信するエンコーダーは、ライブ エンコーディングに対して有効なです。 詳細については、次を参照してください。 [Azure Media Services によるライブ エンコードの実行を有効になっているチャネルを操作](media-services-manage-live-encoder-enabled-channels.md)します。

このチュートリアルでは、Azure Media Service Explorer (AMSE) ツールを使用して、Azure Media Services (AMS) を管理する方法を示します。 このツールは、Windows PC でのみ実行されます。 Mac または Linux の場合は、Azure クラシック ポータルを使用して作成する [チャネル](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) と [プログラム](media-services-portal-creating-live-encoder-enabled-channel#create-and-manage-a-program)します。

## 前提条件

- ライブ イベントを作成するには、Elemental Live Web インターフェイスの使用に関する知識が必要です。
- [Azure Media Services アカウントを作成します。](media-services-create-account.md)
- 1 つ以上のストリーミング ユニットが割り当てられたストリーミング エンドポイントが実行中であることを確認します。 詳細については、を参照してください [Media Services アカウントでストリーミング エンドポイントを管理](media-services-manage-origins.md)。

- 最新バージョンのインストール、 [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) ツールです。
- ツールを起動し、AMS アカウントに接続します。

## ヒント

- 可能な限り、有線のインターネット接続を使用します。
- 帯域幅要件の目安は、ストリーミングのビットレートの 2 倍です。 これは必須の要件ではありませんが、ネットワークの混雑の影響を軽減することができます。
- ソフトウェア ベースのエンコーダーを使用する際は、不要なプログラムを終了します。

## RTP インジェストを使用する Elemental Live

このセクションでは、RTP 経由で単一ビットレートのライブ ストリームを送信する Elemental Live エンコーダーを構成する方法を示します。 詳細については、次を参照してください。 [over RTP MPEG TS ストリーム](media-services-manage-live-encoder-enabled-channels.md#channel)します。

### チャネルの作成

1.  AMSE ツールで、**[Live]** タブに移動して、チャネル領域内を右クリックします。 メニューから **[チャネルの作成]** を選択します。

![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. チャネルの名前を指定します。説明フィールドは省略可能です。 [チャネル設定] の [Live Encoding] オプションで入力プロトコルを **[RTP (MPEG TS)]** に設定して、**[Standard]** を選択します。 それ以外の設定はすべてそのままにしておくことができます。


**[新しいチャネルを今すぐ開始する]** が選択されていることを確認します。

3. クリックして **チャネルの作成**します。
![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

>[AZURE.NOTE] チャネルの開始までに 20 分程度かかることがあります。

チャネルの起動中にすることができます [エンコーダーを構成する](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp)します。
>[AZURE.IMPORTANT] チャネルが準備完了の状態になるとすぐに課金が開始されることに注意してください。 詳細については、次を参照してください。 [チャネルの状態の](media-services-manage-live-encoder-enabled-channels.md#states)します。

### <a id=configure_elemental_rtp></a>基本的なライブ エンコーダーを構成します。

このチュートリアルでは、次の出力設定が使用されます。 このセクションの残りの部分では、構成の手順の詳細について説明します。

**ビデオ**:

- コーデック: H.264
- プロファイル: 高 (レベル 4.0)
- ビットレート: 5000 kbps
- キーフレーム: 2 秒 (60 秒)
- フレーム レート: 30

**オーディオ**:

- コーデック: AAC (LC)
- ビットレート: 192 kbps
- サンプル レート: 44.1 kHz


#### 構成の手順

1. **Elemental Live** Web インターフェイスに移動し、**UDP/TS** ストリーミングのエンコーダーを設定します。

2. 新しいイベントが作成されたら、出力グループまでスクロールし、**UDP/TS** 出力グループを追加します。

3. **[新しいストリーム]** を選択し、**[出力の追加]** をクリックして、新しい出力を作成します。

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental13.png)
    >[AZURE.NOTE] ストリームのエラー時にエンコーダーが再接続できるように、Elemental のイベントのタイムコードを [システム クロック] に設定することをお勧めします。

4. 出力が作成されたので、**[ストリームの追加]** をクリックします。 これで、出力の設定が構成されました。
5. 作成した "Stream 1" まで下にスクロールし、左の **[ビデオ]** タブをクリックして、**[詳細]** 設定セクションを展開します。

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Elemental Live では多様なカスタマイズが可能ですが、AMS へのストリーミングを開始する場合は、次の設定をお勧めします。

    - 解像度: 1280 x 720
    - フレームレート: 30
    - GOP サイズ: 60 フレーム
    - インターレース モード: プログレッシブ
    - ビットレート: 5000000 ビット/秒 (ネットワークの制限に基づいて調整可能)

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

6. チャネルの入力 URL を取得します。

    AMSE ツールに戻り、チャネルの完了状態を確認します。 状態が **[開始中]** から **[実行中]** に変わったら、入力 URL を取得することができます。

    チャネル名を右クリックして、経由でのホバー時に移動して、チャネルが実行されているときに **をクリップボードにコピー入力 URL** し、[* * 主な入力 
    URL**.

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental6.png)

1. この情報を Elemental の **[プライマリ宛先]** フィールドに貼り付けます。 その他のすべての設定は、既定値にしておくことができます。

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    冗長性を高めるには、UDP/TS のストリーミング用に別の [出力] タブを作成することで、セカンダリ入力 URL を使用してこれらの手順を繰り返します。

7. **[作成]** (新しいイベントが作成された場合) または **[更新]** (既存のイベントを編集する場合) をクリックして、エンコーダーの開始に進みます。

>[AZURE.IMPORTANT] Elemental Live Web インターフェイスの **[開始]** をクリックする前に、チャネルが準備可能になっていることを確認する**必要があります**。 
>また、15 分を超えるイベントがある場合を除き、チャネルを準備可能のままにしないでください。

ストリームが 30 秒間実行されたら、AMSE ツールに戻り、再生をテストします。

### 再生をテストする

1. AMSE ツールに移動し、テストするチャネルを右クリックします。 メニューが表示されたら、**[プレビューの再生]** にマウスを合わせ、**[Azure Media Player を使用]** を選択します。

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

ストリームがプレーヤーに表示されている場合は、エンコーダーが AMS に接続するように正しく構成されています。

エラーが表示される場合は、チャネルをリセットして、エンコーダー設定を調整する必要があります。 参照してください、 [トラブルシューティング](media-services-troubleshooting-live-streaming.md) ガイダンスのトピックです。

### プログラムを作成する

1. チャネルの再生が確認されたら、プログラムを作成します。 AMSE ツールの **[Live]** タブで、プログラム領域内を右クリックし、**[新しいプログラムの作成]** を選択します。

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental9.png)

2. 必要に応じてプログラムに名前を付け、**[アーカイブ ウィンドウの長さ]** (既定では 4 時間) を調整します。 ストレージの場所を指定することも、既定値のままにすることもできます。
3. **[プログラムを今すぐ開始]** ボックスを選択します。
4. **[プログラムの作成]** をクリックします。

    注: プログラムの作成は、チャネルの作成ほど時間はかかりません。

5. プログラムが実行されたら、再生を確認するために、プログラムを右クリックして **[プログラムの再生]** に移動し、**[Azure Media Player を使用]** を選択します。
6. 確認したら、プログラムをもう一度クリックし、**[出力 URL をクリップボードにコピー]** を選択します (または、メニューの **[プログラムの情報と設定]** オプションから、この情報を取得します)。

これで、ストリームをプレーヤーに埋め込んだり、ライブ表示のために対象ユーザーに配信したりできるようになりました。

## トラブルシューティング

参照してください、 [トラブルシューティング](media-services-troubleshooting-live-streaming.md) ガイダンスのトピックです。


## Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





