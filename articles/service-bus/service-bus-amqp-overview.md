<properties 
    pageTitle="Service Bus AMQP の概要 | Microsoft Azure" 
    description="Azure での Advanced Message Queuing Protocol (AMQP) 1.0 の使用について説明します。" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor="mattshel"/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/05/2015" 
    ms.author="sethm"/>



# Service Bus での AMQP 1.0 サポート

Azure Service Bus クラウド サービスと内部設置型の両方 [Service Bus for Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) 、Advanced Message Queueing Protocol (AMQP) 1.0 をサポートします。 AMQP を使用すると、オープンな標準プロトコルを使用したクロス プラットフォームのハイブリッド アプリケーションをビルドできます。 異なる言語とフレームワークを使用して作成され、異なるオペレーティング システムで実行可能であるコンポーネントを使用して、アプリケーションを構築できます。 これらのコンポーネントはすべて Service Bus に接続でき、構造化されたビジネス メッセージを効率よく完全な忠実度でシームレスに交換できます。

## 概要: AMQP 1.0 の紹介とその重要な理由

これまで、メッセージ指向のミドルウェア製品では、クライアント アプリケーションとブローカーの間の通信に独自プロトコルが使用されてきました。 つまり、特定のベンダーのメッセージング ブローカーを選択すると、そのベンダーのライブラリを使用してクライアント アプリケーションをそのブローカーに接続する必要があります。 この結果、アプリケーションを別の製品に移植するには、接続するすべてのアプリケーションをコード変更する必要があるため、そのベンダーへの依存の度合いが高くなります。 

さらに、異なるベンダーのメッセージング ブローカーを接続するのは面倒であり、 通常は、システム間でメッセージを移動し、独自のメッセージ形式間で変換を行うためにアプリケーション レベルのブリッジが必要になります。 これは頻繁に見られる要件です。たとえば、以前の異種システムに新しい統合インターフェイスを提供した場合や、合併後に IT システムを統合した場合などです。

ソフトウェア業界は動きの速いビジネスです。新しいプログラミング言語やアプリケーション フレームワークがときには困惑するほどのペースで導入されます。 同様に、IT システムの要件は時間の経過と共に進化しているため、開発者は最新のプラットフォームの機能を活用しようとします。 また、選択したメッセージング ベンダーがプラットフォームをサポートしていないこともあります。 独自のメッセージング プロトコルのため、他のベンダーが新しいプラットフォームのライブラリを提供することはできません。 このため、メッセージング製品を継続して使用できるようにするには、ゲートウェイまたはブリッジの構築などの方法を使用する必要があります。

AMQP (Advanced Message Queuing Protocol) 1.0 はこのような問題に促されて開発されました。 ほとんどの金融サービス企業と同じようにメッセージ指向ミドルウェアのヘビー ユーザーである JP Morgan Chase で生み出されました。 目的はシンプルです。つまり、さまざまな言語、フレームワーク、オペレーティング システムを使用して構築されたコンポーネント (すべて広範なサプライヤーの最良のコンポーネントを使用) を使用してメッセージ ベースのアプリケーションを作成できるようにする、オープン標準メッセージング プロトコルを作ることです。

## AMQP 1.0 の技術的な特徴

AMQP 1.0 は、堅牢なクロス プラットフォーム メッセージング アプリケーションを作成するために使用できる、効率的で信頼性の高い回線レベルのメッセージング プロトコルです。 このプロトコルには、安全で信頼性の高い効率的なメッセージ転送を二者間で行うメカニズムを定義するという、シンプルな目的があります。 メッセージ自体は、異種混在の送信者と受信者が構造化されたビジネス メッセージを完全な忠実度で交換でき、移植できるデータ表現を使用してエンコード化されます。 最も重要な特徴の概要を次に示します。

*    **効率的な**: AMQP 1.0 は、接続指向のプロトコルを使用してバイナリ エンコード プロトコル命令とビジネス メッセージをそれを介して転送されます。 高度なフロー制御スキームが組み込まれており、ネットワークと接続コンポーネントの利用率が最大限に高められます。 そのため、このプロトコルは効率性、柔軟性、相互運用性のバランスを取るように設計されました。
*    **信頼性の高い**: AMQP 1.0 プロトコルでは、広範な信頼性の保証、ファイア アンド フォーゲット信頼性の高いからと正確に交換されるメッセージの 1 回限りの承認配信します。
*    **柔軟な**: AMQP 1.0 はさまざまなテクノロジをサポートするために使用できる柔軟なプロトコルです。 同じプロトコルをクライアント間通信、クライアント/ブローカー間通信、およびブローカー間通信に使用できます。
*    **ブローカー モデルに依存しない**: AMQP 1.0 の仕様は、ブローカーによって使用されるメッセージング モデルの要件を行いません。 これは、既存のメッセージング ブローカーに AMQP 1.0 のサポートを簡単に追加できることを意味します。

## AMQP 1.0 は Standard (大文字の S で始まる場合)

AMQP 1.0 は国際標準であり、ISO および IEC によって、ISO/IEC 19464:2014 として承認されています。

AMQP 1.0 は、2008 年以降、20 社を超える企業 (テクノロジ サプライヤーとエンド ユーザー企業の両方) で構成される中核的なグループにより開発されています。 その間、ユーザー企業は実際のビジネス要件を提供し、テクノロジ ベンダーはそれらの要件に合わせてプロトコルを進化させてきました。 プロセス全般にわたり、ベンダーはワークショップに参加し、実装間の相互運用性を検証するために協力してきました。

2011 年 10 月、開発作業は構造化情報標準促進協会 (OASIS) 内の専門委員会に移され、2012 年 10 月に OASIS AMQP 1.0 Standard がリリースされました。 この標準の開発中、次の企業が専門委員会に参加しました。

*    **テクノロジ ベンダー**: Axway Software、Huawei Technologies、IIT Software、INETCO Systems、Kaazing、Microsoft、Mitre Corporation、Primeton Technologies、Progress ソフトウェア、Red Hat、SITA、Software AG、Solace Systems、VMware、WSO2、Zenika。
*    **ユーザー企業**: Bank of America、Credit Suisse、Deutsche Boerse、Goldman Sachs、JPMorgan Chase。

オープン標準の利点としては、次のような点がよく挙げられます。

*    ベンダー ロックインの可能性が下がる
*    相互運用性
*    ライブラリとツールを広範に利用できる
*    陳腐化を予防できる
*    知識が豊富なスタッフを利用できる
*    リスクが低く扱いやすい

## AMQP 1.0 と Service Bus

Azure Service Bus で AMQP 1.0 がサポートされるため、仲介型メッセージング機能 (Service Bus キューとトピック発行/サブスクライブ) をさまざまなプラットフォームから効率的なバイナリ プロトコルを使って利用できるようになります。 さらに、さまざまな言語、フレームワーク、およびオペレーティング システムを使って作成されたコンポーネントで構成されたアプリケーションを作成できます。

次の図は、標準の Java Message Service (JMS) API を使用して記述された Linux で実行される Java クライアントと、Windows で実行される .NET クライアントが、AMQP 1.0 を使用して Service Bus 経由でメッセージを交換する、サンプルのデプロイメントを示しています。

![][0]

**図 1: サービス バスと AMQP 1.0 を使用したクロス プラットフォーム メッセージングを示すサンプルの展開シナリオ**

現時点では、次のクライアント ライブラリが Service Bus で機能することがわかっています。

| 言語 | ライブラリ                                                                       |
|----------|-------------------------------------------------------------------------------|
| Java     | Apache Qpid Java Message Service (JMS) クライアント<br/>IIT Software SwiftMQ Java クライアント |
| C        | Apache Qpid Proton-C                                                          |
| PHP      | Apache Qpid Proton-PHP                                                        |
| Python   | Apache Qpid Proton-Python                                                     |
| C#       | AMQP .Net Lite                                                                |

**図 2: AMQP 1.0 クライアント ライブラリの表**

## 概要

*    AMQP 1.0 は、プラットフォーム間共通のハイブリッド アプリケーションを構築するために使用できる、信頼性の高いオープンなメッセージング プロトコルです。 AMQP 1.0 は OASIS の標準です。
*    AMQP 1.0 サポートは、Azure Service Bus と Windows Server の Service Bus (Service Bus 1.1) で利用できるようになりました。 料金は、既存のプロトコルと同じです。

## 次のステップ

さらに詳しい情報については、 次のリンク先を参照してください。

- [AMQP で .NET から Service Bus を使用する]
- [AMQP で Java から Service Bus を使用する]
- [AMQP で Python から Service Bus を使用する]
- [AMQP で PHP から Service Bus を使用する]
- [Azure Linux VM に Apache Qpid Proton-C をインストールする]
- [Windows Server 用 Service Bus の AMQP]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Using Service Bus from .NET with AMQP]: service-bus-amqp-dotnet.md
[Using Service Bus from Java with AMQP]: service-bus-amqp-java.md
[Using Service Bus from Python with AMQP]: service-bus-amqp-python.md
[Using Service Bus from PHP with AMQP]: service-bus-amqp-php.md
[Installing Apache Qpid Proton-C on an Azure Linux VM]: service-bus-amqp-apache.md
[AMQP in Service Bus for Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
