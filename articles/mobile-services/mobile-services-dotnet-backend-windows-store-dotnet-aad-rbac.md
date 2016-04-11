<properties
    pageTitle=".NET および Azure Active Directory を使用した Mobile Services でのロール ベースのアクセス制御 (Windows ストア) | Microsoft Azure"
    description=".NET バックエンドで Mobile Service を使用して、Windows ストア アプリケーションで Azure Active Directory ロールに基づいてアクセスを制御する方法について説明します。"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="wesmc"/>

# JavaScript および Azure Active Directory を使用した Mobile Services でのロール ベースのアクセス制御

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

##概要

ロール ベースのアクセス制御 (RBAC) は、ユーザーが保持できるロールに権限を割り当てる方法です。 特定のクラスのユーザーができることとできないことの境界を適切に定義します。 このチュートリアルでは、Azure Mobile Services に基本的な RBAC を追加する方法について説明します。

このチュートリアルでは、ロール ベースのアクセス制御について示しながら、Azure Active Directory (AAD) で定義された Sales グループに対する各ユーザーのメンバーシップを確認します。 .NET モバイル サービス バックエンドを使用するとアクセスの確認を行いますが、 [Graph REST API] Azure Active Directory 用です。 Sales グループに属するユーザーのみが、データの照会を許可されます。


>[AZURE.NOTE] このチュートリアルの目的は、認証方法を含めた認証の知識を拡張することです。 最初に完了する必要が、 [Add Authentication to your app] Azure Active Directory 認証プロバイダーを使用したチュートリアルです。 使用した TodoItem アプリケーションを更新するこのチュートリアルでは引き続き、 [Add Authentication to your app] チュートリアルです。

##前提条件

このチュートリアルには、次のものが必要です。

* Windows 8.1 で実行されている Visual Studio 2013。
* 完了、 [Add Authentication to your app] Azure Active Directory 認証プロバイダーを使用したチュートリアルです。




##統合アプリケーションのキーを生成する


中に、 [Add Authentication to your app] チュートリアルでは、統合アプリケーションに対する登録を作成するには、実行したときに、 [Register to use an Azure Active Directory Login] 手順です。 このセクションでは、その統合アプリケーションのクライアント ID でディレクトリ情報を読み取るときに使用するキーを生成します。

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##Sales グループとメンバーシップを作成する

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]



##モバイル サービスにカスタム承認属性を作成する

このセクションでは、モバイル サービスの操作でアクセス確認を実行するときに使用できる、新しいカスタム承認属性を作成します。 この属性は、渡されたロール名に基づいて Active Directory グループを検索します。 その後、そのグループのメンバーシップに基づいてアクセス確認を行います。

1. Visual Studio でプロジェクトを右クリックしてモバイル サービス .NET バックエンドを **NuGet パッケージの管理**します。

2. NuGet パッケージ マネージャー] ダイアログ ボックスで次のように入力します。 **ADAL** を見つけてインストールするには、検索条件で、 **Active Directory 認証ライブラリ** 、モバイル サービス用です。 このチュートリアルは、ADAL パッケージの 3.3.205061641-alpha (プレリリース) バージョンで最後にテストされています。

3. Visual Studio で、モバイル サービス プロジェクトを右クリックし、クリックして **追加** し **新しいフォルダー**します。 新しいフォルダーに名前を **ユーティリティ**します。

4. Visual Studio で、右クリック、新しい **ユーティリティ** フォルダーという名前の新しいクラス ファイルを追加および **AuthorizeAadRole.cs**します。

    ![][0]

5. AuthorizeAadRole.cs ファイルの先頭に次の `using` ステートメントを追加します。

        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using Newtonsoft.Json;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;
        using System.IO;

6. AuthorizeAadRole.cs で、Utilities 名前空間に次の列挙型を追加します。 この例ではのみを扱う、 **Sales** ロールです。 それ以外は、単に使用する可能性のあるグループの例です。

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

7. AuthorizeAadRole.cs で、Utilities 名前空間に次の `AuthorizeAadRole` クラス定義を追加します。

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;
            private ApiServices services = null;

            // Constants used with ADAL and the Graph REST API for AAD
            private const string AadInstance = "https://login.windows.net/{0}";
            private const string GraphResourceId = "https://graph.windows.net/";
            private const string APIVersion = "?api-version=2013-04-05";

            // App settings pulled from the Mobile Service
            private string tenantdomain;
            private string clientid;
            private string clientkey;
            private Dictionary<int, string> groupIds = new Dictionary<int, string>();

            private string token = null;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

            // private class used to serialize the Graph REST API web response
            private class MembershipResponse
            {
                public bool value;
            }

            public AadRoles Role { get; private set; }

            // Generate a local dictionary for the role group ids configured as
            // Mobile Service app settings
            private void InitGroupIds()
            {
            }

            // Use ADAL and the authentication app settings from the Mobile Service to
            // get an AAD access token
            private string GetAADToken()
            {
            }

            // Given an AAD user id, check membership against the group associated with the role.
            private bool CheckMembership(string memberId)
            {
            }

            // Called when the user is attempting authorization
            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }



8. AuthorizeAadRole.cs で、`AuthorizeAadRole` クラスの `InitGroupIds` メソッドを次のように更新します。 このメソッドは、各ロールへのグループ ID のディクショナリ マッピングを作成します。

        private void InitGroupIds()
        {
            string groupId;

            if (services == null)
                return;

            if (!groupIds.ContainsKey((int)AadRoles.Sales))
            {
                if (services.Settings.TryGetValue("AAD_SALES_GROUP_ID", out groupId))
                {
                    groupIds.Add((int)AadRoles.Sales, groupId);
                }
                else
                    services.Log.Error("AAD_SALES_GROUP_ID app setting not found.");
            }
        }


9. AuthorizeAadRole.cs で、`AuthorizeAadRole` クラスの `GetAADToken` メソッドを更新します。 このメソッドは、Mobile Service に格納されているアプリの設定を使用して、ADAL から AAD へのアクセス トークンを取得します。

    >[AZURE.NOTE] ADAL for .NET には、既定では、Active Directory に対して余分なネットワーク トラフィックを軽減するために、メモリ内キャッシュが含まれています。 ただし、独自のキャッシュ実装を作成したり、完全にキャッシュを無効にしたりできます。 詳細については、次を参照してください。 [ADAL for .NET]します。

        // Use ADAL and the authentication app settings from the Mobile Service to get an AAD access token
        private async Task<string> GetAADToken()
        {
            // Try to get the required AAD authentication app settings from the mobile service.
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);

            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);
            if (result != null)
                token = result.AccessToken;
            else
                services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

10. AuthorizeAadRole.cs で、`AuthorizeAadRole` クラスの `CheckMembership` メソッドを更新します。 このメソッドは、ユーザーのオブジェクト ID を受け取ります。 その後、AAD Graph REST API を使用してそのオブジェクト ID を調べて、`AuthorizeAadRole` クラスで選択されているロールに関連付けられているグループのメンバー ID かどうかを確認します。

        private bool CheckMembership(string memberId)
        {
            bool membership = false;
            string url = GraphResourceId + tenantdomain + "/isMemberOf" + APIVersion;
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);

            // Use the Graph REST API to check group membership in the AAD
            try
            {
                request.Method = "POST";
                request.ContentType = "application/json";
                request.Headers.Add("Authorization", token);
                using (var sw = new StreamWriter(request.GetRequestStream()))
                {
                    // Request body must have the group id and a member id to check for membership
                    string body = String.Format("\"groupId\":\"{0}\",\"memberId\":\"{1}\"",
                        groupIds[(int)Role], memberId);
                    sw.Write("{" + body + "}");
                }

                WebResponse response = request.GetResponse();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string json = sr.ReadToEnd();
                MembershipResponse membershipResponse = JsonConvert.DeserializeObject<MembershipResponse>(json);
                membership = membershipResponse.value;
            }
            catch (Exception e)
            {
                services.Log.Error("OnAuthorization() exception : " + e.Message);
            }

            return membership;
        }


11. AuthorizeAadRole.cs で、`AuthorizeAadRole` クラスの `OnAuthorization` メソッドを次のコードで更新します。 このコードは、Mobiile Service を呼び出しているユーザーが AAD で認証済みであることを予期します。  その後、ユーザーの AAD オブジェクト ID を取得し、ロールに対応する Active Directory グループとのメンバーシップを確認します。

    >[AZURE.NOTE] 名前で、Active Directory グループを参照する可能性があります。 ただし、多くの場合には、モバイル サービス アプリケーションの設定としてグループ ID を格納する方が適切です。 これは、グループ名の方が変更されることが多く、ID は同じままであるためです。

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed
            // through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
            }

            bool isAuthorized = false;
            try
            {
                // Initialize a mapping for the group id to our enumerated type
                InitGroupIds();

                // Retrieve a AAD token from ADAL
                GetAADToken();
                if (token == null)
                {
                    services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                }
                else
                {
                    // Check group membership to see if the user is part of the group that corresponds to the role
                    if (!string.IsNullOrEmpty(groupIds[(int)Role]))
                    {
                        ServiceUser serviceUser = controller.User as ServiceUser;
                        if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                        {
                            var idents = serviceUser.GetIdentitiesAsync().Result;
                            AzureActiveDirectoryCredentials clientAadCredentials =
                                idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                            if (clientAadCredentials != null)
                            {
                                isAuthorized = CheckMembership(clientAadCredentials.ObjectId);
                            }
                        }
                    }
                }
            }
            catch (Exception e)
            {
                services.Log.Error(e.Message);
            }
            finally
            {
                if (isAuthorized == false)
                {
                    services.Log.Error("Denying access");

                    actionContext.Response = actionContext.Request
                        .CreateErrorResponse(HttpStatusCode.Forbidden,
                            "User is not logged in or not a member of the required group");
                }
            }
        }

12. AuthorizeAadRole.cs に変更を保存します。

##データベース操作にロール ベースのアクセス確認を追加する

1. Visual Studio で、展開、 **コント ローラー** モバイル サービス プロジェクトの下のフォルダーです。 TodoItemController.cs を開きます。

2. TodoItemController.cs で、カスタム承認属性を含む Utilities 名前空間に対して `using` ステートメントを追加します。

        using todolistService.Utilities;

3. TodoItemController.cs では、アクセスを確認する方法に応じて、属性をコントローラー クラスまたは個別のメソッドに追加できます。 すべてのコントローラー操作で同じロールに基づいてアクセスを確認する場合は、単に属性をクラスに追加します。 このチュートリアルをテストするために、属性を次のようにクラスに追加します。

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    挿入、更新、および削除の各操作についてのみアクセスを確認する場合は、次のようにそれらのメソッドに対してだけ属性を設定します。

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }


4. TodoItemController.cs を保存し、モバイル サービスをビルドして、構文エラーがないことを確認します。
5. モバイル サービスを Azure アカウントに発行します。


##クライアントのアクセスをテストする

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[Add Authentication to your app]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Register to use an Azure Active Directory Login]: mobile-services-how-to-register-active-directory-authentication.md
[Graph REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[ADAL for .NET]: https://msdn.microsoft.com/library/azure/jj573266.aspx


