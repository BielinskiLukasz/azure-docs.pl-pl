<properties 
    pageTitle="車両テレメトリ分析ソリューション プレイブック | Microsoft Azure" 
    description="Cortana Analytics の機能を使用して、車両の状態と運転の習慣に関するリアルタイムの予測的な洞察を得ます。" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="paulettm" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/02/2015" 
    ms.author="bradsev" />



# 車両テレメトリ分析ソリューション プレイブック

この**メニュー**は、このプレイブック内の各章にリンクされています。

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## 概要

スーパー コンピューターをラボからガレージに持ち出しました。 これらの最先端の自動車には、毎秒数百万というイベントを追跡、監視するためのおびただしい数のセンサーが搭載されています。 2020 年までに、これらの車の大半はインターネットに接続されることが予測されます。 この豊富なデータを利用して、クラス最高の安全性、信頼性、そして運転体験を実現することを想像してみてください。 マイクロソフトは Cortana Analytics によりこの想像を現実のものにしました。

Microsoft の Cortana Analytics は完全に管理されたビッグ データで、データをインテリジェントなアクションに変換する高度な分析スイートです。 ここでは、Cortana Analytics 車両テレメトリ分析ソリューション テンプレートを紹介します。 このソリューションでは、車の販売代理店、自動車メーカー、保険会社が Cortana Analytics の機能を使用して、車両の状態や運転の習慣などに関するリアルタイムの予測分析をどのように実現できるかについて説明します。

としてソリューションを実装、 [lambda アーキテクチャ パターン](https://en.wikipedia.org/wiki/Lambda_architecture) の Cortana Analytics platform の潜在的な完全版を示すリアルタイムおよびバッチ処理します。 これは車両テレメトリ シミュレーターを備え、Event Hubs を活用して数百万におよぶシミュレートされた車両テレメトリ イベントを Azure に取り込み、Stream Analytics を使用して車両の状態に関するリアルタイム情報を取得し、そのデータを長期的なストレージで維持してより高度な一括分析を実現します。 リアルタイムのバッチ処理での異常検出に Machine Learning を活用して予測分析を行います。 HDInsight を活用して大規模にデータを変換し、Data Factory がオーケストレーション、スケジュール設定、リソース管理、およびバッチ処理のパイプラインの監視を処理します。 最後に、Power BI がこのソリューションに機能豊富なダッシュボードを提供し、リアルタイムなデータおよび予測分析を視覚化します。

## アーキテクチャ

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*図 1-車両テレメトリ分析ソリューションのアーキテクチャ*

このソリューションは次の **Cortana Analytics コンポーネント**を含み、エンド ツー エンドの統合を説明します。


- **Event Hubs** 数百万の車両テレメトリ イベントを Azure に取り込みます。
- **Stream Analytics** 車両の状態に関するリアルタイムの洞察を得て、より高度な一括分析のために長期的なストレージにそのデータを保持します。
- **Machine Learning** リアルタイムのバッチ処理での異常検出にこれを活用して予測分析を行います。
- **HDInsight** 大量のデータを変換します。
- **Data Factory** オーケストレーション、スケジュール設定、リソース管理、およびバッチ処理のパイプラインの監視を処理します。
- **Power BI** このソリューションに機能豊富なダッシュボードを提供し、リアルタイムなデータおよび予測分析を視覚化します。

このソリューションでは、2 つの異なる **データ ソース**にアクセスします。

- **シミュレートされた車両の信号と診断**: 車両テレマティックス シミュレーターは診断情報、車両の状態に対応する信号、特定の時間における運転のパターンを出力します。
- **車両カタログ**: モデルのマッピングに対する VIN を含む参照データセット。





