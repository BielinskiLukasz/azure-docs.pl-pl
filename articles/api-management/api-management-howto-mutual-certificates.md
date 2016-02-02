<properties 
    pageTitle="Azure API Management でクライアント証明書認証を使用してバックエンド サービスを保護する方法" 
    description="Azure API Management でクライアント証明書認証を使用してバックエンド サービスを保護する方法を説明します。" 
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
    ms.date="12/07/2015" 
    ms.author="sdanie"/>


# Azure API Management でクライアント証明書認証を使用してバックエンド サービスを保護する方法

API Management には、クライアント証明書を使用して API のバックエンド サービスへのアクセスを保護する機能が備わっています。 このガイドでは、API パブリッシャー ポータルで証明書を管理する方法、および証明書を使用してバックエンド サービスにアクセスするように API を構成する方法を示します。

API Management REST API を使用して証明書の管理については、次を参照してください。 [Azure API Management REST API 証明書エンティティ []][]します。

## <a name="prerequisites"> </a>の前提条件

このガイドは、クライアント証明書認証を使用して API のバックエンド サービスにアクセスするように、API Management サービス インスタンスを構成する方法を示しています。 このトピックの手順を実行する前に、バックエンド サービスがクライアント証明書認証用に構成されていること、および API Management パブリッシャー ポータルでのアップロードのために証明書へのアクセス権限と証明書のパスワードとを持っていることが必要です。

## <a name="step1"> </a>クライアント証明書のアップロード

最初に、ご利用の API Management サービスの Azure クラシック ポータルで **[管理]** をクリックします。 API Management パブリッシャー ポータルが表示されます。

![API Publisher ポータル][api-management-management-console]

>API Management サービス インスタンスを作成がない場合は、次を参照してください。 [API Management サービス インスタンスの [] を作成する][] で、 [Azure API Management の [] を使ってみる][] チュートリアルです。

左側の **[API Management]** メニューで **[セキュリティ]** をクリックして、**[クライアント証明書]** をクリックします。

![クライアント証明書][api-management-security-client-certificates]

新しい証明書をアップロードするには、**[証明書のアップロード]** をクリックします。

![証明書のアップロード][api-management-upload-certificate]

使用する証明書を参照してから、証明書のパスワードを入力します。

>証明書は、**.pfx** 形式でなければなりません。 自己署名証明書も許可されます。

![証明書のアップロード][api-management-upload-certificate-form]

**[アップロード]** をクリックして、証明書をアップロードします。

>証明書のパスワードは、このとき検証されます。 パスワードが正しくない場合には、エラー メッセージが表示されます。

![アップロードされた証明書][api-management-certificate-uploaded]

証明書は、アップロードされた後に、**[クライアント証明書]** タブに表示されます。 複数の証明書の場合は、件名または拇印の最後の 4 文字以下で説明されているように証明書を使用する API を構成するときに、証明書を選択するために使用を行うように [ゲートウェイ認証に、クライアント証明書を使用するように API 構成][] セクションです。

## <a name="step1a"> </a>クライアント証明書の削除

証明書を削除するには、対象の証明書の横にある **[削除]** をクリックします。

![証明書の削除][api-management-certificate-delete]

**[はい、削除します (Yes, delete it)]** をクリックして確定します。

![削除の確定][api-management-confirm-delete]

証明書が API によって使用されている場合、警告画面が表示されます。 証明書を削除するには、最初に、証明書を使用するように構成されたすべての API からそれを削除する必要があります。

![削除の確定][api-management-confirm-delete-policy]

## <a name="step2"> </a>ゲートウェイの認証にクライアント証明書を使用する API を構成します。

左側の **[API Management]** メニューで **[API]** をクリックして、対象となる API の名前をクリックしてから、**[セキュリティ]** タブをクリックします。

![API セキュリティ][api-management-api-security]

**[証明書を使用]** ドロップダウン リストで、**[クライアント証明書]** を選択します。

![クライアント証明書][api-management-mutual-certificates]

**[クライアント証明書]** ドロップダウン リストから必要な証明書を選択します。 複数の証明書がある場合は、以前のセクションに記載されているとおり、件名または拇印の最後の 4 文字に注目して対象となる証明書を判別できます。

![証明書の選択][api-management-select-certificate]

**[保存]** をクリックして API に対する構成の変更を保存します。

>この変更は即時に有効になり、その API の操作の呼び出しは、証明書を使用してバックエンド サーバーを認証するようになります。

![API 変更の保存][api-management-save-api]

>API のバックエンド サービスのゲートウェイ認証に証明書を指定すると、その API のポリシーに追加され、ポリシー エディターで表示できるようになります。

![証明書ポリシー][api-management-certificate-policy]

## 次のステップ

HTTP 基本認証や共有シークレット認証など、バックエンド サービスをセキュリティで保護する他の方法の詳細については、次のビデオを参照してください。

> [AZURE.VIDEO last-mile-security]


[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png 
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png 
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png 
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png 
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png 
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png 
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png 
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png 
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png 
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png 
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png 
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png 
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png 
[how to add operations to an api]: api-management-howto-add-operations.md 
[how to add and publish a product]: api-management-howto-add-products.md 
[monitoring and analytics]: ../api-management-monitoring.md 
[add apis to a product]: api-management-howto-add-products.md#add-apis 
[publish a product]: api-management-howto-add-products.md#publish-product 
[get started with azure api management]: api-management-get-started.md 
[get started with advanced api configuration]: api-management-get-started-advanced.md 
[api management policy reference]: api-management-policy-reference.md 
[caching policies]: api-management-policy-reference.md#caching-policies 
[create an api management service instance]: api-management-get-started.md#create-service-instance 
[azure api management rest api certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx 
[webapp-graphapi-dotnet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet 
[prerequisites]: #prerequisites 
[upload a client certificate]: #step1 
[delete a client certificate]: #step1a 
[configure an api to use a client certificate for gateway authentication]: #step2 
[test the configuration by calling an operation in the developer portal]: #step3 
[next steps]: #next-steps 

