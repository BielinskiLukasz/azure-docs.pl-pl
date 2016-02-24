<properties 
    pageTitle=".NET を使用した Media Services アカウントへの接続" 
    description="このトピックでは、.NET を使用して、Media Services に接続する方法を説明します。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="12/05/2015" 
    ms.author="juliako"/>


# Media Services SDK for .NET を使用した Media Services アカウントへの接続

> [AZURE.SELECTOR]
- [REST ()](media-services-rest-connect_programmatically.md)
- [.NET](media-services-dotnet-connect_programmatically.md)


このトピックでは、Media Services SDK for .NET でプログラミングする場合に、Microsoft Azure Media Services にプログラムから接続する方法について説明します。


## Media Services への接続

プログラムで Media Services に接続するには、事前に Azure アカウントを設定し、そのアカウントに Media Services を構成し、Media Services SDK for .NET で開発するための Visual Studio プロジェクトをセットアップする必要があります。 詳細については、「Setup for Development with the Media Services SDK for .NET (Media Services SDK による開発のためのセットアップ)」をご覧ください。

Media Services アカウントのセットアップ プロセスの最後で、次の必須接続値を取得しました。 これらの値を使用して、プログラムから Media Services に接続します。

- Media Services アカウント名

- Media Services アカウント キー

これらの値を確認する「Azure の管理ポータルに、Media Service アカウントを選択し [] をクリックしての使用」を参照していますください。、"**キーの管理**"ポータル ウィンドウの下部にあるアイコン。 各テキスト ボックスの横にあるアイコンをクリックすると、値がシステム クリップボードにコピーされます。


## CloudMediaContext インスタンスの作成

作成する必要があります。 Media Services に対するプログラミングを開始する、 **CloudMediaContext** サーバー コンテキストを表すインスタンス。  **CloudMediaContext** ジョブ、アセット、ファイル、アクセス ポリシー、ロケーターなど、重要なコレクションへの参照が含まれています。

>[AZURE.NOTE]  **CloudMediaContext** クラスはスレッド セーフではありません。 スレッドごと、一連の操作ごとに、新しい CloudMediaContext を作成する必要があります。


CloudMediaContext には、5 つのコンストラクター オーバーロードがあります。 取得するコンス トラクターを使用することをお勧め **MediaServicesCredentials** をパラメーターとして。 詳細については、次を参照してください。、 **アクセス制御サービス トークンの再利用する** を参照します。 

次の例では、public CloudMediaContext (MediaServicesCredentials credentials) コンストラクターを使用します。

    // _cachedCredentials and _context are class member variables. 
    _cachedCredentials = new MediaServicesCredentials(
                    _mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
    _context = new CloudMediaContext(_cachedCredentials);


## Access Control Service トークンの再利用

このセクションでは、パラメーターとして MediaServicesCredentials を受け取る CloudMediaContext コンストラクターを使用して Access Control Service トークンを再利用する方法を示します。


[Azure の Active Directory Access Control](https://msdn.microsoft.com/library/hh147631.aspx) (Access Control Service または ACS とも呼ばれます) は、web アプリケーションにアクセスするために認証およびユーザーを認証する簡単な方法を提供するクラウド ベースのサービスです。 Microsoft Azure Media Services は、ACS トークンを必要とする OAuth プロトコルを使用して、サービスへのアクセスを制御します。 Media Services は、承認サーバーから ACS トークンを受け取ります。

Media Services SDK を使用して開発する場合、SDK コードでトークンを管理できるため、トークンの処理をしないことも選択できます。 ただし、ACS トークンを完全に SDK で管理すると、不要なトークン要求が生成されます。 トークン要求には時間がかかり、クライアントとサーバーのリソースが消費されます。 また、ACS サーバーは、要求の発生率が高すぎる場合、要求を調整します。 1 秒あたり 30 要求の上限を参照してください [ACS サービスの制限事項](https://msdn.microsoft.com/library/gg185909.aspx) 詳細です。

Media Services SDK Version 3.0.0.0 からは、ACS トークンを再利用できるようになりました。  **CloudMediaContext** を受け取るコンス トラクター **MediaServicesCredentials** をパラメーターとして複数のコンテキスト間で ACS トークンの共有を有効にします。 MediaServicesCredentials クラスは、Media Services の資格情報をカプセル化します。 ACS トークンが使用可能で、有効期限がわかっている場合は、トークンを使用して新しい MediaServicesCredentials インスタンスを作成し、そのインスタンスを CloudMediaContext のコンストラクターに渡すことができます。 有効期限が切れると、Media Services SDK によって、トークンが自動的に更新されることにご注意ください。 次の例で示すように、ACS トークンを再利用するには、2 つの方法があります。

- キャッシュできる、 **MediaServicesCredentials** (たとえば、静的クラス変数) 内のメモリ内のオブジェクト。 次に、CloudMediaContext コンストラクターに、キャッシュされたオブジェクトを渡します。 MediaServicesCredentials オブジェクトには ACS トークンが含まれており、トークンの有効期限が切れていない場合は、トークンを再利用できます。 有効期限が切れている場合、トークンは、MediaServicesCredentials コンストラクターに渡された資格情報を使用して、Media Services SDK によって更新されます。

    なお、 **MediaServicesCredentials** 受け取るの RefreshToken が呼び出された後、オブジェクトが有効なトークンを取得します。  **CloudMediaContext** 呼び出し、 **RefreshToken** コンス トラクター内のメソッドです。 トークン値を外部ストレージに保存する場合は、トークン データを保存する前に、TokenExpiration 値が有効かどうかを必ずご確認ください。 有効でない場合は、キャッシュする前に RefreshToken を呼び出します。

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

        
        // Use the cached credentials to create a new CloudMediaContext object.
        if(_cachedCredentials == null)
        {
            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
        }
        
        CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- AccessToken 文字列と TokenExpiration の値もキャッシュできます。 キャッシュした値は、キャッシュしたトークン データを使用して後から新しい MediaServicesCredentials オブジェクトを作成するときに使用できます。  これはトークンが複数のプロセスやコンピューターで安全に共有される場合に、特に便利です。

    次のコード スニペットでは、この例では定義されていない SaveTokenDataToExternalStorage メソッド、GetTokenDataFromExternalStorage メソッド、UpdateTokenDataInExternalStorageIfNeeded メソッドを呼び出しています。 これらのメソッドを定義すると、外部ストレージにトークン データを格納し、格納したデータを取得して更新できます。 

        CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
        
        // Get token values from the context.
        var accessToken = context1.Credentials.AccessToken;
        var tokenExpiration = context1.Credentials.TokenExpiration;
        
        // Save token values for later use. 
        // The SaveTokenDataToExternalStorage method should check 
        // whether the TokenExpiration value is valid before saving the token data. 
        // If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
        SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
        
    保存したトークン値を使用して、MediaServicesCredentials を作成します。


        var accessToken = "";
        var tokenExpiration = DateTime.UtcNow;
        
        // Retrieve saved token values.
        GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
        
        // Create a new MediaServicesCredentials object using saved token values.
        MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
        {
            AccessToken = accessToken,
            TokenExpiration = tokenExpiration
        };
        
        CloudMediaContext context2 = new CloudMediaContext(credentials);

    Update the token copy in case the token was updated by the Media Services SDK. 
    
        if(tokenExpiration != context2.Credentials.TokenExpiration)
        {
            UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
        }
        

- 負荷分散や地理的分散の目的などで、複数の Media Services アカウントを使用している場合、System.Collections.Concurrent.ConcurrentDictionary コレクションを使用して、MediaServicesCredentials オブジェクトをキャッシュできます (ConcurrentDictionary コレクションは、複数のスレッドから同時にアクセスできるキー/値のペアのスレッドセーフ コレクションを表します)。 この場合、GetOrAdd メソッドを使用して、キャッシュした資格情報を取得できます。 

        // Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
        private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
            new ConcurrentDictionary<string, MediaServicesCredentials>();
        

        // Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
        static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
        {
            CloudMediaContext cloudMediaContext;
            MediaServicesCredentials mediaServicesCredentials;
        
            mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
                accountName,
                valueFactory => new MediaServicesCredentials(accountName, accountKey));
        
            cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
        
            return cloudMediaContext;
        }
        
## 中国北部リージョンにある Media Services アカウントへの接続

アカウントが中国北部リージョンに存在する場合は、次のコンストラクターを使用します。

    public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

次に例を示します。


    _context = new CloudMediaContext(
        new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
        _mediaServicesAccountName,
        _mediaServicesAccountKey,
        "urn:WindowsAzureMediaServices",
        "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## 構成への接続値の格納

接続値、特に、アカウント名とパスワードなどの秘密性の高い値は保存しておくことを強くお勧めします。 また、秘密性の高い構成データは暗号化することをお勧めします。 Windows 暗号化ファイル システム (EFS) を使用すると、構成ファイル全体を暗号化できます。 ファイルに対して EFS を有効にするには、ファイルを右クリックし、選択 **プロパティ**, での暗号化を有効にして、 **[詳細設定** の設定] タブをクリックします。 または、保護された構成を使用して、構成ファイルの選択した部分を暗号化するためのカスタム ソリューションも作成できます。 参照してください [保護された構成を使用して構成情報の暗号化](https://msdn.microsoft.com/library/53tyfkaw.aspx)します。

次の App.config ファイルには、必須接続値が含まれています。 値、 <appSettings> 要素は、Media Services アカウントのセットアップ プロセスから取得した必要な値です。

    <configuration>
      <appSettings>
        <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
        <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
      </appSettings>
    </configuration>


使用できる構成から接続値を取得する、 **ConfigurationManager** クラスし、コードでフィールドに値を割り当てます。
    
    private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
    private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

