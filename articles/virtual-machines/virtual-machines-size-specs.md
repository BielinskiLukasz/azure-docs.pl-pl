<properties
 pageTitle="仮想マシンのサイズ | Microsoft Azure"
 description="仮想マシンのさまざまなサイズとその容量を一覧を示します。"
 services="virtual-machines"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>

<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="12/11/2015"
 ms.author="cynthn"/>

# 仮想マシンのサイズ

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 概要

この記事では、アプリとワークロードの実行に使用できる仮想マシン ベースのコンピューティング リソースの利用可能なサイズとオプションについて説明します。  また、これらのリソースの使用を計画するときに注意する必要のあるデプロイの考慮事項も示します。 さまざまなサイズの料金については、次を参照してください。 [Virtual Machines 料金](https://azure.microsoft.com/pricing/details/virtual-machines/)します。

Azure Vm で一般的な制限を表示する、次を参照してください。 [Azure サブスクリプションとサービスの制限、クォータ、および制約](../azure-subscription-service-limits.md)します。

標準のサイズは、複数のシリーズ (A、D、DS、G、および GS) で構成されます。 これらのサイズに関する考慮事項は次のとおりです。

*   D シリーズ VM は、より高いコンピューティング能力と一時ディスクのパフォーマンスを必要とするアプリケーションを実行するように設計されています。 D シリーズ VM は、より高速なプロセッサ、より高いメモリ対コア比、一時ディスク用ソリッド ステート ドライブ (SSD) を提供します。 詳細については、Azure ブログのお知らせを参照してください。 [新しい D シリーズ仮想マシンのサイズ](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)します。

*   オリジナルの D シリーズに続く Dv2 シリーズには、より強力な CPU が備わっています。 Dv2 シリーズの CPU は D シリーズの CPU よりも、およそ 35% 高速です。 これは最新世代の 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) プロセッサに基づいており、Intel Turbo Boost Technology 2.0 を使用することで、最大 3.2 GHz まで実現できます。 Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。

    Dv2 シリーズ地域可用性については、このスケジュールに基づきます。
        10 月の 15: 米国東部 2、米国中部、米国中北部中央、米国西部
        11 月の 15: 米国東部、北ヨーロッパ、西ヨーロッパ
        年 1 月 ' 16: 米国中南、APAC 東、APAC 南東部、東日本、日本西部、
                オーストラリア東部、オーストラリア南東部、ブラジル南部


*   G シリーズ VM は、最大のサイズと最高のパフォーマンスを提供し、Intel Xeon E5 V3 ファミリのプロセッサが搭載されたホスト上で実行されます。

*   DS シリーズと GS シリーズの VM では、Premium Storage を使用できます。これは、高負荷の I/O ワークロードのための、高パフォーマンスで待機時間の少ない記憶域を提供します。 これらの VM では、仮想マシンのディスクをホストするためのソリッド ステート ドライブ (SSD) が使用されており、ローカル SSD ディスク キャッシュも提供されます。 Premium Storage は特定のリージョンで使用できます。 詳細については、「 [Premium Storage: Azure の仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage-premium-storage-preview-portal.md)します。

仮想マシンのサイズは価格に影響します。 また、サイズは仮想マシンの処理、メモリ、記憶容量にも影響します。 Storage のコストは、ストレージ アカウントで使用されるページに基づいて個別に計算されます。 詳細については、「 [Virtual Machines 料金](http://azure.microsoft.com/pricing/details/virtual-machines/) と [Azure Storage 料金](http://azure.microsoft.com/pricing/details/storage/)します。 VMss の記憶域に関する詳細については、次を参照してください。 [ディスクとバーチャル マシンの Vhd について ](virtual-machines-disks-vhds.md)します。

サイズを決定する際に役立つ考慮事項は次のとおりです。


*   Azure データ センターの物理ホストの一部サポートしていません A5 ~ などの大きな仮想マシン サイズ A11\。 その結果、エラー メッセージが表示 **バーチャル マシンの構成に失敗しました <machine name>** または **を仮想マシンを作成できませんでした <machine name>** と新しいサイズに既存のバーチャル マシンのサイズを変更する;、2013 年 4 月 16 日は以前に作成された仮想ネットワークでの新しいバーチャル マシンの作成または新しいバーチャル マシンを追加すると、既存のクラウド サービスです。 参照してください  [エラー:「仮想マシンを構成できませんでした」](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) 上、」を参照してください。  

*   A8/A10 と A9/A11 の仮想マシンのサイズは、同じ容量です。 A8 と A9 の仮想マシンのインスタンスには、仮想マシン間の高速通信用のリモート ダイレクト メモリ アクセス (RDMA) ネットワークに接続された追加のネットワーク アダプターが含まれています。 A8 と A9 のインスタンスは、実行中にノード間で持続的で待ち時間の少ない通信を必要とする高パフォーマンス コンピューティング アプリケーション (Message Passing Interface (MPI) を使用するアプリケーションなど) 向けに設計されています。 A10 と A11 の仮想マシン インスタンスには、追加のネットワーク アダプターが含まれていません。 A10 と A11 のインスタンスは、ノード間で一定した待ち時間の少ない通信を必要としない高パフォーマンス コンピューティング アプリケーション (パラメトリックまたは Embarrassingly Parallel アプリケーションとも呼ばれる) 向けに設計されています。

*   Dv2 シリーズ、D シリーズ、G シリーズ、DS/GS の対応するは、高速な Cpu を必要とするアプリケーションに最適なより優れたローカル ディスクのパフォーマンス、またはより高いメモリ要求されています。  多数のエンタープライズ レベルのアプリケーションに、強力な組み合わせで対処します。





## サイズ一覧表

次の表に、サイズとそのサイズで提供される容量を示します。

>[AZURE.NOTE] 記憶域の容量が 1024年を使用して表される ^ GB の測定単位として 3 バイトです。 これはギガバイト、または 2 進数定義とも呼ばれます。 使用する進法が異なるサイズを比較する場合、2 進数のサイズが 10 進数よりも小さく見える場合でも、1024^3 は 1000^3 より大きいため、特定のサイズ (1 GB など) では、2 進法の方が 10 進法よりも提供される容量が大きいことに注意してください。



## Standard レベル: A シリーズ

クラシック デプロイ モデルでは、一部の VM サイズが Powershell および CLI で若干異なります。

* Standard_A0: ExtraSmall 
* Standard_A1: Small
* Standard_A2: Medium
* Standard_A3: Large
* Standard_A4: ExtraLarge

<br>

|サイズ |CPU コア数|メモリ|NIC (最大)|最大 ディスク サイズ|最大 データ ディスク (各 1023 GB)|最大 IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard_A0\ExtraSmall |1|768 MB|1| 一時ディスク = 20 GB |1|1 x 500|
|Standard_A1\Small|1|1.75 GB|1|一時ディスク = 70 GB |2|2 x 500|
|Standard_A2\Medium|2|3.5 GB|1|一時ディスク = 135 GB |4|4 x 500|
|Standard_A3\Large|4|7 GB|2|一時ディスク = 285 GB |8|8 x 500|
|Standard_A4\ExtraLarge|8|14 GB|4|一時ディスク = 605 GB |16|16 x 500|
|Standard_A5|2|14 GB|1|一時ディスク = 135 GB |4|4 x 500|
|Standard_A6|4|28 GB|2|一時ディスク = 285 GB |8|8 x 500|
|Standard_A7|8|56 GB|4|一時ディスク = 605 GB |16|16 x 500|
|Standard_A8|8|56 GB|2| 一時 = 382 GB 注: 情報とこのサイズの使用に関する注意事項については、次を参照してください。 [A8、A9、A10、A11 コンピューティング集中型インスタンス](http://go.microsoft.com/fwlink/p/?linkid=328042)します。 |16|16 x 500|
|Standard_A9|16|112 GB|4| 一時 = 382 GB 注: 情報とこのサイズの使用に関する注意事項については、次を参照してください。 [A8、A9、A10、A11 コンピューティング集中型インスタンス](http://go.microsoft.com/fwlink/p/?linkid=328042)します。 |16|16 x 500|
|Standard_A10|8|56 GB|2| 一時 = 382 GB 注: 情報とこのサイズの使用に関する注意事項については、次を参照してください。 [A8、A9、A10、A11 コンピューティング集中型インスタンス](http://go.microsoft.com/fwlink/p/?linkid=328042)します。 |16|16 x 500|
|Standard_A11|16|112 GB|4| 一時 = 382 GB 注: 情報とこのサイズの使用に関する注意事項については、次を参照してください。 [A8、A9、A10、A11 コンピューティング集中型インスタンス](http://go.microsoft.com/fwlink/p/?linkid=328042)します。 |16|16 x 500|

## Standard レベル: D シリーズ

|サイズ |CPU コア数|メモリ|NIC (最大)|最大 ディスク サイズ|最大 データ ディスク (各 1023 GB)|最大 IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard_D1 |1|3.5 GB|1|一時的 (SSD) = 50 GB |2|2 x 500|
|Standard_D2 |2|7 GB|2|一時的 (SSD) = 100 GB |4|4 x 500|
|Standard_D3 |4|14 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard_D4 |8|28 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard_D11 |2|14 GB|2|一時的 (SSD) = 100 GB |4|4 x 500|
|Standard_D12 |4|28 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard_D13 |8|56 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard_D14 |16|112 GB|8|一時的 (SSD) = 800 GB |32|32 x 500|

## Standard レベル: Dv2 シリーズ

|サイズ |CPU コア数|メモリ|NIC (最大)|最大 ディスク サイズ|最大 データ ディスク (各 1023 GB)|最大 IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard_D1_v2 |1|3.5 GB|1|一時的 (SSD) = 50 GB |2|2 x 500|
|Standard_D2_v2 |2|7 GB|2|一時的 (SSD) = 100 GB |4|4 x 500|
|Standard_D3_v2 |4|14 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard_D4_v2 |8|28 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard_D5_v2 |16|56 GB|8|一時的 (SSD) = 800 GB |32|32 x 500|
|Standard_D11_v2 |2|14 GB|2|一時的 (SSD) = 100 GB |4|4 x 500|
|Standard_D12_v2 |4|28 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard_D13_v2 |8|56 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard_D14_v2 |16|112 GB|8|一時的 (SSD) = 800 GB |32|32 x 500|

## Standard レベル: DS シリーズ*

|サイズ |CPU コア数|メモリ|NIC (最大)|最大 ディスク サイズ|最大 データ ディスク (各 1023 GB)|キャッシュ サイズ (GB)|最大 ディスク IOPS および amp;帯域幅|
|---|---|---|---|---|---|---|---|
|Standard_DS1 |1|3.5|1|ローカル SSD ディスク = 7 GB |2|43| 3,200 32 MB/秒 |
|Standard_DS2 |2|7|2|ローカル SSD ディスク = 14 GB |4|86| 6,400 64 MB/秒 |
|Standard_DS3 |4|14|4|ローカル SSD ディスク = 28 GB |8|172| 12,800 128 MB/秒 |
|Standard_DS4 |8|28|8|ローカル SSD ディスク = 56 GB |16|344| 25,600 256 MB/秒 |
|Standard_DS11 |2|14|2|ローカル SSD ディスク = 28 GB |4|72| 6,400 64 MB/秒 |
|Standard_DS12 |4|28|4|ローカル SSD ディスク = 56 GB |8|144| 12,800 128 MB/秒 |
|Standard_DS13 |8|56|8|ローカル SSD ディスク = 112 GB |16|288| 25,600 256 MB/秒 |
|Standard_DS14 |16|112|8|ローカル SSD ディスク = 224 GB |32|576| 50,000 512 MB/秒 |

*DS シリーズ VM で可能な 1 秒あたりの入力/出力操作 (IOPS) とスループット (帯域幅) の最大値は、ディスクのサイズによる影響を受けます。 詳細については、「 [Premium Storage: Azure の仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage-premium-storage-preview-portal.md)します。

## Standard レベル: G シリーズ

|サイズ |CPU コア数|メモリ|NIC (最大)|最大 ディスク サイズ|最大 データ ディスク (各 1023 GB)|最大 IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard_G1 |2|28 GB|1|ローカル SSD ディスク = 384 GB |4|4 x 500|
|Standard_G2 |4|56 GB|2|ローカル SSD ディスク = 768 GB |8|8 x 500|
|Standard_G3 |8|112 GB|4|ローカル SSD ディスク = 1,536 GB |16|16 x 500|
|Standard_G4 |16|224 GB|8|ローカル SSD ディスク = 3,072 GB |32|32 x 500|
|Standard_G5 |32|448 GB|8|ローカル SSD ディスク = 6,144 GB |64| 64 x 500 |

## Standard シリーズ: GS シリーズ

|サイズ |CPU コア数|メモリ|NIC (最大)|最大 ディスク サイズ|最大 データ ディスク (各 1023 GB)|キャッシュ サイズ (GB)|最大 ディスク IOPS および amp;帯域幅|
|---|---|---|---|---|---|---|---|
|Standard_GS1|2|28|1|ローカル SSD ディスク = 56 GB |4|264| 5,000 125 MB/秒 |
|Standard_GS2|4|56|2|ローカル SSD ディスク = 112 GB |8|528| 10,000 250 MB/秒 |
|Standard_GS3|8|112|4|ローカル SSD ディスク = 224 GB |16|1056| 20,000 500 MB/秒 |
|Standard_GS4|16|224|8|ローカル SSD ディスク = 448 GB |32|2112| 40,000 1,000 MB/秒 |
|Standard_GS5|32|448|8|ローカル SSD ディスク = 896 GB |64|4224| 80,000 2,000 MB/秒 |


### 関連項目

[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)

[A8、A9、A10、A11 コンピューティング集中型インスタンスについて](virtual-machines-a8-a9-a10-a11-specs.md)



