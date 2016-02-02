<properties 
    pageTitle="Azure API Management で API Inspector を使用して呼び出しをトレースする方法" 
    description="Azure API Management で API Inspector を使用して呼び出しをトレースする方法について説明します。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/03/2015" 
    ms.author="sdanie"/>


# Azure API Management で API Inspector を使用して呼び出しをトレースする方法

API Management には、API のデバッグとトラブルシューティングに役立つ API Inspector ツールが用意されています。 API Inspector は、プログラムで使用することも、開発者ポータルから直接使用することもできます。

API Inspector のものトレース、トレース操作だけでなく [ポリシー式](https://msdn.microsoft.com/library/azure/dn910913.aspx) 評価します。 デモについては、次を参照してください。 [クラウド カバー エピソード 177: API の管理機能より](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) を 21:00 まで早送りしています。

このガイドでは、API Inspector の使い方を順をおって説明していきます。
>[AZURE.NOTE] API Inspector のトレースがのみが生成されに属しているサブスクリプション キーが含まれる要求に使用できる、 [管理者](api-management-howto-create-groups.md) アカウントです。

## <a name="trace-call"> </a> の呼び出しをトレースする API Inspector を使用します。

API Inspector を使用するには、**ocp-apim-trace: true** 要求ヘッダーを操作の呼び出しに追加します。その後、**ocp-apim-trace-location** 応答ヘッダーで示される URL を使用してトレースをダウンロードし、内容を確認します。 この操作は、プログラムで実行することも、開発者ポータルから直接実行することもできます。

このチュートリアルで構成されている基本的な電卓 API を使用して操作をトレースする API Inspector を使用する方法を示しています、 [最初の Api](api-management-get-started.md) 概要チュートリアルです。 このチュートリアルをまだ完了していない場合は、Basic Calculator API をインポートするか (わずかの時間しかかかりません)、Echo API など、他のお好きな API を使用できます。 それぞれの API Management サービス インスタンスには、Echo API があらかじめ構成されています。API Management を体験、学習する目的で使用することができます。 Echo API は、受け取った入力をそのまま返します。 これを使用するには、任意の HTTP 動詞を呼び出します。すると、送った値がそのまま返されます。



最初に、ご利用の API Management サービスの Azure クラシック ポータルで **[開発者ポータル]** をクリックします。 開発者ポータルには、API の操作を見てテストするための便利な環境が用意されており、操作を直接呼び出すことができます。

>まだ API Management サービス インスタンスを作成していない場合は、次を参照してください。 [API Management サービス インスタンスの [] を作成する][] で、 [Azure API Management の [] を使ってみる][] チュートリアルです。

![API Management 開発者ポータル][api-management-developer-portal-menu]

上部のメニューの **[API]** をクリックし、**[Basic Calculator]** をクリックします。

![Echo API][api-management-api]

**2 つの整数を追加する**操作を試すには、**[試してみる]** をクリックします。

![試してみる][api-management-open-console]

既定のパラメーターの値はそのままにし、**[サブスクリプション キー]** ボックスの一覧で使用する製品のサブスクリプション キーを選択します。

開発者ポータルの既定では、**Ocp-Apim-Trace** ヘッダーは **true** に設定されています。 このヘッダーにより、トレースが生成されるかどうかが構成されます。

![送信][api-management-http-get]

**[送信]** をクリックし、操作を呼び出します。

![送信][api-management-send-results]

応答ヘッダーは、次の例のような値を含む **ocp-apim-trace-location** となります。

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

トレースは、次のステップに示すように、指定された場所からダウンロードして内容を確認できます。

## <a name="inspect-trace"> </a>トレースの確認

トレース内の値を確認するには、**ocp-apim-trace-location** URL からトレース ファイルをダウンロードします。 これは JSON 形式のテキスト ファイルで、次の例に示すようなエントリを含んでいます。

    {
        "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
        "traceEntries": {
            "inbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0725926",
                    "data": {
                        "request": {
                            "method": "GET",
                            "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "Connection",
                                    "value": "Keep-Alive"
                                },
                                {
                                    "name": "Host",
                                    "value": "contoso5.azure-api.net"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "mapper",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0726213",
                    "data": {
                        "configuration": {
                            "api": {
                                "from": "/calc",
                                "to": {
                                    "scheme": "http",
                                    "host": "calcapi.cloudapp.net",
                                    "port": 80,
                                    "path": "/api",
                                    "queryString": "",
                                    "query": {},
                                    "isDefaultPort": true
                                }
                            },
                            "operation": {
                                "method": "GET",
                                "uriTemplate": "/add?a={a}&b={b}"
                            },
                            "user": {
                                "id": 1,
                                "groups": [
                                    "Administrators",
                                    "Developers"
                                ]
                            },
                            "product": {
                                "id": 1
                            }
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0727522",
                    "data": {
                        "message": "Request is being forwarded to the backend service.",
                        "request": {
                            "method": "GET",
                            "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "X-Forwarded-For",
                                    "value": "33.52.215.35"
                                }
                            ]
                        }
                    }
                }
            ],
            "outbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1960601",
                    "data": {
                        "response": {
                            "status": {
                                "code": 200,
                                "reason": "OK"
                            },
                            "headers": [
                                {
                                    "name": "Pragma",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Length",
                                    "value": "124"
                                },
                                {
                                    "name": "Cache-Control",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Type",
                                    "value": "application/xml; charset=utf-8"
                                },
                                {
                                    "name": "Date",
                                    "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                                },
                                {
                                    "name": "Expires",
                                    "value": "-1"
                                },
                                {
                                    "name": "Server",
                                    "value": "Microsoft-IIS/8.5"
                                },
                                {
                                    "name": "X-AspNet-Version",
                                    "value": "4.0.30319"
                                },
                                {
                                    "name": "X-Powered-By",
                                    "value": "ASP.NET"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1961112",
                    "data": {
                        "message": "Response headers have been sent to the caller. Starting to stream the response body."
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1963155",
                    "data": {
                        "message": "Response body streaming to the caller is complete."
                    }
                }
            ]
        }
    }

## <a name="next-steps"> </a>次のステップ

-   [その他のトピックもチェックして、 [高度な API の構成を使ってみる][] チュートリアルです。
-   トレース内のポリシー表現のデモを見る [クラウド カバー エピソード 177: API の管理機能より](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)します。 デモを表示するには、21:00 まで早送りします。

>[AZURE.VIDEO episode-177-more-api-management-features-with-vlad-vinogradsky]


[use api inspector to trace a call]: #trace-call 
[inspect the trace]: #inspect-trace 
[next steps]: #next-steps 
[configure api settings]: api-management-howto-create-apis.md#configure-api-settings 
[responses]: api-management-howto-add-operations.md#responses 
[how create and publish a product]: api-management-howto-add-products.md 
[get started with azure api management]: api-management-get-started.md 
[create an api management service instance]: api-management-get-started.md#create-service-instance 
[get started with advanced api configuration]: api-management-get-started-advanced.md 
[azure classic portal]: https://manage.windowsazure.com/ 
[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png 
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png 
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png 
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png 
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png 
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png 
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png 

