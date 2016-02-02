<properties 
    pageTitle="ポータルを使用してコンテンツ キー承認ポリシーを構成します。" 
    description="コンテンツ キー承認ポリシーを構成する方法について説明します。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/04/2015" 
    ms.author="juliako"/>




# コンテンツ キー承認ポリシーを構成する

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


## 概要

Microsoft Azure Media Services では、Advanced Encryption Standard (AES) (128 ビット暗号化キーを使用) と PlayReady DRM を使用して動的に暗号化したコンテンツを配信できます。 Media Services は、クライアントがキーまたはライセンスを取得して暗号化されたコンテンツを再生する **Key\License 配信サービス**も提供しています。

このトピックでは、**Azure クラシック ポータル**を使用して、コンテンツ キー承認ポリシーを構成する方法について説明します。 キーは、動的にコンテンツを暗号化するために、後で使用できます。 現時点で暗号化できるストリーミング形式は、HLS、MPEG DASH、およびスムーズ ストリーミングであることに注意してください。 HDS 形式のストリーミングやプログレッシブ ダウンロードは暗号化できません。

プレーヤーが動的に暗号化するように設定されたストリームを要求すると、Media Services は、AES または PlayReady 暗号化で構成済みのキーを使用してコンテンツを暗号化します。 ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。 ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定した承認ポリシーを評価します。


複数のコンテンツ キーを設定する、または Media Services のキー配信サービス以外の **Key\License 配信サービス** URL を指定する場合は、Media Services .NET SDK または REST API を使用します。

[Media Services .NET SDK を使用してコンテンツ キー承認ポリシーを構成します。](media-services-dotnet-configure-content-key-auth-policy.md)

[Media Services REST API を使用してコンテンツ キー承認ポリシーを構成します。](media-services-rest-configure-content-key-auth-policy.md)

### いくつかの考慮事項が適用されます。

- 動的パッケージングや動的暗号化を使用するには、少なくとも 1 つのストリーミング予約ユニットが必要です。 詳細については、次を参照してください。 [メディア サービスの規模変更方法](media-services-manage-origins.md#scale_streaming_endpoints)します。
- 資産には、一連のアダプティブ ビットレート MP4 または アダプティブ ビットレート スムーズ ストリーミング ファイルが含まれている必要があります。 詳細については、次を参照してください。 [アセットをエンコードする](media-services-encode-asset.md)します。
- キー配信サービスでは、ContentKeyAuthorizationPolicy とそれに関連するオブジェクト (ポリシーのオプションと制限) を 15 分間キャッシュします。 ContentKeyAuthorizationPolicy を作成して、"Token" 制限を使用するように指定した場合に、"Token" 制限をテストしてから、ポリシーを "Open" 制限に更新すると、ポリシーが "Open" バージョンのポリシーに切り替わるまで、約 15 分かかります。


## 方法: キーの承認ポリシーを構成する

キーの承認ポリシーを構成するには、**[コンテンツ保護]** ページを選択します。

Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーには、**オープン**、**トークン**、または**IP** 承認制限を設定することができます (**IP** は REST または .NET SDK を使用して構成できます)。

### オープン制限

**オープン**制限では、キーを要求するすべてのユーザーにシステムがキーを配信します。 この制限は、テストに便利です。

![OpenPolicy][open_policy]

### トークン制限

トークン制限ポリシーを選択するには、**[トークン]** ボタンをクリックします。

**トークン**制限ポリシーには、**Secure Token Service** (STS) によって発行されたトークンを含める必要があります。 Media Services でのトークンをサポートする、 **Simple Web Tokens** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 形式と **JSON Web Token** (JWT) 形式です。 詳細については、次を参照してください。 [JWT トークン認証](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)します。

Media Services では、**Secure Token Service** は提供されません。 トークンを発行するには、カスタム STS を作成するか、Microsoft Azure ACS を活用できます。 STS は、トークン、トークンの制限の構成で指定した、指定されたキーと問題要求で署名を作成するように構成する必要があります。 Media Services のキー配信サービスは、トークンが有効で、トークン内のクレームがコンテンツ キー向けに構成されたクレームと一致する場合、暗号化キーをクライアントに返します。 詳細については、次を参照してください。 [トークンを発行するように使用する Azure ACS](http://mingfeiy.com/acs-with-key-services)します。

**トークン**制限ポリシーを構成する場合は、**検証キー**、**発行者**、**対象ユーザー**の値を設定する必要があります。 プライマリ検証キーには、トークンの署名に使用されたキーが含まれ、発行者は、トークンを発行するセキュリティ トークン サービスです。 対象ユーザー (スコープとも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。 Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。

### PlayReady

**PlayReady** を使用してコンテンツを保護する場合、PlayReady ライセンス テンプレートを定義する XML 文字列を承認ポリシーで指定する必要があります。 既定では、次のポリシーが設定されます。

    <PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <LicenseType>Nonpersistent</LicenseType>
          <PlayRight>
            <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
          </PlayRight>
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

クリックして、 **ポリシー xml のインポート** ボタンをクリックし、定義されている XML スキーマに準拠した別の XML を指定 [ここ](https://msdn.microsoft.com/library/azure/dn783459.aspx)します。


## Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## 次のステップ

これでをコンテンツ キー承認ポリシーを構成している「、 [方法: Azure クラシック ポータルを使用して暗号化有効にする](../media-services-manage-content#encrypt/) トピックです。



[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png 
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png 

