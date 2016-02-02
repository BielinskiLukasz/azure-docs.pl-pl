<properties 
   pageTitle="StorSimple のハードウェア コンポーネントの交換 | Microsoft Azure"
   description="StorSimple デバイスの PCM、バッテリ、コントローラー モジュール、EBOD コントローラー、ディスク ドライブ、およびシャーシを安全に交換する方法について説明します。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/31/2015"
   ms.author="alkohli" />


# StorSimple のハードウェア コンポーネントの交換

## 概要

ハードウェア コンポーネントの交換に関するチュートリアルでは、Microsoft Azure StorSimple デバイスのハードウェア コンポーネントと、それらのコンポーネントを取り外して交換するために必要な手順について説明します。 この記事は安全性アイコンについて説明し、詳細なチュートリアルへの参照情報や、交換可能なコンポーネントの一覧を掲載しています。
>[AZURE.IMPORTANT] 削除するか、StorSimple のコンポーネントを交換する前に、確認することを確認します、 [安全性アイコンの表記規則](#safety-icon-conventions) およびその他の [安全に関する注意事項](storsimple-safety.md)します。

### 安全性アイコンの表記規則

次の表で、これらのチュートリアルで使用される安全性アイコンについて説明します。 デバイスのコンポーネントを取り外して交換する手順を進めるときに、これらの安全性アイコンに細心の注意を払ってください。

| アイコン| テキスト| 追加情報|
|:---- |:---- |:-----------|
| ![警告のアイコン](./media/storsimple-hardware-component-replacement/Warning.png)| **危険!**| 回避しなければ、死亡事故または重大な負傷を招く危険な状況を示します。この記号の文字は、最も重大な状況の場合にのみ使用されます。|
| ![警告のアイコン](./media/storsimple-hardware-component-replacement/Warning.png)| **警告!**| 回避しなければ、死亡事故または重大な負傷を招く可能性のある危険な状況を示します。|
| ![注意アイコン](./media/storsimple-hardware-component-replacement/Caution.png)| **注意!**| 回避しなければ、軽度または中程度の負傷を招く可能性のある危険な状況を示します。|
| ![注意事項アイコン](./media/storsimple-hardware-component-replacement/NoticeIcon.png)| **注意事項:**| 重要だが、危険に関連するものではない情報を示します。|
| ![感電の危険アイコン](./media/storsimple-hardware-component-replacement/Electric.png)| **感電の危険**| 電圧が高いことを示します。|
| ![ヘビー ウェイト アイコン](./media/storsimple-hardware-component-replacement/Weight.png)| **ヘビー ウェイト**| |
| ![ユーザーによる保守不可能な部品アイコン](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png)| **ユーザーによる保守不可能な部品**| 適切な訓練を受けている場合を除き、触らないでください。|
| ![指示確認アイコン](./media/storsimple-hardware-component-replacement/ReadInstructions.png)| **最初にすべての指示を確認してください**| |
| ![危険情報アイコン](./media/storsimple-hardware-component-replacement/TipHazard.png)| **危険情報**| |

### 開始する前に

デバイスと、このチュートリアルで使用される安全性アイコンについての情報をよく理解してください。 移動して [安全にインストールして、StorSimple デバイスを動作させる](storsimple-safety.md) の詳細について。 確認して、 [安全に関する注意事項](storsimple-safety.md#handling-precautions) 、StorSimple デバイスを処理する前にします。

コンポーネントを交換する前に、次の情報を考慮してください。

![警告アイコン](./media/storsimple-hardware-component-replacement/Warning.png) ![感電アイコン](./media/storsimple-hardware-component-replacement/Electric.png) **警告!**

- StorSimple デバイスのコンポーネントに触るときは、静電気除去マットまたは帯電防止マットを使用して、身体の静電気を逃がしてください。

- 回路に触れないでください。 回路が露出してる可能性があるコンポーネントに触るときは、用意されているハンドルとガイドを使用してください。

![警告アイコン](./media/storsimple-hardware-component-replacement/Warning.png) ![アイコンが表示](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **に注意してください:**

モジュールを交換するときは、**エンクロージャの後部に空のベイを絶対に残さないでください**。 問題のある部分を取り外す前に、交換用モジュールまたは空のモジュールを用意してください。

## ハードウェア コンポーネントの交換手順

Microsoft Azure StorSimple デバイスは、主エンクロージャまたは EBOD エンクロージャに収納されるさまざまなプラグイン モジュールで構成されています。 8100 は 1 つの主エンクロージャを持つデバイスであり、8600 は 1 つの主エンクロージャと 1 つの EBOD エンクロージャを持つデュアル エンクロージャ デバイスです。

デバイスの主なハードウェア コンポーネントを次の表にまとめて示します。 **交換手順**の欄に示されているリンクをクリックすると、関連付けられているチュートリアルに移動します。

| コンポーネント| 台数| プラグイン モジュール?| 交換手順
|:---------|:--------|:--------------|:---------------------|
| シャーシ| 1| いいえ| [StorSimple デバイスのシャーシを交換してください。](storsimple-chassis-replacement.md)|
| プライマリ コントローラー| 2| はい| [StorSimple デバイスのコント ローラー モジュールを交換します。](storsimple-controller-replacement.md)|
| 764W 電源冷却モジュール (PCM)| 2| はい| [StorSimple デバイスの電源および冷却モジュールを交換します。](storsimple-power-cooling-module-replacement.md)|
| バックアップ バッテリ| 2| あり| [StorSimple デバイスのバックアップ バッテリ モジュールを交換します。](storsimple-battery-replacement.md)|
| ディスク ドライブ| 12| あり| [StorSimple デバイス上のディスク ドライブ交換します。](storsimple-disk-drive-replacement.md)|

**表 1** 主エンクロージャのハードウェア コンポーネント

主エンクロージャと EBOD エンクロージャは、I/O モジュールが異なります。 また、PCM のワット数が異なります。 主エンクロージャの Pcm は 764 W、EBOD エンクロージャは 580 w.主エンクロージャの Pcm には、バックアップ バッテリ モジュールも含まれます。

| コンポーネント| 台数| プラグイン モジュール?| 交換手順
|:---------|:--------|:--------------|:---------------------|
| シャーシ| 1| いいえ| [StorSimple デバイスのシャーシを交換してください。](storsimple-chassis-replacement.md)|
| EBOD コントローラー| 2| はい| [StorSimple デバイスの EBOD コント ローラーを交換します。](storsimple-EBOD-controller-replacement.md)|
| 580W 電源冷却モジュール (PCM)| 2| はい| [StorSimple デバイスの電源および冷却モジュールを交換します。](storsimple-power-cooling-module-replacement.md)|
| ディスク ドライブ| 12| はい| [StorSimple デバイス上のディスク ドライブ交換します。](storsimple-disk-drive-replacement.md)|

**表 2** EBOD エンクロージャのハードウェア コンポーネント

デバイスのプラグイン モジュールは、次の前面図と背面図で強調表示されています。 これらの図を使用して、さまざまなプラグイン モジュールの交換が必要になった場合に、その場所を判別できます。 前面図はディスク ドライブを示し、EBOD エンクロージャと主エンクロージャの背面図はプラグイン モジュールを示しています。

![デバイスのフロントプレーン (ディスク ドライブ付き)](./media/storsimple-hardware-component-replacement/IC741028.png)

**図 1**  デバイスの正面

| ラベル| 説明|
|:----|:----------|
| 0 ～ 11| ディスク ドライブ (合計 12 台)|

主エンクロージャと EBOD エンクロージャの両方に、ドライブ キャリア モジュールがあります。 シャーシには、12 3.5"ディスク ドライブ、3 × 4 に配置されたが格納されています。

![デバイスの主エンクロージャ モジュールのバックプレーン](./media/storsimple-hardware-component-replacement/IC740994.png)

**図 2**  主エンクロージャの背面

| ラベル| 説明|
|:----|:----------|
| 1| PCM 0|
| 2| PCM 1|
| 3| コントローラー 0|
| 4| コントローラー 1|

![デバイスの EBOD エンクロージャ プラグイン モジュールのパックプレーン](./media/storsimple-hardware-component-replacement/IC769599.png)

**図 3**  EBOD エンクロージャの背面

| ラベル| 説明|
|:----|:----------|
| 1| PCM 0|
| 2| PCM 1|
| 3| EBOD コントローラー 0|
| 4| EBOD コントローラー 1|

## 現場交換可能ユニット

StorSimple デバイスでは、次の現場交換可能ユニット (FRU) を利用できます。

- シャーシ (統合操作パネルを含む)

- 764 W 電源および冷却モジュール

- 580 W 電源および冷却モジュール

- ドライブ キャリア モジュール付きハード ディスク ドライブ

- コントローラー モジュール

- EBOD コントローラー モジュール

- バックアップ バッテリ モジュール

- ラック取り付け用レール キット

ください [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md) して、これらの交換装置のいずれかで注文します。

## 次のステップ

すべて確認 [安全に関する情報](storsimple-safety.md) StorSimple ハードウェア コンポーネントを交換する前にします。





