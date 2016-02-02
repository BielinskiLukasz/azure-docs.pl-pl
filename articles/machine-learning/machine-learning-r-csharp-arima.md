<properties 
    pageTitle="予測 - 自己回帰統合の移動平均 (ARIMA) | Microsoft Azure" 
    description="予測 - 自己回帰統合の移動平均 (ARIMA)" 
    services="machine-learning" 
    documentationCenter="" 
    authors="yijichen" 
    manager="paulettm" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/01/2015" 
    ms.author="yijichen"/> 



# 予測 - 自己回帰統合の移動平均 (ARIMA)

これは、 [サービス](https://datamarket.azure.com/dataset/aml_labs/arima) 実装自己回帰統合の移動平均 (ARIMA)、ユーザーが指定した履歴データに基づいて予測を生成します。 今年、特定の製品の需要が増加するでしょうか。 クリスマス シーズンの製品販売を予測して、効果的な在庫計画を策定できるでしょうか。 予測モデルは、このような質問に答えることができます。 これらのモデルは、過去のデータを指定すると、隠れた傾向や季節性を検証し、将来の傾向を予測します。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できます。 この Web サービスのもう 1 つの目的は、Azure Machine Learning を使用して R コード上に Web サービスを作成する方法の例を示すことです。 数行の R コードを記述し、Azure Machine Learning Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。 この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。

## Web サービスの使用

このサービスは、4 つの引数を使用して、ARIMA 予測を計算します。
入力引数は、次のとおりです。

* Frequency - 生データの頻度 (毎日/毎週/毎月/毎四半期/毎年) を示します。
* Horizon - 将来の予測時間枠
* Date - 時間の新しい時系列データを追加します。
* Value - 新しい時系列データの値を追加します。

このサービスの出力は、計算された予測値です。

入力例は以下のとおりです。

* Frequency - 12
* Horizon - 12
* 日付 - 2012 1 月 15 日、2012 2 月 15 日、3/15/2012年; 2012 4 月 15 日; 2012 5 月 15 日; 2012 6 月 15 日; 2012 月 15 日 7、8/2012 年 15 日、9/15/2012年、2012 10 月 15 日; 2012 11 月 15 日、12/15/2012 です。
1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013;
1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* 値 - 3.479; 3.68; 3.832; 3.941; 3.797; 3.586; 3.508; 3.731; 3.915; 3.844; 3.634; 3.549; 3.557; 3.785; 3.782; 3.601; 3.544; 3.556; 3.65; 3.709; 3.682; 3.511 です。
3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509

>Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST や GET メソッドによって呼び出すことができます。

自動でサービスの利用の複数の方法があります (アプリケーション例が [ここ](http://microsoftazuremachinelearning.azurewebsites.net/ArimaForecasting.aspx))します。

### Web サービスを使用する C# コードを開始します。

    public class Input
    {
        public string frequency;
        public string horizon;
        public string date;
        public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
         byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
         return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    
    void Main()
    {
        var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
        var json = JsonConvert.SerializeObject(input);
        var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
    
        var httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        var query = httpClient.PostAsync(acitionUri,new StringContent(json));
        var result = query.Result.Content;
        var scoreResult = result.ReadAsStringAsync().Result;
    }

## Web サービスの作成

>この Web サービスは、Azure Machine Learning を使用して作成されました。 無料評価版として入門ビデオ実験を作成し、 [web サービスを公開](machine-learning-publish-a-machine-learning-web-service.md), を参照してください [azure.com/ml](http://azure.com/ml)します。 Web サービスを作成した実験のスクリーン ショット、および実験内の各モジュールに対するコード例を以下に示します。

Azure Machine Learning 内で、新しい空白の実験が作成されました。 入力データのサンプルは、事前定義済みのデータ スキーマにアップロードされました。 データにリンクされているスキーマは、 [R スクリプトの実行 ][execute-r-script] ARIMA の予測 R. からされ' と 'forecast' 関数を使用してモデルを生成するモジュール

### 実験フロー:

![ワークスペースの作成][2]

#### モジュール 1:

    # Add in the CSV file with the data in the format shown below 

![ワークスペースの作成][3]

#### モジュール 2:

    # data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)
    
    # preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]
    
    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)
    
    # fit a time-series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- auto.arima(train_ts)
    train_model <- forecast(fit1, h = data$horizon)
    plot(train_model)
    
    # produce forecasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
    
    # data output
    maml.mapOutputPort("data.forecast");

## 制限事項

これは、ARIMA 予測のきわめて簡単な例です。 上のコード例からわかるように、エラーのキャッチは実装されません。このサービスは、すべての変数が連続した正の値で、頻度を 1 より大きい整数にする必要があります。 日付と値のベクターの長さは同じにします。 日付変数の形式は、'/mm/dd/yyyy' とします。

## FAQ

Web サービスや marketplace への発行の使用に関するよく寄せられる質問は、次を参照してください。 [ここ](machine-learning-marketplace-faq.md)します。



[1]: ./media/machine-learning-r-csharp-arima/arima-img1.png 
[2]: ./media/machine-learning-r-csharp-arima/arima-img2.png 
[3]: ./media/machine-learning-r-csharp-arima/arima-img3.png 
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/ 

