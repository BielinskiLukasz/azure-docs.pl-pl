<properties 
    pageTitle="Machine Learning での線形回帰の使用 | Microsoft Azure" 
    description="Excel と Azure Machine Learning Studio での線形回帰モデルの比較" 
    metaKeywords="" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="paulettm" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/11/2015" 
    ms.author="kbaroni;garye" />

# Azure Machine Learning での線形回帰の使用

> *Kate Baroni* と *Ben Boatman* はエンタープライズ マイクロソフトの Data Insights Center of Excellence ソリューション設計者です。 この記事では、Azure Machine Learning を使用して、クラウド ベースのソリューションに既存の回帰分析スイートを移行する彼らの実験について説明します。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 目標

次の 2 つの目標を念頭においてプロジェクトを開始しました。  

1. 予測分析を使用して、組織の毎月の収益予測の精度を向上させる  
2. Azure ML を使用して、結果の確認、最適化、高速化、拡張を行う  

多くの企業と同様に、組織は毎月の収益予測プロセスを通過しています。 小さなビジネス アナリスト チームが、Machine Learning を使用してプロセスをサポートし、予測の精度を向上させる任務に取り組みました。  チームは、数か月かけて複数のソースからデータを収集し、サービスの売上予測に関連するキーとなる属性を識別する統計分析を使用してデータ属性を実行しました。  次のステップは、Excel のデータで統計的回帰モデルのプロトタイプ作成を開始することでした。  数週間で、現在のフィールドや財務の予測プロセスを上回る Excel の回帰モデルを作成しました。 これが予測結果のベースラインになりました。  


この予測分析を Azure ML に推し進めて、Azure ML の予測パフォーマンスを向上させる方法を見つけるため、次の一歩を踏み出しました。


## 予測パフォーマンスのパリティの成功

最初の優先順位は、Azure ML と Excel の回帰モデル間でパリティを成功させることでした。  同じデータを使用して、トレーニング データとテスト データに均等に分割し、Excel と Azure ML 間で予測パフォーマンスのパリティを成功させようとしました。   最初は失敗しました。 Excel モデルは、Azure ML モデルよりもパフォーマンスが優れていました。   失敗の原因は、Azure ML のベース ツール設定の知識不足でした。 Azure ML 製品チームと協力することで、データ セットに必要な基本設定をよく理解し、2 つのモデル間のパリティに成功しました。  

### Excel で回帰モデルを作成する
Excel 回帰は、Excel Analysis ToolPak で見つかった標準的な線形回帰モデルを使用しました。 

計算しました *平均絶対 % 誤差* し、モデルのパフォーマンス測定に使用します。  Excel を使用して作業モデルに到着するのに 3 か月かかりました。  多くの学習を Azure ML の実験に取り込み、これが最終的に要件の理解に役立ちました。

### Azure Machine Learning で同等の実験を作成する  
次の手順に従って、Azure ML で実験を作成しました。  

1.  データセットを csv ファイルとして Azure ML にアップロードしました (非常に小さいファイル)
2.  新しい実験を作成および使用される、 [プロジェクト列][project-columns] を Excel で使用される同じデータ特徴選択モジュール   
3.  使用される、 [分割][split] モジュール (と *相対表現* モード) が、Excel で実行されたのと同じトレーニング セットにデータを分割するには  
4.  実験が完了、 [線形回帰][linear-regression] モジュール (既定のオプションのみ)、文書化し、結果、Excel の回帰モデルを比較

### 最初の結果を確認する
最初は、Excel モデルが Azure ML モデルよりも明らかにパフォーマンスが優れていました。  

|   |Excel|Azure ML|
|---|:---:|:---:|
|パフォーマンス|   |  |
|<ul style="list-style-type: none;"><li>自由度調整済み決定係数</li></ul>| 0.96 |該当なし|
|<ul style="list-style-type: none;"><li>係数 <br />決定</li></ul>|該当なし|   0.78<br />(低精度)|
|平均絶対誤差 |  $9.5M|  $ 19.4M|
|平均絶対誤差 (%)|   6.03%|  12.2%

Azure ML チームの開発者とデータ サイエンティストによってプロセスと結果が実行されると、有益な情報がすぐに提供されました。  

* 使用すると、 [線形回帰][linear-regression] Azure ML でモジュールは、2 つの方法が提供されます。
    *  オンライン勾配降下: 大規模な問題に適しています。
    *  通常の最小二乗法: これは、線形回帰と聞くと多くの人が考える方法です。 小さなデータセットの場合は、通常の最小二乗法が最適な選択肢です。
*  パフォーマンスを向上させるために、L2 正則化重みパラメーターを調整することを検討します。 既定では 0.001 に設定されていますが、小さなデータ セットでは、パフォーマンスを向上させるために 0.005 に設定しました。    

### 問題の解決
Recommendations を適用した結果、Azure ML で Excel と同等のベースライン パフォーマンスを達成しました。   

|| Excel|Azure ML (初期値)|最小二乗法を使用した Azure ML|
|---|:---:|:---:|:---:|
|ラベル付きの値  |実際の数値|同じ|同じ|
|学習者  |Excel -> データ分析 -> 回帰|線形回帰。|線形回帰|
|学習者のオプション|該当なし|既定値|最小二乗<br />L2 = 0.005|
|データ セット|26 行、3 特徴、1 ラベル。   すべて数値。|同じ|同じ|
|分割: トレーニング|Excel は最初の 18 行でトレーニングを行い、最後の 8 行でテストしました。|同じ|同じ|
|Split: テスト|最後の 8 行に Excel の回帰式を適用しました|同じ|同じ|
|**パフォーマンス**||||
|自由度調整済み決定係数|0.96|該当なし||
|決定係数|該当なし|0.78|0.952049|
|平均絶対誤差 |$9.5M|$ 19.4M|$9.5M|
|平均絶対誤差 (%)|<span style="background-color: 00FF00;"> 6.03%</span>|12.2%|<span style="background-color: 00FF00;"> 6.03%</span>|

さらに、Excel の係数を Azure のトレーニング済みモデルの特徴の重みと十分に比較しました。

||Excel の係数|Azure の特徴の重み|
|---|:---:|:---:|
|切片/偏り|19470209.88|19328500|
|特徴 A|0.832653063|0.834156|
|特徴 B|11071967.08|11007300|
|特徴 C|25383318.09|25140800|

## 次のステップ

Excel 内で Azure ML Web サービスを使用したいと考えました。  ビジネス アナリストは Excel に依存しているため、Excel データの行で Azure ML Web サービスを呼び出し、予測値を Excel に返す方法が必要でした。   

また、Azure ML で使用可能なオプションとアルゴリズムを使用して、モデルを最適化したいと考えました。

### Excel との統合
ソリューションは、トレーニング済みのモデルから Web サービスを作成することで、Azure ML の回帰モデルを運用することでした。  数分以内に、Web サービスを作成し、これを Excel から直接呼び出して予測収益値を返すことができます。    

 *Web サービスのダッシュ ボード* セクションには、ダウンロード可能な Excel ブックが含まれています。  ブックには、Web サービス API とスキーマ情報が埋め込まれて事前に書式設定されています。   クリックすると *Excel ブックのダウンロード*, され、それが開き、ローカル コンピューターに保存することができます。    

![][1]
 
以下に示すように、ブックを開いた状態で、定義済みのパラメーターを青色の [パラメーター] セクションにコピーします。  パラメーターが入力されると、Excel は AzureML Web サービスを呼び出し、予測されたスコア付けラベルが緑色の [予測値] セクションに表示されます。  ブックは、[パラメーター] に入力されたすべての行項目に対して、トレーニング済みのモデルに基づいてパラメーターの予測を作成していきます。   この機能を使用する方法の詳細については、次を参照してください。 [Excel から Azure Machine Learning Web サービスを消費して](machine-learning-consuming-from-excel.md)します。 

![][2]
 
### 最適化と今後の実験
これまで、Excel モデルを使用してベースラインを構築し、Azure ML 線形回帰モデルの最適化を進めてきました。  モジュールを使用して [フィルターに基づく特徴選択][filter-based-feature-selection] 初期データの選択を改善する要素を実現しました 4.6% のパフォーマンス向上を実現平均絶対誤差です。   今後のプロジェクトのために、データ属性に反復処理を行う数週間を節約できる可能性があるこの特徴を使用して、モデリングに使用する正しい特徴セットを検索します。  

次になどのアルゴリズムを含める予定 [ベイズ][bayesian-linear-regression] または [ブースト デシジョン ツリー][boosted-decision-tree-regression] 実験では、パフォーマンスを比較します。    

回帰を試してみる場合に適切なデータセットは、多くの数値属性を持つエネルギー効率回帰のサンプル データセットです。 このデータセットは、ML Studio でサンプル データセットの一部として提供されています。  さまざまな学習モジュールを使用して、暖房負荷または冷房負荷のいずれかを予測できます。  次の表は、さまざまな回帰のパフォーマンスを比較したもので、エネルギー効率データセットに対する、ターゲット変数の冷房負荷の予測を示しています。 

|モデル|平均絶対誤差|二乗平均平方根誤差|相対絶対誤差|相対二乗誤差|決定係数
|---|---|---|---|---|---
|ブースト デシジョン ツリー|0.930113|1.4239|0.106647|0.021662|0.978338
|線形回帰 (勾配降下)|2.035693|2.98006|0.233414|0.094881|0.905119
|ニューラル ネットワーク回帰|1.548195|2.114617|0.177517|0.047774|0.952226
|線形回帰 (通常の最小二乗法)|1.428273|1.984461|0.163767|0.042074|0.957926  

## 重要なポイント 

Excel の回帰と Azure Machine Learning の実験を並行して実行することで、多くのことを学びました。 Excel でベースライン モデルを作成し、Azure ML を使用してモデルを比較 [線形回帰][linear-regression] Azure ML の学習、データの選択とモデルのパフォーマンスを改善する機会を発見しましたを支援します。         

使用するが賢明である発見 [フィルターに基づく特徴選択][filter-based-feature-selection] を今後の予測プロジェクトを促進するためです。  データに特徴選択を適用することで、Azure ML で全体のパフォーマンスがさらに向上したモデルを作成できます。 

予測分析の予測を Azure ML から Excel に体系的に転送する機能を使用することで、さまざまなビジネス ユーザーに正常に結果を提供できる機能が大幅に向上します。     


## リソース
次のリソースは、回帰を使用する際に役立ちます。  

* Excel での回帰。  場合は Excel で回帰をしようとしたことはありません、このチュートリアルが簡単: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* 回帰と予測。  Tyler Chessman は時系列線形回帰の優れた初心者向けの説明を含む Excel で予測する方法を説明するブログ記事を作成しました。 [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
*   通常の最小二乗法の線形回帰: 欠点、問題、注意点  概要と回帰の説明:   [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/
](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ )

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 


