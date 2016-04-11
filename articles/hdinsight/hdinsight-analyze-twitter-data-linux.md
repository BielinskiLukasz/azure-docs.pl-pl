<properties
    pageTitle="HDInsight の Apache Hive を使用した Twitter データの分析 | Microsoft Azure"
    description="Python を使用して特定のキーワードを含むツイートを格納してから、HDInsight で Hive と Hadoop を使用して未加工の Twitter データを検索可能な Hive テーブルに変換する方法について説明します。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="larryfr"/>

# HDInsight での Hive を使用した Twitter データの分析

このドキュメントでは、Twitter streaming API を使用してツイートを取得し、Linux ベースの HDInsight クラスターで Apache Hive を使用して、JSON 形式のデータを処理します。 結果として、特定の単語が含まれた最も多くのツイートを送信した Twitter ユーザーのリストが返されます。

> [AZURE.NOTE] このドキュメントの各部分を使用できますが、Windows ベースの HDInsight クラスター (Python と Hive など)、多くの手順は Linux ベースの HDInsight クラスターの使用に基づいています。 固有の手順について、Windows ベースのクラスターに、次を参照してください。 [Twitter データの分析 HDInsight で Hive を使用して](hdinsight-analyze-twitter-data.md)します。

###前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- A __Azure HDInsight の Linux ベースのクラスター__します。 クラスターを作成する方法の詳細については、次を参照してください。 [Linux ベースの HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md) クラスターを作成する手順についてです。

-  __SSH クライアント__です。 Linux ベースの HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。

    * [Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ と [pip](https://pypi.python.org/pypi/pip)

-  __Azure CLI__します。 詳細については、次を参照してください [をインストールし、Azure CLI の構成。](../xplat-cli-install.md)

##Twitter Feed の取得

Twitter では、取得することができます、 [それぞれのツイートのデータ](https://dev.twitter.com/docs/platform-objects/tweets) REST API を通じて JavaScript Object Notation (JSON) ドキュメントとして。 [OAuth](http://oauth.net) の API に認証が必要です。 作成することも必要があります、 _Twitter アプリケーション_ API にアクセスするために使用する設定値を格納します。

###Twitter アプリケーションを作成する

1. Web ブラウザーからへのサインイン [https://apps.twitter.com/](https://apps.twitter.com/)します。 クリックして、 **今すぐサインアップ** Twitter アカウントを持っていない場合は、リンクします。
2. クリックして **新しいアプリを作成する**です。
3. 入力 **名前**, 、**説明**, 、**web サイト**します。 URL を行うことができます、 **web サイト** フィールドです。 次のテーブルは使用する値のサンプルを示しています。

    | フィールド | 値 |
    |:----- |:----- |
    | 名前  | MyHDInsightApp |
    | 説明 | MyHDInsightApp |
    | Web サイト | http://www.myhdinsightapp.com |
    
4. 確認 **Yes, I agree**, 、] をクリックし、 **Twitter アプリケーションを作成する**です。
5. クリックして、 **権限** ] タブをクリックします。 既定のアクセス許可は **読み取り専用**します。 このチュートリアルにはこれで十分です。
6. クリックして、 **Keys and Access Tokens** ] タブをクリックします。
7. クリックして **、アクセス トークンを作成**します。
8. クリックして **Test OAuth** ページの右上隅にします。
9. 書き留めて **コンシューマー キー**, 、**コンシューマー シークレット**, 、**アクセス トークン**, 、および **アクセス トークン シークレット**します。 これらの値は後で必要になります。

>[AZURE.NOTE] Windows で curl コマンドを使用する場合は、オプションの値の単一引用符の代わりに二重引用符を使用します。

###ツイートをダウンロードする

次の Python コードが Twitter から 10,000 のツイートをダウンロードしてという名前のファイルに保存 __tweets.txt__します。

> [AZURE.NOTE] 次の手順は、Python が既にインストールされているために、HDInsight クラスターで実行されます。

1. SSH を使用して HDInsight クラスターに接続します。

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    SSH ユーザー アカウントを保護するためにパスワードを使用している場合は、パスワードの入力を求められます。 公開キーを使用している場合、`-i` パラメーターを使用して、対応する秘密キーを指定することが必要な場合があります。 たとえば、「`ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`」のように入力します。
        
    Linux ベースの HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。
    
    * [Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows)
    
2. 既定では、 __pip__ ユーティリティは、HDInsight ヘッド ノードにインストールされていません。 次のコマンドを使用して、このユーティリティをインストールし、更新します。

        sudo apt-get install python-pip
        sudo pip install --upgrade pip

3. ツイートをダウンロードするコードが依存 [Tweepy](http://www.tweepy.org/) と [Progressbar](https://pypi.python.org/pypi/progressbar/2.2)します。 これらをインストールするには、次のコマンドを使用します。

        sudo apt-get install python-dev libffi-dev libssl-dev
        sudo apt-get remove python-openssl
        sudo pip install tweepy==3.2.0 progressbar pyOpenSSL requests[security]
        
    > [AZURE.NOTE] Python openssl、python デベロッパー、libffi デベロッパー、libssl デベロッパー、に関する注意事項、および要求 [セキュリティ] のインストールの削除について bits では、Python から SSL 経由で Twitter に接続するときに InsecurePlatform 警告を回避します。
    >
    > 回避する Tweepy v3.2.0 が使用 [エラー](https://github.com/tweepy/tweepy/issues/576) ツイートを処理するときに発生することができます。

4. という名前の新しいファイルを作成する次のコマンドを使用して __gettweets.py__:

        nano gettweets.py

5. 内容として次を使用して、 __gettweets.py__ ファイルです。 _ のプレース ホルダー情報を置き換える_コンシューマー/_シークレット__, 、__コンシューマー/_キー__, 、__アクセス/_トークン__, 、および __access/_トークン/_シークレット__ Twitter アプリケーションの情報を使用します。

        #!/usr/bin/python
        
        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys
        
        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'
        
        #The number of tweets we want to get
        max_tweets=10000
        
        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()
        
            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True
        
            #Handle any errors that may occur
            def on_error(self, status):
                print status
        
        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. 使用 __ctrl キーを押しながら X__, 、し __Y__ ファイルを保存します。

7. 次のコマンドを使用してファイルを実行し、ツイートをダウンロードします。

        python gettweets.py

    進行状況のインジケーターが表示され、ツイートのダウンロードとファイルへの保存の進行状況が 100% までカウントされます。

###データをアップロードする

WASB (HDInsight で使用される分散ファイル システム) にデータをアップロードするには、次のコマンドを使用します。

    hadoop fs -mkdir -p /tutorials/twitter/data
    hadoop fs -copyFromLocal tweets.txt /tutorials/twitter/data/tweets.txt

クラスター内のすべてのノードがアクセスできる場所にデータが保存されます。

##HiveQL ジョブの実行


1. 次のコマンドを使用して、HiveQL ステートメントを含むファイルを作成します。

        nano twitter.hql
    
    このファイルの内容として、次のコードを使用します。

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
        
        
3. キーを押して __ctrl キーを押しながら X__, 、キーを押します __Y__ ファイルを保存します。

4. 次のコマンドを使用して、ファイルに含まれている HiveQL を実行します。

        hive -i twitter.hql     
        
    Hive シェルを読み込む () で HiveQL を実行するが、 __twitter.hql__ ファイルを開き、最後に返す、 `hive >` プロンプトです。
    
5.  `hive >` プロンプトで、次の使用からのデータを選択できることを確認して、 __ツイート__ 内の HiveQL によって作成されたテーブル、 __twitter.hql__ ファイル。
        
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;

    語を含む 10 個のツイートの最大値が返されます __Azure__ メッセージ テキストにします。

##次のステップ

このチュートリアルでは、Azure 上で HDInsight を使用し、Twitter から収集したデータを照会、探索、分析するため、構造化されていない JSON データセットを構造化された Hive テーブルへ変換する方法を学習しました。 詳細については、次を参照してください。

- [HDInsight の使用](hdinsight-hadoop-linux-tutorial-get-started.md)
- [HDInsight を使用したフライトの遅延データの分析](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

