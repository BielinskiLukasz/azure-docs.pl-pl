<properties 
    pageTitle="Python Machine Learning スクリプトの実行 |Microsoft Azure" 
    description="Azure Machine Learning における Python スクリプトのサポート、基本的な使用シナリオ、機能、制限事項の基になる設計原則について説明します。" 
    keywords="python machine learning,pandas,python pandas,python scripts, execute python scripts"
    services="machine-learning"
    documentationCenter="" 
    authors="bradsev" 
    manager="paulettm" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/11/2015" 
    ms.author="bradsev" />



# Azure Machine Learning Studio での Python Machine Learning スクリプトの実行

このトピックでは、Azure Machine Learning における現在の Python スクリプトのサポートの基になる設計原則について説明します。 また、既存のコードのインポートや視覚化のエクスポートのサポートを含む主要な機能の概要と、最後に、いくつかの制限事項と進行中の作業についても説明します。

[Python](https://www.python.org/) の多くのデータ サイエンティストでに欠かせないツールです。 洗練された簡潔な構文、クロス プラットフォームのサポート、膨大で強力なライブラリ、完成度の高い開発ツールを備えています。 Python は、データの取り込みと処理から、トレーニング、検証、モデルのデプロイへの特徴構築に至る、Machine Learning のモデリングで通常使用されるワークフローのすべてのフェーズで使用されています。

Azure Machine Learning Studio は、Machine Learning の実験のさまざまな部分への Python スクリプトの埋め込みと、スケーラブルで運用可能な Microsoft Azure の Web サービスとしてシームレスな公開をサポートしています。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Machine Learning での Python スクリプトの設計原則

Azure Machine Learning Studio での Python の主要なインターフェイスを使用して、 [Python スクリプトの実行 ][execute-python-script] 図 1 に示すモジュールです。

![Image1](./media/machine-learning-execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Image2](./media/machine-learning-execute-python-scripts/embedded-machine-learning-python-script.png)

図 1. **Python スクリプトの実行**モジュール

[Python スクリプトの実行 ][execute-python-script] モジュールは、最大 3 つの入力を受け付けるし、R アナログのと同じように (後で説明)、最大 2 つの出力を生成する、 [R スクリプトの実行 ][execute-r-script] モジュールです。 実行する Python コードが特別に指定されたエントリ ポイントとしてパラメーター ボックスに入力された関数と呼ばれる `azureml_main`します。 次にこのモジュールの実装に使用される主要な設計原則を示します。

1.  *Python ユーザーにとって慣用的であること。*ほとんどの Python ユーザーは、モジュール内の関数としてコードを組み込むため、最上位レベルのモジュールに多くの実行可能ステートメントを置くことは比較的まれです。 そのため、単なるステートメントのシーケンスとは対照的に、スクリプト ボックスにも特別に指定された Python 関数が入力されます。 関数で公開されるオブジェクトは、標準的な Python ライブラリの型をなどは [Pandas](http://pandas.pydata.org/) データ フレームと [NumPy](http://www.numpy.org/) 配列。
2.  *ローカルとクラウド間の実行が高品質であること。*Python コードの実行に使用されるバックエンドがに基づいて [Anaconda](https://store.continuum.io/cshop/anaconda/) 2.1、広く使用されているプラットフォーム間科学的な Python ディストリビューションです。 最も一般的な Python パッケージが 200 個近く付属しています。 そのため、データ サイエンティストをデバッグし、そのローカル Azure Machine Learning 互換性のある Anaconda 環境などの既存の開発環境を使用して、コードを修飾できます [IPython](http://ipython.org/) notebook または [Python Tools for Visual Studio](http://aka.ms/ptvs) し、信頼性の高い Azure Machine Learning の実験の一部として実行します。 さらに、 `azureml_main` SDK がインストールされているか、エントリ ポイントは、vanilla Python 関数であり、Azure Machine Learning 固有のコードがなくても作成できます。
3.  *他のAzure Machine Learning モジュールとシームレスに構成できること。* [Python スクリプトの実行 ][execute-python-script] モジュール、入力と出力と標準的な Azure Machine Learning はデータセットを受け取ります。 基になるフレームワークは、透過的かつ効率的に Azure Machine Learning と Python のランタイムを埋めます (不足値などの特徴をサポート)。 そのため、Python は既存の Azure Machine Learning ワークフロー (R や SQLite の呼び出しを含む) と組み合わせて使用できます。 ユーザーは次のようなワークフローを予測できます。
  * データの前処理とクリーニングに Python と Pandas を使用する。
  * データを SQL 変換にフィードし、フォームの特徴に複数のデータセットを結合する。
  * Azure Machine Learning のアルゴリズムの広範なコレクションを使用したモデルのトレーニング。
  * R を使用した結果の評価と後処理。


## Machine Learning での Python スクリプトの基本的な使用シナリオ

調査いくつかの基本的な使用方法のこのセクションで、 [Python スクリプトの実行 ][execute-python-script] モジュールです。
前述のように、Python モジュールへの入力は、Pandas データ フレームとして公開されます。 Python Pandas の詳細と、効果的かつ効率的にデータを操作するための Python Pandas の使用方法については、「*Python によるデータ分析*」(Sebastopol, CA.: O'Reilly, 2012) をご覧ください。 この関数は、Python の内部にパッケージ化された 1 つの Pandas データ フレームを返す必要があります [シーケンス](https://docs.python.org/2/c-api/sequence.html) タプル、リスト、NumPy 配列などです。 このシーケンスの最初の要素は、モジュールの最初の出力ポートに返されます。 図 2 に、この方法を示します。

![Image3](./media/machine-learning-execute-python-scripts/map-of-python-script-inputs-outputs.png)

図 2. パラメーターに入力ポートをマッピングし、出力ポートに値を返す。

詳細なセマンティクスの入力ポートのパラメーターにマップする方法、 `azureml_main` 関数は、表 1 に表示されます。

![image1T](./media/machine-learning-execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

表 1 関数パラメーターへの入力ポートのマッピング。

入力ポートと関数パラメーター間のマッピングは位置指定されています。つまり、最初に接続された入力ポートは関数の最初のパラメーターにマップされ、2 番目の入力は (接続した場合) 関数の2 番目のパラメーターにマップされます。

## 入力と出力の種類の変換

前述のように、Azure Machine Learning の入力データセットは Pandas 内のデータ フレームに変換され、出力データ フレームは Azure Machine Learning データセットに戻されます。 次の変換が実行されます。

1.  文字列と数値列は、現状のまま変換され、データセット内の不足値は Pandas 内で 'NA' 値に変換されます。 戻す場合も同じ変換が行われます (Pandas 内の NA 値は Azure Machine Learning で不足値に変換されます)。
2.  Pandas のインデックス ベクターは、Azure Machine Learning でサポートされていないため、Python 関数内のすべての入力データ フレームには、常に 0 から、行数から 1 を引いた数までの 64 ビットの数字のインデックスがあります。
3.  Azure Machine Learning データセットには、文字列以外の重複する列名と列名を指定できません。 出力データ フレームに数値以外の列が含まれている場合、フレームワーク `str` 列の名前にします。 同様に、重複する列名は、確実に一意の名前になるように自動的に破棄されます。 サフィックス (2) は最初の重複に追加され、サフィックス (3) は2 つ目の重複に追加されます。

## Python スクリプトの操作

どの [Python スクリプトの実行 ][execute-python-script] web サービスとして発行スコア付け実験で使用されているモジュールが呼び出されます。 たとえば、図 3 は、1 つの Python 式を評価するコードを含む、スコア付け実験を示します。

![image4](./media/machine-learning-execute-python-scripts/figure3a.png)

![image5](./media/machine-learning-execute-python-scripts/python-script-with-python-pandas.png)

図 3: Python 式を評価するための Web サービス。

この実験から作成された Web サービスは、入力として Python 式を受け取り (文字列として)、これを Python インタープリターに送信し、式と評価結果の両方を含むテーブルを返します。

## 既存の Python スクリプト モジュールをインポートする

多くのデータ サイエンティスト向けの一般的な使用例は、既存の Python スクリプトを Azure Machine Learningの実験に組み込むことです。 連結して 1 つのスクリプト ボックスにすべてのコードを貼り付けるのではなく、 [Python スクリプトの実行 ][execute-python-script] モジュールが、Python モジュールを含む zip ファイルの接続先 3 番目の入力ポートを受け入れます。 このファイルは実行時に実行フレームワークによって解凍され、その内容が Python インタープリターのライブラリ パスに追加されます。  `Azureml_main` エントリ ポイント関数が直接これらのモジュールをインポートし、ことができます。

たとえば、単純な “Hello, World” 関数を含む Hello.py ファイルについて考えてみます。

![image6](./media/machine-learning-execute-python-scripts/figure4.png)

図 4: ユーザー定義関数

次に、Hello.py を含む Hello.zip ファイルを作成します。

![image7](./media/machine-learning-execute-python-scripts/figure5.png)

図 5: ユーザー定義の Python コードを含む zip ファイル。

次に、これをデータセットとして Azure Machine Learning Studio にアップロードします。
簡単な実験を作成して実行する場合は、モジュールを使用します。

![image8](./media/machine-learning-execute-python-scripts/figure6a.png)

![image9](./media/machine-learning-execute-python-scripts/figure6b.png)

図 6: ユーザー定義の Python コードを使ったサンプル実験は zip ファイルとしてアップロードされる。

モジュール出力の zip ファイルがパッケージ化されていることを示していますし、関数 `print_hello` が実際に実行します。
 
![image10](./media/machine-learning-execute-python-scripts/figure7.png)

図 7: ユーザー定義関数内で使用中、 [Python スクリプトの実行 ][execute-python-script] モジュールです。

## 視覚化の操作

ブラウザーでビジュアル化できる MatplotLib を使用して作成されたプロットはによって返されることができます、 [Python スクリプトの実行 ][execute-python-script]します。 ただし、R を使用している場合、プロットは自動的にリダイレクトされません。このため、プロットを Azure Machine Learning に返す場合、ユーザーはすべてのプロットを PNG ファイルに明示的に保存する必要があります。

MatplotLib からのイメージを生成するには、次の手順を完了する必要があります。

* 既定の Qt ベース レンダラーから "AGG"にバックエンドを切り替える
* 新しい figure オブジェクトを作成する
* 軸を取得し、そこにすべてのプロットを生成する
* PNG ファイルに図を保存する

下記の図 8 に、Pandas の scatter_matrix 関数を使用して散布図マトリックスを作成するプロセスを示します。

![image1v](./media/machine-learning-execute-python-scripts/figure-v1-8.png)

図 8: イメージに MatplotLib の図を保存します。



図 9 は、上記のスクリプトを使用して、2 番目の出力ポートからプロットを返す実験を示しています。

![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9a.png)

![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9b.png)

図 9: Python コードから生成されたプロットの視覚化。

複数の図をさまざまなイメージに保存して返すことができるため、Azure Machine Learning ランタイムはすべてのイメージを取得し、それらを視覚化のために連結できます。


## 高度な例

Azure Machine Learning にインストールされている Anaconda 環境には、NumPy、SciPy、Scikits-Learn などの一般的なパッケージが含まれています。これらは、通常の機械学習パイプラインのさまざまなデータ処理タスクで効果的に使用できます。 たとえば、次の実験とスクリプトは、データセットの特徴の重要度スコアを計算するための Scikits-Learn でのアンサンブル学習者の使用を示しています。 このスコアは、別の機械学習モデルに供給する前に、監視する特徴を選択するために使用されます。

重要度スコアを計算し、それに基づいて特徴を順序付ける Python 関数を以下に示します。

![image11](./media/machine-learning-execute-python-scripts/figure8.png)

図 10: スコアによって特徴を順位付ける関数。
 
次の実験は、Azure Machine Learning の “ピマ インディアン糖尿病” データセットにおける特徴の重要度スコアを計算して返します。

![image12](./media/machine-learning-execute-python-scripts/figure9a.png)
![image13](./media/machine-learning-execute-python-scripts/figure9b.png)

図 11: ピマ インディアン糖尿病の特徴の順位付けの実験

## 制限事項

[Python スクリプトの実行 ][execute-python-script] 現在次の制限があります。

1.  *セキュリティで保護された実行。*Python ランタイムは、現在セキュリティで保護されているため、永続的な方法でのネットワークやローカル ファイル システムへのアクセスを許可しません。 ローカルに保存されているすべてのファイルは分離され、モジュールが終了すると削除されます。 Python コードは、現在のディレクトリとそのサブディレクトリを除く、実行中のコンピューターのほとんどのディレクトリにアクセスできません。
2.  *高度な開発とデバッグ サポートの欠如。*Python モジュールは、現在、Intellisense やデバッグなどの IDE 機能をサポートしていません。 また、モジュールが実行時に失敗した場合は、完全な Python スタック トレースが使用できますが、モジュールのログ出力に表示される必要があります。 ユーザーは、IPython などの環境で自身の Python スクリプトを作成してデバッグし、そのコードをモジュールにインポートすることを勧めします。
3.  *1 つのデータ フレームの出力。*Python のエントリ ポイントは、1 つのデータ フレームのみを出力として返すことができます。 現在は、Azure Machine Learning ランタイムに直接戻されたトレーニング済みのモデルなどの、任意の Python オブジェクトを返すことはできません。 ような [R スクリプトの実行 ][execute-r-script], 、同じ制限があり、ただし多くの可能な場合がオブジェクトを変換、バイトの配列戻り値にそのデータ フレームの内部です。
4.  *Python のインストールをカスタマイズできない。* 現時点では、カスタムの Python モジュールを追加する唯一の方法は、前に説明した zip ファイルのメカニズムを使用することです。 これは、小さなモジュールに適していますが、大きなモジュール (特にネイティブ DLL を使用するモジュール) や大量のモジュールでは、使用が面倒です。


## まとめ

[Python スクリプトの実行 ][execute-python-script] モジュールにより、データ サイエンティスト Azure Machine learning のクラウドでホストされる機械学習のワークフローに既存の Python コードを組み込む web サービスの一部としてシームレスに運用します。 Python スクリプト モジュールは、Azure Machine Learning の他のモジュールと自然に相互運用し、データの探索から前処理、特徴の抽出、評価、結果の後処理までのタスクの範囲で使用できます。 実行で使用されるバックエンド ランタイムは、Anaconda (十分にテストされ、広く使用されている Python ディストリビューション) に基づいています。 これにより、ユーザーは簡単に既存のコード資産をクラウドに配布できます。

追加機能を提供する予定は来月、 [Python スクリプトの実行 ][execute-python-script] のトレーニングや Python でのモデルを運用にし、開発と Azure Machine Learning Studio でコードをデバッグにより優れたサポートを追加する機能などのモジュール。

## 次のステップ

詳細については、次を参照してください。、 [Python デベロッパー センター](/develop/python/)します。



[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/ 
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/ 

