<properties
    pageTitle="Azure Machine Learning FAQ | Microsoft Azure"
    description="Azure Machine Learning の概要: 効率的な予測モデリングのためのクラウド サービスについて、その課金、機能、制限についての疑問点に答える FAQ です。"
    keywords="machine learning introduction,predictive modeling,what is machine learning"
    services="machine-learning"
    documentationCenter=""
    authors="pablissima"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2015"
    ms.author="paulettm"/>


# Azure Machine Learning に関してよく寄せられる質問 (FAQ): 課金、機能、制限、サポート

この FAQ では、Web サービスを使用して予測モデリングとソリューションの運用を可能にするクラウド サービスである Azure Machine Learning についての疑問に答えます。 サービスの課金モデル、機能、制限、サポートなど、その使用に関して多く寄せられる質問を取り上げています。

## 一般的な質問

**Azure Machine Learning とは**

Azure Machine Learning は、クラウドで予測分析ソリューションを作成、テスト、操作、管理するための、十分に管理されたサービスです。 ブラウザーさえあれば、サインアップ、データのアップロード、機械学習の実験をすぐに始めることができます。 ドラッグ アンド ドロップによる予測モデリング、モジュールの大きなパレット、開始用テンプレートのライブラリによって、一般的な機械学習のタスクがシンプルになり、作業時間が短縮されます。 詳細については、次を参照してください。、 [Azure Machine Learning サービスの概要](/services/machine-learning/)します。 主要な用語と概念を説明する機械学習の概要を参照してください。 [Azure Machine Learning の概要](machine-learning-what-is-machine-learning.md)します。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Machine Learning Studio とは**

Machine Learning Studio は、Web ブラウザーからアクセスできるワークベンチ環境です。 Machine Learning Studio は、視覚的な構成のインターフェイスを持つモジュールのパレットをホストし、実験の形式でエンド ツー エンドのデータ技術ワークフローを容易に構築できます。

詳細については、Machine Learning Studio、を参照してください [Machine Learning Studio は](machine-learning-what-is-ml-studio.md)。

**Machine Learning API サービスとは**

Machine Learning API サービスでは、Machine Learning Studio に組み込まれた予測モデルを、拡張性の高い、フォールト トレランスに優れた Web サービスとしてデプロイできます。 Machine Learning API サービスによって作成された Web サービスが REST API です。REST API は、外部アプリケーションと予測分析モデルの間の通信インターフェイスを提供します。

参照してください [Machine Learning web サービスへの接続](machine-learning-connect-to-azure-machine-learning-web-service.md) の詳細。


## 課金に関する質問

**Machine Learning ではどのように請求が行われますか。**

課金と料金情報については、次を参照してください。 [Machine Learning 料金](http://azure.microsoft.com/pricing/details/machine-learning/)します。

**Azure Machine Learning に無料試用版はありますか。**

 Azure の無料試用版にサインアップすると、1 か月間すべての Azure サービスを試すことができます。 Azure 無料評価版の詳細については、次を参照してください。 [Azure 無料評価版 FAQ](/pricing/free-trial-faq/)します。

## Machine Learning Studio に関する質問

### 実験の作成

**実験グラフ用のバージョン管理や Git 統合はありますか。**

いいえ、実験を行うたびにグラフのバージョンが保存されるため、他のユーザーが変更することはできません。

### Machine Learning 用データのインポートとエクスポート

**Machine Learning はどのようなデータ ソースをサポートしていますか。**

データは、ローカル ファイルをデータセットとしてアップロードする方法、またはデータをインポートするリーダー モジュールを使用する方法のいずれかの方法で Machine Learning Studio に読み込むことができます。 ローカル ファイルは、Machine Learning Studio に新しいデータセットを追加することでアップロードできます。 参照してください [Machine Learning Studio にトレーニング データをインポート](machine-learning-import-data.md) をサポートされているファイル形式の詳細について参照してください。


#### <a id="ModuleLimit"></a>大きさは、データ セットされます自分のモジュールのか。

Machine Learning Studio のモジュールは、一般的に、最大 10 GB の高密度数値データのデータセットをサポートしています。 モジュールが 1 つ以上の入力を受け取る場合は、10 GB はすべての入力サイズの合計です。 インジェストの前に、Hive または Azure SQL Database のクエリ経由で、またはカウントによる学習の前処理によって、より大きなデータセットをサンプリングすることもできます。

次の種類のデータは、特徴の正規化の際により大きなデータセットに展開でき、10 GB 未満に制限されています。

- スパース
- カテゴリ
- 文字列
- バイナリ データ

次のモジュールは、10 GB 未満のデータセットに制限されています。

- 推奨モジュール
- SMOTE モジュール
- スクリプト モジュール: R、Python、SQL
- 出力データ サイズが入力データ サイズを超えるモジュール ("結合"、"特徴ハッシュ" など)。
- イテレーションの数が非常に大きい場合のクロス検証、パラメーターの掃引、順序回帰、一対全多クラス。

数 GB を超えるデータセットの場合は、ローカル ファイルから直接アップロードするのではなく、Azure Storage または Azure SQL Database にデータをアップロードするか、HDInsight を使用する必要があります。


#### <a id="UploadLimit"></a>データのアップロードの制限とは

数 GB を超えるデータセットの場合は、ローカル ファイルから直接アップロードするのではなく、Azure Storage または Azure SQL Database にデータをアップロードするか、HDInsight を使用する必要があります。

**Amazon S3 からデータを読み取ることはできますか。**

使用するかどうかは、少量のデータがあり、http URL 経由で公開する、 [リーダー ][reader] モジュールです。 まず Azure ストレージに転送し、使用するデータの大量の [リーダー ][reader] モジュールを実験に取り込みます。


**組み込みイメージの入力機能はありますか。**

[イメージの入力機能について学習できます、 [イメージ リーダー ][image-reader] 参照します。

### モジュール

**探しているアルゴリズム、データ ソース、データ形式、データ変換操作が Azure ML Studio で見つかりません。どうすればいいでしょうか。**

お客様がアクセスできる、 [ユーザー フィードバック フォーラム](http://go.microsoft.com/fwlink/?LinkId=404231) 機能からの要求について調査中です。 探している機能が既に要求されている場合は、その要求に投票を追加できます。 探している機能が存在しない場合は、新しい要求を作成します。 このフォーラムでの要求の状態も参照できます。 この一覧はこまめに追跡され、機能の提供状況は頻繁に更新されます。 さらに、R と Python の組み込みのサポートを使用して、必要に応じてカスタム変換を作成できます。


**既存のコードを ML Studio に取り込むことはできますか。**

はい。ML Studio に既存の R コードを取り込み、それを Azure Machine Learning で提供される学習ツールと同じ実験で実行し、Azure Machine Learning 経由で Web サービスとしてデプロイすることができます。 参照してください [実験の拡張 ](machine-learning-extend-your-experiment-with-r.md)します。

* * のようなものを使用することは、 [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language)  モデルを定義するでしょうか * *。

いいえ。サポートされていません。ただし、カスタムの R と Python コードを使用してモジュールを定義できます。


### データ処理

**実験内で対話的にデータを視覚化する (R の視覚化以上) 機能はありますか。**

モジュールの出力をクリックすると、データの視覚化でき、統計情報を取得することができます。

**ブラウザーで結果やデータをプレビューするときに、行数と列数が制限されているのはなぜですか。**

データがブラウザーに送信されると大きくなる可能性があるため、ML Studio のパフォーマンスが低下しないように、データ サイズが制限されています。 データ全体を表示するには、データや結果をダウンロードし、Excel などのツールを使用することをお勧めします。

### アルゴリズム

**Machine Learning Studio では既存のどの ML アルゴリズムがサポートされていますか。**

Machine Learning Studio は、拡張性の高い強化された意思決定ツリー、ベイズの推薦システム、ディープ ニューラル ネットワーク、Microsoft Research で開発された意思決定のジャングルなど、最先端のアルゴリズムを提供します。 Vowpal Wabbit のような拡張性が高いオープン ソース機械学習パッケージも含まれます。 Machine Learning Studio は、複数クラスと二項分類、回帰、クラスタリングで、機械学習アルゴリズムをサポートします。 完全な一覧を参照してください [機械学習モジュール ][machine-learning-modules]します。

**データを使用するための正しい Machine Learning アルゴリズムを自動的に提案していますか。**

いいえ。ただし、Machine Learning Studio には、各アルゴリズムの結果を比較して問題に合ったアルゴリズムを決定できるさまざまな方法があります。

**提供されているアルゴリズムから 1 つのアルゴリズムを選ぶ際のガイドラインはありますか。**
参照してください [アルゴリズムの選択方法 ](machine-learning-algorithm-choice.md)します。

**提供されているアルゴリズムは、R または Python で記述されていますか。**

いいえ。これらのアルゴリズムは、多くの場合、より高いパフォーマンスを提供するためにコンパイル済みの言語で記述されています。

**アルゴリズムの詳細は提供されていますか。**

ドキュメントでアルゴリズムに関する情報を提供しています。使用するアルゴリズムを最適化するために、チューニング用に提供されているパラメーターについて説明しています。

**オンライン ラーニングの任意のサポートはありますか。**

いいえ。現在はプログラムによる再トレーニングのみがサポートされています。

**組み込みのモジュールを使用してニューラル ネットワーク モデルのレイヤーを視覚化できますか。**

番号

**C# または他の言語で独自のモジュールを作成できますか。**

現在、新しいカスタム モジュールは R でのみ作成できます。

### R モジュール

**Machine Learning Studio で使用できるのはどの R パッケージですか。**

Machine Learning Studio は、現在、400 を超える R パッケージをサポートしています。サポートされるパッケージは常に増加しています。 参照してください [実験の拡張 ](machine-learning-extend-your-experiment-with-r.md) にサポートされる R パッケージの一覧を取得する方法について説明します。 パッケージの名前を提供する、パッケージがこの一覧にない場合は、 [ユーザー フィードバック フォーラム](http://go.microsoft.com/fwlink/?LinkId=404231)します。

**カスタム R モジュールを構築することはできますか。**

はいを参照してください [Azure Machine Learning におけるカスタム R モジュールの作成者](machine-learning-custom-r-modules.md) の詳細。

**R 用の REPL 環境はありますか。**

いいえ。Studio には R 用の REPL 環境はありません。

### Python モジュール

**カスタム Python モジュールを構築することはできますか。**

現在はできませんが、標準の Python モジュールまたはそれらのセットを使用して同じ結果を実現できます。

**Python 用の REPL 環境はありますか。**

Machine Learning Studio では Jupyter Notebooks を使用できます。 詳細については、[概要 Jupyter Notebook Azure ML Studio で] を参照してください (http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)

## Web サービス

### モデルをプログラムによって再トレーニングする

**AzureML モデルをプログラムによって再トレーニングする方法はありますか。**
再トレーニング API を使用します。 サンプル コードは、使用可能な [ここ](https://azuremlretrain.codeplex.com/)します。

### 作成

**ローカルまたはインターネットに接続していないアプリケーションでモデルをデプロイすることはできますか。**
番号


**すべての Web サービスで予測される基本的な遅延はありますか。**

を参照してください [Azure サブスクリプションの制限](../azure-subscription-service-limits.md)

### 最初の起動時にドメインに参加しているマシンになるように VM をプロビジョニングするには、

**バッチ実行サービスと要求応答サービスとして予測モデルを実行するのはいつがいいですか。**

要求応答サービス (RRS) は、待ち時間が短く、拡張性の高い Web サービスで、実験環境で作成、デプロイされたステートレスなモデルへのインターフェイスを提供するために使用されます。 バッチ実行サービス (BES) は、データ レコードのバッチに対して非同期でスコアを付けるためのサービスです。 BES への入力は、RRS で使用されるデータ入力と似ています。 主な違いは、BES が、BLOB サービス、Azure の Table サービス、Azure SQL Database、HDInsight (Hive Query)、HTTP ソースなどのさまざまなソースからレコードのブロックを読み取ることです。 詳細については、次を参照してください。 [Machine Learning web サービスを使用する方法について](machine-learning-consume-web-services.md)します。

**Web サービスにデプロイされたモデルはどのように更新できますか。**

既にデプロイされたサービスの予測モデルの更新は、トレーニング済みのモデルの作成および保存に使用される実験の変更および再実行と同じくらい簡単です。 新しいバージョンのトレーニング済みのモデルが使用できるようになると、ML Studio はステージング環境の Web サービスを更新するかどうかをたずねます。 ステージング環境の Web サービスに更新が適用された後に、同じ更新を運用環境の Web サービスにも適用できるようになります。 参照してください [Machine Learning web サービスの配置](machine-learning-publish-a-machine-learning-web-service.md) デプロイされた web サービスを更新する方法の詳細。

Retraining API を使用することもできます。 サンプル コードでは、使用可能な [ここ](https://azuremlretrain.codeplex.com/)します。

**運用環境にデプロイされた Web サービスはどのように監視できますか。**

予測モデルが運用環境にデプロイされると、Azure クラシック ポータルから監視できます。 デプロイされた各サービスには専用のダッシュボードがあり、ここでそのサービスについての監視情報を参照できます。

**RRS/BES の出力を確認できまる場所はありますか。**

RRS の場合は通常、Web サービスの応答に結果が表示されます。 BLOB に書き込むこともできます。 BES の場合、既定では BLOB に出力が書き込まれます。 また、ライター モジュールを使用してデータベースやテーブルに出力を書き込むこともできます。

 **Web サービスを作成できるのは Studio で作成されたモデルからのみですか。
 番号 Jupyter Notebook および RStudio から直接 web サービスを作成することもできます。

## 拡張性

**Web サービスのスケーラビリティはどれくらいですか。**

現時点では、既定のエンドポイントは 1 つのエンドポイントにつき 20 件の同時 RRS 要求でプロビジョニングされます。 エンドポイントあたり 200 の要求への同時要求を拡張して、」の説明に従って、web サービスあたり 10,000 のエンドポイントを各 web サービスを拡張することができます、 [API エンドポイントのスケーリング](machine-learning-scaling-endpoints.md) 記事です。 BES の場合、各エンドポイントは一度に 40 件の要求を処理でき、40 件を超えた要求はキューに登録されます。 これらのキューに登録された要求は、キューから放出されると自動的に実行されます。


**R のジョブはノード間に分散されますか。**

番号


**どれくらいのデータ量をトレーニングできますか。**

Machine Learning Studio のモジュールは、一般的に、最大 10 GB の高密度数値データのデータセットをサポートしています。 モジュールが 1 つ以上の入力を受け取る場合は、10 GB はすべての入力サイズの合計です。 インジェストの前に、Hive または Azure SQL Database のクエリ経由で、またはカウントによる学習の前処理によって、より大きなデータセットをサンプリングすることもできます。

次の種類のデータは、特徴の正規化の際により大きなデータセットに展開でき、10 GB 未満に制限されています。

- スパース
- カテゴリ
- 文字列
- バイナリ データ

次のモジュールは、10 GB 未満のデータセットに制限されています。

- 推奨モジュール
- SMOTE モジュール
- スクリプト モジュール: R、Python、SQL
- 出力データ サイズが入力データ サイズを超えるモジュール ("結合"、"特徴ハッシュ" など)。
- イテレーションの数が非常に大きい場合のクロス検証、パラメーターの掃引、順序回帰、一対全多クラス。

数 GB を超えるデータセットの場合は、ローカル ファイルから直接アップロードするのではなく、Azure Storage または Azure SQL Database にデータをアップロードするか、HDInsight を使用する必要があります。


**ベクターのサイズに制限はありますか。**

行と列はそれぞれ、int 型の最大の制限で .NET に制限: 2,147, 483,647 です。

**これを実行する VM のサイズを調整することはできますか。**

番号

## セキュリティと可用性

* * 既定では、実稼働環境でデプロイされた web サービスの http エンドポイントへのアクセスのあるユーザーですか。 エンドポイントにアクセスを制限する方法ですか? * *

Web サービスがデプロイされた後に、そのサービスに対して既定のエンドポイントが作成されます。 既定のエンドポイントは運用環境にデプロイされ、API キーを使用して呼び出すことができます。 追加のエンドポイントは、独自のキーを使用して Azure クラシック ポータルから、または Web Service Management API を使用してプログラムにより追加することができます。 運用環境およびステージング環境で Web サービスを呼び出すには、アクセス キーが必要です。 詳細については、次を参照してください。 [Machine Learning web サービスへの接続](machine-learning-connect-to-azure-machine-learning-web-service.md)します。


**ストレージ アカウントが見つからない場合はどうなりますか。**

Machine Learning Studio は、ワークフローを実行する際に中間データを保存する、ユーザーが指定した Azure ストレージ アカウントに依存しています。 このストレージ アカウントは、ワークスペースの作成時に Machine Learning Studio に提供されます。 ワークスペースの作成後にストレージ アカウントが削除され、見つけることができない場合は、ワークスペースが機能しなくなり、そのワークスペースのすべての実験が失敗します。

誤ってストレージ アカウントを削除した場合に回復する唯一の方法は、削除されたストレージ アカウンと同じリージョンに同じ名前のストレージ アカウントを再作成することです。 その後、アクセス キーを再同期してください。


**ストレージ アカウントのアクセス キーの同期が失われてしまった場合はどうなりますか。**
Machine Learning Studio は、ワークフローを実行する際に中間データを保存する、ユーザーが指定した Azure ストレージ アカウントに依存しています。 このストレージ アカウントは、ワークスペースの作成時に Machine Learning Studio に提供され、アクセス キーは、そのワークスペースに関連付けられます。 ワークスペースの作成後にアクセス キーが変更され、ワークスペースがストレージ アカウントにアクセスできない場合は、ワークスペースが機能しなくなり、そのワークスペースのすべての実験が失敗します。

ストレージ アカウントのアクセス キーを変更した場合は、Azure クラシック ポータルのワークスペースの設定でアクセス キーを再同期をご確認ください。


## Azure Marketplace

を参照してください [公開と Machine Learning Marketplace でのアプリの使用に関する FAQ](machine-learning-marketplace-faq.md)

## サポートとトレーニング

**どこで Azure ML のトレーニングを受講できますか。**

[Azure Machine Learning ドキュメント センター](/services/machine-learning/) ビデオ チュートリアルやハウツー ガイドをホストします。 順を追ったハウツー ガイドでは、サービスの概要に加え、データのインポートから、データのクリーニング、予測モデルの構築、Azure ML を使用した運用環境へのデプロイまで、データ技術のライフ サイクルをひととおり説明します。

Machine Learning Center には継続的に新しい資料が追加されます。 Machine Learning Center に上の他のラーニング情報については、要求を送信する [ユーザー フィードバック フォーラム](https://windowsazure.uservoice.com/forums/257792-machine-learning)します。

でトレーニングを検索することも [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning)

**どのようにして Azure Machine Learning のサポートを受けることができますか。**

Azure Machine Learning のテクニカル サポートを取得するには [Azure サポート](/support/options/) 選択 **Machine Learning**します。

Azure Machine Learning については、MSDN にコミュニティ フォーラムがあり、ここで、Azure ML に関連する質問をすることができます。 このフォーラムは Azure ML チームが監視しています。 Visit [Azure Forum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).




[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/ 
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/ 
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/ 
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/ 
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/ 
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/ 

