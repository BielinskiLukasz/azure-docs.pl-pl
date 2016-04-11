<properties
    pageTitle="JavaScript バックエンド モバイル サービスのバックエンド タスクのスケジュール | Microsoft Azure"
    description="Azure Mobile Services のスケジューラを使用し、日程に基づいて実行される JavaScript バックエンド ジョブを定義します。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="glenga"/>

# Mobile Services での繰り返し発生するジョブのスケジュール

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
- [.NET バックエンド](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [Javascript バックエンド](mobile-services-schedule-recurring-tasks.md)

このトピックでは、Azure クラシック ポータルのジョブ スケジューラ機能を使用して、定義したスケジュールに基づいて実行されるサーバー スクリプト コードを定義する方法について説明します。 このスクリプトは、リモート サービス (ここでは Twitter) に対する確認を定期的に行い、結果を新しいテーブルに格納します。 スケジュールできる定期的なタスクには、次のようなものがあります。

+ 古いデータ レコードまたは重複しているデータ レコードの取得。
+ 外部データ (ツイート、RSS エントリ、場所情報など) の要求と格納。
+ 格納されている画像の処理とサイズ変更。

このチュートリアルでは、ジョブ スケジューラを使用してスケジュールされたジョブを作成する手順について説明します。このジョブでは、Twitter にツイート データを要求し、そのツイートを新しい Updates テーブルに格納します。

##<a name="get-oauth-credentials"></a>Twitter v1.1 API へのアクセスを登録して資格情報を保存する

[AZURE.INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

##<a name="create-table"></a>新しい Updates テーブルを作成する

次に、ツイートを格納するための新しいテーブルを作成する必要があります。

2.  [Azure classic portal], をクリックして、 **データ** タブに移動して、モバイル サービスの [ **[作成**します。

3.  **テーブル名** 型 _更新_, 、チェック ボタンをクリックします。

##<a name="add-job"></a>新しいスケジュール済みジョブを作成する

次に、Twitter にアクセスしてツイート データを新しい Updates テーブルに格納するための、スケジュールされたジョブを作成します。

2. クリックして、 **スケジューラ** ] タブの [ **[作成**します。

    >[AZURE.NOTE]モバイル サービスを実行すると <em>Free</em> 層、ことができるだけの時間に 1 つのスケジュールされたジョブを実行します。 有料レベルでは、10 個までのスケジュールされたジョブを同時に実行できます。

3. [スケジューラ] ダイアログ ボックスで、次のように入力します。 _getUpdates_ の、 **ジョブ名**, を、スケジュールの間隔と単位を設定して、チェック ボタンをクリックします。

    という名前の新しいジョブを作成この **getUpdates**します。

4. 作成した新しいジョブをクリックし、 **スクリプト** タブし、プレース ホルダー関数を置換 **getUpdates** を次のコード。

        var updatesTable = tables.getTable('Updates');
        var request = require('request');
        var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json?q=%23mobileservices&result_type=recent";

        // Get the service configuration module.
        var config = require('mobileservice-config');

        // Get the stored Twitter consumer key and secret.
        var consumerKey = config.twitterConsumerKey,
            consumerSecret = config.twitterConsumerSecret
        // Get the Twitter access token from app settings.
        var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
            accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

        function getUpdates() {
            // Check what is the last tweet we stored when the job last ran
            // and ask Twitter to only give us more recent tweets
            appendLastTweetId(
                twitterUrl,
                function twitterUrlReady(url){
                    // Create a new request with OAuth credentials.
                    request.get({
                        url: url,
                        oauth: {
                            consumer_key: consumerKey,
                            consumer_secret: consumerSecret,
                            token: accessToken,
                            token_secret: accessTokenSecret
                        }},
                        function (error, response, body) {
                        if (!error && response.statusCode == 200) {
                            var results = JSON.parse(body).statuses;
                            if(results){
                                console.log('Fetched ' + results.length + ' new results from Twitter');
                                results.forEach(function (tweet){
                                    if(!filterOutTweet(tweet)){
                                        var update = {
                                            twitterId: tweet.id,
                                            text: tweet.text,
                                            author: tweet.user.screen_name,
                                            date: tweet.created_at
                                        };
                                        updatesTable.insert(update);
                                    }
                                });
                            }
                        } else {
                            console.error('Could not contact Twitter');
                        }
                    });

                });
         }
        // Find the largest (most recent) tweet ID we have already stored
        // (if we have stored any) and ask Twitter to only return more
        // recent ones
        function appendLastTweetId(url, callback){
            updatesTable
            .orderByDescending('twitterId')
            .read({success: function readUpdates(updates){
                if(updates.length){
                    callback(url + '&since_id=' + (updates[0].twitterId + 1));
                } else {
                    callback(url);
                }
            }});
        }

        function filterOutTweet(tweet){
            // Remove retweets and replies
            return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
        }


    このスクリプトでは、保存された資格情報を使用して Twitter クエリ API を呼び出し、`#mobileservices` というハッシュタグが含まれる最近のツイートを要求します。 テーブルに格納される前に、重複しているツイートやリプライが結果から削除されます。

    >[AZURE.NOTE]このサンプルでは、いくつかの行のみが、実行時にスケジュールされている各テーブルに挿入されていると仮定します。 ループで多数の行が挿入される場合には、Free レベルで実行すると、接続数を使い果たす可能性があります。 このような場合、挿入をバッチで実行する必要があります。 詳細については、「[方法: 一括挿入を実行する](mobile-services-how-to-use-server-scripts.md#bulk-inserts)」を参照してください。

6. クリックして **一度だけ実行** スクリプトをテストします。

    これにより、ジョブが保存され、実行されます。ただし、スケジューラ内では無効の状態のままです。

7. [戻る] ボタンをクリックし **データ**, 、] をクリックして、 **更新** テーブルで、をクリックして **参照**, 、Twitter データがテーブルに挿入されたことを確認します。

8. [戻る] ボタンをクリックし、をクリックして **スケジューラ**, [ **getUpdates**, 、] をクリックし、 **を有効にする**です。

    これにより、指定されたスケジュール (ここでは 1 時間ごと) でジョブが実行されるようになります。

これで、スケジュールされた新しいジョブがモバイル サービスに作成されました。 このジョブは、無効化または変更するまで、スケジュールに従って実行されます。

## <a name="nextsteps"></a>関連項目

* [Mobile Services server script reference]
  <br/>サーバー スクリプトの登録および使用について説明します。

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Next steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: /develop/mobile/how-to-guides/register-for-twitter-authentication
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7


