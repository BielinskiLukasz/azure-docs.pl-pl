<properties 
    pageTitle="Azure Machine Learning Studio とは | Microsoft Azure"
    description="そのまま使うことのできるアルゴリズムやモジュールを含んだライブラリから、ドラッグ アンド ドロップでモデルをすばやく構築できるツール Azure ML Studio の概要です。"
    keywords="azure machine learning,azure ml, ml studio"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2015"
    ms.author="garye"/>

# Azure Machine Learning Studio とは

Microsoft Azure Machine Learning Studio は、データを活用した予測分析ソリューションの構築、テスト、デプロイをドラッグ アンド ドロップで行うことができる、コラボレーションに対応したツールです。 Machine Learning Studio でモデルを Web サービスとして公開すれば、カスタム アプリや BI ツール (Excel など) からそのモデルを簡単に利用することができます。

Machine Learning Studio があれば、最新のデータ サイエンスとクラウド リソースを活用して、独自に所有するデータを使った予測分析を実現することができます。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Machine Learning Studio の対話型ワークスペース

一般的な予測分析モデルの作成では、1 種類以上のソースからデータを入手し、さまざまなデータ操作と統計的特徴を適用してデータを変換することにより、一連の結果を生成します。 このようなモデルの作成プロセスは対話型プロセスになります。 十分にトレーニングされた有効なモデルが作成されるまで、さまざまな特徴とパラメーターを繰り返し調整します。

**Azure Machine Learning Studio** 簡単にビルド、テスト、および反復処理、予測分析モデルの対話型、視覚的ワークスペースで表示できます。 ドラッグ アンド ドロップする ***データセット*** と分析 ***モジュール*** 対話型の上に ***キャンバス***, 、フォームを同時に接続、 ***実験***, 、これを ***実行*** Machine Learning Studio でします。 モデルのデザインに反復処理する ***編集*** 、実験 ***保存*** 、再実行して、必要に応じてコピーします。 準備ができたら、ときに、変換、 ***トレーニング実験*** に、 ***予測実験***, 、し ***発行*** として、 ***web サービス*** モデルは、他のユーザーがアクセスできるようにします。

>[AZURE.TIP] ダウンロードして、Machine Learning Studio の機能の概要を示すダイアグラムを印刷する、次を参照してください。 [Azure Machine Learning Studio の機能の概要図](machine-learning-studio-overview-diagram.md)します。

データセットとモジュールを視覚的に接続すれば予測分析モデルが完成するため、プログラミングは必要ありません。

![Azure ML Studio の図: 実験の作成、各種ソースのデータの読み取り、スコア付けされたデータの書き込み、モデルの書き込み][ml-studio-overview]

## Machine Learning Studio の概要

入力すると [Machine Learning Studio](https://studio.azureml.net) するを参照してください、 **ホーム** ページです。 そこからドキュメントやビデオ、ウェビナーなど、有益なリソースにアクセスすることができます。

3 つのタブが上部にある: **ホーム** (ここで開始)、 **Studio**, 、および **ギャラリー**します。

### スタジオ

クリックして、 **Studio** ] タブをクリックして、Microsoft アカウントまたは職場または学校アカウントを使用してサインインを求められます。 サインインすると、次のタブが左側に表示されます。

- **実験** -作成された実験の実行、およびドラフトとして保存
- **WEB サービス** -Web サービス実験からにデプロイされています。
- **ノートブック** -作成した Jupyter notebook
- **データセット** -Studio にアップロードしたデータセット
- **トレーニング済みのモデル** -モデルを実験でトレーニングを受けて、Studio で保存しています。
- **設定** -アカウントとリソースを構成に使用できる設定のコレクション。

### ギャラリー

クリックして、 **ギャラリー** ] タブをクリックして、Cortana Analytics ギャラリーに表示されます。 ギャラリーは、データ サイエンティストや開発者のコミュニティが、Cortana Analytics Suite のコンポーネントを使用してソリューションを共有できる場です。

ギャラリーの詳細については、次を参照してください。 [共有 Cortana Analytics ギャラリー内のソリューションの検出と](machine-learning-gallery-how-to-use-contribute-publish.md)です。

## 実験の構成要素

実験にはデータセットが含まれます。データセットからデータが分析モジュールに提供され、分析モジュールを接続することで予測分析モデルが完成します。 有効な実験に求められる具体的な条件を以下に示します。

- 実験には少なくとも 1 つのデータセットと 1 つのモジュールがある。
- データセットはモジュールにのみ接続できる。
- モジュールはデータセットにも別のモジュールにも接続できる。
- モジュールのすべての入力ポートが、何らかの形でデータ フローに接続されている。
- モジュールの必須パラメーターがすべて設定されている。

実験を最初から作成するか、既にあるサンプル実験をテンプレートとして使用してください。 詳細については、次を参照してください。 [のサンプル実験を使用して新しい実験を作成する](machine-learning-sample-experiments.md)です。

簡単な実験の作成例を参照してください。 [Azure Machine Learning Studio で簡単な実験の作成](machine-learning-create-experiment.md)します。

予測分析ソリューションを作成するより詳細なチュートリアルは、次を参照してください。 [Azure Machine Learning を使用した予測ソリューションの開発](machine-learning-walkthrough-develop-predictive-solution.md)します。

### データセット

データセットを Machine Learning Studio にアップロードすることで、これらのデータセットをモデル作成プロセスで使用できるようになります。 Machine Learning Studio には数多くのサンプル データセットが既に含まれているため、これらを実験で試すことができます。また、必要に応じてさらにデータセットをアップロードできます。 提供されるデータセットには以下のようなものがあります。

- **さまざまな自動車の燃費データ** の数、シリンダー数や馬力などによって分類された、自動車の燃費 (MPG) が値です。
- **乳がんデータ** -乳がんの診断データです。
- **森林火災データ** -森林火災の規模ポルトガル北東地域を対象にします。

利用可能なデータセットは、実験を作成するときにキャンバスの左側の一覧から選択できます。

Machine Learning Studio に含まれるサンプル データセットの一覧は、次を参照してください。 [Azure Machine Learning Studio でサンプル データ セットを使用して](machine-learning-use-sample-datasets.md)します。

### モジュール

モジュールとは、データに対して実行できるアルゴリズムのことです。 Machine Learning Studio には、データの受信機能や、データのトレーニング、スコア付け、検証などに対応したさまざまなモジュールが用意されています。 提供されるモジュールには以下のようなものがあります。

- [ARFF への変換][convert-to-arff] -.NET でシリアル化されたデータセットを属性関係ファイル フォーマット (ARFF) に変換します。
- [基本的な統計][elementary-statistics] -平均や標準偏差などの基本的な統計を計算します。
- [線形回帰][linear-regression] -オンライン傾斜降下に基づく線形回帰モデルを作成します。
- [モデルのスコア付け][score-model] -トレーニングされた分類または回帰モデルをスコア付けします。

利用可能なモジュールは、実験を作成するときにキャンバスの左側の一覧から選択できます。  

モジュールに一連のパラメーターが含まれている場合、これらを使用してモジュールの内部アルゴリズムを構成することができます。 モジュールのパラメーターが表示されます、キャンバスでモジュールを選択すると、 **プロパティ** キャンバスの右側のウィンドウです。 このウィンドウでパラメーターを変更することにより、モデルを微調整できます。

いくつかのヘルプ内の移動に関する機械学習アルゴリズムが使用可能なは、大規模なライブラリを参照してください。 [Microsoft Azure Machine Learning のアルゴリズムを選択する方法](machine-learning-algorithm-choice.md)します。

## 予測分析 Web サービスのデプロイ

予測分析モデルの準備が整ったら、それを Machine Learning Studio から Web サービスとしてデプロイすることができます。 このプロセスの詳細については、次を参照してください。 [Azure Machine Learning web サービスを配置](machine-learning-publish-a-machine-learning-web-service.md)します。

[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/


