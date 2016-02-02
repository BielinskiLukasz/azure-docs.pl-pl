<properties
    pageTitle="キャッシュを追加して Azure API Management のパフォーマンスを向上させる | Microsoft Azure"
    description="API Management のサービスの呼び出しで、遅延、帯域幅の消費、Web サービスの負荷を改善させる方法について説明します。"
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
    ms.topic="get-started-article"
    ms.date="12/16/2015"
    ms.author="sdanie"/>


# キャッシュを追加して Azure API Management のパフォーマンスを向上させる

API Management では、応答のキャッシュ用に操作を構成できます。 応答のキャッシュを行うと、API の遅延、帯域幅の消費、頻繁に変更されないデータの Web サービスの負荷が大幅に小さくなります。

このガイドでは、API の応答のキャッシュを追加して、サンプルの Echo API 操作のポリシーを構成する方法を示します。 その後は、開発者ポータルで操作を呼び出してキャッシュの動作を確認することができます。
>[AZURE.NOTE] ポリシー式を使用してキーによって項目をキャッシュする方法については、次を参照してください。 [Azure API Management でのキャッシュ カスタム](api-management-sample-cache-by-key.md)します。

## 前提条件

このガイドの手順を実行するには、API Management サービスのインスタンスに API と成果物を構成しておく必要があります。 API Management サービス インスタンスを作成がない場合は、次を参照してください。 [API Management サービス インスタンスの [] を作成する][] で、 [Azure API Management の [] を使ってみる][] チュートリアルです。

## <a name="configure-caching"> </a>キャッシュ用の操作を構成します。

このステップでは、サンプル Echo API の **GET Resource (cached)** 操作のキャッシュ設定を確認します。
>[AZURE.NOTE] それぞれの API Management サービス インスタンスには、Echo API があらかじめ構成されています。API Management を体験、学習する目的で使用することができます。 詳細については、次を参照してください。 [Azure API Management の [] を使ってみる][]します。

最初に、ご利用の API Management サービスの Azure クラシック ポータルで **[管理]** をクリックします。 API Management パブリッシャー ポータルが表示されます。

![パブリッシャー ポータル][api-management-management-console]

左側の **[API Management]** メニューの **[API]** をクリックし、**[Echo API]** をクリックします。

![Echo API][api-management-echo-api]

**[操作]** タブをクリックし、**[操作]** ボックスの一覧の **[GET Resource (cached)]** 操作をクリックします。

![Echo API 操作][api-management-echo-api-operations]

**[キャッシュ]** タブをクリックして、この操作のキャッシュ設定を表示します。

![[キャッシュ] タブ][api-management-caching-tab]

操作に対してキャッシュを有効にするには、**[有効]** チェック ボックスをオンにします。 この例では、キャッシュは有効になっています。

それぞれの操作の応答は、**[クエリ文字列パラメーターごとにキャッシュ]** フィールドと **[ヘッダーごとにキャッシュ]** フィールドの値に基づいてキー付けされます。 クエリ文字列パラメーターまたはヘッダーに基づいて複数の応答をキャッシュに格納するには、これらの 2 つのフィールドを使用して構成します。

**[期間]** は、キャッシュに入れられた応答の有効期間を指定します。 この例では、期間は 1 時間に相当する **3,600** 秒に設定されています。

この例のキャッシュ構成を使用した場合、**GET Resource (cached)** 操作への最初の要求に対し、バックエンド サービスから応答が返されます。 この応答は、キャッシュに格納され、指定されたヘッダーとクエリ文字列パラメーターによってキー付けされます。 パラメーターが一致する後続の操作の呼び出しに対しては、キャッシュの有効期間が超過するまで、キャッシュに格納された応答が返されます。

## <a name="caching-policies"> </a>キャッシュ ポリシーの確認

このステップでは、サンプル Echo API の **GET Resource (cached)** 操作のキャッシュ設定を確認します。

**[キャッシュ]** タブで操作に対してキャッシュ設定を構成すると、操作に対してキャッシュ ポリシーが追加されます。 これらのポリシーは、ポリシー エディターで表示および編集できます。

左側の **[API Management]** メニューの **[ポリシー]** をクリックし、**[操作]** ドロップダウン リストの **[Echo API / GET Resource (cached)]** を選択します。

![ポリシー スコープの操作][api-management-operation-dropdown]

ポリシー エディターにこの操作のポリシーが表示されます。

![API Management policy editor][api-management-policy-editor]

この操作のポリシー定義には、前のステップで **[キャッシュ]** タブを使用して確認したキャッシュ構成を定義するポリシーが含まれています。

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

>[AZURE.NOTE] ポリシー エディターでキャッシュ ポリシーに加えた変更は、操作の **[キャッシュ]** タブに反映されます (また、その逆の操作を行った場合も同様に変更が反映されます)。

## <a name="test-operation"> </a>操作の呼び出しし、キャッシュのテスト

キャッシュの動作を確認するには、開発者ポータルから操作を呼び出します。 右上のメニューで、**[開発者ポータル]** をクリックします。

![開発者ポータル][api-management-developer-portal-menu]

上部のメニューで **[API]** をクリックし、**[Echo API]** を選択します。

![Echo API][api-management-apis-echo-api]

>アカウントに対して構成されている (またはアカウントから見える) API が 1 つしかない場合、[API] をクリックすると、その API の操作に直接誘導されます。

**[GET Resource (cached)]** 操作を選択し、**[コンソールを開く]** をクリックします。

![コンソールを開く][api-management-open-console]

コンソールを使用すると、開発者ポータルから直接操作を呼び出すことができます。

![コンソール][api-management-console]

**[param1]** と **[param2]** については既定値のままにしておきます。

**[サブスクリプション キー]** ドロップダウン リストで目的のキーを選択します。 アカウントのサブスクリプションが 1 つしかない場合は自動的にそのサブスクリプションが選択されます。

**[要求ヘッダー]** ボックスに「**sampleheader:value1**」と入力します。

**[HTTP Get]** をクリックし、応答ヘッダーをメモしておきます。

**[要求ヘッダー]** ボックスに「**sampleheader:value2**」と入力します。**[HTTP Get]** をクリックします。

応答内の **sampleheader** の値が前と同じ **value1** であることに注目してください。 異なる値をいくつか試して、最初の呼び出しでキャッシュに格納された応答が返されることを確かめます。

**[param2]** フィールドに「**25**」と入力し、**[HTTP Get]** をクリックします。

応答内の **sampleheader** の値が **value2** になっていることに注目してください。 操作の結果はクエリ文字列によってキー付けされているため、以前のキャッシュに格納された応答は返されません。

## <a name="next-steps"> </a>次のステップ

-   [その他のトピックもチェックして、 [高度な API の構成を使ってみる][] チュートリアルです。
-   キャッシュ ポリシーの詳細については、次を参照してください。 [キャッシュ ポリシーの []][] で、 [API Management のポリシーを参照 []][]します。
-   ポリシー式を使用してキーによって項目をキャッシュする方法については、次を参照してください。 [Azure API Management でのキャッシュ カスタム](api-management-sample-cache-by-key.md)します。


[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png 
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png 
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png 
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png 
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png 
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png 
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png 
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png 
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png 
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png 
[how to add operations to an api]: api-management-howto-add-operations.md 
[how to add and publish a product]: api-management-howto-add-products.md 
[monitoring and analytics]: api-management-monitoring.md 
[add apis to a product]: api-management-howto-add-products.md#add-apis 
[publish a product]: api-management-howto-add-products.md#publish-product 
[get started with azure api management]: api-management-get-started.md 
[get started with advanced api configuration]: api-management-get-started-advanced.md 
[api management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx 
[caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx 
[create an api management service instance]: api-management-get-started.md#create-service-instance 
[configure an operation for caching]: #configure-caching 
[review the caching policies]: #caching-policies 
[call an operation and test the caching]: #test-operation 
[next steps]: #next-steps 

