<properties 
    pageTitle="Media Services REST API による資産配信ポリシーの構成" 
    description="このトピックでは、Media Services REST API を利用し、さまざまな資産配信ポリシーを構成する方法を示します。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2015" 
    ms.author="juliako"/>


# 方法: 資産の配信ポリシーを構成する

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

動的に暗号化した資産を配信する場合、Media Services におけるコンテンツ配信ワークフローの手順の 1 つとして、資産の配信ポリシーを構成します。 資産の配信ポリシーは、資産を配信する方法、つまりどのストリーミング プロトコルで資産を動的パッケージングするか (例 : MPEG DASH、HLS、スムーズ ストリーミング、またはすべて)、資産を動的に暗号化するかどうか、どの暗号化方法を使用するか (エンベロープ暗号化または共通暗号化) を Media Services に示します。

このトピックでは、資産の配信ポリシーを作成して構成する理由と方法をご説明します。
>[AZURE.NOTE]動的パッケージ化と動的暗号化を使用するには、少なくとも 1 つのスケール ユニット (ストリーミング ユニットとも呼ばれる) が存在している必要があります。 詳細については、次を参照してください。 [メディア サービスの規模変更方法](media-services-manage-origins.md#scale_streaming_endpoints)します。 
>
>また、資産には、一連のアダプティブ ビットレート MP4、またはアダプティブ ビットレート スムーズ ストリーミング ファイルが含まれている必要があります。  

1 つの資産にはさまざまなポリシーを適用できます。 たとえば、スムーズ ストリーミングに PlayReady 暗号化を適用し、MPEG DASH と HLS に AES エンベロープ暗号化を適用できます。 配信ポリシーで定義されていないプロトコル (たとえば、プロトコルとして HLS のみを指定する 1 つのポリシーを追加した場合) は、ストリーミングからブロックされます。 ただし、資産配信ポリシーをまったく定義していない場合は例外となります。 この場合、すべてのプロトコルが平文で許可されます。

ストレージ暗号化資産を配信する場合は、資産の配信ポリシーを構成する必要があることにご注意ください。 資産をストリームするには、ストリーミング サーバーでストレージ暗号化を解除し、指定された配信ポリシーを使用してコンテンツをストリームする必要があります。 たとえば、Advanced Encryption Standard (AES) エンベロープ暗号化キーを使用して暗号化された資産を配信するには、ポリシーの種類を **DynamicEnvelopeEncryption** に設定します。 ストレージ暗号化を解除して資産を平文でストリームするには、ポリシーの種類を **NoDynamicEncryption** に設定します。 これらのポリシーの種類を構成する例を次に示します。

資産の配信ポリシーの構成方法に応じて、動的パッケージングと動的暗号化を実行し、次のストリーミング プロトコル (スムーズ ストリーミング、HLS、MPEG DASH、HDS ストリーム) を使用してストリームを行うことができます。

次の一覧に、スムース、HLS、DASH、HDS でストリームを行うために使用するフォーマットを示します。

スムーズ ストリーミング:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

HDS

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

アセットを発行してストリーミング URL を構築する方法については、次を参照してください。 [ストリーミング URL の構築](media-services-deliver-streaming-content.md)します。
>[AZURE.NOTE] Media Services REST API を使用する場合は、次のことに考慮します。
>
>Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。 詳細については、次を参照してください。 [Media Services REST API 開発用セットアップ](media-services-rest-how-to-use.md)します。

>Https://media.windows.net に正常に接続されると、別の Media Services の URI を指定する 301 リダイレクトを受け取ります。 」の説明に従って、新しい URI に後続の呼び出しを行う必要があります [REST API を使用して Media Services に接続する](media-services-rest-connect_programmatically.md)します。


## 資産の配信ポリシーを解除する

### <a id="create_asset_delivery_policy"></a>アセット配信ポリシーを作成します。

次の HTTP 要求により、アセット配信ポリシーを動的な暗号化を適用しないと、次のプロトコルのいずれかでストリームを配信するを指定する: MPEG DASH、HLS、およびスムーズ ストリーミング プロトコル。

AssetDeliveryPolicy を作成するときに指定できる値について、次を参照してください。、 [AssetDeliveryPolicy を定義するときに使用される型](#types) セクションです。


要求:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net
    
    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

応答:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT
    
    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}

### <a id="link_asset_with_asset_delivery_policy"></a>アセットをアセット配信ポリシーにリンク

次の HTTP 要求により、指定した資産が資産配信ポリシーにリンクされます。

要求:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-3344-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net
    
    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

応答:

    HTTP/1.1 204 No Content

## DynamicEnvelopeEncryption 資産の配信ポリシー

### EnvelopeEncryption タイプのコンテンツ キーを作成して資産にリンクする

DynamicEnvelopeEncryption delivery 配信ポリシーを指定する際に、資産を EnvelopeEncryption タイプのコンテンツ キーにリンクする必要があります。 詳細についてを参照してください: [コンテンツ キーの作成](media-services-rest-create-contentkey.md))します。


### <a id="get_delivery_url"></a>配信 URL を取得します。

前の手順で作成したコンテンツ キーの指定された配信方法向けの配信 URL を取得します。 クライアントは取得した URL を使用し、保護されたコンテンツを再生するために AES キーまたは PlayReady ライセンスを要求します。

HTTP 要求の本文に含める URL の種類を指定します。 PlayReady を使用してコンテンツを保護する場合は、keyDeliveryType に 1 を指定して({"keyDeliveryType":1})、Media Services PlayReady ライセンス取得 URL を要求します。 エンベロープ暗号化を使用してコンテンツを保護する場合は、keyDeliveryType に 2 を指定して ({"keyDeliveryType":2})、キー取得 URL を要求します。

要求:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423452029&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=IEXV06e3drSIN5naFRBdhJZCbfEqQbFZsGSIGmawhEo%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21
    
    {"keyDeliveryType":2}

応答:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}

### 資産の配信ポリシーを作成する

次の HTTP 要求により、**HLS** プロトコルに動的エンベロープ暗号化 (**DynamicEnvelopeEncryption**) を適用するように構成された **AssetDeliveryPolicy** が作成されます (この例では、それ以外のプロトコルはストリーミングからブロックされます)。


AssetDeliveryPolicy を作成するときに指定できる値について、次を参照してください。、 [AssetDeliveryPolicy を定義するときに使用される型](#types) セクションです。

要求:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}

応答:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}

### 資産を資産配信ポリシーにリンクする

を参照してください [アセットをアセット配信ポリシーにリンク](#link_asset_with_asset_delivery_policy)

## DynamicCommonEncryption 資産の配信ポリシー

### CommonEncryption タイプのコンテンツ キーを作成して資産にリンクする

DynamicCommonEncryption 配信ポリシーを指定する際に、資産を CommonEncryption タイプのコンテンツ キーにリンクする必要があります。 詳細についてを参照してください: [コンテンツ キーの作成](media-services-rest-create-contentkey.md))します。


### 配信 URL を取得する

前の手順で作成されたコンテンツ キーの PlayReady 配信方法向けの配信 URL を取得します。 クライアントは取得した URL を使用し、保護されたコンテンツを再生するために PlayReady ライセンスを要求します。 詳細については、次を参照してください。 [配信 URL を取得](#get_delivery_url)します。

### 資産の配信ポリシーを作成する

次の HTTP 要求により、**Smooth Streaming** プロトコルに動的共通暗号化 (**DynamicCommonEncryption**) を適用するように構成された **AssetDeliveryPolicy** が作成されます (この例では、他のプロトコルはストリーミングからブロックされます)。

AssetDeliveryPolicy を作成するときに指定できる値について、次を参照してください。、 [AssetDeliveryPolicy を定義するときに使用される型](#types) セクションです。


要求:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}

Widevine DRM を使用してコンテンツを保護する場合は、WidevineLicenseAcquisitionUrl (値 7 が設定されています) を使用するように AssetDeliveryConfiguration の値を更新し、ライセンス配信サービスの URL を指定します。 Widevine ライセンスを配信するため、次の AMS パートナーを使用することができます: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), 、[EZDRM](http://ezdrm.com/), 、[castLabs](http://castlabs.com/company/partners/azure/)します。

次に例を示します。


    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

>[AZURE.NOTE]Widevine を使用して暗号化する場合、配信は DASH でのみ実行できます。 資産配信プロトコルに必ず DASH (2) を指定してください。

### 資産を資産配信ポリシーにリンクする

を参照してください [アセットをアセット配信ポリシーにリンク](#link_asset_with_asset_delivery_policy)


## <a id="types"></a>AssetDeliveryPolicy の定義に使用される型

### AssetDeliveryProtocol

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,
    
        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,
    
        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,
    
        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,
    
        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,
    
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,
    
        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 
    
        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  
    
        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,
    
        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }

### ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    ///
    </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,
    
        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,
    
        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,
    
        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3
    
    }

### AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,
    
        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,
    
        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
    
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,
    
        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,
    
        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,
    
        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,
    
        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

## Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





