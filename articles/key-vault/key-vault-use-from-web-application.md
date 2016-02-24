<properties 
    pageTitle ="Web アプリケーションから Azure Key Vault の使用 |Microsoft Azure] 
    description ="このチュートリアルを使用する web アプリケーションから Azure Key Vault を使用する方法について説明します"。 
    サービス =「キー コンテナー」 
    documentationCenter="" 
    authors ="adamhurwitz" 
    manager =""
    タグ =「azure リソース マネージャー」//>

<tags 
    ms.service="key-vault" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2015" 
    ms.author="adhurwit"/>

# Web アプリケーションからの Azure Key Vault の使用 #

## はじめに  
このチュートリアルを使用すると、Azure の Web アプリケーションから Azure Key Vault を使用する方法について学習できます。 ここでは、Web アプリケーションで使用できるように Azure Key Vault からシークレットにアクセスするプロセスについて説明します。

**推定所要時間:** 15 分


Azure Key Vault の概要については、次を参照してください。 [Azure Key Vault は何ですか。](key-vault-whatis.md)

## 前提条件 

このチュートリアルを完了するには、以下が必要です。

- Azure Key Vault のシークレットへの URI
- Key Vault にアクセスできる Azure Active Directory に登録された Web アプリケーションのクライアント ID とクライアント シークレット
- Web アプリケーション。 Web アプリとして Azure にデプロイされた ASP.NET MVC アプリケーションの手順について説明します。 

> [AZURE.NOTE]  示された手順を完了している必要が [Azure Key Vault を使ってみる](key-vault-get-started.md) このチュートリアルでは web アプリケーションのシークレットとクライアント ID とクライアント シークレットへの URI があるようにします。 

Key Vault にアクセスする Web アプリケーションは、Azure Active Directory に登録されていて、Key Vault へのアクセス権が付与されているアプリケーションです。 そうでない場合は、概要のチュートリアルにあるアプリケーションの登録に関するトピックに戻り、記載されている手順を繰り返します。 

このチュートリアルは、Azure 上での Web アプリケーション作成の基本を理解している Web 開発者向けに設計されています。 Azure の Web アプリに関する詳細については、次を参照してください。 [Web Apps の概要](../app-service-web-overview.md)します。



## <a id="packages"></a>Nuget パッケージを追加します。 ##
Web アプリケーションをインストールしておく必要のあるパッケージは 3 つあります。 

- Active Directory 認証ライブラリ: Azure Active Directory と対話してユーザー ID を管理するためのメソッドが含まれています。
- Azure Key Vault ライブラリ: Azure Key Vault と対話するためのメソッドが含まれています。


これらの 3 つのパッケージはすべて、パッケージ マネージャー コンソールで Install-Package コマンドを使用するとインストールできます。 

    // this is currently the latest stable version of ADAL
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault 


## <a id="webconfig"></a>Web.Config を変更します。 ##
次のように、web.config ファイルに追加する必要のある 3 つのアプリケーション設定があります。 

    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


今後 Azure の Web アプリとしてアプリケーションをホストしない場合は、web.config に実際のクライアント ID、クライアント シークレット、およびシークレットの URI の値を追加する必要があります。 追加しない場合は、これらのダミーの値をそのままにしてください。セキュリティ レベルを上げるために、Azure ポータルで実際の値を追加する予定です。 


## <a id="gettoken"></a>アクセス トークンを取得するメソッドを追加します。 ##
Key Vault API を使用するには、アクセス トークンが必要です。 Key Vault クライアントによって Key Vault API の呼び出しが処理されますが、アクセス トークンを取得する関数を指定する必要があります。  

Azure Active Directory からアクセス トークンを取得するコードを次に示します。 このコードはアプリケーション内の任意の場所に配置できます。 Utils クラスまたは EncryptionHelper クラスを追加します。  

    //add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Web.Configuration;
    
    //this is an optional property to hold the secret after it is retrieved
    public static string EncryptSecret { get; set; }

    //the method that will be provided to the KeyVaultClient
    public async static Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
        
        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");
        
        return result.AccessToken;
    }

> [AZURE.NOTE] クライアント シークレットを使用します。
> Azure AD アプリケーションを認証する最も簡単な方法は、クライアント ID とクライアント シークレットを使用します。 また、Web アプリケーションでこれらを使用すると、義務を分離して、キーの管理をさらに制御できます。 ただし、これは構成の設定にクライアント シークレットを配置することに依存しています。この配置は、構成の設定に保護するシークレットを配置するのと同じくらい危険な可能性があります。 クライアント ID とクライアント シークレットではなく、クライアント ID と証明書を使用して Azure AD のアプリケーションを認証する方法の詳細については、以下を参照してください。 



## <a id="appstart"></a>アプリケーションの起動時のシークレットを取得します。 ##
ここで、Key Vault API を呼び出してシークレットを取得するコードが必要になります。 次のコードは、それが必要になる前に呼び出されれば、どこに配置してもかまいません。 ここでは、このコードを Global.asax の Application Start イベントに配置しました。これにより、コードは起動時に 1 回実行され、アプリケーションでシークレットを使用できるようになります。 

    //add these using statements
    using Microsoft.Azure.KeyVault;
    using System.Web.Configuration;

    // I put my GetToken method in a Utils class. Change for wherever you placed your method. 
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

    var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;
    
    //I put a variable in a Utils class to hold the secret for general  application use. 
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>(省略可能) Azure ポータルでのアプリケーション設定の追加します。 ##
Azure の Web アプリがある場合は、Azure ポータルでアプリ設定の実際の値を追加できます。 これにより、実際の値は web.config ファイルに存在しなくなりますが、個別のアクセス制御機能があるポータルによって保護されます。 これらの値は、web.config で入力した値の代わりに使用されます。 名前が同じであるかどうかを確認してください。

![Azure ポータルに表示されるアプリケーション設定][1]


## クライアント シークレットではなく、証明書を使用して認証する 
Azure AD アプリケーションを認証する別の方法は、クライアント ID とクライアント シークレットではなく、クライアント ID と証明書を使用する方法です。 Azure の Web アプリで証明書を使用する手順を次に示します。

1. 証明書を取得または作成する
2. 証明書を Azure AD アプリケーションに関連付ける
3. 証明書を使用する Web アプリにコードを追加する
4. 証明書を Web アプリに追加する


**証明書の作成を取得または**
ここで、テスト証明書を行います。 開発者コマンド プロンプトで証明書を作成する場合に使用できる、いくつかのコマンドを次に示します。 証明書ファイルの作成先となるディレクトリを変更します。 

    makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
    pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

終了日と .pfx のパスワードをメモしておいてください (この例では、2016 年 7 月 31 日と test123)。 この情報は以下で必要になります。 

テスト証明書の作成の詳細については、次を参照してください [方法:、独自のテスト証明書の作成。](https://msdn.microsoft.com/en-in/library/ff699202.aspx)


**証明書を Azure AD アプリケーションに関連付ける**
証明書がある場合は、Azure AD アプリケーションに関連付ける必要があります。 ただし、現在 Microsoft Azure 管理ポータルではサポートされていません。 代わりに Powershell を使用する必要があります。 実行する必要のあるコマンドを次に示します。

    $x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    
    PS C:\> $x509.Import("C:\data\KVWebApp.cer")
    
    PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())
    
    PS C:\> $now = [System.DateTime]::Now
    
    # this is where the end date from the cert above is used
    PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31") 
    
    PS C:\> $adapp = New-AzureADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow
    
    PS C:\> $sp = New-AzureADServicePrincipal -ApplicationId $adapp.ApplicationId
    
    PS C:\> Set-AzureKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToKeys all -ResourceGroupName 'contosorg'
    
    # get the thumbprint to use in your app settings
    PS C:\>$x509.Thumbprint

これらのコマンドを実行すると、Azure AD でアプリケーションを確認できます。 最初にアプリケーションが表示されない場合は、"自社で使用するアプリケーション" ではなく "自社が所有するアプリケーション" で検索します。 

Azure AD アプリケーションのオブジェクトと ServicePrincipal オブジェクトの詳細については、次を参照してください [アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト。](../active-directory/active-directory-application-objects.md)



**証明書を使用するよう Web アプリにコードを追加します。**
証明書にアクセスし、認証に使用する Web アプリに、コードを追加します。 

まず、証明書にアクセスするコードがあります。 

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint, 
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


StoreLocation は LocalMachine ではなく CurrentUser であることに注意してください。 また、ここではテスト証明書を使用しているため、Find メソッドに対して ' false' を指定しています。


次に、CertificateHelper を使用し、認証に必要な ClientAssertionCertificate を作成するコードがあります。 

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


アクセス トークンを取得する新しいコードを次に示します。 これにより、上記の GetToken メソッドが置き換えられます。 便宜上、別の名前を指定しています。 

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

使いやすくするために、このコードをすべて Web アプリ プロジェクトの Utils クラスに配置してあります。 

最後のコード変更は、Application_Start メソッドで行います。 最初に、GetCert() メソッドを呼び出して ClientAssertionCertificate を読み込む必要があります。 次に、新しい KeyVaultClient を作成するときに指定するコールバック メソッドを変更します。 これにより、上記のコードが置き換わることに注意してください。 

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Web アプリに証明書の追加します。**
Web アプリに証明書を追加すると、単純な 2 段階のプロセスです。 まず Azure ポータルに移動し、Web アプリに移動します。 Web アプリの [設定] ブレードで、[カスタム ドメインおよび SSL] のエントリをクリックします。 開いたブレードで、先ほど作成した証明書 KVWebApp.pfx をアップロードし、pfx のパスワードを覚えているかどうかを確認できます。 

![Azure ポータルで Web アプリに証明書の追加][2]


最後に実行する必要があるが WEBSITE\_LOAD\_CERTIFICATES の名前と値を持つ Web アプリにアプリケーション設定を追加するには * です。 これにより、すべての証明書が読み込まれます。 アップロードした証明書のみを読み込む場合は、そのサムプリントのコンマ区切りリストを入力できます。 

Web アプリに証明書を追加する方法の詳細については、次を参照してください [Azure web サイトのアプリケーションで証明書の使用。](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)



## <a id="next"></a>次のステップ ##


プログラミング リファレンスを参照してください [Azure Key Vault c# クライアント API リファレンス](https://msdn.microsoft.com/library/azure/dn903628.aspx)します。


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 

