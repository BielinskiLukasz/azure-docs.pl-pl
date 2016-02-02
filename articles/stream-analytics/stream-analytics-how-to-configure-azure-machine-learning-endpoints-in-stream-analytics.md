<properties 
    pageTitle="Stream Analytics で Azure Machine Learning のエンドポイントを構成する方法 | Microsoft Azure" 
    description="Stream Analytics のユーザー定義機械言語関数"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="paulettm" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="12/10/2015" 
    ms.author="jeffstok"
/>


# Stream Analytics への Machine Learning の統合

Stream Analytics では、Azure Machine Learning のエンドポイントを呼び出す、ユーザー定義の関数をサポートしています。 この機能の REST API のサポートの詳細については、 [ストリーム分析の REST API ライブラリ](https://msdn.microsoft.com/library/azure/dn835031.aspx)します。 この資料では、Stream Analytics にこの機能を正しく実装するために必要な補足的な情報を示します。 チュートリアルも提供されて、使用可能な [ここ](stream-analytics-machine-learning-integration-tutorial.md)します。

## 概要: Azure Machine Learning の用語

Microsoft Azure Machine Learning には、データを活用した予測分析ソリューションの構築、テスト、デプロイをドラッグ アンド ドロップで行うことができる、コラボレーションに対応したツールがあります。 このツールは、*Azure Machine Learning Studio* と呼ばれています。 Studio は、Machine Learning リソースと通信し、設計を簡単に構築、テストおよび反復処理するために使用できます。 これらのリソースとその定義のとおりです。

- **ワークスペース**: *ワークスペース*は、その他のすべての Machine Learning リソースを管理および制御するためにそれらを保持するコンテナーです。
- **実験**: *実験*は、データセットを使用して Machine Learning モデルをトレーニングするデータ科学者によって作成されます。
- **エンドポイント**: *エンドポイント*は、機能を入力として取り、特定の Machine Learning モデルを適用し、スコア付けされた出力を返す Azure Machine Learning のオブジェクトです。
- **スコア付け Web サービス**: *スコア付け Web サービス*とは、前述のエンドポイントの集合です。

各エンドポイントには、バッチの実行と同期の実行用の API があります。 Stream Analytics では、同期実行を使用します。 特定のサービスの名前は、 [要求/応答サービス](../machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs) AzureML studio にします。

## Stream Analytics のジョブに必要な Machine Learning リソース

Stream Analytics のジョブを処理する、要求/応答エンドポイント、 [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md#get-an-azure-machine-learning-authorization-key) swagger 定義はすべてのされ正常に実行します。 Stream Analytics には、Swagger エンドポイントの URL を作成し、インターフェイスを検索し、既定の UDF の定義をユーザーに返す追加エンドポイントがあります。

## REST API を使用した Stream Analytics および Machine Learning UDF の構成

REST API を使用すると、ジョブを構成して Azure Machine Language 関数を呼び出すことができます。 手順は次のとおりです。

1. Stream Analytics のジョブの作成
2. 入力の定義
3. 出力の定義
4. ユーザー定義関数 (UDF) の作成
5. UDF を呼び出す Stream Analytics 変換の記述
6. ジョブの開始

## 基本的なプロパティを持つ UDF の作成

次に、Azure Machine Learning のエンドポイントにバインドされる *newudf* という名前のスカラー UDF を作成するサンプル コードの例に示します。 *エンドポイント* (サービス URI) は、選択したサービスの API のヘルプ ページにあり、*apiKey* はサービスのメイン ページにあります。

PUT:/subscriptions/<subscriptionId>/resourcegroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>? api-version =<apiVersion>

要求本文の例

    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }

## 既定の UDF 用の RetrieveDefaultDefinition エンドポイントの呼び出し

スケルトン UDF を作成したら、UDF を完全に定義する必要があります。 RetreiveDefaultDefinition エンドポイントを使用すると、Azure Machine Learning エンドポイントにバインドされているスカラー関数の既定の定義を取得できます。 以下のペイロードでは、Azure Machine Learning エンドポイントにバインドされているスカラー関数の既定の UDF 定義を取得する必要があります。 PUT 要求で既に渡されているので、実際のエンドポイントは指定されません。 Stream Analytics は、要求で明示的に渡さる場合、そのエンドポイントを呼び出します。 そうでない場合、最初に参照したものを使用します。 ここでは UDF は単一の文字列型のパラメーター (文) を取り、その文の “sentiment” ラベルである文字列型の単一の出力を返します。

投稿:/subscriptions/<subscriptionId>/resourcegroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition? api-version =<apiVersion>

要求本文の例

    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }

このサンプル出力は、次のようになります。


    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }

## 応答を使用した UDF の PATCH

ここで前の応答を使用して、以下のように UDF に対して PATH を実行します。

更新プログラム:/subscriptions/<subscriptionId>/resourcegroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>? api-version =<apiVersion>

RetrieveDefaultDefinition からの出力を要求本文:

## UDF を呼び出すための Stream Analytics 変換の実装

ここで各入力イベントにつき (scoreTweet という名前の) UDF に対し問い合わせを行い、そのイベントの応答を出力に書き込みます。

    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }

詳細については、以下を参照してください。

## 問い合わせ

詳細については、当社を参照してください [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用を開始します。](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブをスケーリングします。](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)




