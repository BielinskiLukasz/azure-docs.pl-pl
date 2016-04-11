<properties
 pageTitle="クラウド サービスのサイズ"
 description="Azure のクラウド サービスの Web ロールと worker ロールのさまざまなサイズを一覧表示します。"
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/14/2015"
 ms.author="adegeo"/>

# Cloud Services のサイズ

このトピックでは、クラウド サービスのロール インスタンス (Web ロールと worker ロール) で使用できるサイズとオプションについて説明します。 また、これらのリソースの使用を計画するときに注意するデプロイメントに関する考慮事項も示します。

Azure Virtual Machines と Cloud Services は、Azure が提供する数種類のコンピューティング リソースの中の 2 つです。 詳細については、次を参照してください。 [をホストしているオプションが提供するコンピューティング Azure](fundamentals-application-models.md)します。

> [AZURE.NOTE]関連する Azure の制限を表示するには、次を参照してください [Azure サブスクリプションとサービスの制限、クォータ、および制約。](../azure-subscription-service-limits.md)

## Web ロールと worker ロールのインスタンスのサイズ

サイズを決定する際に役立つ考慮事項は次のとおりです。

* D シリーズ VM インスタンスは、より高いコンピューティング能力と一時ディスクのパフォーマンスを必要とするアプリケーションを実行するように設計されています。 D シリーズ VM は、より高速なプロセッサ、より高いメモリ対コア比、一時ディスク用ソリッド ステート ドライブ (SSD) を提供します。 詳細については、Azure ブログのお知らせを参照してください。 [新しい D シリーズ仮想マシンのサイズ](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)します。  

*   オリジナルの D シリーズに続く Dv2 シリーズには、より強力な CPU が備わっています。 Dv2 シリーズの CPU は D シリーズの CPU よりも、およそ 35% 高速です。 これは最新世代の 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) プロセッサに基づいており、Intel Turbo Boost Technology 2.0 を使用することで、最大 3.2 GHz まで実現できます。 Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。

    Dv2 シリーズ地域可用性については、このスケジュールに基づきます。
        10 月の 15: 米国東部 2、米国中部、米国中北部中央、米国西部
        11 月の 15: 米国東部、北ヨーロッパ、西ヨーロッパ
        年 1 月 ' 16: 米国中南、APAC 東、APAC 南東部、東日本、日本西部、
                オーストラリア東部、オーストラリア南東部、ブラジル南部

* Web ロールと worker ロールは、システム要件により Azure Virtual Machines よりも多くの一時ディスク領域が必要です。 システム ファイルは、Windows ページファイルに 4 GB、Windows ダンプ ファイルに 2 GB の領域を予約します。  

* OS ディスクには Windows ゲスト OS が含まれており、Program Files フォルダー (別のディスクを指定していない場合はスタートアップ タスクで実行されたインストールを含む)、レジストリの変更、System32 フォルダー、.NET Framework も含まれています。  

* ローカル リソース ディスクには、Azure ログと構成ファイル、Azure 診断 (IIS ログを含む)、ユーザーが定義したローカル ストレージ リソースが含まれています。  

* アプリ (アプリケーション) ディスクには、.cspkg ファイルが抽出される場所で、Web サイト、バイナリ、ロール ホスト プロセス、スタートアップ タスク、web.config などが含まれています。  

* A8/A10 と A9/A11 の仮想マシンのサイズは、同じ容量です。 A8 と A9 の仮想マシンのインスタンスには、仮想マシン間の高速通信用のリモート ダイレクト メモリ アクセス (RDMA) ネットワークに接続された追加のネットワーク アダプターが含まれています。 A8 と A9 のインスタンスは、実行中にノード間で持続的で待ち時間の少ない通信を必要とする高パフォーマンス コンピューティング アプリケーション (Message Passing Interface (MPI) を使用するアプリケーションなど) 向けに設計されています。 A10 と A11 の仮想マシン インスタンスには、追加のネットワーク アダプターが含まれていません。 A10 と A11 のインスタンスは、ノード間で一定した待ち時間の少ない通信を必要としない高パフォーマンス コンピューティング アプリケーション (パラメトリックまたは Embarrassingly Parallel アプリケーションとも呼ばれる) 向けに設計されています。  

|サイズ|CPU<br>コア|メモリ|ディスク サイズ|
|---|---|---|---|
|ExtraSmall|1|768 MB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 15384 MB<br/>アプリ = 約 1.5 GB|
|Small|1|1.75 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 225304 MB<br/>アプリ = 約 1.5 GB|
|中|2|3.5 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 496664 MB<br/>アプリ = 約 1.5 GB|
|Large|4|7 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 1018904 MB<br/>アプリ = 約 1.5 GB|
|ExtraLarge|8|14 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 2083864 MB<br/>アプリ = 約 1.5 GB|
|A5|2|14 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 496664 MB<br/>アプリ = 約 1.5 GB|
|A6|4|28 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 1018904 MB<br/>アプリ = 約 1.5 GB|
|A7|8|56 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 2083864 MB<br/>アプリ = 約 1.5 GB
|A8|8|56 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 1856172 MB<br/>アプリ = 約 1.5 GB<blockquote> 注: このサイズの使用に関する考慮事項とについてを参照してください <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8、A9、A10、A11 コンピューティング集中型インスタンスおよび</a>です。</blockquote>|
|A9|16|112 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 1856172 MB<br/>アプリ = 約 1.5 GB<blockquote> 注: このサイズの使用に関する考慮事項とについてを参照してください <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8、A9、A10、A11 コンピューティング集中型インスタンスおよび</a>です。</blockquote>|
|A10|8|56 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 1856172 MB<br/>アプリ = 約 1.5 GB<blockquote> 注: このサイズの使用に関する考慮事項とについてを参照してください <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8、A9、A10、A11 コンピューティング集中型インスタンスおよび</a>です。</blockquote>|
|A11|16|112 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 1856172 MB<br/>アプリ = 約 1.5 GB<blockquote> 注: このサイズの使用に関する考慮事項とについてを参照してください <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8、A9、A10、A11 コンピューティング集中型インスタンスおよび</a>です。</blockquote>|
|Standard_D1|1|3.5 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 46104 MB<br/>アプリ = 約 1.5 GB|
|Standard_D2|2|7 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 97304 MB<br/>アプリ = 約 1.5 GB|
|Standard_D3|4|14 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 199704 MB<br/>アプリ = 約 1.5 GB|
|Standard_D4|8|28 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 404504 MB<br/>アプリ = 約 1.5 GB|
|Standard_D11|2|14 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 97304 MB<br/>アプリ = 約 1.5 GB|
|Standard_D12|4|28 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 199704 MB<br/>アプリ = 約 1.5 GB|
|Standard_D13|8|56 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 404504 MB<br/>アプリ = 約 1.5 GB|
|Standard_D14|16|112 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 814104 MB<br/>アプリ = 約 1.5 GB|
|Standard_D1_v2|1|3.5 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 46104 MB<br/>アプリ = 約 1.5 GB|
|Standard_D2_v2|2|7 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 97304 MB<br/>アプリ = 約 1.5 GB|
|Standard_D3_v2|4|14 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 199704 MB<br/>アプリ = 約 1.5 GB|
|Standard_D4_v2|8|28 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 404504 MB<br/>アプリ = 約 1.5 GB|
|Standard_D5_v2|16|56 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 814104 MB<br/>アプリ = 約 1.5 GB|
|Standard_D11_v2|2|14 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 97304 MB<br/>アプリ = 約 1.5 GB|
|Standard_D12_v2|4|28 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 199704 MB<br/>アプリ = 約 1.5 GB|
|Standard_D13_v2|8|56 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 404504 MB<br/>アプリ = 約 1.5 GB|
|Standard_D14_v2|16|112 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 814104 MB<br/>アプリ = 約 1.5 GB|
## Cloud Services のサイズの構成

ロール インスタンスの仮想マシンのサイズを、サービス定義ファイルに記述されているサービス モデルの一部として指定できます。 ロールのサイズによって、CPU コアの数、メモリ容量、および実行中のインスタンスに割り当てられるローカル ファイル システムのサイズが決まります。 ロールのサイズは、アプリケーションのリソース要件に基づいて選択します。

Web ロール インスタンスのロール サイズを Small に設定する例を次に示します。


    <WebRole name="WebRole1" vmsize="Small">
    …
    </WebRole>

指定したローカル リソース サイズが上表の最大ローカル リソース サイズに等しいかそれよりも小さいことを確認します。
## 次のステップ

[Azure のクラウド サービスのセットアップ](https://msdn.microsoft.com/library/hh124108)  


