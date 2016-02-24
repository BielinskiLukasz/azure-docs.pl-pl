<properties
    pageTitle="別の実験のデータを Machine Learning Studio にインポートする | Microsoft Azure"
    description="Azure Machine Learning Studio にトレーニング データを保存して別の実験で保存する方法。"
    keywords="import data,data,data sources,training data"
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
    ms.date="10/07/2015"
    ms.author="garye;bradsev" />


# 別の実験のデータを Azure Machine Learning Studio にインポートする

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


実験の中間結果を別の実験の一部として使用することが必要になる場合があります。  この場合は、モジュールをデータセットとして保存します。

1. データセットとして保存するモジュールの出力を右クリックします。

2. クリックして **データセットとして保存**します。

3. メッセージが表示されたら、名前と、データセットを簡単に識別できるような説明を入力します。

4. クリックして、 **OK** チェック マークします。

保存が完了すると、ワークスペースのどの実験でもデータセットを使用できるようになります。 これが見つかります、 **保存されたデータセット** モジュール パレットの一覧です。


