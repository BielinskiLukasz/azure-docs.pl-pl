<properties
    pageTitle="Azure API Management で API を保護する | Microsoft Azure"
    description="クォータとスロットル (レート制限) ポリシーを使用して、API を保護する方法について説明します。"
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
    ms.date="12/07/2015"
    ms.author="sdanie"/>


# Azure API Management でレート制限を使用して API を保護する

このガイドでは、Azure API Management でレート制限やクォータ ポリシーを構成することによって、いかに簡単にバックエンド API の保護を追加できるかを示します。

このチュートリアルでは、開発者が毎分最大 10 回、1 週間に最大 200 回まで呼び出すことができる "無料試用版" の API 成果物を作成します。 次に、API を発行し、レート制限ポリシーをテストします。
>[AZURE.NOTE] 既に製品が構成されているし、このチュートリアルで使用する、途中のレッスンできますに [構成呼び出しレート制限とクォータ ポリシーの][] を無料の評価版成果物の代わりに、製品を使用するチュートリアルに従います。

## <a name="create-product"> </a>製品を作成するには

この手順では、サブスクリプションの承認が不要な無料試用版の成果物を作成します。

最初に、ご利用の API Management サービスの Azure クラシック ポータルで **[管理]** をクリックします。 API Management パブリッシャー ポータルが表示されます。

![パブリッシャー ポータル][api-management-management-console]

>API Management サービス インスタンスを作成がない場合は、次を参照してください。 [API Management サービス インスタンスの [] を作成する][] で、 [Azure API Management の [] を使ってみる][] チュートリアルです。

左側の **[API Management]** メニューにある **[成果物]** をクリックして、**[成果物]** ページを表示します。

![製品の追加][api-management-add-product]

**[成果物の追加]** をクリックして **[新しい成果物の追加]** ダイアログ ボックスを表示します。

![新しいの製品を追加][api-management-new-product-window]

**[タイトル]** ボックスに、「**無料試用版**」と入力します。

**説明** ボックスに、次のテキストを入力します。
 **サブスクライバーは 200 呼び出し/週のアクセスが拒否された後、最大 10 個の呼び出し/1 分に実行できるようにします。**

API Management の成果物は、保護することも開くこともできます。 保護された成果物を使用するには、事前にサブスクライブする必要があります。 オープンな成果物は、サブスクリプションがなくても使用できます。 **[サブスクリプションが必要]** チェック ボックスがオンになっていることを確認し、サブスクリプションが必要な保護された成果物を作成します。 これは、既定の設定です。

この成果物に対するサブスクリプションの申し込みを管理者の審査の下で承認または拒否する場合は、**[サブスクリプションの承認を必須とする]** チェック ボックスをオンにします。 このチェック ボックスがオフの場合、サブスクリプションの申し込みは自動承認されます。 この例では、サブスクリプションを自動的に承認するため、チェック ボックスはオフにしてください。

開発者のアカウントで複数回新しい成果物にサブスクライブできるようにするには、**[複数の同時サブスクリプションを許可する]** チェック ボックスをオンにします。 このチュートリアルでは複数の同時サブスクリプションを使用しないため、これはオフのままにしておいてください。

すべての値を入力したら、**[保存]** をクリックして成果物を作成します。

![製品が追加されました][api-management-product-added]

**Administrators** グループのユーザーには、既定で、新しい成果物が表示されます。 ここでは、**Developers** グループを追加します。 **[無料試用版]** をクリックし、**[表示]** タブをクリックします。

>API Management では、開発者に成果物の表示を許可するかどうかが、グループを使用して管理されます。 成果物の表示の可否はグループに対して付与されます。開発者は、自分が所属するグループから見える成果物を表示してサブスクライブすることができます。 詳細については、次を参照してください。 [を作成し、Azure API Management の [] でグループを使用する方法][]します。

![開発者グループの追加][api-management-add-developers-group]

**[Developers]** チェック ボックスをオンにし、**[保存]** をクリックします。

## <a name="add-api"> </a>API を製品に追加するには

このチュートリアル ステップでは、新しい無料試用版の成果物に Echo API を追加します。

>それぞれの API Management サービス インスタンスには、Echo API があらかじめ構成されています。API Management を体験、学習する目的で使用することができます。 詳細については、次を参照してください。 [Azure API Management の [] を使ってみる][]します。

成果物を構成するには、左側の **[API Management]** メニューにある **[成果物]** をクリックし、**[無料試用版]** をクリックします。

![製品の構成][api-management-configure-product]

**[成果物への API の追加]** をクリックします。

![API を製品に追加][api-management-add-api]

**[Echo API]** を選択して、**[保存]** をクリックします。

![Echo API の追加][api-management-add-echo-api]

## <a name="policies"> </a>呼び出しレート制限とクォータ ポリシーを構成するには

レート制限とクォータは、ポリシー エディターで構成します。 左側の **[API Management]** メニューの下にある **[ポリシー]** をクリックします。 **[成果物]** ボックスの一覧で **[無料試用版]** をクリックします。

![製品のポリシー][api-management-product-policy]

**[ポリシーの追加]** をクリックしてポリシー テンプレートをインポートし、レート制限とクォータ ポリシーの作成を開始します。

![ポリシーの追加][api-management-add-policy]

ポリシーを挿入し、ポリシー テンプレートの **inbound** または **outbound** セクションにカーソルを置きます。 レート制限ポリシーとクォータ ポリシーは inbound ポリシーなので、カーソルを inbound 要素に置きます。

![ポリシー エディター][api-management-policy-editor-inbound]

2 つのポリシーを追加このチュートリアルでは、 [制限呼び出しレート][] と [セット使用量クォータ][] ポリシーです。

![ポリシー ステートメント][api-management-limit-policies]

**inbound** ポリシー要素にカーソルを置いたら、**[呼び出しレート制限]** の横の矢印をクリックしてそのポリシー テンプレートを挿入します。

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**[呼び出しレート制限]** は、成果物レベルのほか、API レベルや個々の操作名レベルで使用することもできます。 このチュートリアルで使用するのは、成果物レベルのポリシーだけです。そのため、**api** 要素と **operation** 要素は **rate-limit** 要素から削除してください。次の例に示すとおり、外部の **rate-limit** 要素だけが残ります。

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

この無料試用版の成果物で許容される呼び出しレートは 1 分間に最大 10 回です。**calls** 属性の値には「**10**」、**renewal-period** 属性の値には「**60**」と入力します。

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

**[使用量クォータの設定]** ポリシーを構成するには、**inbound** 要素内に新しく追加した **rate-limit** 要素のすぐ下にカーソルを置き、**[使用量クォータの設定]** の左側の矢印をクリックします。

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

このポリシーも成果物レベルで適用するためのものなので、**api** と **operation** の name 要素は削除してください。その例を次に示します。

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

クォータは、一定時間あたりの呼び出し回数、帯域幅、またはその両方を基準にすることができます。 このチュートリアルでは、帯域幅に基づく帯域幅調整は行いません。**bandwidth** 属性は削除してください。

    <quota calls="number" renewal-period="seconds">
    </quota>

この無料試用版の成果物には、呼び出し回数を 1 週間あたり 200 回とするクォータを割り当てます。 **calls** 属性の値として「**200**」を指定し、**renewal-period** 属性の値として「**604800**」を指定してください。

    <quota calls="200" renewal-period="604800">
    </quota>

>ポリシーの間隔は秒単位で指定します。 1 週間の秒数は、日数 (7) に 1 日の時間数 (24)、1 時間 (60) の分数、1 分間の秒数 (60) を掛けて求めることができます (7 * 24 * 60 * 60 = 604800)。

完成したポリシーは、次のようになります。

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />
    
    </inbound>
    <outbound>
    
        <base />
    
        </outbound>
    </policies>

必要なポリシーの構成が完成したら、**[保存]** をクリックします。

![ポリシーの保存][api-management-policy-save]

## <a name="publish-product"> </a> 成果物の発行するには

API を追加し、ポリシーを構成したら、成果物を開発者が使用できるように発行する必要があります。 成果物を構成するには、左側の **[API Management]** メニューにある **[成果物]** をクリックし、**[無料試用版]** をクリックします。

![製品の構成][api-management-configure-product]

**[発行]** をクリックし、**[はい。発行します]** をクリックして確定します。

![製品の発行][api-management-publish-product]

## <a name="subscribe-account"> </a>製品に開発者アカウントを購読するには

発行された成果物は、開発者がサブスクライブして使用できます。

>API Management インスタンスの管理者には、すべての成果物に対するサブスクリプションが自動的に設定されます。 このチュートリアル ステップでは、いずれかの開発者アカウント (管理者以外) に、無料試用版の成果物のサブスクリプションを追加します。 ご利用の開発者アカウントが Administrators ロールに属し、既にサブスクリプションが存在する場合でも、この手順に従ってかまいません。

左側の **[API Management]** メニューにある **[ユーザー]** をクリックし、目的の開発者アカウントの名前をクリックします。 この例では、**Clayton Gragg** という開発者アカウントを使用します。

![開発者の構成][api-management-configure-developer]

**[サブスクリプションの追加]** をクリックします。

![サブスクリプションの追加][api-management-add-subscription-menu]

**[無料試用版]** を選択し、**[サブスクライブ]** をクリックします。

![サブスクリプションの追加][api-management-add-subscription]
>[AZURE.NOTE] このチュートリアルの場合、無料試用版の成果物の複数の同時サブスクリプションは無効になっています。 有効になっている場合は、次の例に示すように、サブスクリプションに名前を付けるように促されます。

![サブスクリプションの追加][api-management-add-subscription-multiple]

**[サブスクライブ]** をクリックすると、ユーザーの **[サブスクリプション]** リストに成果物が表示されます。

![サブスクリプションを追加しました][api-management-subscription-added]

## <a name="test-rate-limit"> </a>操作の呼び出しし、レート制限をテストするには

無料試用版成果物の構成と発行は以上で完了です。今度は、いくつかの操作を呼び出して、レート制限ポリシーをテストしてみましょう。
右上のメニューにある **[開発者ポータル]** をクリックして開発者ポータルに切り替えてください。

![開発者ポータル][api-management-developer-portal-menu]

上部のメニューの **[API]** をクリックし、**[Echo API]** をクリックします。

![開発者ポータル][api-management-developer-portal-api-menu]

**[GET リソース]** をクリックし、**[コンソールを開く]** をクリックします。

![コンソールを開く][api-management-open-console]

既定のパラメーターの値はそのままにし、対象となる無料試用版の成果物のサブスクリプション キーを選択します。

![サブスクリプション キー][api-management-select-key]
>[AZURE.NOTE] 複数のサブスクリプションがある場合は、必ず、**[無料試用版]** のキーを選択してください。そうしないと、これまでの手順で構成したポリシーが有効になりません。

**[HTTP GET]** をクリックして応答を確認します。 **[応答のステータス]** が "**200 OK**" と表示されることに注目してください。

![Operation results][api-management-http-get-results]

呼び出しレート制限ポリシー (1 分間に 10 回) を超える頻度で **[HTTP Get]** をクリックします。 レート制限ポリシーを超えると、応答ステータスとして "**429 要求が多すぎます**" が返されます。

![操作の結果][api-management-http-get-429]

**[応答ヘッダー]** 領域と **[応答の内容]** 領域は、再試行が成功するまでの残り時間を示します。

1 分間に 10 回という呼び出しレート制限ポリシーが有効な場合、レート制限超過となった連続 10 回の呼び出しの 1 回目から 60 秒が経過するまで、その成果物に対する以降の呼び出しはエラーになります。 この例の場合、残り時間は 43 秒です。

## <a name="next-steps"> </a>次のステップ

-   [その他のトピックもチェックして、 [高度な API の構成を使ってみる][] チュートリアルです。
-   詳細およびレート制限とクォータの設定のデモについては、次のビデオをご覧ください。

> [AZURE.VIDEO rate-limits-and-quotas]



[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png 
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png 
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png 
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png 
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png 
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png 
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png 
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png 
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png 
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png 
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png 
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png 
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png 
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png 
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png 
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png 
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png 
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png 
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png 
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png 
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png 
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png 
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png 
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png 
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png 
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png 
[how to add operations to an api]: api-management-howto-add-operations.md 
[how to add and publish a product]: api-management-howto-add-products.md 
[monitoring and analytics]: ../api-management-monitoring.md 
[add apis to a product]: api-management-howto-add-products.md#add-apis 
[publish a product]: api-management-howto-add-products.md#publish-product 
[get started with azure api management]: api-management-get-started.md 
[how to create and use groups in azure api management]: api-management-howto-create-groups.md 
[view subscribers to a product]: api-management-howto-add-products.md#view-subscribers 
[get started with azure api management]: api-management-get-started.md 
[create an api management service instance]: api-management-get-started.md#create-service-instance 
[next steps]: #next-steps 
[create a product]: #create-product 
[configure call rate limit and quota policies]: #policies 
[add an api to the product]: #add-api 
[publish the product]: #publish-product 
[subscribe a developer account to the product]: #subscribe-account 
[call an operation and test the rate limit]: #test-rate-limit 
[get started with advanced api configuration]: api-management-get-started-advanced.md 
[limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate 
[set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota 

