<properties 
    pageTitle="Python で Notification Hubs を使用する方法" 
    description="Python バックエンドから Azure Notification Hubs を使用する方法について説明します。" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="wesmc7777"
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="python" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="11/01/2015" 
    ms.author="yuaxu"/>


# Python で Notification Hubs を使用する方法

[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

MSDN のトピックで説明したように Notification Hub REST インターフェイスを使用して Java、PHP、Python や Ruby バックエンドから Notification Hubs のすべての機能にアクセスできます [通知ハブの REST Api](http://msdn.microsoft.com/library/dn223264.aspx)します。
> [AZURE.NOTE] これは Python で、通知の送信を実装するためのサンプル参照実装であり、正式にサポートされている通知ハブの Python SDK ではありません。

> [AZURE.NOTE] このサンプルは Python 3.4 を使用して書き込まれます。

このトピックでは、次の方法について説明します。

* Python で Notification Hubs 機能の REST クライアントを記述します。
* 通知ハブ REST API には、Python インターフェイスを使用して通知を送信します。
* デバッグ/教育目的のための HTTP REST 要求/応答のダンプを取得します。

行うことができる、 [入門チュートリアル](notification-hubs-windows-store-dotnet-get-started.md) したモバイル プラットフォームの選択のためには、Python のバックエンド部分を実装します。
> [AZURE.NOTE] このサンプルの範囲は通知の送信のみに制限され、登録の管理は行いません。

## クライアント インターフェイス

メイン クライアント インターフェイスが備わっている同じメソッドを提供、 [.NET Notification Hubs SDK](http://msdn.microsoft.com/library/jj933431.aspx)します。 これでこのサイトで現在利用でき、インターネット上のコミュニティから提供されたすべてのチュートリアルとサンプルを直接訳せるようになります。

[Python REST ラッパー サンプル] で利用可能なすべてのコードを見つけることができます。

たとえば、クライアントを作成する場合:

    isDebug = True
    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

Windows トースト通知を送信する場合:

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

## 実装

まだない場合は、[入門チュートリアル] バック エンドを実装しなければ、最後のセクションまでに従ってください。

REST ラッパーを実装するすべての詳細を参照して [MSDN](http://msdn.microsoft.com/library/dn530746.aspx)します。 このセクションでは、Notification Hubs REST エンドポイントにアクセスし、通知を送信するために必要な主要手順の Python 実装について説明します。

1. 接続文字列を解析する
2. 認証トークンを生成する
3. HTTP REST API を使用して通知を送信する

### 接続文字列を解析する

接続文字列を解析するコンストラクターを持つクライアントを実装するメイン クラスです。

    class NotificationHub:
        API_VERSION = "?api-version=2013-10"
        DEBUG_SEND = "&test"
    
        def __init__(self, connection_string=None, hub_name=None, debug=0):
            self.HubName = hub_name
            self.Debug = debug
    
            # Parse connection string
            parts = connection_string.split(';')
            if len(parts) != 3:
                raise Exception("Invalid ConnectionString.")
    
            for part in parts:
                if part.startswith('Endpoint'):
                    self.Endpoint = 'https' + part[11:]
                if part.startswith('SharedAccessKeyName'):
                    self.SasKeyName = part[20:]
                if part.startswith('SharedAccessKey'):
                    self.SasKeyValue = part[16:]

### セキュリティ トークンを作成する

セキュリティ トークン作成の詳細については [ここ](http://msdn.microsoft.com/library/dn495627.aspx)します。
次のメソッドを **NotificationHub** クラスに追加し、接続文字列から抽出された現在の要求と、資格情報の URI に基づいたトークンを作成します。

    @staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))
    
    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)
    
    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest
    
    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### HTTP REST API を使用して通知を送信する

最初に、通知を表すクラスの定義を使用します。

    class Notification:
        def __init__(self, notification_format=None, payload=None, debug=0):
            valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
            if not any(x in notification_format for x in valid_formats):
                raise Exception(
                    "Invalid Notification format. " +
                    "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
    
            self.format = notification_format
            self.payload = payload
    
            # array with keynames for headers
            # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
            # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
            # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
            self.headers = None

このクラスは、ネイティブ通知本体のコンテナー、またはテンプレート通知の場合にはプロパティのセットと、形式 (ネイティブ プラットフォームまたはテンプレート) およびプラットフォーム固有のプロパティを含むヘッダーのセットです (Apple 有効期限プロパティや WNS ヘッダーなど)。

参照してください、 [通知ハブの REST Api ドキュメント](http://msdn.microsoft.com/library/dn495827.aspx) および利用可能なすべてのオプションについての特定の通知プラットフォームの形式です。

このクラスを利用して、**NotificationHub** クラス内に送信通知メソッドを作成できます。

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)
    
        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")
    
        connection.request('POST', url, payload, headers)
        response = connection.getresponse()
    
        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")
    
        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)
    
        connection.close()
    
    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION
    
        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']
    
        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload
    
        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }
    
        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression
    
        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})
    
        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)
    
        self.make_http_request(url, payload_to_send, headers)
    
    def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)
    
    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)
    
    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)
    
    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)
    
    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)
    
        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}
    
        self.send_notification(nh, tags)
    
    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)
    
        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}
    
        self.send_notification(nh, tags)
    
    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

上記のメソッドは、HTTP POST 要求、および通知を送信する正しい本体とヘッダーを通知ハブの /messages エンドポイントに送信します。

### デバッグ プロパティを使用して、詳細なログ記録を有効にする

通知ハブの初期化中にデバッグ プロパティを有効にすると、HTTP 要求に関する詳細なログの情報と応答ダンプ、詳細な通知メッセージの送信結果が書き込まれます。 
と呼ばれるこのプロパティが最近追加 [通知ハブの TestSend プロパティ](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx)
これは、通知の送信結果に関する詳細情報を返します。 
使用するには、次を使用して初期化します。

    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

結果として HTTP URL が取得する通知ハブの送信要求には、"test"クエリ文字列が追加されます。

## <a name="complete-tutorial"></a>チュートリアルを完了します。

ここで、Python バックエンドから通知を送信して、使用についてのチュートリアルを完了できます。

Notification Hubs クライアント (代替 [入門チュートリアル] の説明に従って接続文字列とハブ名) を初期化します。

    hub = NotificationHub("myConnectionString", "myNotificationHubName")

次に、ターゲット モバイル プラットフォームに応じて送信コードを追加します。 このサンプルでは、send_windows_notification (Windows)、send_apple_notification (Apple) など、プラットフォーム通知の送信を有効にするレベルの高いメソッドを追加します。

### Windows ストアおよび Windows Phone 8.1 (非 Silverlight)

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

### Windows Phone 8.0 および 8.1 Silverlight

    hub.send_mpns_notification(toast)

### iOS

    alert_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_apple_notification(alert_payload)

### Android

    gcm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_gcm_notification(gcm_payload)

### Kindle Fire

    adm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_adm_notification(adm_payload)

### Baidu

    baidu_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_baidu_notification(baidu_payload)

Python コードを実行すると、ターゲット デバイスに表示される通知が生成されます。

## 次に例を示します。

### デバッグ プロパティを有効にする

通知ハブの初期化中にデバッグ フラグを有効にすると、詳細な HTTP 要求と応答ダンプだけでなく、次のような NotificationOutcome が表示され、要求で渡される HTTP ヘッダーや通知ハブから受信した HTTP 応答について把握できるようになります。
    ![][1]

通知ハブの結果についての詳細が表示されます。

- 例: プッシュ通知サービスにメッセージが正常に送信される場合。

        <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- プッシュ通知の対象が見つからなかった場合、応答では次が表示される可能性が高くなります (登録でタグが一致しなかったために登録がなかったことを示す)。

        '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'


### Windows にトースト通知をブロードキャストする

ブロードキャストのトースト通知を Windows クライアントに送信するときにヘッダーも送信されることに注意してください。

    hub.send_windows_notification(wns_payload)

![][2]

### タグ (またはタグ式) を指定して通知を送信する

HTTP 要求に追加される HTTP ヘッダーのタグに注意してください (次の例では 'sports' ペイロードの登録のみに対し通知を送信します)

    hub.send_windows_notification(wns_payload, "sports")

![][3]

### 複数のタグを指定すて通知を送信する

複数のタグが送信される場合に HTTP ヘッダーが変更するタグに注意してください。

    tags = {'sports', 'politics'}
    hub.send_windows_notification(wns_payload, tags)

![][4]

### テンプレート化された通知

HTTP ヘッダーが変更する形式と、ペイロードの本文が HTTP 要求本文の一部として送信されることに注意してください。

**クライアント側 - 登録済みのテンプレート**

        var template =
                        @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";

**サーバー側 - ペイロードの送信**

        template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
        hub.send_template_notification(template_payload)

![][5]


## 次のステップ

このトピックでは、Notification Hubs 用の単純な Python REST クライアントの作成方法を説明しました。 次は、以下を実行できます。

* 上記のコードをすべて含むフル [Python REST ラッパー サンプル] をダウンロードします。
* 引き続き、「[ニュース速報チュートリアル]」で Notification Hubs のタグ付け機能について学習してください。
* 引き続き Notification Hubs のテンプレート機能について学習して「[ニュースのローカライズ チュートリアル]




[python rest wrapper sample]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python 
[get started tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ 
[breaking news tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/ 
[localizing news tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/ 
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png 
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png 
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png 
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png 
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png 

