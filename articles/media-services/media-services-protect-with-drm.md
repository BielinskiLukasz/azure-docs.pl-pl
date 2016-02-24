<properties
    pageTitle="PlayReady または Widevine の動的共通暗号化を使用する"
    description="Microsoft Azure Media Services を使用すると、MPEG DASH、Smooth Streaming、および HTTP ライブ ストリーミング (HLS) のストリームを Microsoft PlayReady DRM で保護して配信できます。 Widevine DRM で暗号化された DASH を配信することもできます。 このトピックでは、PlayReady と Widevine DRM を使用して動的に暗号化する方法について説明します。"
    services="media-services"
    documentationCenter=""
    authors="Juliako,Mingfeiy"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="12/09/2015"
    ms.author="juliako"/>


#PlayReady または Widevine の動的共通暗号化を使用する

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Microsoft Azure Media Services では、暗号化された MPEG DASH、スムーズ ストリーミングを配信することができで保護された HTTP Live Streaming (HLS) ストリーム [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/) ライセンス。 Widevine DRM ライセンスで暗号化された DASH ストリームを配信することもできます。 PlayReady と Widevine はいずれも共通暗号化 (ISO/IEC 23001-7 CENC) 仕様に従って暗号化されます。 使用する [AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (バージョン 3.5.1 で始まる) または Widevine を使用する、AssetDeliveryConfiguration を構成する REST API です。

Media Services では、Microsoft PlayReady ライセンスの配信サービスが提供されます。 また、Media Services で提供される API を使用して、保護されたコンテンツをユーザーが再生するときに PlayReady DRM ランタイムが適用する権限と制限を構成できます。 ユーザーが PlayReady で保護されたコンテンツを要求すると、プレーヤー アプリケーションが AMS ライセンス サービスにライセンスを要求します。 AMS ライセンス サービスは、プレーヤーが承認されていれば、ライセンスをプレーヤーに発行します。 PlayReady ライセンスには、クライアント プレーヤーがコンテンツの暗号化解除とストリーミングに使用できる暗号化解除キーが含まれています。

Media Services .NET SDK バージョン 3.5.2 以降、Media Services を使用すると Widevine ライセンス テンプレートを構成し、Widevine ライセンスを取得することもできます。 

>[AZURE.NOTE]Azure メディア サービスによって提供される Widevine ライセンス配信サービスはプレビュー段階です。 詳細については、次を参照してください。 [このブログ](http://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)します。

Widevine ライセンスを配信するため、以下の AMS パートナーを使用することもできます。 [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), 、[EZDRM](http://ezdrm.com/), 、[castLabs](http://castlabs.com/company/partners/azure/)します。 詳細についてを参照してください: との統合 [Axinom](media-services-axinom-integration.md) と [castLabs](media-services-castlabs-integration.md)します。

Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープンまたはトークン制限) を指定できます。 トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services でのトークンをサポートする、 [Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) 形式と [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) 形式です。 詳細については、「コンテンツ キー承認ポリシーを構成する」を参照してください。

動的暗号化を使用するには、一連のマルチビットレート MP4 ファイルまたはマルチビットレート Smooth Streaming ソース ファイルを含む資産が必要です。 また、資産の配信ポリシーを構成する必要があります (このトピック内で後述します)。 次に、ストリーミング URL で指定された形式に基づいて、オンデマンド ストリーミング サーバーは、選択されたプロトコルでストリームを配信できるようにします。 その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの各要求に応じて、適切な HTTP 応答を構築して返します。

このトピックには、PlayReady や Widevine など複数の DRM で保護されたメディアを配信するアプリケーションの開発に取り組む開発者にとって有用な情報が含まれています。 このトピックでは、認証ポリシーを使用する PlayReady ライセンス配信サービスの構成方法を説明します。これにより、許可されたクライアントのみが PlayReady または Widevine のライセンスを受け取ることができるようになります。 また、DASH を介した PlayReady または Widevine DRM による動的な暗号化を使用する方法も説明します。

>[AZURE.NOTE]動的暗号化を使用するには、まず、少なくとも 1 つのスケール単位 (ストリーミング ユニットとも呼ばれます) を取得する必要があります。 詳細については、次を参照してください。 [メディア サービスの規模変更方法](media-services-manage-origins.md#scale_streaming_endpoints)します。


##サンプルのダウンロード

この記事で説明されているサンプルをダウンロードすることができます [ここ](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)します。

##動的共通暗号化と DRM ライセンス配信サービスの構成

以下では、Media Services ライセンス配信サービスと動的暗号化を使用して、PlayReady で資産を保護する際に実行する必要のある一般的な手順について説明します。

1. 資産を作成し、その資産にファイルをアップロードします。 
1. ファイルが含まれる資産をアダプティブ ビットレート MP4 セットにエンコードします。
1. コンテンツ キーを作成し、それをエンコードした資産に関連付けます。 Media Services では、コンテンツ キーに資産の暗号化キーが含まれています。 
1. コンテンツ キーの承認ポリシーを構成します。 コンテンツ キー承認ポリシーを構成する必要があります。コンテンツ キーがクライアントに配信されるには、クライアントがこのコンテンツ キー承認ポリシーを満たしている必要があります。

    コンテンツ キー承認ポリシーを作成するときに、次を指定する必要があります。 配信方法 (PlayReady または Widevine) の制限 (オープンまたはトークン) と、キーをクライアントに配信する方法を定義するキー配信タイプに固有の情報 ([PlayReady](media-services-playready-license-template-overview.md) または [Widevine](media-services-widevine-license-template-overview.md) ライセンス テンプレート)。 
1. 資産の配信ポリシーを構成します。 配信ポリシーの構成には、配信プロトコル (たとえば、MPEG DASH、HLS、HDS、Smooth Streaming、またはそのすべて)、動的暗号化の種類 (たとえば、共通暗号化)、PlayReady または Widevine のライセンス取得 URL が含まれます。 
 
    同じ資産の各プロトコルに異なるポリシーを適用できます。 たとえば、PlayReady 暗号化を Smooth/DASH に適用し、AES Envelope を HLS に適用できます。 配信ポリシーで定義されていないプロトコル (たとえば、プロトコルとして HLS のみを指定する 1 つのポリシーを追加した場合) は、ストリーミングからブロックされます。 ただし、資産配信ポリシーをまったく定義していない場合は例外となります。 この場合、すべてのプロトコルが平文で許可されます。
1. ストリーミング URL を取得するために OnDemand ロケーターを作成します。

このトピックの最後に、詳細な .NET の例があります。

前述したワークフローを図にすると、次のようになります。 ここでは、認証のためにトークンが使用されています。

![PlayReady による保護](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

このトピックの残りの部分では、詳細な説明、コード例、および前述したタスクの実行方法を説明するトピックへのリンクを紹介します。 

##現時点での制限事項

資産の配信ポリシーを追加または更新する場合は、関連するロケーターを削除し (存在する場合)、新しいロケーターを作成する必要があります。

Azure Media Services で Widevine を使用して暗号化するときの制限事項として、現在は複数のコンテンツ キーはサポートされていません。 

##資産を作成し、その資産にファイルをアップロードする

ビデオの管理、エンコード、およびストリーミングを行うには、最初にコンテンツを Microsoft Azure Media Services にアップロードする必要があります。 コンテンツをアップロードすると、クラウドにコンテンツが安全に保存され、処理したりストリーミングしたりできるようになります。 

詳細については、次を参照してください。 [Media Services アカウントへのファイルのアップロード](media-services-dotnet-upload-files.md)します。

##ファイルが含まれる資産をアダプティブ ビットレート MP4 セットにエンコードする

動的暗号化を使用する場合に必要となるのは、一連のマルチビットレート MP4 ファイルまたはマルチビットレート Smooth Streaming ソース ファイルを含む資産の作成のみです。 そうすれば、マニフェストおよびフラグメント要求で指定された形式に基づき、オンデマンド ストリーミング サーバーによって、ユーザーが選択したプロトコルでストリームを受信するようになります。 その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて、適切な応答を構築して返します。 詳細については、次を参照してください。、 [動的パッケージの概要](media-services-dynamic-packaging-overview.md) トピックです。

エンコードする方法については、次を参照してください。 [メディア エンコーダー スタンダードを使用してアセットをエンコードする方法](media-services-dotnet-encode-with-media-encoder-standard.md)します。
    

##<a id="create_contentkey"></a>コンテンツ キーを作成し、エンコード済みアセットに関連付けます

Media Services では、コンテンツ キーに、資産を暗号化するときに使用するキーが含まれています。

詳細については、次を参照してください。 [コンテンツ キーセットを作成する](media-services-dotnet-create-contentkey.md)です。


##<a id="configure_key_auth_policy"></a>コンテンツ キー承認ポリシーを構成します。

Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーを構成する必要があります。キーがクライアント (プレーヤー) に配信されるには、クライアントがこのコンテンツ キー承認ポリシーを満たしている必要があります。 コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープンまたはトークン制限) を指定できます。

詳細については、次を参照してください。 [コンテンツ キー承認ポリシーを構成する](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption)です。

##<a id="configure_asset_delivery_policy"></a>資産配信ポリシーを構成する 

資産の配信ポリシーを構成します。 資産の配信ポリシーの構成には、次の内容が含まれます。

- DRM ライセンス取得 URL 
- 資産配信プロトコル (たとえば、MPEG DASH、HLS、HDS、Smooth Streaming、またはすべて)。 
- 動的暗号化のタイプ (この場合は、共通暗号化) 

詳細については、次を参照してください。 [アセット配信ポリシーを構成する ](media-services-rest-configure-asset-delivery-policy.md)です。

##<a id="create_locator"></a>オンデマンド ストリーミング URL を取得するためにストリーミング ロケーターを作成します。

Smooth、DASH、HLS のストリーミング URL をユーザーに提供する必要があります。

>[AZURE.NOTE]を追加またはアセットの配信ポリシーを更新する場合は、(存在する場合)、既存のロケーターを削除し、新しいロケーターを作成する必要があります。

アセットを発行してストリーミング URL を構築する方法については、次を参照してください。 [ストリーミング URL の構築](media-services-deliver-streaming-content.md)します。

##テスト トークンを取得する

キー認証ポリシーに使用されたトークンによる制限に基づいて、テスト トークンを取得します。

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

    
使用することができます、 [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) 、ストリームをテストします。

##<a id="example"></a>例


次の例では、Azure Media Services SDK for .Net バージョン 3.5.2 で導入された機能を示します (具体的には、Widevine ライセンス テンプレートを定義し、Azure Media Services に Widevine ライセンスを要求する機能)。 パッケージのインストールには、次の Nuget パッケージ コマンドを使用しました。

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. 新しいコンソール プロジェクトを作成します。
1. NuGet を使用して、Azure Media Services .NET SDK をインストールして追加します。
2. その他の参照 System.Configuration を追加します。
2. アカウント名とキー情報が含まれた構成ファイルを追加します。
    
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>
            
                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>
            
                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>

1. コンテンツ配信元となるストリーミング エンドポイントのストリーミング ユニットを少なくとも 1 つ取得する。 詳細についてを参照してください: [ストリーミング エンドポイントを構成](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal)します。

1. Program.cs ファイルのコードを、このセクションで示されているコードで上書きします。
    
    必ず変数を更新して、入力ファイルが置かれているフォルダーをポイントするようにしてください。
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
        using Newtonsoft.Json;
        
        namespace DynamicEncryptionWithDRM
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
                    Console.WriteLine();
        
                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);
        
                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();
        
                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();
        
                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
        
                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
        
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
        
                    Console.ReadLine();
                }
        
        
                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }
        
                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
        
                    var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
        
                    Console.WriteLine("Created assetFile {0}", assetFile.Name);
        
                    var policy = _context.AccessPolicies.Create(
                                            assetName,
                                            TimeSpan.FromDays(30),
                                            AccessPermissions.Write | AccessPermissions.List);
        
                    var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
        
                    Console.WriteLine("Upload {0}", assetFile.Name);
        
                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);
        
                    locator.Delete();
                    policy.Delete();
        
                    return inputAsset;
                }
        
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Create a task with the encoding details, using a string preset.
                    // In this case "H264 Multiple Bitrate 720p" preset is used.
                    ITask task = job.Tasks.AddNew("My encoding task",
                        processor,
                        "H264 Multiple Bitrate 720p",
                        TaskOptions.None);
        
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
        
                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {
                    // Create envelope encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
        
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy          
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                            {
                                new ContentKeyAuthorizationPolicyRestriction
                                {
                                    Name = "Open",
                                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                                    Requirements = null
                                }
                            };
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.Widevine,
                            restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with no restrictions").
                                Result;
        
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                            {
                                new ContentKeyAuthorizationPolicyRestriction
                                {
                                    Name = "Token Authorization Policy",
                                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                                    Requirements = tokenTemplateString,
                                }
                            };
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.Widevine,
                                restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
                }
        
                static private string GenerateTokenRequirements()
                {
                    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
        
                    template.PrimaryVerificationKey = new SymmetricVerificationKey();
                    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
                    template.Audience = _sampleAudience.ToString();
                    template.Issuer = _sampleIssuer.ToString();
                    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
        
                    return TokenRestrictionTemplateSerializer.Serialize(template);
                }
        
                static private string ConfigurePlayReadyLicenseTemplate()
                {
                    // The following code configures PlayReady License Template using .NET classes
                    // and returns the XML string.
        
                    //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
                    //It contains a field for a custom data string between the license server and the application 
                    //(may be useful for custom app logic) as well as a list of one or more license templates.
                    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
        
                    // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
                    // to be returned to the end users. 
                    //It contains the data on the content key in the license and any rights or restrictions to be 
                    //enforced by the PlayReady DRM runtime when using the content key.
                    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
                    //Configure whether the license is persistent (saved in persistent storage on the client) 
                    //or non-persistent (only held in memory while the player is using the license).  
                    licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
        
                    // AllowTestDevices controls whether test devices can use the license or not.  
                    // If true, the MinimumSecurityLevel property of the license
                    // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
                    licenseTemplate.AllowTestDevices = true;
        
                    // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
                    // It grants the user the ability to playback the content subject to the zero or more restrictions 
                    // configured in the license and on the PlayRight itself (for playback specific policy). 
                    // Much of the policy on the PlayRight has to do with output restrictions 
                    // which control the types of outputs that the content can be played over and 
                    // any restrictions that must be put in place when using a given output.
                    // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
                    //then the DRM runtime will only allow the video to be displayed over digital outputs 
                    //(analog video outputs won’t be allowed to pass the content).
        
                    //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
                    // If the output protections are configured too restrictive, 
                    // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.
        
                    // For example:
                    //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);
        
                    responseTemplate.LicenseTemplates.Add(licenseTemplate);
        
                    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
                }
        
        
                private static string ConfigureWidevineLicenseTemplate()
                {
                    var template = new WidevineMessage
                    {
                        allowed_track_types = AllowedTrackTypes.SD_HD,
                        content_key_specs = new[]
                        {
                                    new ContentKeySpecs
                                    {
                                        required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                        security_level = 1,
                                        track_type = "SD"
                                    }
                                },
                        policy_overrides = new
                        {
                            can_play = true,
                            can_persist = true,
                            can_renew = false
                        }
                    };
        
                    string configuration = JsonConvert.SerializeObject(template);
                    return configuration;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
                    Uri widevineURl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                                    {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                                    {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineURl.ToString()},
        
                        };
        
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);
        
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
        
                }
        
        
                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);
        
                    // Create a locator to the streaming content on an origin. 
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));
        
                    // Create a URL to the manifest file. 
                    return originLocator.Path + assetFile.Name;
                }
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int length)
                {
                    var returnValue = new byte[length];
        
                    using (var rng =
                        new System.Security.Cryptography.RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(returnValue);
                    }
        
                    return returnValue;
                }
            }
        }

##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##関連項目

[Configure Widevine packaging with AMS (AMS を使用して Widevine パッケージを構成する)](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Announcing Google Widevine license delivery services public preview in Azure Media Services (Azure Media Services での Google Widevine ライセンス配信サービス パブリック プレビューのお知らせ)](http://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)

