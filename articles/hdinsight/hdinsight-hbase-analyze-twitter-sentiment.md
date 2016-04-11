<properties 
    pageTitle="HBase で Twitter のセンチメントをリアルタイム分析する | Microsoft Azure" 
    description="HDInsight (Hadoop) クラスターで HBase を使用し、Twitter からリアルタイムでビックデータのセンチメントを分析する方法について取り上げます。" 
    services="hdinsight" 
    documentationCenter="" 
    authors="mumian" 
    manager="paulettm" 
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/02/2015" 
    ms.author="jgao"/>

# HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析する

方法については、リアルタイム [センチメント分析](http://en.wikipedia.org/wiki/Sentiment_analysis) HDInsight (Hadoop) クラスターで HBase を使用して Twitter からのビッグ データのです。


ビッグ データの多くはソーシャル Web サイトからもたらされます。 Twitter などのサイトが公開している API を介して収集したデータは、現在の動向を分析して把握するための有益な情報源となります。 このチュートリアルでは、以下の事柄を実行するためのコンソール ストリーミング サービス アプリケーションと ASP.NET Web アプリケーションを開発します。

![][img-app-arch]

- ストリーミングのアプリケーション
    - Twitter streaming API を使用して、ジオタグ付けされたツイートをリアルタイムで取得します。
    - そうしたツイートのセンチメントを評価します。
    - センチメント情報を、Microsoft HBase SDK を使用して HBase に格納します。
- Azure Web サイト アプリケーション
    - リアルタイム統計結果を ASP.NET Web アプリケーションを使用して Bing マップにプロットします。 ツイートを視覚化すると、次のようになります。

    ![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
    
    特定のキーワードを使用してツイートを照会し、ツイートで表明された意見の感情が肯定的、否定的、中立のどれであるかを取得できます。

Visual Studio ソリューションの完全なサンプルが GitHub にあります: [リアルタイム ソーシャル センチメン分析アプリ](https://github.com/maxluk/tweet-sentiment)します。































##<a id="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **HDInsight での HBase クラスター**します。 クラスターのプロビジョニング方法については、「  [HBase HDInsight での Hadoop を使い始める] [hbase-get-started]します。 このチュートリアルを読み進めるには、次のデータが必要です。


    <table border="1">
    <tr><th>Cluster property</th><th>Description</th></tr>
    <tr><td>HBase cluster name</td><td>Your HDInsight HBase cluster name. For example: https://myhbase.azurehdinsight.net/</td></tr>
    <tr><td>Cluster user name</td><td>The Hadoop user account name. The default Hadoop user name is <strong>admin</strong>.</td></tr>
    <tr><td>Cluster user password</td><td>The Hadoop cluster user password.</td></tr>
    </table>

- **ワークステーション** Visual Studio 2013 がインストールされているとします。 手順については、次を参照してください。 [Visual Studio のインストール](http://msdn.microsoft.com/library/e2h7fzkw.aspx)します。





##<a id="twitter"></a>Twitter アプリケーション ID とシークレットを作成する

Twitter streaming Api を使用して [OAuth](http://oauth.net/) 要求を承認します。 OAuth を使用するための最初の手順は、Twitter 開発者サイトで新しいアプリケーションを作成することです。

**Twitter アプリケーション ID とシークレットを作成するには**

1. サインイン [Twitter アプリ](https://apps.twitter.com/)します。 クリックして、 **今すぐサインアップ** Twitter アカウントを持っていない場合は、リンクします。
2. クリックして **新しいアプリを作成する**です。
3. 入力、 **名前**, 、**説明**, 、および **web サイト**します。 [Website] フィールドは実際には使用しません。 有効な URL である必要はありません。 次のテーブルは使用する値のサンプルを示しています。

    <table border="1">
    <tr><th>フィールド</th><th>値</th></tr>
    <tr><td>名前</td><td>MyHDInsightHBaseApp</td></tr>
    <tr><td>説明</td><td>MyHDInsightHBaseApp</td></tr>
    <tr><td>Web サイト</td><td>http://www.myhdinsighthbaseapp.com</td></tr>
    </table>

    > [AZURE.NOTE] Twitter アプリケーションの名前は一意の名前である必要があります。  

4. 確認 **Yes, I agree**, 、] をクリックし、 **Twitter アプリケーションを作成する**です。
5. クリックして、 **権限** ] タブをクリックします。 既定のアクセス許可は **読み取り専用**します。 このチュートリアルにはこれで十分です。 
6. クリックして、 **Keys and Access Tokens** ] タブをクリックします。
7. クリックして **、アクセス トークンを作成**します。
8. クリックして **Test OAuth** ページの右上隅にします。
9. コピー、 **コンシューマー キー**, 、**コンシューマー シークレット**, 、**アクセス トークン**, 、および **アクセス トークン シークレット** 値。 これらの値は後で必要になります。

    ![hdi.hbase.twitter.sentiment.twitter.app][img-twitter-app]






























##<a id="streaming"></a>簡単な Twitter ストリーミング サービスを作成する

ツイートを取得し、ツイート センチメント スコアを計算し、処理したツイート ワードを HBase に送信するコンソール アプリケーションを作成する必要があります。

**センチメント辞書ファイルをダウンロードするには**

1. 参照 [リアルタイム ソーシャル センチメン分析アプリ](https://github.com/maxluk/tweet-sentiment)します。
2. クリックして **ZIP のダウンロード**します。
3. このファイルをローカルに解凍します。
4. パスをコピー、 **./tweet-sentiment/SimpleStreamingService/data/dictionary/dictionary.tsv** ファイルです。 この情報は後でアプリケーションで必要になります。

**Visual Studio ソリューションを作成するには**

1. 開いている **Visual Studio**します。
2.  **ファイル** ] メニューをポイント **新規**, 、クリックして **プロジェクト**します。
3. 次の値を入力または選択します。

    - テンプレートの場合: **Visual C# の場合と Windows デスクトップ/コンソール アプリケーション**
    - [名前]: **TweetSentimentStreaming** 
    - 場所: **C:\Tutorials**
    - ソリューション名: **TweetSentimentStreaming**

4. クリックして **OK** を続行します。
 


**Nuget パッケージをインストールして SDK 参照を追加するには**

1.  **ツール** ] メニューのをクリックして **Nuget パッケージ マネージャー**, 、クリックして **パッケージ マネージャー コンソール**します。 コンソール パネルがページ下部で開きます。
2. インストールする、次のコマンドを使用して、 [HBase .NET SDK](https://www.nuget.org/packages/Microsoft.HBase.Client/) HBase クラスターにアクセスするクライアント ライブラリでは、パッケージ、および [Tweetinvi API](https://www.nuget.org/packages/TweetinviAPI/) パッケージは、Twitter API にアクセスするために使用します。

        Install-Package Microsoft.HBase.Client
        Install-Package TweetinviAPI
    
3.  **ソリューション エクスプ ローラー**, を右クリックして **参照**, 、クリックして **参照の追加**します。
4. 左側のウィンドウで展開 **アセンブリ**, 、クリックして **Framework**します。
5. 右側のウィンドウでの前にチェック ボックスをオンに **System.Configuration**, 、クリックして **OK**します。



**Tweeter ストリーミング サービス クラスを定義するには**

1.  **ソリューション エクスプ ローラー**, を右クリックして **TweetSentimentStreaming**, 、] をポイント **追加**, 、クリックして **クラス**します。
2.  **名前** フィールドに「 **HBaseWriter**, 、クリックしてして **追加**します。
3.  **HBaseWriter.cs**, 、次の追加 **を使用して** ステートメントをファイルの先頭にします。

        using System.IO;        
        using System.Threading;
        using System.Globalization;
        using Microsoft.HBase.Client;
        using Tweetinvi.Core.Interfaces;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

4. 内部 **HbaseWriter.cs**, 、クラスの新しい呼び出しを追加 **DictionaryItem**:

        public class DictionaryItem
        {
            public string Type { get; set; }
            public int Length { get; set; }
            public string Word { get; set; }
            public string Pos { get; set; }
            public string Stemmed { get; set; }
            public string Polarity { get; set; }
        }

    このクラス構造は、センチメント辞書ファイルを解析するために使用します。 このデータは、それぞれのツイートのセンチメント スコアを計算するときに使用します。

5. 内部、 **HBaseWriter** クラスを次の定数と変数を定義します。

        // HDinsight HBase cluster and HBase table information
        const string CLUSTERNAME = "https://<HBaseClusterName>.azurehdinsight.net/";
        const string HADOOPUSERNAME = "<HadoopUserName>"; //the default name is "admin"
        const string HADOOPUSERPASSWORD = "<HaddopUserPassword>";
        const string HBASETABLENAME = "tweets_by_words";

        // Sentiment dictionary file and the punctuation characters
        const string DICTIONARYFILENAME = @"..\..\data\dictionary\dictionary.tsv";
        private static char[] _punctuationChars = new[] { 
            ' ', '!', '\"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.

        // For writting to HBase
        HBaseClient client;

        // a sentiment dictionary for estimate sentiment. It is loaded from a physical file.
        Dictionary<string, DictionaryItem> dictionary;
        
        // use multithread write
        Thread writerThread;
        Queue<ITweet> queue = new Queue<ITweet>();
        bool threadRunning = true;

6. などの定数値を設定 **& lt;HBaseClusterName >**, 、**& lt;HadoopUserName >**, 、および **& lt;HaddopUserPassword >**します。 HBase テーブル名を変更する場合には、それに応じて Web アプリケーション内のテーブル名も変更しなければなりません。

    このチュートリアルで後ほど、dictionary.tsv ファイルをダウンロードして特定のフォルダーに移動します。

7. 内の次の関数を定義する、 **HBaseWriter** クラス。

        // This function connects to HBase, loads the sentiment dictionary, and starts the thread for writting.
        public HBaseWriter()
        {
            ClusterCredentials credentials = new ClusterCredentials(new Uri(CLUSTERNAME), HADOOPUSERNAME, HADOOPUSERPASSWORD);
            client = new HBaseClient(credentials);

            // create the HBase table if it doesn't exist
            if (!client.ListTables().name.Contains(HBASETABLENAME))
            {
                TableSchema tableSchema = new TableSchema();
                tableSchema.name = HBASETABLENAME;
                tableSchema.columns.Add(new ColumnSchema { name = "d" });
                client.CreateTable(tableSchema);
                Console.WriteLine("Table \"{0}\" is created.", HBASETABLENAME);
            }

            // Load sentiment dictionary from a file
            LoadDictionary();

            // Start a thread for writting to HBase
            writerThread = new Thread(new ThreadStart(WriterThreadFunction));
            writerThread.Start();
        }

        ~HBaseWriter()
        {
            threadRunning = false;
        }

        // Enqueue the Tweets received
        public void WriteTweet(ITweet tweet)
        {
            lock (queue)
            {
                queue.Enqueue(tweet);
            }
        }

        // Load sentiment dictionary from a file
        private void LoadDictionary()
        {
            List<string> lines = File.ReadAllLines(DICTIONARYFILENAME).ToList();
            var items = lines.Select(line =>
            {
                var fields = line.Split('\t');
                var pos = 0;
                return new DictionaryItem
                {
                    Type = fields[pos++],
                    Length = Convert.ToInt32(fields[pos++]),
                    Word = fields[pos++],
                    Pos = fields[pos++],
                    Stemmed = fields[pos++],
                    Polarity = fields[pos++]
                };
            });

            dictionary = new Dictionary<string, DictionaryItem>();
            foreach (var item in items)
            {
                if (!dictionary.Keys.Contains(item.Word))
                {
                    dictionary.Add(item.Word, item);
                }
            }
        }

        // Calculate sentiment score
        private int CalcSentimentScore(string[] words)
        {
            Int32 total = 0;
            foreach (string word in words)
            {
                if (dictionary.Keys.Contains(word))
                {
                    switch (dictionary[word].Polarity)
                    {
                        case "negative": total -= 1; break;
                        case "positive": total += 1; break;
                    }
                }
            }
            if (total > 0)
            {
                return 1;
            }
            else if (total < 0)
            {
                return -1;
            }
            else
            {
                return 0;
            }
        }

        // Popular a CellSet object to be written into HBase
        private void CreateTweetByWordsCells(CellSet set, ITweet tweet)
        {
            // Split the Tweet into words
            string[] words = tweet.Text.ToLower().Split(_punctuationChars);

            // Calculate sentiment score base on the words
            int sentimentScore = CalcSentimentScore(words);
            var word_pairs = words.Take(words.Length - 1)
                                  .Select((word, idx) => string.Format("{0} {1}", word, words[idx + 1]));
            var all_words = words.Concat(word_pairs).ToList();

            // For each word in the Tweet add a row to the HBase table
            foreach (string word in all_words)
            {
                string time_index = (ulong.MaxValue - (ulong)tweet.CreatedAt.ToBinary()).ToString().PadLeft(20) + tweet.IdStr;
                string key = word + "_" + time_index;

                // Create a row
                var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };

                // Add columns to the row, including Tweet identifier, language, coordinator(if available), and sentiment 
                var value = new Cell { column = Encoding.UTF8.GetBytes("d:id_str"), data = Encoding.UTF8.GetBytes(tweet.IdStr) };
                row.values.Add(value);
                
                value = new Cell { column = Encoding.UTF8.GetBytes("d:lang"), data = Encoding.UTF8.GetBytes(tweet.Language.ToString()) };
                row.values.Add(value);
                
                if (tweet.Coordinates != null)
                {
                    var str = tweet.Coordinates.Longitude.ToString() + "," + tweet.Coordinates.Latitude.ToString();
                    value = new Cell { column = Encoding.UTF8.GetBytes("d:coor"), data = Encoding.UTF8.GetBytes(str) };
                    row.values.Add(value);
                }

                value = new Cell { column = Encoding.UTF8.GetBytes("d:sentiment"), data = Encoding.UTF8.GetBytes(sentimentScore.ToString()) };
                row.values.Add(value);

                set.rows.Add(row);
            }
        }

        // Write a Tweet (CellSet) to HBase
        public void WriterThreadFunction()
        {
            try
            {
                while (threadRunning)
                {
                    if (queue.Count > 0)
                    {
                        CellSet set = new CellSet();
                        lock (queue)
                        {
                            do
                            {
                                ITweet tweet = queue.Dequeue();
                                CreateTweetByWordsCells(set, tweet);
                            } while (queue.Count > 0);
                        }

                        // Write the Tweet by words cell set to the HBase table
                        client.StoreCells(HBASETABLENAME, set);
                        Console.WriteLine("\tRows written: {0}", set.rows.Count);
                    }
                    Thread.Sleep(100);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Exception: " + ex.Message);
            }
        }

    このコードにより、以下の機能が提供されます。

    - **Hbase [HBaseWriter()] への接続**: を作成する HBase SDK を使用して、 *ClusterCredentials* オブジェクトのクラスター URL と Hadoop ユーザー資格情報を使用し、作成、 *HBaseClient* ClusterCredentials オブジェクトを使用してオブジェクトです。
    - **HBase テーブル [HBaseWriter()] の作成**: メソッド呼び出しは *HBaseClient.CreateTable()*します。
    - **HBase テーブル [WriterThreadFunction()] への書き込み**: メソッド呼び出しは *HBaseClient.StoreCells()*します。

**Program.cs を完成させるには**

1.  **ソリューション エクスプ ローラー**, をダブルクリックして **Program.cs** を開きます。
2. ファイルの先頭には、次のコードを追加 **を使用して** ステートメント。

        using System.Configuration;
        using System.Diagnostics;
        using Tweetinvi;

3. 内部、 **プログラム** クラスを次の定数を定義します。

        const string TWITTERAPPACCESSTOKEN = "<TwitterApplicationAccessToken";
        const string TWITTERAPPACCESSTOKENSECRET = "TwitterApplicationAccessTokenSecret";
        const string TWITTERAPPAPIKEY = "TwitterApplicationAPIKey";
        const string TWITTERAPPAPISECRET = "TwitterApplicationAPISecret";

4. 使用している Twitter アプリケーション値と一致するように定数値を設定します。

3. 変更、 **Main()** 関数を次のようにします。

        static void Main(string[] args)
        {
            TwitterCredentials.SetCredentials(TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET, TWITTERAPPAPIKEY, TWITTERAPPAPISECRET);

            Stream_FilteredStreamExample();
        }

4. 以下の関数をこのクラスに追加します。

        private static void Stream_FilteredStreamExample()
        {
            for (; ; )
            {
                try
                {
                    HBaseWriter hbase = new HBaseWriter();
                    var stream = Stream.CreateFilteredStream();
                    stream.AddLocation(Geo.GenerateLocation(-180, -90, 180, 90));

                    var tweetCount = 0;
                    var timer = Stopwatch.StartNew();

                    stream.MatchingTweetReceived += (sender, args) =>
                    {
                        tweetCount++;
                        var tweet = args.Tweet;

                        // Write Tweets to HBase
                        hbase.WriteTweet(tweet);

                        if (timer.ElapsedMilliseconds > 1000)
                        {
                            if (tweet.Coordinates != null)
                            {
                                Console.ForegroundColor = ConsoleColor.Green;
                                Console.WriteLine("\n{0}: {1} {2}", tweet.Id, tweet.Language.ToString(), tweet.Text);
                                Console.ForegroundColor = ConsoleColor.White;
                                Console.WriteLine("\tLocation: {0}, {1}", tweet.Coordinates.Longitude, tweet.Coordinates.Latitude);
                            }

                            timer.Restart();
                            Console.WriteLine("\tTweets/sec: {0}", tweetCount);
                            tweetCount = 0;
                        }
                    };

                    stream.StartStreamMatchingAllConditions();
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Exception: {0}", ex.Message);
                }
            }
        }



**ストリーム サービスを実行するには**

1. Visual Studio からキーを押して **f5 キーを押して**します。 コンソール アプリケーションのスクリーンショットは次のようになります。

    ![hdinsight.hbase.twitter.sentiment.streaming.service][img-streaming-service]
2. Web アプリケーションの作成中はストリーミング コンソール アプリケーションを実行したままにし、さらに多くのデータを使用できるようにしてください。



























##<a id="web"></a>Twitter センチメントを視覚化する Azure Website で Web サイトを作成する

このセクションでは、リアルタイムのセンチメント データを HBase から読み取り、データを Bing マップにプロットする ASP.NET MVC Web アプリケーションを作成します。

**ASP.NET MVC Web アプリケーションを作成するには**

1. Visual Studio を開きます。
2. クリックして **ファイル**, 、] をクリックして **新規**, 、] をクリックし、 **プロジェクト**します。
3. 次の情報を入力します。

    - テンプレート カテゴリ: **Visual c#/Web**
    - テンプレートの場合: **ASP.NET Web アプリケーション**
    - [名前]: **[tweetsentimentweb]**
    - 場所: **C:\Tutorials** 
4. Click **OK**.
5.  **テンプレートを選択して**, 、クリックして **MVC**します。 
6.  **Microsoft Azure**, 、クリックして **サブスクリプションの管理**します。
7.  **Microsoft Azure サブスクリプションの管理**, 、クリックして **サインイン**します。
8. Azure の資格情報を入力します。 Azure サブスクリプション情報が表示される、 **アカウント** ] タブをクリックします。
9. をクリックして **閉じる** を閉じる、 **Microsoft Azure サブスクリプションの管理** ウィンドウです。
10.  **新しい ASP.NET プロジェクト - TweetSentimentWeb**, 、クリックして **OK**します。
11.  **Microsoft Azure サイト設定の構成**, を選択、 **地域** に最も近いです。 データベース サーバーを指定する必要はありません。 
12. Click **OK**.

**Nuget パッケージをインストールするには**

1.  **ツール** ] メニューのをクリックして **Nuget パッケージ マネージャー**, 、クリックして **パッケージ マネージャー コンソール**します。 コンソール パネルがページ下部で開きます。
2. インストールする次のコマンドを使用して、 [HBase .NET SDK](https://www.nuget.org/packages/Microsoft.HBase.Client/) パッケージは、HBase クラスターにアクセスするクライアント ライブラリ。

        Install-Package Microsoft.HBase.Client 

**HBaseReader クラスを追加するには**

1.  **ソリューション エクスプ ローラー**, 、展開 **[TweetSentiment**します。
2. 右クリック **モデル**, 、クリックして **追加**, 、順にクリック **クラス**します。
3.  **名前** フィールドに「 **HBaseReader.cs**, 、クリックしてして **追加**します。
4. コードを次のコードに置き換えます。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        
        using System.Configuration;
        using System.Threading.Tasks;
        using System.Text;
        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;
        
        namespace TweetSentimentWeb.Models
        {
            public class HBaseReader
            {
                // For reading Tweet sentiment data from HDInsight HBase
                HBaseClient client;
        
                // HDinsight HBase cluster and HBase table information
                const string CLUSTERNAME = "<HBaseClusterName>";
                const string HADOOPUSERNAME = "<HBaseClusterHadoopUserName>"
                const string HADOOPUSERPASSWORD = "<HBaseCluserUserPassword>";
                const string HBASETABLENAME = "tweets_by_words";
        
                // The constructor
                public HBaseReader()
                {
                    ClusterCredentials creds = new ClusterCredentials(
                                    new Uri(CLUSTERNAME),
                                    HADOOPUSERNAME,
                                    HADOOPUSERPASSWORD);
                    client = new HBaseClient(creds);
                }
        
                // Query Tweets sentiment data from the HBase table asynchronously 
                public async Task<IEnumerable<Tweet>> QueryTweetsByKeywordAsync(string keyword)
                {
                    List<Tweet> list = new List<Tweet>();
        
                    // Demonstrate Filtering the data from the past 6 hours the row key
                    string timeIndex = (ulong.MaxValue -
                        (ulong)DateTime.UtcNow.Subtract(new TimeSpan(6, 0, 0)).ToBinary()).ToString().PadLeft(20);
                    string startRow = keyword + "_" + timeIndex;
                    string endRow = keyword + "|";
                    Scanner scanSettings = new Scanner
                    {
                        batch = 100000,
                        startRow = Encoding.UTF8.GetBytes(startRow),
                        endRow = Encoding.UTF8.GetBytes(endRow)
                    };
        
                    // Make async scan call
                    ScannerInformation scannerInfo =
                        await client.CreateScannerAsync(HBASETABLENAME, scanSettings);
        
                    CellSet next;
        
                    while ((next = await client.ScannerGetNextAsync(scannerInfo)) != null)
                    {
                        foreach (CellSet.Row row in next.rows)
                        {
                            // find the cell with string pattern "d:coor" 
                            var coordinates =
                                row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:coor");
        
                            if (coordinates != null)
                            {
                                string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
        
                                var sentimentField =
                                    row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:sentiment");
                                Int32 sentiment = 0;
                                if (sentimentField != null)
                                {
                                    sentiment = Convert.ToInt32(Encoding.UTF8.GetString(sentimentField.data));
                                }
        
                                list.Add(new Tweet
                                {
                                    Longtitude = Convert.ToDouble(lonlat[0]),
                                    Latitude = Convert.ToDouble(lonlat[1]),
                                    Sentiment = sentiment
                                });
                            }
        
                            if (coordinates != null)
                            {
                                string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
                            }
                        }
                    }
        
                    return list;
                }
            }
        
            public class Tweet
            {
                public string IdStr { get; set; }
                public string Text { get; set; }
                public string Lang { get; set; }
                public double Longtitude { get; set; }
                public double Latitude { get; set; }
                public int Sentiment { get; set; }
            }
        }

4. 内部、 **HBaseReader** 定数の値を次のように変更します。

    - **CLUSTERNAME**: HBase クラスター名、たとえば、 *https://<HBaseClusterName>.azurehdinsight.net/*します。 
    - **HADOOPUSERNAME**: HBase クラスター Hadoop ユーザーのユーザー名。 既定の名前は *admin*します。
    - **HADOOPUSERPASSWORD**: HBase クラスター Hadoop ユーザーのパスワード。
    - **HBASETABLENAME** ="tweets_by_words"です。

    HBase テーブル名は **"tweets_by_words";**します。 値は、ストリーミング サービスで送信した値と同じでなければなりません。そのようにすると、Web アプリケーションは同じ HBase テーブルのデータを読み取ることができます。




**TweetsController コントローラーを追加するには**

1.  **ソリューション エクスプ ローラー**, 、展開 **TweetSentimentWeb**します。
2. 右クリック **コント ローラー**, 、クリックして **追加**, 、順にクリック **コント ローラー**します。
3. クリックして **Web API 2 コント ローラー - 空**, 、クリックして **追加**します。
4.  **コント ローラー名** フィールドに「 **TweetsController**, 、クリックしてして **追加**します。
5.  **ソリューション エクスプ ローラー**, 、ファイルを開く TweetsController.cs をダブルクリックします。
5. ファイルを以下のように変更します。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        
        using System.Threading.Tasks;
        using TweetSentimentWeb.Models;
        
        namespace TweetSentimentWeb.Controllers
        {
            public class TweetsController : ApiController
            {
                HBaseReader hbase = new HBaseReader();
        
                public async Task<IEnumerable<Tweet>> GetTweetsByQuery(string query)
                {
                    return await hbase.QueryTweetsByKeywordAsync(query);
                }
            }
        }

**heatmap.js を追加するには**

1.  **ソリューション エクスプ ローラー**, 、展開 **TweetSentimentWeb**します。
2. 右クリック **スクリプト**, 、] をクリックして **追加**, 、クリックして **JavaScript ファイル**します。
3.  **項目名** フィールドに「 **heatmap.js**します。
4. 以下のコードをファイルに貼り付けます。 このコードの作成者は Alastair Aitchison です。 詳細については、次を参照してください。 [Bing Maps AJAX v7 HeatMap ライブラリ](http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/)します。

        /*******************************************************************************
        * Author: Alastair Aitchison
        * Website: http://alastaira.wordpress.com
        * Date: 15th April 2011
        * 
        * Description: 
        * This JavaScript file provides an algorithm that can be used to add a heatmap
        * overlay on a Bing Maps v7 control. The intensity and temperature palette
        * of the heatmap are designed to be easily customisable.
        *
        * Requirements:
        * The heatmap layer itself is created dynamically on the client-side using
        * the HTML5 <canvas> element, and therefore requires a browser that supports
        * this element. It has been tested on IE9, Firefox 3.6/4 and 
        * Chrome 10 browsers. If you can confirm whether it works on other browsers or
        * not, I'd love to hear from you!
        
        * Usage:
        * The HeatMapLayer constructor requires:
        * - A reference to a map object
        * - An array or Microsoft.Maps.Location items
        * - Optional parameters to customise the appearance of the layer
        *  (Radius,, Unit, Intensity, and ColourGradient), and a callback function
        *
        */
        
        var HeatMapLayer = function (map, locations, options) {
        
            /* Private Properties */
            var _map = map,
              _canvas,
              _temperaturemap,
              _locations = [],
              _viewchangestarthandler,
              _viewchangeendhandler;
        
            // Set default options
            var _options = {
                // Opacity at the centre of each heat point
                intensity: 0.5,
        
                // Affected radius of each heat point
                radius: 1000,
        
                // Whether the radius is an absolute pixel value or meters
                unit: 'meters',
        
                // Colour temperature gradient of the map
                colourgradient: {
                    "0.00": 'rgba(255,0,255,20)',  // Magenta
                    "0.25": 'rgba(0,0,255,40)',    // Blue
                    "0.50": 'rgba(0,255,0,80)',    // Green
                    "0.75": 'rgba(255,255,0,120)', // Yellow
                    "1.00": 'rgba(255,0,0,150)'    // Red
                },
        
                // Callback function to be fired after heatmap layer has been redrawn 
                callback: null
            };
        
            /* Private Methods */
            function _init() {
                var _mapDiv = _map.getRootElement();
        
                if (_mapDiv.childNodes.length >= 3 && _mapDiv.childNodes[2].childNodes.length >= 2) {
                    // Create the canvas element
                    _canvas = document.createElement('canvas');
                    _canvas.style.position = 'relative';
        
                    var container = document.createElement('div');
                    container.style.position = 'absolute';
                    container.style.left = '0px';
                    container.style.top = '0px';
                    container.appendChild(_canvas);
        
                    _mapDiv.childNodes[2].childNodes[1].appendChild(container);
        
                    // Override defaults with any options passed in the constructor
                    _setOptions(options);
        
                    // Load array of location data
                    _setPoints(locations);
        
                    // Create a colour gradient from the suppied colourstops
                    _temperaturemap = _createColourGradient(_options.colourgradient);
        
                    // Wire up the event handler to redraw heatmap canvas
                    _viewchangestarthandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangestart', _clearHeatMap);
                    _viewchangeendhandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangeend', _createHeatMap);
        
                    _createHeatMap();
        
                    delete _init;
                } else {
                    setTimeout(_init, 100);
                }
            }
        
            // Resets the heat map
            function _clearHeatMap() {
                var ctx = _canvas.getContext("2d");
                ctx.clearRect(0, 0, _canvas.width, _canvas.height);
            }
        
            // Creates a colour gradient from supplied colour stops on initialisation
            function _createColourGradient(colourstops) {
                var ctx = document.createElement('canvas').getContext('2d');
                var grd = ctx.createLinearGradient(0, 0, 256, 0);
                for (var c in colourstops) {
                    grd.addColorStop(c, colourstops[c]);
                }
                ctx.fillStyle = grd;
                ctx.fillRect(0, 0, 256, 1);
                return ctx.getImageData(0, 0, 256, 1).data;
            }
        
            // Applies a colour gradient to the intensity map
            function _colouriseHeatMap() {
                var ctx = _canvas.getContext("2d");
                var dat = ctx.getImageData(0, 0, _canvas.width, _canvas.height);
                var pix = dat.data; // pix is a CanvasPixelArray containing height x width x 4 bytes of data (RGBA)
                for (var p = 0, len = pix.length; p < len;) {
                    var a = pix[p + 3] * 4; // get the alpha of this pixel
                    if (a != 0) { // If there is any data to plot
                        pix[p] = _temperaturemap[a]; // set the red value of the gradient that corresponds to this alpha
                        pix[p + 1] = _temperaturemap[a + 1]; //set the green value based on alpha
                        pix[p + 2] = _temperaturemap[a + 2]; //set the blue value based on alpha
                    }
                    p += 4; // Move on to the next pixel
                }
                ctx.putImageData(dat, 0, 0);
            }
        
            // Sets any options passed in
            function _setOptions(options) {
                for (attrname in options) {
                    _options[attrname] = options[attrname];
                }
            }
        
            // Sets the heatmap points from an array of Microsoft.Maps.Locations  
            function _setPoints(locations) {
                _locations = locations;
            }
        
            // Main method to draw the heatmap
            function _createHeatMap() {
                // Ensure the canvas matches the current dimensions of the map
                // This also has the effect of resetting the canvas
                _canvas.height = _map.getHeight();
                _canvas.width = _map.getWidth();
        
                _canvas.style.top = -_canvas.height / 2 + 'px';
                _canvas.style.left = -_canvas.width / 2 + 'px';
        
                // Calculate the pixel radius of each heatpoint at the current map zoom
                if (_options.unit == "pixels") {
                    radiusInPixel = _options.radius;
                } else {
                    radiusInPixel = _options.radius / _map.getMetersPerPixel();
                }
        
                var ctx = _canvas.getContext("2d");
        
                // Convert lat/long to pixel location
                var pixlocs = _map.tryLocationToPixel(_locations, Microsoft.Maps.PixelReference.control);
                var shadow = 'rgba(0, 0, 0, ' + _options.intensity + ')';
                var mapWidth = 256 * Math.pow(2, _map.getZoom());
        
                // Create the Intensity Map by looping through each location
                for (var i = 0, len = pixlocs.length; i < len; i++) {
                    var x = pixlocs[i].x;
                    var y = pixlocs[i].y;
        
                    if (x < 0) {
                        x += mapWidth * Math.ceil(Math.abs(x / mapWidth));
                    }
        
                    // Create radial gradient centred on this point
                    var grd = ctx.createRadialGradient(x, y, 0, x, y, radiusInPixel);
                    grd.addColorStop(0.0, shadow);
                    grd.addColorStop(1.0, 'transparent');
        
                    // Draw the heatpoint onto the canvas
                    ctx.fillStyle = grd;
                    ctx.fillRect(x - radiusInPixel, y - radiusInPixel, 2 * radiusInPixel, 2 * radiusInPixel);
                }
        
                // Apply the specified colour gradient to the intensity map
                _colouriseHeatMap();
        
                // Call the callback function, if specified
                if (_options.callback) {
                    _options.callback();
                }
            }
        
            /* Public Methods */
        
            this.Show = function () {
                if (_canvas) {
                    _canvas.style.display = '';
                }
            };
        
            this.Hide = function () {
                if (_canvas) {
                    _canvas.style.display = 'none';
                }
            };
        
            // Sets options for intensity, radius, colourgradient etc.
            this.SetOptions = function (options) {
                _setOptions(options);
            }
        
            // Sets an array of Microsoft.Maps.Locations from which the heatmap is created
            this.SetPoints = function (locations) {
                // Reset the existing heatmap layer
                _clearHeatMap();
                // Pass in the new set of locations
                _setPoints(locations);
                // Recreate the layer
                _createHeatMap();
            }
        
            // Removes the heatmap layer from the DOM
            this.Remove = function () {
                _canvas.parentNode.parentNode.removeChild(_canvas.parentNode);
        
                if (_viewchangestarthandler) { Microsoft.Maps.Events.removeHandler(_viewchangestarthandler); }
                if (_viewchangeendhandler) { Microsoft.Maps.Events.removeHandler(_viewchangeendhandler); }
        
                _locations = null;
                _temperaturemap = null;
                _canvas = null;
                _options = null;
                _viewchangestarthandler = null;
                _viewchangeendhandler = null;
            }
        
            // Call the initialisation routine
            _init();
        };
        
        // Call the Module Loaded method
        Microsoft.Maps.moduleLoaded('HeatMapModule');


**TwitterStream.js を追加するには**

1.  **ソリューション エクスプ ローラー**, 、展開 **TweetSentimentWeb**します。
2. 右クリック **スクリプト**, 、] をクリックして **追加**, 、クリックして **JavaScript ファイル**します。
3.  **項目名** フィールドに「**twitterStream.js**します。
4. 以下のコードをコピーして、ファイルに貼り付けます。

        var liveTweetsPos = [];
        var liveTweets = [];
        var liveTweetsNeg = [];
        var map;
        var heatmap;
        var heatmapNeg;
        var heatmapPos;
        
        function initialize() {
            // Initialize the map
            var options = {
                credentials: "AvFJTZPZv8l3gF8VC3Y7BPBd0r7LKo8dqKG02EAlqg9WAi0M7la6zSIT-HwkMQbx",
                center: new Microsoft.Maps.Location(23.0, 8.0),
                mapTypeId: Microsoft.Maps.MapTypeId.ordnanceSurvey,
                labelOverlay: Microsoft.Maps.LabelOverlay.hidden,
                zoom: 2.5
            };
            var map = new Microsoft.Maps.Map(document.getElementById('map_canvas'), options);
        
            // Heatmap options for positive, neutral and negative layers
        
            var heatmapOptions = {
                // Opacity at the centre of each heat point
                intensity: 0.5,
        
                // Affected radius of each heat point
                radius: 15,
        
                // Whether the radius is an absolute pixel value or meters
                unit: 'pixels'
            };
        
            var heatmapPosOptions = {
                // Opacity at the centre of each heat point
                intensity: 0.5,
        
                // Affected radius of each heat point
                radius: 15,
        
                // Whether the radius is an absolute pixel value or meters
                unit: 'pixels',
        
                colourgradient: {
                    0.0: 'rgba(0, 255, 255, 0)',
                    0.1: 'rgba(0, 255, 255, 1)',
                    0.2: 'rgba(0, 255, 191, 1)',
                    0.3: 'rgba(0, 255, 127, 1)',
                    0.4: 'rgba(0, 255, 63, 1)',
                    0.5: 'rgba(0, 127, 0, 1)',
                    0.7: 'rgba(0, 159, 0, 1)',
                    0.8: 'rgba(0, 191, 0, 1)',
                    0.9: 'rgba(0, 223, 0, 1)',
                    1.0: 'rgba(0, 255, 0, 1)'
                }
            };
        
            var heatmapNegOptions = {
                // Opacity at the centre of each heat point
                intensity: 0.5,
        
                // Affected radius of each heat point
                radius: 15,
        
                // Whether the radius is an absolute pixel value or meters
                unit: 'pixels',
        
                colourgradient: {
                    0.0: 'rgba(0, 255, 255, 0)',
                    0.1: 'rgba(0, 255, 255, 1)',
                    0.2: 'rgba(0, 191, 255, 1)',
                    0.3: 'rgba(0, 127, 255, 1)',
                    0.4: 'rgba(0, 63, 255, 1)',
                    0.5: 'rgba(0, 0, 127, 1)',
                    0.7: 'rgba(0, 0, 159, 1)',
                    0.8: 'rgba(0, 0, 191, 1)',
                    0.9: 'rgba(0, 0, 223, 1)',
                    1.0: 'rgba(0, 0, 255, 1)'
                }
            };
        
            // Register and load the Client Side HeatMap Module
            Microsoft.Maps.registerModule("HeatMapModule", "scripts/heatmap.js");
            Microsoft.Maps.loadModule("HeatMapModule", {
                callback: function () {
                    // Create heatmap layers for positive, neutral and negative tweets
                    heatmapPos = new HeatMapLayer(map, liveTweetsPos, heatmapPosOptions);
                    heatmap = new HeatMapLayer(map, liveTweets, heatmapOptions);
                    heatmapNeg = new HeatMapLayer(map, liveTweetsNeg, heatmapNegOptions);
                }
            });
        
            $("#searchbox").val("xbox");
            $("#searchBtn").click(onsearch);
            $("#positiveBtn").click(onPositiveBtn);
            $("#negativeBtn").click(onNegativeBtn);
            $("#neutralBtn").click(onNeutralBtn);
            $("#neutralBtn").button("toggle");
        }
        
        function onsearch() {
            var uri = 'api/tweets?query=';
            var query = $('#searchbox').val();
            $.getJSON(uri + query)
                .done(function (data) {
                    liveTweetsPos = [];
                    liveTweets = [];
                    liveTweetsNeg = [];
        
                    // On success, 'data' contains a list of tweets.
                    $.each(data, function (key, item) {
                        addTweet(item);
                    });
        
                    if (!$("#neutralBtn").hasClass('active')) {
                        $("#neutralBtn").button("toggle");
                    }
                    onNeutralBtn();
                })
                .fail(function (jqXHR, textStatus, err) {
                    $('#statustext').text('Error: ' + err);
                });
        }
        
        function addTweet(item) {
            //Add tweet to the heat map arrays.
            var tweetLocation = new Microsoft.Maps.Location(item.Latitude, item.Longtitude);
            if (item.Sentiment > 0) {
                liveTweetsPos.push(tweetLocation);
            } else if (item.Sentiment < 0) {
                liveTweetsNeg.push(tweetLocation);
            } else {
                liveTweets.push(tweetLocation);
            }
        }
        
        function onPositiveBtn() {
            if ($("#neutralBtn").hasClass('active')) {
                $("#neutralBtn").button("toggle");
            }
            if ($("#negativeBtn").hasClass('active')) {
                $("#negativeBtn").button("toggle");
            }
        
            heatmapPos.SetPoints(liveTweetsPos);
            heatmapPos.Show();
            heatmapNeg.Hide();
            heatmap.Hide();
        
            $('#statustext').text('Tweets: ' + liveTweetsPos.length + "   " + getPosNegRatio());
        }
        
        function onNeutralBtn() {
            if ($("#positiveBtn").hasClass('active')) {
                $("#positiveBtn").button("toggle");
            }
            if ($("#negativeBtn").hasClass('active')) {
                $("#negativeBtn").button("toggle");
            }
        
            heatmap.SetPoints(liveTweets);
            heatmap.Show();
            heatmapNeg.Hide();
            heatmapPos.Hide();
        
            $('#statustext').text('Tweets: ' + liveTweets.length + "   " + getPosNegRatio());
        }
        
        function onNegativeBtn() {
            if ($("#positiveBtn").hasClass('active')) {
                $("#positiveBtn").button("toggle");
            }
            if ($("#neutralBtn").hasClass('active')) {
                $("#neutralBtn").button("toggle");
            }
        
            heatmapNeg.SetPoints(liveTweetsNeg);
            heatmapNeg.Show();
            heatmap.Hide();;
            heatmapPos.Hide();;
        
            $('#statustext').text('Tweets: ' + liveTweetsNeg.length + "\t" + getPosNegRatio());
        }
        
        function getPosNegRatio() {
            if (liveTweetsNeg.length == 0) {
                return "";
            }
            else {
                var ratio = liveTweetsPos.length / liveTweetsNeg.length;
                var str = parseFloat(Math.round(ratio * 10) / 10).toFixed(1);
                return "Positive/Negative Ratio: " + str;
            }
        }


**layout.cshtml を変更するには**

1.  **ソリューション エクスプ ローラー**, 、展開 **[tweetsentimentweb]**, 、展開 **ビュー**, 、展開 **Shared**, をダブルクリックし、**Layout.cshtml**します。
2. 次のコンテンツに置き換えます。

        <!DOCTYPE html>
        <html>
        <head>
            <meta charset="utf-8" />
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>@ViewBag.Title</title>
            @Styles.Render("~/Content/css")
            @Scripts.Render("~/bundles/modernizr")
            <!-- Bing Maps -->
            <script type="text/javascript" src="http://ecn.dev.virtualearth.net/mapcontrol/mapcontrol.ashx?v=7.0&mkt=en-gb"></script>
            <!-- Spatial Dashboard JavaScript -->
            <script src="~/Scripts/twitterStream.js" type="text/javascript"></script>
        </head>
        <body onload="initialize()">
            <div class="navbar navbar-inverse navbar-fixed-top">
                <div class="container">
                    <div class="navbar-header">
                        <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                            <span class="icon-bar"></span>
                            <span class="icon-bar"></span>
                            <span class="icon-bar"></span>
                        </button>
                    </div>
                    <div class="navbar-collapse collapse">
                        <div class="row">
                            <ul class="nav navbar-nav col-lg-5">
                                <li class="col-lg-12">
                                    <div class="navbar-form">
                                        <input id="searchbox" type="search" class="form-control">
                                        <button type="button" id="searchBtn" class="btn btn-primary">Go</button>
                                    </div>
                                </li>
                            </ul>
                            <ul class="nav navbar-nav col-lg-7">
                                <li>
                                    <div class="navbar-form">
                                        <div class="btn-group" data-toggle="buttons-radio">
                                            <button type="button" id="positiveBtn" class="btn btn-primary">Positive</button>
                                            <button type="button" id="neutralBtn" class="btn btn-primary">Neutral</button>
                                            <button type="button" id="negativeBtn" class="btn btn-primary">Negative</button>
                                        </div>
                                    </div>
                                </li>
                                <li><span id="statustext" class="navbar-text"></span></li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
            <div class="map_container">
                @RenderBody()
            </div>
            @Scripts.Render("~/bundles/jquery")
            @Scripts.Render("~/bundles/bootstrap")
            @RenderSection("scripts", required: false)
        </body>
        </html>



**Index.cshtml を変更するには**

1.  **ソリューション エクスプ ローラー**, 、展開 **[tweetsentimentweb]**, 、展開 **ビュー**, 、展開 **ホーム**, をダブルクリックし、 **Index.cshtml**します。
2. 次のコンテンツに置き換えます。

        @{
            ViewBag.Title = "Tweet Sentiment";
        }
        
        <div class="map_container">
            <div id="map_canvas"/>
        </div>

**site.css ファイルを変更するには**

1.  **ソリューション エクスプ ローラー**, 、展開 **TweetSentimentWeb**, 、展開 **コンテンツ**, をダブルクリックし、 **Site.css**します。
2. ファイルに、以下のコードを追加します。
        
        /* make container, and thus map, 100% width */
        .map_container {
            width: 100%;
            height: 100%;
        }
        
        #map_canvas{
          height:100%;
        }
        
        #tweets{
          position: absolute;
          top: 60px;
          left: 75px;
          z-index:1000;
          font-size: 30px;
        }

**global.asax ファイルを変更するには**

1.  **ソリューション エクスプ ローラー**, 、展開 **TweetSentimentWeb**, をダブルクリックし、 **Global.asax**します。
2. 次の追加 **を使用して** ステートメント。

        using System.Web.Http;

2. 内の次の行を追加、 **Application_Start()** 関数。

        // Register API routes
        GlobalConfiguration.Configure(WebApiConfig.Register);
  
    API ルートの登録を変更し、Web API コントローラーが MVC アプリケーション内で動作するようにします。

**Web アプリケーションを実行するには**

1. ストリーミング サービス コンソール アプリケーションが実行され、リアルタイムの変更が表示されることを確認します。
2. キーを押して **f5 キーを押して** web アプリケーションを実行します。

    ![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
2. テキスト ボックスに、キーワードを入力し、クリックして **移動**します。  HBase テーブルで収集したデータによっては、キーワードが検出できない場合もあります。 「love」、「xbox」、「playstation」などの一般的なキーワードを試してください。 
3. 間で切り替える **正**, 、**中立**, 、および **負** 、対象のセンチメントを比較します。
4. 別の時間帯にストリーミング サービスを実行し、同じキーワードを検索して結果を比較してください。

 
Azure Web サイトにこのアプリケーションをデプロイすることもできます。 手順については、次を参照してください。 [Azure Websites と ASP.NET を使ってみる][website-get-started]します。
 
##<a id="nextsteps"></a>次のステップ

このチュートリアルでは、ツイートを取得し、ツイートのセンチメントを分析し、センチメント データを HBase に保存し、リアルタイムの Twitter センチメント データを Bing マップに表示する方法について学習しました。 詳細については、次を参照してください。

- [HDInsight の使用][hdinsight-get-started]
- [HDInsight での HBase レプリケーションの構成](hdinsight-hbase-geo-replication.md) 
- [HDInsight での Hadoop を使用した Twitter データの分析][hdinsight-analyze-twitter-data]
- [HDInsight を使用したフライトの遅延データの分析][hdinsight-analyze-flight-delay-data]
- [Develop C# Hadoop streaming programs for HDInsight (HDInsight 用 C# Hadoop ストリーミング プログラムの開発)][hdinsight-develop-streaming]
- [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)][hdinsight-develop-mapreduce]


[hbase-get-started]: ../hdinsight-hbase-get-started.md
[website-get-started]: ../web-sites-dotnet-get-started.md



[img-app-arch]: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
[img-twitter-app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
[img-streaming-service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
[img-bing-map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png



[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-analyze-twitter-data]: hdinsight-analyze-twitter-data.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md




[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
 

