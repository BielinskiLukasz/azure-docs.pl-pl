<properties
    pageTitle="Azure App Service の Web アプリの高度な構成と拡張機能"
    description="XML ドキュメント変換 (XDT) の宣言を使用して、Azure App Service の Web アプリ内の ApplicationHost.config ファイルを変換し、プライベート拡張機能を追加してカスタムの管理操作を有効にします。"
    authors="cephalin"
    writer="cephalin"
    editor="mollybos"
    manager="wpickett"
    services="app-service"
    documentationCenter=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="cephalin"/>

# Azure App Service の Web アプリの高度な構成と拡張機能

使用して [XML ドキュメント変換](http://msdn.microsoft.com/library/dd465326.aspx) 変換することができます (XDT) の宣言、 [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) Azure App Service で web アプリ内のファイルです。 また、XDT 宣言を使用して、プライベート拡張機能を追加し、カスタム Web アプリ管理アクションを有効にすることもできます。 この記事には、Web インターフェイスを使用して PHP 設定の管理を有効にする、サンプルの PHP Manager Web アプリ拡張機能が含まれています。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="transform"></a>ApplicationHost.config を使用した高度な構成
App Service プラットフォームにより、Web アプリ構成の柔軟性と制御を実現できます。 IIS の標準的な ApplicationHost.config 構成ファイルは、App Service 内で直接編集することはできませんが、プラットフォームは XML ドキュメント変換 (XDT) に基づく宣言型の ApplicationHost.config 変換モデルをサポートします。

この変換機能を活用するコンテンツ XDT に ApplicationHost.xdt ファイルを作成およびサイト ルート (d:\home\site) の下に、 [Kudu コンソール](https://github.com/projectkudu/kudu/wiki/Kudu-console)します。 場合によっては、変更を反映するために Web App を再起動する必要があります。

PHP 5.4 を使用する Web アプリに新しいカスタム環境変数を追加する方法を、次の applicationHost.xdt サンプルに示します。

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.webServer>
                <fastCgi>
                    <application>
                        <environmentVariables>
                                <environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
                        </environmentVariables>
                    </application>
                </fastCgi>
        </system.webServer>
    </configuration>


変換ステータスと詳細を記録したログ ファイルは、FTP ルートの LogFiles\Transform で利用できます。

その他のサンプルを参照してください。 [https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)します。

**注**<br />
下にあるモジュールのリストから要素を `system.webServer` 削除または並べ替えできませんが、一覧への追加は可能です。


##<a id="extend"></a> Web アプリの拡張

###<a id="overview"></a> プライベート Web アプリ拡張の概要

App Service では、管理操作の機能拡張ポイントとして Web アプリ拡張機能を使用できます。 実際、App Service プラットフォームの一部の機能は、事前インストールされた拡張機能として実装されています。 事前インストールされたプラットフォーム拡張機能を変更することはできませんが、独自アプリでプライベート拡張機能を作成および構成することができます。 この機能も、XDT 宣言に依存します。 プライベート Web アプリ拡張機能を作成するための主要な手順を次に示します。

1. Web アプリ拡張機能 **コンテンツ**: App Service でサポートされている任意の web アプリケーションを作成
2. Web アプリ拡張機能 **宣言**: ApplicationHost.xdt ファイルを作成
3. Web アプリ拡張機能 **展開**: の下の SiteExtensions フォルダーにコンテンツを配置します。 `root`

Web アプリに対する内部リンクは、ApplicationHost.xdt ファイル内で指定したアプリケーション パスを基準とする相対パスを指す必要があります。 ApplicationHost.xdt ファイルに対してどのような変更を加えた場合でも、Web アプリのリサイクル、つまり停止と再起動が必要です。

**注**: これらのキー要素の詳細については、「 [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)します。

プライベート Web アプリ拡張機能を作成して有効にする手順を示す、詳細な例が掲載されています。 PHP Manager 例を次のソース コードはからダウンロードできます [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager)します。

###<a id="SiteSample"></a> Web アプリ拡張機能の例: PHP Manager

PHP Manager は、PHP の .ini ファイルを直接変更する代わりに、Web インターフェイスを使用して PHP 設定を簡単に表示および構成するために Web アプリ管理者が使用できる Web アプリ拡張機能です。 PHP 用の一般的な構成ファイルに該当するのは、Program Files の下にある php.ini ファイルと、 Web アプリのルート フォルダーにある user.ini ファイルです。 App Service プラットフォーム上で php.ini ファイルを直接編集することはできないため、PHP Manager 拡張機能は user.ini ファイルを使用して設定の変更を適用します。

####<a id="PHPwebapp"></a> PHP Manager Web アプリケーション

デプロイされた PHP Manager のホーム ページを次に示します。

![TransformSitePHPUI][TransformSitePHPUI]

ここからわかるように、Web アプリ拡張機能は通常の Web アプリケーションに似ていますが、Web アプリのルート フォルダーに追加の ApplicationHost.xdt ファイルが配置されています (この記事の次のセクションには、ApplicationHost.xdt ファイルに関する詳細が掲載されています)。

PHP Manager 拡張機能は、Visual Studio ASP.NET MVC 4 Web アプリケーション テンプレートを使用して作成されたものです。 ソリューション エクスプローラーの次のビューに、PHP Manager 拡張機能の構造を示します。

![TransformSiteSolEx][TransformSiteSolEx]

ファイル I/O で必要とされる唯一の特殊なロジックは、Web アプリの wwwroot ディレクトリが配置されている場所を示すことです。 次のコード例に示すように、環境変数 "HOME" は Web アプリのルート パスを示しており、"site\wwwroot" を追加すると wwwroot パスを作成することができます。

    /// <summary>
    /// Gives the location of the .user.ini file, even if one doesn't exist yet
    /// </summary>
    private static string GetUserSettingsFilePath()
    {
            var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
            if (rootPath == null)
            {
                rootPath = System.IO.Path.GetTempPath(); // For testing purposes
            };
            var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
            return userSettingsFile;
    }


ディレクトリ パスを作成した後、ファイルを読み書きする通常のファイル I/O 操作を使用できます。

Web アプリ拡張機能に関して注意する 1 つの点は、内部リンクの処理に関係しています。  Web アプリ上の内部リンクに対応する絶対パスを指定した HTML ファイル内で任意のリンクを使用している場合は、それらのリンクの先頭に、ルートとして拡張機能名を追加する必要があります。 拡張機能にとってルートは、"/" のみではなく、"/`[your-extension-name]`/" になったため、このような指定が必要です。したがって、すべての内部リンクも同様に更新する必要があります。 たとえば、コード内に次のリンクが含まれているとします。

`"<a href="/Home/Settings">PHP Settings</a>"`

このリンクが Web アプリ拡張機能の一部である場合は、リンクを次の形式にする必要があります。

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Web アプリケーション内で相対パスのみを使用するか、ASP.NET アプリケーションの場合は適切なリンクを自動的に作成する `@Html.ActionLink` メソッドを使用する方法により、この要件を回避できます。

####<a id="XDT"></a> applicationHost.xdt ファイル

Web アプリの拡張機能のコードを入れる HOME%\SiteExtensions\[your-extension-name %]。 これを、拡張機能ルートと呼びます。  

開発した Web アプリ拡張機能を applicationHost.config ファイルに登録するには、ApplicationHost.xdt という名前のファイルを拡張機能のルートに配置する必要があります。 ApplicationHost.xdt ファイルの内容を次のようにする必要があります。

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.applicationHost>
                <sites>
                    <site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
                        <!-- NOTE: Add your extension name in the application paths below -->
                        <application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
                        <application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
                            <virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
                        </application>
                    </site>
                </sites>
        </system.applicationHost>
    </configuration>

拡張機能名として選択する名前は、拡張機能のルート フォルダーと同じ名前にする必要があります。

この操作により、SCM サイトの下にある `system.applicationHost` サイト一覧に対して、新しいアプリケーション パスを追加する効果が得られます。 SCM サイトは、特定のアクセス資格情報を持つサイト管理エンドポイントです。 それには、URL `https://[your-site-name].scm.azurewebsites.net` があります。  

    <system.applicationHost>
        ...
        <site name="~1[your-website]" id="1716402716">
                <bindings>
                    <binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
                    <binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
                </bindings>
                <traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
                <detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
                <logFile logSiteId="false" />
                <application path="/" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
                </application>
                <!-- Note the custom changes that go here -->
                <application path="/[your-extension-name]" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
                </application>
            </site>
    </sites>
      ...
    </system.applicationHost>

###<a id="deploy"></a> Web アプリ拡張のデプロイ

Web アプリ拡張機能をインストールするために、FTP を使用して、拡張機能のインストール先となる Web アプリの `\SiteExtensions\[your-extension-name]` フォルダーに Web アプリケーションのすべてのファイルをコピーすることができます。  この場所に ApplicationHost.xdt ファイルもコピーしてください。 拡張機能を有効にするには、Web アプリを再起動します。

次の場所で、Web アプリ拡張機能が表示されます。

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

HTTPS が使用され、".scm" が含まれていることを除き、開発する Web アプリの URL に似た URL を使用することに注意してください。

開発や調査の際に、`WEBSITE_PRIVATE_EXTENSIONS` キーと値 `0` を使用したアプリ設定を追加して、Web アプリのすべてのプライベート (プレインストールされていない) 拡張を無効にすることはできません。

>[AZURE.NOTE] 場合は、Azure アカウントがサインアップする前に Azure App Service の使用を開始するには、 [App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751), 、App Service で有効期間の短いスターター web アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Web サイトから App Service への変更のガイドを参照してください: [Azure App Service と既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 

