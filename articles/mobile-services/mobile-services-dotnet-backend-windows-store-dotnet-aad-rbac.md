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

## 概要

ロール ベースのアクセス制御 (RBAC) は、ユーザーが保持できるロールに権限を割り当てる方法です。 特定のクラスのユーザーができることとできないことの境界を適切に定義します。 このチュートリアルでは、Azure Mobile Services に基本的な RBAC を追加する方法について説明します。

このチュートリアルでは、ロール ベースのアクセス制御について示しながら、Azure Active Directory (AAD) で定義された Sales グループに対する各ユーザーのメンバーシップを確認します。 Azure Active Directory の [Graph REST API] を使用して .NET Mobile Service バックエンドでは、アクセス チェックが行われます。 Sales グループに属するユーザーのみが、データの照会を許可されます。

>[AZURE.NOTE] このチュートリアルは、認証方法を含めた認証の知識を深めることを目的としています。 Azure Active Directory 認証プロバイダーを使用して、[アプリに認証の追加] のチュートリアルを完了して必要です。 このチュートリアルを引き続き [アプリに認証の追加] このチュートリアルで使用した TodoItem アプリケーションを更新します。

## 前提条件

このチュートリアルには、次のものが必要です。

* Windows 8.1 で実行されている Visual Studio 2013。
* Azure Active Directory 認証プロバイダーを使用して [アプリに認証の追加] このチュートリアルを完了します。




## 統合アプリケーションのキーを生成する

[アプリに認証の追加] チュートリアル中に作成した、統合アプリケーションに対する登録を [Azure Active Directory アカウント ログインを使用するサービスに登録] を実行したときに手順です。 このセクションでは、その統合アプリケーションのクライアント ID でディレクトリ情報を読み取るときに使用するキーを生成します。

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## Sales グループとメンバーシップを作成する

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]



## モバイル サービスにカスタム承認属性を作成する

このセクションでは、モバイル サービスの操作でアクセス確認を実行するときに使用できる、新しいカスタム承認属性を作成します。 この属性は、渡されたロール名に基づいて Active Directory グループを検索します。 その後、そのグループのメンバーシップに基づいてアクセス確認を行います。

1. Visual Studio で、モバイル サービスの .NET バックエンド プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. [NuGet パッケージ マネージャー] ダイアログ ボックスで、検索条件に「**ADAL**」と入力してモバイル サービスの **Active Directory 認証ライブラリ**を検索し、インストールします。 このチュートリアルは、ADAL パッケージの 3.3.205061641-alpha (プレリリース) バージョンで最後にテストされています。

3. Visual Studio で、モバイル サービス プロジェクトを右クリックし、**[追加]**、**[新しいフォルダー]** の順にクリックします。 新しいフォルダーに "**Utilities**" という名前を付けます。

4. Visual Studio で、新しい **Utilities** フォルダーを右クリックし、"**AuthorizeAadRole.cs**" という名前の新しいクラス ファイルを追加します。

    ![][0]

5. AuthorizeAadRole.cs ファイルの次のコードを追加 `を使用して` ステートメントをファイルの先頭にします。

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

6. AuthorizeAadRole.cs で、Utilities 名前空間に次の列挙型を追加します。 この例では、**Sales** ロールだけを使用します。 それ以外は、単に使用する可能性のあるグループの例です。

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

7. AuthorizeAadRole.cs で、次のコードを追加 `AuthorizeAadRole` クラス、Utilities 名前空間を定義します。

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

8. AuthorizeAadRole.cs で、更新、 `InitGroupIds` メソッドを `AuthorizeAadRole` クラスの次のようにします。 このメソッドは、各ロールへのグループ ID のディクショナリ マッピングを作成します。

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

9. AuthorizeAadRole.cs で、更新、 `GetAADToken` メソッドを `AuthorizeAadRole` クラスです。 このメソッドは、Mobile Service に格納されているアプリの設定を使用して、ADAL から AAD へのアクセス トークンを取得します。
 >[AZURE.NOTE] ADAL for .NET には、Active Directory に対する余分なネットワーク トラフィックを軽減するために、既定でメモリ内トークン キャッシュが含まれます。 ただし、独自のキャッシュ実装を作成したり、完全にキャッシュを無効にしたりできます。 詳細については、[ADAL for .NET] を参照してください。

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

10. AuthorizeAadRole.cs で、更新、 `CheckMembership` メソッドを `AuthorizeAadRole` クラスです。 このメソッドは、ユーザーのオブジェクト ID を受け取ります。 使用して、AAD Graph REST API されてで選択したロールに関連付けられているグループのメンバー id を表示するには、そのオブジェクト id を確認して、 `AuthorizeAadRole` クラス

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

11. AuthorizeAadRole.cs で、更新、 `OnAuthorization` メソッドで、 `AuthorizeAadRole` クラスを次のコードとします。 このコードは、Mobiile Service を呼び出しているユーザーが AAD で認証済みであることを予期します。 その後、ユーザーの AAD オブジェクト ID を取得し、ロールに対応する Active Directory グループとのメンバーシップを確認します。
>[AZURE.NOTE] Active Directory グループは名前で検索できます。 ただし、多くの場合には、モバイル サービス アプリケーションの設定としてグループ ID を格納する方が適切です。 これは、グループ名の方が変更されることが多く、ID は同じままであるためです。

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

## データベース操作にロール ベースのアクセス確認を追加する

1. Visual Studio で、モバイル サービス プロジェクト内の **Controllers** フォルダーを展開します。 TodoItemController.cs を開きます。

2. TodoItemController.cs では、追加、 `を使用して` カスタム承認属性を含む utilities 名前空間のステートメントです。

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


## クライアントのアクセスをテストする

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]










[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png 
[add authentication to your app]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md 
[how to register with the azure active directory]: mobile-services-how-to-register-active-directory-authentication.md 
[directory sync scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx 
[store server scripts]: mobile-services-store-scripts-source-control.md 
[register to use an azure active directory login]: mobile-services-how-to-register-active-directory-authentication.md 
[graph rest api]: http://msdn.microsoft.com/library/azure/hh974478.aspx 
[ismemberof]: http://msdn.microsoft.com/library/azure/dn151601.aspx 
[adal for .net]: https://msdn.microsoft.com/library/azure/jj573266.aspx 

