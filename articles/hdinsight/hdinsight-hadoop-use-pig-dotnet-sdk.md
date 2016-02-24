<properties
   pageTitle="HDInsight で Hadoop Pig と .NET の使用 | Microsoft Azure"
   description=".NET SDK for Hadoop を使用して HDInsight で Hadoop に Pig ジョブを送信する方法を説明します。"
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>

#HDInsight で .NET SDK for Hadoop を使用した Pig ジョブの実行

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

このドキュメントでは、.NET SDK for Hadoop を使用して HDInsight クラスターで Hadoop に Pig ジョブを送信する方法の例を紹介します。

HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。 Pig では、一連のデータ変換をモデル化することで MapReduce 操作を作成できます。 基本的な C# アプリケーションを使用して、Pig ジョブを HDInsight クラスターに送信する方法を説明します。

[AZURE.INCLUDE [azure-portal](../../includes/hdinsight-azure-portal.md)]

* [HDInsight で .NET SDK for Hadoop を使用した Pig ジョブの実行](hdinsight-hadoop-use-pig-dotnet-sdk-v1.md)

##<a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Azure HDInsight (HDInsight での Hadoop) クラスター (Windows または Linux ベースのいずれか)

* Visual Studio 2012 または 2013 または 2015

##<a id="certificate"></a>管理証明書の作成

Azure HDInsight にアプリケーションを認証するには、自己署名証明書を作成して開発ワークステーションにインストールすると同時に、それを Azure サブスクリプションにもアップロードする必要があります。

この方法に関する手順については、「[自己署名証明書の作成](http://go.microsoft.com/fwlink/?LinkId=511138)」をご覧ください。

> [AZURE.NOTE] 証明書を作成するときに必ずを使用すると、わかりやすい名前を付けて、その後に使用されます。

##<a id="subscriptionid"></a>サブスクリプション ID の検索

各 Azure サブスクリプションは、サブスクリプション ID と呼ばれる GUID で識別されます。 次の手順に従って、この値を検索します。

1. [Azure ポータル][preview-portal] にアクセスします。

2. ポータルの左側のバーで、次のように選択します。 __すべて参照__, 選択してから、 __サブスクリプション__ から、 __参照__ ブレードです。

3. 内にある情報から、 __サブスクリプション__ ブレードで、使用しての値をメモするサブスクリプションを検索、 **サブスクリプション ID** 列です。

後で使用するため、サブスクリプション ID を書き留めます。

##<a id="create"></a>アプリケーションを作成する

1. Visual Studio 2012 または 2013 を開く
2.  **ファイル** メニューの [ **新規** し、[ **プロジェクト**します。
3. 新しいプロジェクトの場合は、次の値を入力または選択します。

    <table>
    <tr>
    <th>プロパティ</th>
    <th>値</th>
    </tr>
    <tr>
    <th>カテゴリ</th>
    <th>テンプレート/Visual C#/Windows</th>
    </tr>
    <tr>
    <th>テンプレート</th>
    <th>コンソール アプリケーション</th>
    </tr>
    <tr>
    <th>名前</th>
    <th>SubmitPigJob</th>
    </tr>
    </table>
4. クリックして **OK** プロジェクトを作成します。
5.  **ツール** メニューの [ **ライブラリ パッケージ マネージャー** または **Nuget パッケージ マネージャー**, 、し、[ **パッケージ マネージャー コンソール**します。
6. コンソールで次のコマンドを実行して、.NET SDK パッケージをインストールします。

        Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

7. ソリューション エクスプ ローラーでダブルクリック **Program.cs** を開きます。 既存のコードを次のコードに置き換えます。

        using System;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
        
        namespace HDInsightSubmitPigJobsDotNet
        {
            class Program
            {
                static void Main(string[] args)
                {
                    var ExistingClusterName = "<HDInsightClusterName>";
                    var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                    var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
                    var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
        
                    // The Pig Latin statements to run
                    string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                        "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                        "RESULT = order FREQUENCIES by COUNT desc;" +
                        "DUMP RESULT;";
        
        
                    HDInsightJobManagementClient _hdiJobManagementClient;
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
        
                    // Define the Pig job
                    var parameters = new PigJobSubmissionParameters()
                    {
                        UserName = ExistingClusterUsername,
                        Query = queryString,
                    };
        
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                    Console.WriteLine("Press ENTER to continue ...");
                    Console.ReadLine();
                }
            }
        }

7. キーを押して **f5 キーを押して** アプリケーションを起動します。
8. キーを押して **ENTER** アプリケーションを終了します。

##<a id="summary"></a>概要

このように、.NET SDK for Hadoop では Pig ジョブを HDInsight クラスターに送信する .NET アプリケーションを作成して、ジョブのステータスを監視し、出力を取得できます。

##<a id="nextsteps"></a>次のステップ

HDInsight での Pig に関する全般的な情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

* [HDInsight での Hadoop の MapReduce を使用します。](hdinsight-use-mapreduce.md)
[プレビュー ポータル]: https://portal.azure.com/
