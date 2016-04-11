<properties
    pageTitle="Machine Learning Web サービスを使用する | Microsoft Azure"
    description="機械学習サービスがデプロイされると、利用可能になっている RESTFul Web サービスを、要求応答サービスまたはバッチ実行サービスのいずれかとして使用できます。"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="paulettm"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="10/19/2015"
    ms.author="bradsev" />


# Machine Learning の実験からデプロイされた Azure Machine Learning Web サービスを使用する方法

## はじめに

Azure Machine Learning の実験は、Web サービスとしてデプロイされると、さまざまなデバイスやプラットフォームが使用できる REST API を提供します。 これは、単純な REST API では JSON 形式のメッセージを使用して受け入れと応答がなされるからです。 Azure Machine Learning ポータルは、R、C#、および Python で Web サービスを呼び出すために使用できるコードを提供します。 ただし、これらのサービスは、次の 3 つの条件を満たす任意のプログラミング言語とデバイスから呼び出すことができます。

* ネットワークに接続している
* HTTPS 要求を実行する SSL の機能がある
* JSON を解析する機能がある (手動またはサポート ライブラリによる)

つまり、Web アプリケーション、モバイル アプリケーション、カスタム デスクトップ アプリケーション、および Excel からもサービスを利用できます。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

Azure Machine Learning の Web サービスは、要求応答サービスまたはバッチ実行サービスの 2 つの異なる方法で使用できます。 各シナリオでは、実験がデプロイされてから使用できる RESTFul Web サービスを通じて機能が提供されます。 Azure Web サービス エンドポイントを使用して Azure で Machine Learning Web サービスを配置すると、サービスが利用状況に基づいて自動的に規模変更されるので、ハードウェア リソースに対する先行投資と継続的なコストを回避できます。

> [AZURE.TIP] 予測される web サービスにアクセスする web アプリを作成する簡単な方法を参照してください。 [web アプリのテンプレートを使用して Azure Machine Learning web サービスの使用](machine-learning-consume-web-service-with-web-app-template.md)します。

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

作成して Azure Machine Learning web サービスをデプロイする方法については、次を参照してください。 [Azure Machine Learning web サービスを配置][publish]します。 Machine Learning の実験の作成と展開のステップ バイ ステップ チュートリアルでは、次を参照してください。 [Azure Machine Learning を使用した予測ソリューションの開発][walkthrough]します。

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md


## 要求応答サービス (RRS)

要求応答サービス (RRS) は、待ち時間が短く、拡張性の高い Web サービスであり、Azure Machine Learning Studio の実験で作成およびデプロイされたステートレスなモデルへのインターフェイスを提供するために使用されます。 このサービスを使用すると、使用側アプリケーションがリアルタイムでの応答を期待するシナリオに対応できます。

RRS は、1 行または複数行の入力パラメーターを受け取り、出力として 1 行または複数行を生成できます。 出力行には複数の列を含めることができます。

RRS の例では、アプリケーションの信頼性を検証しています。 この例では、数百万のアプリケーションのインストールが予想されます。 アプリケーションの開始時に、関連する入力を使用して RRS サービスへの呼び出しを実行します。 次にアプリケーションは、アプリケーションの実行を許可またはブロックするサービスからの検証応答を受信します。


## バッチ実行サービス (BES)

バッチ実行サービス (BES) は、データ レコードのバッチに対して、大量に非同期でスコアリングを処理するためのサービスです。 BES の入力には、blob、Azure のテーブル、SQL Azure、HDInsight (Hive クエリの結果など)、HTTP のソースなど、さまざまなソースからのレコードのバッチが含まれています。 BES の出力には、スコアの結果が含まれます。 結果は、Azure blob ストレージ内のファイルに出力し、記憶域のエンドポイントからのデータは、応答で返されます。

BES は、個人やモノのインターネット (IOT) の定期的にスケジュールされたスコア付けなど、応答がすぐには必要でない場合に役立ちます。

## 例
RRS と BES の両方の動作方法を示すために、Azure の Web サービスの例を使用します。 このサービスは、IOT (モノのインターネット) のシナリオで使用されます。 ここではシンプルにするために、デバイスは、`cog_speed` という値を 1 つのみ送信し、1 つの応答を取得します。

RR または BES のいずれかのサービスを呼び出すために必要な 4 つの情報があります。 この情報は、サービスのページからすぐに使用できる [Azure Machine Learning サービス ページ](https://studio.azureml.net) 実験がデプロイされるとします。 画面の左側にある Web サービスのリンクをクリックすると、デプロイされたサービスが表示されます。 特定のサービスに関する情報を検索するために、RRS と BES の両方の API ヘルプ ページのリンクがあります。

1.   **サービス API キー**, サービスのメインページで利用できます。
2.   **サービス URI**, 選択したサービスの API ヘルプ ページで利用できます。
3.  予期される **API 要求の本文**, 選択したサービスの API ヘルプ ページで利用できます。
4.  予期される **API 応答の本文**, 選択したサービスの API ヘルプ ページで利用できます。

次の 2 つの例では、必要なコードを説明するために c# 言語が使用されており、対象プラットフォームは、Windows 8 デスクトップです。

### RRS の例
URI のほかに、API ヘルプ ページで、定義とコード サンプルを入力および出力します。 API の入力が特にこのサービスに対して呼び出され、API 呼び出しのペイロードになります。

**要求のサンプル**

    {
      "Inputs": {
        "input1": {
          "ColumnNames": [
            "cog_speed"
          ],
          "Values": [
            [
              "0"
            ],
            [
              "1"
            ]
          ]
        }
      },
      "GlobalParameters": {}
    }


同様に、API の応答も特にこのサービスに対して呼び出されます。

**応答のサンプル**

    {
      "Results": {
        "output1": {
          "type": "DataTable",
          "value": {
            "ColumnNames": [
              "cog_speed"
            ],
            "ColumnTypes": [
              "Numeric"
            ].
          "Values": [
            [
              "0"
            ],
            [
              "1"
            ]
          ]
        }
      },
      "GlobalParameters": {}
    }

ページの下部に、コード例が表示されます。 c# の実装のコード サンプルを次に示します。

**サンプル コード**

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Formatting;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;

    namespace CallRequestResponseService
    {
        public class StringTable
        {
            public string[] ColumnNames { get; set; }
            public string[,] Values { get; set; }
        }

        class Program
        {
            static void Main(string[] args)
            {
                InvokeRequestResponseService().Wait();
            }

            static async Task InvokeRequestResponseService()
            {
                using (var client = new HttpClient())
                {
                    var scoreRequest = new
                    {
                        Inputs = new Dictionary<string, StringTable> () {
                            {
                                "input1",
                                new StringTable()
                                {
                                    ColumnNames = new string[] {"cog_speed"},
                                    Values = new string[,] {  { "0"},  { "1"}  }
                                }
                            },
                        GlobalParameters = new Dictionary<string, string>() { }
                    };

                    const string apiKey = "abc123"; // Replace this with the API key for the web service
                    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);

                    client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");

                    // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
                    // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
                    // For instance, replace code such as:
                    //      result = await DoSomeTask()
                    // with the following:
                    //      result = await DoSomeTask().ConfigureAwait(false)

                    HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                    if (response.IsSuccessStatusCode)
                    {
                        string result = await response.Content.ReadAsStringAsync();
                        Console.WriteLine("Result: {0}", result);
                    }
                    else
                    {
                        Console.WriteLine("Failed with status code: {0}", response.StatusCode);
                    }
                }
            }
        }
    }

### BES の例
API ヘルプ ページで、URI だけでなく、使用できるいくつかの呼び出しに関する情報が表示されます。 RR サービスとは異なり、BES サービスは非同期です。 つまり、BES API は単に実行対象のジョブをキューに登録し、呼び出し元はジョブの状態をポーリングして、完了すると表示します。 バッチ ジョブで現在サポートされている操作を次に示します。

1. バッチ ジョブの作成 (送信)
1. そのバッチ ジョブの開始
1. バッチ ジョブの状態または結果の取得
1. 実行中のバッチ ジョブのキャンセル

**1.バッチ実行ジョブを作成します。**

Azure Machine Learning サービス エンドポイントのバッチ ジョブを作成するときに、このバッチ実行を定義するいくつかのパラメーターを指定できます。

* **入力**: バッチ ジョブの入力場所への blob 参照が格納されているを表します。
* **GlobalParameters**: 実験用に定義できるグローバル パラメーターのセットを表します。 Azure Machine Learning の実験では、サービスの実行をカスタマイズする必須パラメーターとオプション パラメーターを使用できます。該当する場合、呼び出し元は必要なすべての必須パラメーターを指定する必要があります。 これらのパラメーターは、キーと値のペアのコレクションとして指定します。
* **出力**: 呼び出し元が、任意の Azure blob の場所にそれらをリダイレクトすることができます、サービスは、1 つ以上の出力を定義した場合。 これにより、サービスの出力を目的の場所に、予測可能な名前で保存できます。これを設定しなければ、出力 BLOB 名はランダムに生成されます。 **注** こと、サービスが必要ですが、出力コンテンツの型に基づいて、サポートされている式として保存されます。
  - データ セット出力: として保存できます **.csv、.tsv、.arff**
  - トレーニング済みモデル出力: として保存できます **.ilearner**

  出力先のオーバーライドは、のコレクションとして指定 *< 出力名、blob の参照 >* ペア、場所、 *出力名* 固有の出力ノード (サービスの API ヘルプ ページにも表示) は、ユーザー定義名と *blob 参照* 出力がリダイレクトされる Azure blob の場所への参照です。

これらのすべてのジョブ作成パラメーターは、サービスの性質によっては省略できます。 たとえば、入力ノードが定義されていないサービスの渡す必要はなくで、 *入力* パラメーターと出力の場所の上書き機能は完全に省略可能な出力は、Azure Machine Learning ワークスペース用に設定されている既定のストレージ アカウントに保存されます。 以下に、入力情報のみが渡されるサービスについて、REST API に渡されるサンプルの要求ペイロードを示します。

**要求のサンプル**

    {
      "Input": {
        "ConnectionString":     
        "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
        "RelativeLocation": "/mycontainer/mydatablob.csv",
        "BaseLocation": null,
        "SasBlobToken": null
      },
      "Outputs": null,
      "GlobalParameters": null
    }

バッチ ジョブの作成 API への応答で、ジョブに関連付けられていた一意のジョブ ID が返されます。 この ID は、他の操作に関してシステム内でこのジョブを参照するための唯一の手段であるため、非常に重要です。  

**応答のサンプル**

    "539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2.バッチ実行ジョブを開始します。**

だけに、システム内でそれが登録されに格納バッチ ジョブを作成、 *未開始* 状態です。 実際に実行するジョブをスケジュールするを呼び出す必要、 **開始** 、ジョブの作成時に、API エンドポイントの API ヘルプ ページし、ジョブ id を提供するサービスの説明が取得されます。

**3.バッチ実行ジョブのステータスを取得します。**

ジョブの ID を GetJobStatus API に渡して、非同期バッチ ジョブの状態をいつでもポーリングできます。 API 応答には、ジョブの現在の状態のインジケーターに加え、バッチ ジョブが正常に完了した場合は、その実際の結果も含まれます。 エラーの場合の詳細については、エラーの実際の理由が返される、 *詳細* プロパティです。

**応答ペイロード**

    {
        "StatusCode": STATUS_CODE,
        "Results": RESULTS,
        "Details": DETAILS
    }

*StatusCode* 、次のいずれかになります。

* Not started
* 実行中
* Failed
* Cancelled
* Finished

 *結果* プロパティは、ジョブが正常に完了した場合にのみ設定されます (これは、 **null** それ以外の場合)。 コレクションとして、結果を返すジョブの完了し、サービスが、少なくとも 1 つの出力ノードが定義されているかどうか、 *[出力名、blob の参照]* ペア、ここで、blob の参照は実際の結果を含む blob への SAS 読み取り専用リファレンスです。

**応答のサンプル**

    {
        "Status Code": "Finished",
        "Results":
        {
            "dataOutput":
            {              
                "ConnectionString": null,
                "RelativeLocation": "outputs/dataOutput.csv",
                "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
                "SasBlobToken": "?sv=2013-08-15&sr=b&sig=ABCD&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
            },
            "trainedModelOutput":
            {              
                "ConnectionString": null,
                "RelativeLocation": "models/trainedModel.ilearner",
                "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
                "SasBlobToken": "?sv=2013-08-15&sr=b&sig=EFGH%3D&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
            },           
        },
        "Details": null
    }

**4.バッチ実行ジョブをキャンセルします。**

実行中のバッチ ジョブは、指定した CancelJob API を呼び出し、ジョブの ID で渡すことで、いつでもキャンセルできます。 これは、ジョブが完了までにかかる時間が長すぎるなど、様々な理由で実行されます。



#### 使用して、 [BES SDK](machine-learning-consume-web-services.md#batch-execution-service-sdk)

 [BES SDK Nugget パッケージ](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/) をバッチ モードでスコアを付けるための呼び出し元の BES を簡略化する機能を提供します。 Nuget パッケージをインストールするには、Visual Studio で [ツール] に移動し、[Nuget パッケージ マネージャー] を選択して、[Package Manager Console] をクリックします。

Web サービスとしてデプロイされる Azure ML の実験には Web サービス入力モジュールを含めることができます。つまり、BLOB の場所への参照という形式で Web サービスの呼び出しを行い、入力値を指定する必要があります。 また、Web サービス入力モジュールを使用せずに、リーダー モジュールを使用することもできます。 この場合、リーダーは通常、実行時にクエリを使用して SQL DB から読み取りを行い、データを取得します。 Web サービス パラメーターを使用して、他のサーバーやテーブルなどを動的に指すことができます。SDK ではこれらの両方のパターンがサポートされています。

次のサンプル コードでは、BES SDK を使用して Azure Machine Learning サービス エンドポイントに対してバッチ ジョブのサブミットおよび監視を行う方法が示されています。 設定と呼び出しの詳細については、コメントを確認してください。

#### **サンプル コード**

    // This code requires the Nuget package Microsoft.Azure.MachineLearning to be installed.
    // Instructions for doing this in Visual Studio:
    // Tools -> Nuget Package Manager -> Package Manager Console
    // Install-Package Microsoft.Azure.MachineLearning

      using System;
      using System.Collections.Generic;
      using System.Threading.Tasks;

      using Microsoft.Azure.MachineLearning;
      using Microsoft.Azure.MachineLearning.Contracts;
      using Microsoft.Azure.MachineLearning.Exceptions;

    namespace CallBatchExecutionService
    {
        class Program
        {
            static void Main(string[] args)
            {               
                InvokeBatchExecutionService().Wait();
            }

            static async Task InvokeBatchExecutionService()
            {
                // First collect and fill in the URI and access key for your web service endpoint.
                // These are available on your service's API help page.
                var endpointUri = "https://ussouthcentral.services.azureml.net/workspaces/YOUR_WORKSPACE_ID/services/YOUR_SERVICE_ENDPOINT_ID/";
                string accessKey = "YOUR_SERVICE_ENDPOINT_ACCESS_KEY";

                // Create an Azure Machine Learning runtime client for this endpoint
                var runtimeClient = new RuntimeClient(endpointUri, accessKey);

                // Define the request information for your batch job. This information can contain:
                // -- A reference to the AzureBlob containing the input for your job run
                // -- A set of values for global parameters defined as part of your experiment and service
                // -- A set of output blob locations that allow you to redirect the job's results

                // NOTE: This sample is applicable, as is, for a service with explicit input port and
                // potential global parameters. Also, we choose to also demo how you could override the
                // location of one of the output blobs that could be generated by your service. You might
                // need to tweak these features to adjust the sample to your service.
                //
                // All of these properties of a BatchJobRequest shown below can be optional, depending on
                // your service, so it is not required to specify all with any request.  If you do not want to
                // use any of the parameters, a null value should be passed in its place.

                // Define the reference to the blob containing your input data. You can refer to this blob by its
                    // connection string / container / blob name values; alternatively, we also support references
                    // based on a blob SAS URI

                    BlobReference inputBlob = BlobReference.CreateFromConnectionStringData(connectionString:                                         "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                        containerName: "YOUR_CONTAINER_NAME",
                        blobName: "YOUR_INPUT_BLOB_NAME");

                    // If desired, one can override the location where the job outputs are to be stored, by passing in
                    // the storage account details and name of the blob where we want the output to be redirected to.

                    var outputLocations = new Dictionary<string, BlobReference>
                        {
                          {
                           "YOUR_OUTPUT_NODE_NAME",
                           BlobReference.CreateFromConnectionStringData(                                     connectionString: "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                                containerName: "YOUR_CONTAINER_NAME",
                                blobName: "YOUR_DESIRED_OUTPUT_BLOB_NAME")
                           }
                        };

                // If applicable, you can also set the global parameters for your service
                var globalParameters = new Dictionary<string, string>
                {
                    { "YOUR_GLOBAL_PARAMETER", "PARAMETER_VALUE" }
                };

                var jobRequest = new BatchJobRequest
                {
                    Input = inputBlob,
                    GlobalParameters = globalParameters,
                    Outputs = outputLocations
                };

                try
                {
                    // Register the batch job with the system, which will grant you access to a job object
                    BatchJob job = await runtimeClient.RegisterBatchJobAsync(jobRequest);

                    // Start the job to allow it to be scheduled in the running queue
                    await job.StartAsync();

                    // Wait for the job's completion and handle the output
                    BatchJobStatus jobStatus = await job.WaitForCompletionAsync();
                    if (jobStatus.JobState == JobState.Finished)
                    {
                        // Process job outputs
                        Console.WriteLine(@"Job {0} has completed successfully and returned {1} outputs", job.Id, jobStatus.Results.Count);
                        foreach (var output in jobStatus.Results)
                        {
                            Console.WriteLine(@"\t{0}: {1}", output.Key, output.Value.AbsoluteUri);
                        }
                    }
                    else if (jobStatus.JobState == JobState.Failed)
                    {
                        // Handle job failure
                        Console.WriteLine(@"Job {0} has failed with this error: {1}", job.Id, jobStatus.Details);
                    }
                }
                catch (ArgumentException aex)
                {
                    Console.WriteLine("Argument {0} is invalid: {1}", aex.ParamName, aex.Message);
                }
                catch (RuntimeException runtimeError)
                {
                    Console.WriteLine("Runtime error occurred: {0} - {1}", runtimeError.ErrorCode, runtimeError.Message);
                    Console.WriteLine("Error details:");
                    foreach (var errorDetails in runtimeError.Details)
                    {
                        Console.WriteLine("\t{0} - {1}", errorDetails.Code, errorDetails.Message);
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Unexpected error occurred: {0} - {1}", ex.GetType().Name, ex.Message);
                }
            }
        }
    }

