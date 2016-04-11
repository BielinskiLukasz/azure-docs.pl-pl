<properties 
    pageTitle="Azure App Service の API アプリに Web アプリを接続する" 
    description="このチュートリアルでは、Azure App Service でホストされる ASP.NET Web アプリから API アプリを使用する方法について示します。" 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="syntaxc4" 
    manager="yochayk" 
    editor="jimbe"/>

<tags
    ms.service="app-service-web"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="09/15/2015"
    ms.author="cfowler"/>

# Azure App Service の API アプリに Web アプリを接続する

このチュートリアルでホストされている ASP.NET web アプリケーションから API アプリを使用する方法を示しています。 [App Service](../app-service.md)します。

## 前提条件

このチュートリアルは、次の API アプリ チュートリアル シリーズに基づいています。

1. [Azure API アプリの作成](../app-service-dotnet-create-api-app)
3. [Azure API アプリのデプロイ](../app-service-dotnet-deploy-api-app)
4. [Azure API アプリのデバッグ](../app-service-dotnet-remotely-debug-api-app)

## API アプリに対してパブリック アクセスができるようにする

 [Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715), 、API アプリを選択します。 をクリックして、 **設定** コマンド バーのボタンをクリックします。  **アプリケーション設定** ブレードで、変更、 **アクセス レベル** に **パブリック (匿名)**します。

![](./media/app-service-web-connect-web-app-to-saas-api/4-5-Change-Access-Level-To-Public.png)

## Visual Studio で ASP.NET MVC アプリケーションを作成する

1. Visual Studio を開きます。 使用して、 **新しいプロジェクト** 新しいを追加するためのダイアログ **ASP.NET Web アプリケーション**します。 Click **OK**.

    ![ファイル > 新規作成 > Web > ASP.NET Web アプリケーション](./media/app-service-web-connect-web-app-to-saas-api/1-Create-New-MVC-App-For-Consumption.png)

1. 選択、 **MVC** テンプレートです。 をクリックして **認証の変更**, 選択してから、 **認証なし**, 、] をクリックし、 **OK** 2 回クリックします。

    ![新しい ASP.NET アプリケーション](./media/app-service-web-connect-web-app-to-saas-api/2-Change-Auth-To-No-Auth.png)

1. ソリューション エクスプ ローラーでは、新しく作成した Web アプリケーション プロジェクトを右クリックして **Azure アプリ参照の追加**します。

    ![Azure API アプリ参照の追加](./media/app-service-web-connect-web-app-to-saas-api/3-Add-Azure-API-App-SDK.png)

1.  **既存の API Apps** ] ドロップダウン リストに接続する API アプリを選択します。

    ![既存の API アプリを選択](./media/app-service-web-connect-web-app-to-saas-api/4-Add-Azure-API-App-SDK-Dialog.png)

    >[AZURE.NOTE] API アプリに接続するためのクライアント コードは、Swagger API エンドポイントから自動的に生成されます。

1. 生成される API コードを活用するには、HomeController.cs ファイルを開き、`Contact` アクションを次のコードに置き換えます。

        public async Task<ActionResult> Contact()
        {
            ViewBag.Message = "Your contact page.";
    
            var contacts = new ContactsList();
            var response = await contacts.Contacts.GetAsync();
            var contactList = response.Body;
    
            return View(contactList);
        }

    ![HomeController.cs コードの更新](./media/app-service-web-connect-web-app-to-saas-api/5-Write-Code-Which-Leverages-Swagger-Generated-Code.png)

1. 連絡先の動的リストを反映するよう、以下のコードで `Contact` ビューを更新します。  
    <pre>// ビュー ファイルの最上部に追加
    @model IList & lt;MyContactsList.Web.Models.Contact & gt;
    
    // 既定の電子メール アドレスを以下で置換
    & lt; h3 & gt;パブリックの連絡先および lt;/h3 & gt;
    & lt; ul & gt;
        @foreach (var contact in Model)
        {
            & lt; li & gt; & lt;、href = & quot;mailto:@contact します。EmailAddress & quot; & gt;@contact します。名前と amp;lt;@contact です。EmailAddress & amp; gt; & lt;/a & gt; & lt;/li & gt;
        }
    & lt;/ul & gt; 
    </pre>

    ![Contact.cshtml コードの更新](./media/app-service-web-connect-web-app-to-saas-api/6-Update-View-To-Reflect-Changes.png)

## App Service の Web Apps に Web アプリケーションをデプロイする

使用可能な指示に従って [Azure web アプリを展開する方法](web-sites-deploy.md)します。

>[AZURE.NOTE] 場合は、Azure アカウントがサインアップする前に Azure App Service の使用を開始するには、 [App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751), 、App Service で有効期間の短いスターター web アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Web サイトから App Service への変更のガイドを参照してください: [Azure App Service と既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)
 


