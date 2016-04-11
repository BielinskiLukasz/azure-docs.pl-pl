<properties
    pageTitle=".NET での Azure Search アプリケーションの初めての使用 | Microsoft Azure | ホスト型クラウド検索サービス"
    description="Azure Search .NET SDK の .NET クライアント ライブラリを使用して Visual Studio ソリューションをビルドする方法についてのチュートリアルです。"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="11/05/2015"
    ms.author="heidist"/>

# .NET での Azure Search アプリケーションの初めての使用

Microsoft Azure のクラウドのホスト型検索サービスである Azure Search を検索エクスペリエンスとして使用するカスタム .NET 検索アプリケーションを Visual Studio 2013 以降で作成する方法を説明します。 このチュートリアルでは、 [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) オブジェクトおよび操作がこの演習で使用するためのクラスをビルドするサービスと同様、Azure Search REST API です。

このサンプルを実行する必要がありますでにサインアップできる Azure Search サービス、 [Azure Classic Portal](https://portal.azure.com)します。

> [AZURE.TIP] このチュートリアルのソース コードをダウンロード [Azure Search .NET サンプル](http://go.microsoft.com/fwlink/p/?LinkId=530196)します。 接続を設定し、要求で送信する方法について説明 [.NET アプリケーションから Azure Search を使用する方法](search-howto-dotnet-sdk.md)します。

## データについて

このサンプル アプリケーションからデータを使用して、 [United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), 、ロードアイランドでフィルター処理、状態のデータセットのサイズを小さきます。 このデータを使用して、病院や学校などの目立つ建物および河川、湖沼、山などの地理的特徴を返す検索アプリケーションを作成します。

このアプリケーションで、 **DataIndexer** プログラムがビルドされ、インデックスを使用して、ロード、 [インデクサー](https://msdn.microsoft.com/library/azure/dn798918.aspx) コンストラクト、パブリック Azure SQL Database からフィルター処理された USGS データセットを取得します。 オンライン データ ソースに対する資格情報と接続情報は、プログラム コードで提供されます。 それ以上の構成は必要ありません。

> [AZURE.NOTE] 件未満に無料の価格レベルの 10,000 ドキュメント制限には、このデータセットにフィルターを適用しました。 標準レベルを使用する場合は、この制限は適用されません。 各価格レベルの容量の詳細については、次を参照してください。 [制限および制約](search-limits-quotas-capacity.md)します。

## Azure Search サービスの作成

1. サインイン、 [Azure クラシック ポータル](https://portal.azure.com)します。

2. ジャンプバーで、次のようにクリックします。 **新規** > **データ + ストレージ** > **検索**します。

     ![][1]

3. サービス名、価格レベル、リソース グループ、サブスクリプション、および場所を構成します。 これらの設定は必須であり、サービスがプロビジョニングされた後は変更できません。

     ![][2]

    - **サービス名** 一意で、以下の小文字、スペースなし、15 文字にする必要があります。 この名前は、Azure Search サービスのエンドポイントの一部になります。 参照してください [名前付け規則](https://msdn.microsoft.com/library/azure/dn857353.aspx) 名前付け規則の詳細についてです。

    - **価格レベル** 容量と課金を決定します。 どちらのレベルも同じ機能を提供しますが、リソース レベルが異なります。

        - **無料**  は他のサブスクライバーと共有しているクラスターで実行します。 Free 版はチュートリアルを試用して概念実証コードを書くには十分な機能を提供しますが、運用アプリケーションには対応していません。 Free サービスは、通常は数分でデプロイできます。
        - **標準的な** 専用リソースで実行され、拡張性に優れています。 最初、Standard サービスは 1 つのレプリカと 1 つのパーティションを使用してプロビジョニングされますが、サービスを作成した後で容量を調整することができます。 標準サービスをデプロイするには、通常は約 15 分かかります。

    - **リソース グループ** はサービスと、一般的な目的に使用されるリソースのコンテナーです。 たとえば、Azure Search、Azure Websites、Azure BLOB ストレージを使用してカスタム検索アプリケーションを構築する場合は、リソース グループを作成することで、これらのサービスをポータル管理ページにまとめておくことができます。

    - **サブスクリプション** 1 つ以上のサブスクリプションがある場合、複数のサブスクリプションの中から選択することができます。

    - **場所** はデータ センターのリージョンです。 現時点では、すべてのリソースは同じデータ センターで実行する必要があります。 複数のデータ センターにリソースを分散させることはできません。

4. クリックして **作成** サービスをプロビジョニングします。

ジャンプバーで、通知を確認します。 サービスが使用できるようになると、通知が表示されます。

<a id="sub-2"></a>
## Azure Search サービスのサービス名と API キーの取得 ##

サービスを作成したら、ポータルに戻って URL または `api-key` を取得します。 Search サービスに接続するには、URL に加えて、呼び出しを認証するための `api-key` が必要になります。

1. ジャンプバーで、次のようにクリックします。 **ホーム** し、[Search サービスを開くには、サービス ダッシュ ボード] をクリックします。

2. サービスのダッシュボードには、基本情報のタイルのほか、管理者キーにアクセスするためのキー アイコンが表示されます。

    ![][3]

3. サービスの URL と管理者キーをコピーします。 これらは、後で Visual Studio プロジェクトの app.config および web.config ファイルに追加するときに必要になります。

## 新しいプロジェクトとソリューションの開始

このソリューションには次の 2 つのプロジェクトが含まれています。

- **DataIndexer**, 、Visual c# コンソール アプリケーション、データを読み込むために使用します。
- **SimpleSearchMVCApp**, 、クエリを実行し、検索結果を返すため、Visual c# ASP.NET MVC Web アプリケーション。

この手順では、両方のプロジェクトを作成します。

1. 開始 **Visual Studio** > **新しいプロジェクト** > **Visual c#** > **コンソール アプリケーション**します。
2. プロジェクトに名前を **DataIndexer** 、ソリューションの名前と **AzureSearchDotNetDemo**します。
3. ソリューション エクスプ ローラーでソリューションを右クリックして **追加** > **新しいプロジェクト** > **Visual c#** > **ASP.NET Web アプリケーション**します。
4. プロジェクトに名前を **SimpleSearchMVCApp**します。
5. 新しい ASP.NET プロジェクトで、MVC テンプレートを選択し、このチュートリアルで使用しないプログラム アーティファクトが作成されないようにオプションをオフにします。

   Azure ホストおよびユニット テストのチェック ボックスをオフにし、認証をなしに設定します。

   ![][10]

プロジェクトの作成が完了すると、ソリューションは次の例に示すようになります。

   ![][4]

## .NET クライアント ライブラリのインストールおよび他のパッケージの更新

1. 右クリック **NuGet パッケージの管理** ソリューション エクスプ ローラーでソリューションです。
2. 指定 **更新** > **安定版のみ** > **をすべて更新**します。

   ![][11]

3. 追加パッケージのインストールを受け入れて、すべての依存関係もインストールされるようにします。

4. 次に、Azure Search .NET クライアント ライブラリをインストールします。 検索を正しく指定してください。そうしないと、パッケージを簡単に検索できません。 右クリック **NuGet パッケージの管理** 再度します。

5. 指定 **オンライン** > **nuget.org** > **プレリリースを含める**, 、検索して *azure.search*します。 クリックして **インストール** ライブラリをインストールします。

   ![][12]

この例で使用するアセンブリの一覧の一部を次に示します。

   ![][5]

## System.Configuration へのアセンブリ参照の追加

**DataIndexer** を使用して **System.Configuration** app.config の構成設定を読み取ります。

1. 右クリック **DataIndexer** > **追加** > **参照** > **Framework** > **System.Configuration**します。  チェック ボックスをオンにします。
2. Click **OK**.

## 構成ファイルの更新

各プロジェクトには、サービス名と API キーを指定する構成ファイルが含まれています。

1.  **DataIndexer**, App.config を次の例では、[SERVICE NAME] の更新に置き換え、および [SERVICE KEY] は、サービスに対して有効な値を持つ。 サービス名は完全な URL ではないことに注意してください。 たとえば、Search サービスのエンドポイントの場合は *https://mysearchsrv.search.microsoft.net*, 、App.config に入力するサービス名は *mysearchsrv*します。

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
          <startup>
             <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
          </startup>
          <appSettings>
            <add key="SearchServiceName" value="[SERVICE NAME]" />
            <add key="SearchServiceApiKey" value="[SERVICE KEY]" />
          </appSettings>
        </configuration>

2.  **SimpleSearchMVCApp**, 、Web.config を次の例では、再び更新 [SERVICE NAME] と [SERVICE KEY] をサービスに有効な値で置換します。

        <?xml version="1.0" encoding="utf-8"?>
        <!--
          For more information on how to configure your ASP.NET application, visit
          http://go.microsoft.com/fwlink/?LinkId=152368
          -->
        <configuration>
          <configSections>
            <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
            <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
          </configSections>
          <connectionStrings>
            <add name="DefaultConnection" providerName="System.Data.SqlClient" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-SimpleMVCApp-20150303114355;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-SimpleMVCApp-20150303114355.mdf" />
          </connectionStrings>
          <appSettings>
            <add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
            <add key="SearchServiceApiKey" value="[API KEY]" />

            <add key="webpages:Version" value="2.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="PreserveLoginUrl" value="true" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
          </appSettings>
          <system.web>
            <httpRuntime targetFramework="4.5" />
            <compilation debug="true" targetFramework="4.5" />
            <authentication mode="Forms">
              <forms loginUrl="~/Account/Login" timeout="2880" />
            </authentication>
            <pages>
              <namespaces>
                <add namespace="System.Web.Helpers" />
                <add namespace="System.Web.Mvc" />
                <add namespace="System.Web.Mvc.Ajax" />
                <add namespace="System.Web.Mvc.Html" />
                <add namespace="System.Web.Optimization" />
                <add namespace="System.Web.Routing" />
                <add namespace="System.Web.WebPages" />
              </namespaces>
            </pages>
            <profile defaultProvider="DefaultProfileProvider">
              <providers>
                <add name="DefaultProfileProvider" type="System.Web.Providers.DefaultProfileProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
              </providers>
            </profile>
            <membership defaultProvider="DefaultMembershipProvider">
              <providers>
                <add name="DefaultMembershipProvider" type="System.Web.Providers.DefaultMembershipProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" enablePasswordRetrieval="false" enablePasswordReset="true" requiresQuestionAndAnswer="false" requiresUniqueEmail="false" maxInvalidPasswordAttempts="5" minRequiredPasswordLength="6" minRequiredNonalphanumericCharacters="0" passwordAttemptWindow="10" applicationName="/" />
              </providers>
            </membership>
            <roleManager defaultProvider="DefaultRoleProvider">
              <providers>
                <add name="DefaultRoleProvider" type="System.Web.Providers.DefaultRoleProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
              </providers>
            </roleManager>
            <!--
                    If you are deploying to a cloud environment that has multiple web server instances,
                    you should change session state mode from "InProc" to "Custom". In addition,
                    change the connection string named "DefaultConnection" to connect to an instance
                    of SQL Server (including SQL Azure and SQL Compact) instead of to SQL Server Express.
              -->
            <sessionState mode="InProc" customProvider="DefaultSessionProvider">
              <providers>
                <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
              </providers>
            </sessionState>
          </system.web>
          <system.webServer>
            <validation validateIntegratedModeConfiguration="false" />
            <handlers>
              <remove name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" />
              <remove name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" />
              <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
              <add name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness32" responseBufferLimit="0" />
              <add name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness64" responseBufferLimit="0" />
              <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
              <remove name="OPTIONSVerbHandler" />
              <remove name="TRACEVerbHandler" />
            </handlers>
          </system.webServer>
          <entityFramework>
            <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
              <parameters>
                <parameter value="v12.0" />
              </parameters>
            </defaultConnectionFactory>
          </entityFramework>
          <runtime>
            <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
              <dependentAssembly>
                <assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" culture="neutral" />
                <bindingRedirect oldVersion="0.0.0.0-6.0.0.0" newVersion="6.0.0.0" />
              </dependentAssembly>
              <dependentAssembly>
                <assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" culture="neutral" />
                <bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
              </dependentAssembly>
              <dependentAssembly>
                <assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" culture="neutral" />
                <bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
              </dependentAssembly>
              <dependentAssembly>
                <assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
                <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
              </dependentAssembly>
              <dependentAssembly>
                <assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
                <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
              </dependentAssembly>
              <dependentAssembly>
                <assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
                <bindingRedirect oldVersion="1.0.0.0-5.2.3.0" newVersion="5.2.3.0" />
              </dependentAssembly>
              <dependentAssembly>
                <assemblyIdentity name="System.Web.WebPages.Razor" publicKeyToken="31bf3856ad364e35" />
                <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
              </dependentAssembly>
            </assemblyBinding>
          </runtime>
        </configuration>


## DataIndexer の変更

このプログラムは、app.config での指定に従って Search サービスに接続し、インデックスを作成して、Azure SQL Database に格納されている USGS データセットを読み込むコンソール アプリケーションです。

現在、プレリリース バージョンのクライアント ライブラリはインデクサーをサポートしていないので、REST API を使用してチュートリアルのこの部分用のインデクサーを作成して使用します。  

このプログラムを実行する前に、2 つの変更を行います。

- 追加 **AzureSearchhelper.cs**, で使用される接続を処理し、シリアル化し、JSON のシリアル化するには、REST API を呼び出すことが要求したときと応答します。

- 置換 **Program.cs**, 、インデックスを作成、およびインデクサーの使用、データの読み込み、メッセージを書き込みます。

### AzureSearchHelper.cs の作成

REST API を呼び出すコードには、接続を処理し、JSON の要求と応答のシリアル化と逆シリアル化の処理を行うクラスが含まれる必要があります。 Azure Search で提供されるサンプルでこのクラスと通常呼ばれる **AzureSearchHelper.cs**します。 このクラスを作成して追加する **DataIndexer**, 、次のコードを使用します。

1. ソリューション エクスプ ローラーで右クリック **DataIndexer** > **追加** > **[新しい項目の** > **コード** > **クラス**します。
2. クラスに名前を **AzureSearchHelper**します。
3. 既定のコードを以下のコードに置き換えます。

        //Copyright 2015 Microsoft

        //Licensed under the Apache License, Version 2.0 (the "License");
        //you may not use this file except in compliance with the License.
        //You may obtain a copy of the License at

        //       http://www.apache.org/licenses/LICENSE-2.0

        //Unless required by applicable law or agreed to in writing, software
        //distributed under the License is distributed on an "AS IS" BASIS,
        //WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        //See the License for the specific language governing permissions and
        //limitations under the License.

        using System;
        using System.Net.Http;
        using System.Text;
        using Newtonsoft.Json;
        using Newtonsoft.Json.Converters;
        using Newtonsoft.Json.Serialization;

        namespace DataIndexer
        {
            public class AzureSearchHelper
            {
                public const string ApiVersionString = "api-version=2015-02-28";

                private static readonly JsonSerializerSettings _jsonSettings;

                static AzureSearchHelper()
                {
                    _jsonSettings = new JsonSerializerSettings
                    {
                        Formatting = Formatting.Indented, // for readability, change to None for compactness
                        ContractResolver = new CamelCasePropertyNamesContractResolver(),
                        DateTimeZoneHandling = DateTimeZoneHandling.Utc
                    };

                    _jsonSettings.Converters.Add(new StringEnumConverter());
                }

                public static string SerializeJson(object value)
                {
                    return JsonConvert.SerializeObject(value, _jsonSettings);
                }

                public static T DeserializeJson<T>(string json)
                {
                    return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
                }

                public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
                {
                    UriBuilder builder = new UriBuilder(uri);
                    string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
                    builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;

                    var request = new HttpRequestMessage(method, builder.Uri);

                    if (json != null)
                    {
                        request.Content = new StringContent(json, Encoding.UTF8, "application/json");
                    }

                    return client.SendAsync(request).Result;
                }

                public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
                {
                    if (!response.IsSuccessStatusCode)
                    {
                        string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
                        throw new Exception("Search request failed: " + error);
                    }
                }
            }
        }



### Program.cs の更新

1. ソリューション エクスプ ローラーで開く **DataIndexer** > **Program.cs**します。
2. Program.cs の内容を次のコードに置き換えます。

        using Microsoft.Azure;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Microsoft.Spatial;
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Timers;

        namespace DataIndexer
        {
            class Program
            {
                private static SearchServiceClient _searchClient;
                private static SearchIndexClient _indexClient;

                // This sample shows how to delete, create, upload documents and query an index
                static void Main(string[] args)
                {
                    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
                    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

                    // Create an HTTP reference to the catalog index
                    _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
                    _indexClient = _searchClient.Indexes.GetClient("geonames");

                    Console.WriteLine("{0}", "Deleting index...\n");
                    if (DeleteIndex())
                    {
                        Console.WriteLine("{0}", "Creating index...\n");
                        CreateIndex();
                        Console.WriteLine("{0}", "Sync documents from Azure SQL...\n");
                        SyncDataFromAzureSQL();
                    }
                    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                    Console.ReadKey();
                }

                private static bool DeleteIndex()
                {
                    // Delete the index if it exists
                    try
                    {
                        _searchClient.Indexes.Delete("geonames");
                    }
                    catch (Exception ex)
                    {
                        Console.WriteLine("Error deleting index: {0}\r\n", ex.Message.ToString());
                        Console.WriteLine("Did you remember to add your SearchServiceName and SearchServiceApiKey to the app.config?\r\n");
                        return false;
                    }

                    return true;
                }

                private static void CreateIndex()
                {
                    // Create the Azure Search index based on the included schema            try
                    {
                        var definition = new Index()
                        {
                            Name = "geonames",
                            Fields = new[]
                            {
                                new Field("FEATURE_ID",     DataType.String)         { IsKey = true,  IsSearchable = false, IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
                                new Field("FEATURE_NAME",   DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
                                new Field("FEATURE_CLASS",  DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
                                new Field("STATE_ALPHA",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
                                new Field("STATE_NUMERIC",  DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
                                new Field("COUNTY_NAME",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
                                new Field("COUNTY_NUMERIC", DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
                                new Field("ELEV_IN_M",      DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
                                new Field("ELEV_IN_FT",     DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
                                new Field("MAP_NAME",       DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
                                new Field("DESCRIPTION",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
                                new Field("HISTORY",        DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
                                new Field("DATE_CREATED",   DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
                                new Field("DATE_EDITED",    DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true}
                            }
                        };

                        _searchClient.Indexes.Create(definition);
                    }
                    catch (Exception ex)
                    {
                        Console.WriteLine("Error creating index: {0}\r\n", ex.Message.ToString());
                    }

                }

                private static void SyncDataFromAzureSQL()
                {
                    // This will use the Azure Search Indexer to synchronize data from Azure SQL to Azure Search
                    Uri _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
                    HttpClient _httpClient = new HttpClient();
                    _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);

                    Console.WriteLine("{0}", "Creating Data Source...\n");
                    Uri uri = new Uri(_serviceUri, "datasources/usgs-datasource");
                    string json = "{ 'name' : 'usgs-datasource','description' : 'USGS Dataset','type' : 'azuresql','credentials' : { 'connectionString' : 'Server=tcp:azs-playground.database.windows.net,1433;Database=usgs;User ID=reader;Password=EdrERBt3j6mZDP;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;' },'container' : { 'name' : 'GeoNamesRI' }} ";
                    HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Put, uri, json);
                    if (response.StatusCode != HttpStatusCode.Created && response.StatusCode != HttpStatusCode.NoContent)
                    {
                        Console.WriteLine("Error creating data source: {0}", response.Content.ReadAsStringAsync().Result);
                        return;
                    }

                    Console.WriteLine("{0}", "Creating Indexer...\n");
                    uri = new Uri(_serviceUri, "indexers/usgs-indexer");
                    json = "{ 'name' : 'usgs-indexer','description' : 'USGS data indexer','dataSourceName' : 'usgs-datasource','targetIndexName' : 'geonames','parameters' : { 'maxFailedItems' : 10, 'maxFailedItemsPerBatch' : 5, 'base64EncodeKeys': false }}";
                    response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Put, uri, json);
                    if (response.StatusCode != HttpStatusCode.Created && response.StatusCode != HttpStatusCode.NoContent)
                    {
                        Console.WriteLine("Error creating indexer: {0}", response.Content.ReadAsStringAsync().Result);
                        return;
                    }

                    Console.WriteLine("{0}", "Syncing data...\n");
                    uri = new Uri(_serviceUri, "indexers/usgs-indexer/run");
                    response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Post, uri);
                    if (response.StatusCode != HttpStatusCode.Accepted)
                    {
                        Console.WriteLine("Error running indexer: {0}", response.Content.ReadAsStringAsync().Result);
                        return;
                    }

                    bool running = true;
                    Console.WriteLine("{0}", "Synchronization running...\n");
                    while (running)
                    {
                        uri = new Uri(_serviceUri, "indexers/usgs-indexer/status");
                        response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
                        if (response.StatusCode != HttpStatusCode.OK)
                        {
                            Console.WriteLine("Error polling for indexer status: {0}", response.Content.ReadAsStringAsync().Result);
                            return;
                        }

                        var result = AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
                        if (result.lastResult != null)
                        {
                            switch ((string)result.lastResult.status)
                            {
                                case "inProgress":
                                    Console.WriteLine("{0}", "Synchronization running...\n");
                                    Thread.Sleep(1000);
                                    break;

                                case "success":
                                    running = false;
                                    Console.WriteLine("Synchronized {0} rows...\n", result.lastResult.itemsProcessed.Value);
                                    break;

                                default:
                                    running = false;
                                    Console.WriteLine("Synchronization failed: {0}\n", result.lastResult.errorMessage);
                                    break;
                            }
                        }
                    }
                }
            }
        }



## DataIndexer のビルドと実行

1. 右クリックし、 **DataIndexer** プロジェクトです。
2. プロジェクトをビルドします。
3. キーを押して **f5 キーを押して** を実行します。

コンソール ウィンドウに次のようなメッセージが表示されます。

![][6]

ポータルで、新しいはず **geonames** インデックス。 ポータルが最新の状態になるまでに数分かかることがあるので、しばらく待ってから結果を確認してください。

![][7]

## SimpleSearchMVCApp の変更

**SimpleSearchMVC** IIS Express でローカルに実行される web アプリです。 検索ボックスを提供し、検索結果を表形式で表示します。

このプログラムを実行する前に、3 つの変更を行います。

- 置換 **HomeController.cs**, 、ユーザー入力を受け付けるために使用します。 という名前の変数にこの例では、検索用語が格納されている *q*します。

- 置換 **index.cshtml**, 、検索用語を提供する web ページの入力を検索結果を表示します。

- 追加 **FeatureSearch.cs**, 、検索クライアントを作成し、検索を実行するクラス。

### HomeController.cs の更新

既定のコードを以下のコードに置き換えます。

    using Microsoft.Azure.Search.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.Mvc;

    namespace SimpleSearchMVCApp.Controllers
    {
        public class HomeController : Controller
        {
            //
            // GET: /Home/
            private FeaturesSearch _featuresSearch = new FeaturesSearch();

            public ActionResult Index()
            {
                return View();
            }

            public ActionResult Search(string q = "")
            {
                // If blank search, assume they want to search everything
                if (string.IsNullOrWhiteSpace(q))
                    q = "*";

                return new JsonResult
                {
                    JsonRequestBehavior = JsonRequestBehavior.AllowGet,
                    Data = _featuresSearch.Search(q)
                };
            }


        }
    }


### Index.cshtml の更新

既定のコードを以下のコードに置き換えます。

    @{
        ViewBag.Title = "Azure Search - Feature Search";
    }

    <script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.min.js"></script>
    <script type="text/javascript">

        $(function () {
            // Execute search if user clicks enter
            $("#q").keyup(function (event) {
                if (event.keyCode == 13) {
                    Search();
                }
            });
        });

        function Search() {
            // We will post to the MVC controller and parse the full results on the client side
            // You may wish to do additional pre-processing on the data before sending it back to the client
            var q = $("#q").val();

            $.post('/home/search',
            {
                q: q
            },
            function (data) {
                var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
                searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
                searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
                searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
                searchResultsHTML += "<td>DATE EDITED</td></tr>";
                for (var i = 0; i < data.length; i++) {
                    searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
                    searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
                    searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
                    searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
                    searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
                    searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
                    searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
                    searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
                    searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
                    searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
                    searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
                }

                $("#searchResults").html(searchResultsHTML);

            });

            function parseJsonDate(jsonDateString) {
                if (jsonDateString != null)
                    return new Date(parseInt(jsonDateString.replace('/Date(', '')));
                else
                    return "";
            }
        };

    </script>
    <h2>USGS Search for Rhode Island</h2>

    <div class="container">
        <input type="search" name="q" id="q" autocomplete="off" size="100" /> <button onclick="Search();">Search</button>
    </div>
    <br />
    <div class="container">
        <div class="row">
            <table id="searchResults" border="1"></table>
        </div>
    </div>


### FeaturesSearch.cs の追加

アプリケーションに検索機能を提供するクラスを追加します。

1. ソリューション エクスプ ローラーで右クリック **SimpleSearchMVCApp** > **追加** > **[新しい項目の** > **コード** > **クラス**します。
2. クラスに名前を **FeaturesSearch**します。
3. 既定のコードを以下のコードに置き換えます。

        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.Linq;
        using System.Web;

        namespace SimpleSearchMVCApp
        {
            public class FeaturesSearch
            {
                private static SearchServiceClient _searchClient;
                private static SearchIndexClient _indexClient;

                public static string errorMessage;

                static FeaturesSearch()
                {
                    try
                    {
                        string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
                        string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

                        // Create an HTTP reference to the catalog index
                        _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
                        _indexClient = _searchClient.Indexes.GetClient("geonames");
                    }
                    catch (Exception e)
                    {
                        errorMessage = e.Message.ToString();
                    }
                }

                public DocumentSearchResponse Search(string searchText)
                {
                    // Execute search based on query string
                    try
                    {
                        SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
                        return _indexClient.Documents.Search(searchText, sp);
                    }
                    catch (Exception ex)
                    {
                        Console.WriteLine("Error querying index: {0}\r\n", ex.Message.ToString());
                    }
                    return null;
                }

            }
        }

### スタートアップ プロジェクトとしての SimpleSearchMVCApp の設定

右クリックし、 **SimpleSearchMVCApp** プロジェクトをスタートアップ プロジェクトとして設定します。

## SimpleSearchMVCApp のビルドと実行

このプログラムをビルドして実行すると、既定のブラウザーで Web ページが表示され、Azure Search サービスのインデックスに格納されている USGS データにアクセスするための検索ボックスが提供されます。

![][8]

### USGS データの検索

USGS データ セットには、ロードアイランド州に関連するレコードが含まれています。 クリックすると **検索** [空の検索ボックスでは、表示されます、上位 50 のエントリが既定です。

検索語句を入力すると、検索を絞り込む条件が検索エンジンに提供されます。 地域の名前を入力してみてください。 *Roger Williams* ロードアイランド州の最初の州知事でした。 多数の公園、建造物、および学校に彼の名前が付けられています。

![][9]

また、次のようなクエリを試し、語句や演算子を追加または削除して検索の範囲を変更してみてください。

- Pawtucket または Pembroke
- goose +cape -neck


## 次のステップ

これは、.NET と USGS データセットに基づく最初の Azure Search チュートリアルです。 カスタム ソリューションで使用できる他の検索機能を紹介できるように、時間をかけてこのチュートリアルを拡張する予定です。

Azure Search についての知識が既にある場合は、このサンプルを基にして、サジェスター (先行入力またはオートコンプリート クエリ)、フィルター、ファセット ナビゲーションなどを試すことができます。 また、件数を追加してドキュメントを一括処理することで検索結果の表示を改善し、ユーザーが結果をページ移動できるようにすることもできます。

Azure Search を初めて使用する場合は、 他のチュートリアルも試して、作成できるものについての理解を深めることをお勧めします。 参照してください、 [ドキュメント ページ](http://azure.microsoft.com/documentation/services/search/) を他のリソースを検索します。 内のリンクを表示することも、 [ビデオとチュートリアルの一覧](search-video-demo-tutorial-list.md) の詳細にアクセスします。

<!--Image references-->
[1]: ./media/search-get-started-dotnet/create-search-portal-1.PNG
[2]: ./media/search-get-started-dotnet/create-search-portal-2.PNG
[3]: ./media/search-get-started-dotnet/create-search-portal-3.PNG

[4]: ./media/search-get-started-dotnet/AzSearch-DotNet-VSSolutionExplorer.png
[5]: ./media/search-get-started-dotnet/AzSearch-DotNet-Assembly.png

[6]: ./media/search-get-started-dotnet/consolemessages.png
[7]: ./media/search-get-started-dotnet/portalindexstatus.png
[8]: ./media/search-get-started-dotnet/usgssearchbox.png
[9]: ./media/search-get-started-dotnet/rogerwilliamsschool.png

[10]: ./media/search-get-started-dotnet/AzSearch-DotNet-MVCOptions.PNG
[11]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-1.PNG
[12]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-2.PNG

