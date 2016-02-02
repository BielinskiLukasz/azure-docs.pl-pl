1. Visual Studio のソリューション エクスプローラーで App_Start フォルダーを展開し、WebApiConfig.cs プロジェクト ファイルを開きます。

2. **ConfigOptions** 定義の後で、次のコード行を Register メソッドに追加します。

        options.PushAuthorization = 
            Microsoft.WindowsAzure.Mobile.Service.Security.AuthorizationLevel.User;

    これにより、プッシュ通知の登録の前にユーザー認証が強制的に行われます。

2. プロジェクトを右クリックし、[**追加**] をクリックし、[**クラス**] をクリックします。

3. 新しい空のクラスの名前を `PushRegistrationHandler` クリックして **追加**します。

4. コード ページの先頭に、次の **using** ステートメントを追加します。

        using System.Threading.Tasks; 
        using System.Web.Http; 
        using System.Web.Http.Controllers; 
        using Microsoft.WindowsAzure.Mobile.Service; 
        using Microsoft.WindowsAzure.Mobile.Service.Notifications; 
        using Microsoft.WindowsAzure.Mobile.Service.Security; 

5. 既存の **PushRegistrationHandler** クラスを次のコードと置き換えます。

     public class PushRegistrationHandler : INotificationHandler
     {
         public Task Register(ApiServices services, HttpRequestContext context,
         NotificationRegistration registration)
         {
             try
             {
                 // Perform a check here for user ID tags, which are not allowed.
                 if(!ValidateTags(registration))
                 {
                     throw new InvalidOperationException(
                         "You cannot supply a tag that is a user ID.");                    
                 }
    
                 // Get the logged-in user.
                 var currentUser = context.Principal as ServiceUser;
    
                 // Add a new tag that is the user ID.
                 registration.Tags.Add(currentUser.Id);
    
                 services.Log.Info("Registered tag for userId: " + currentUser.Id);
             }
             catch(Exception ex)
             {
                 services.Log.Error(ex.ToString());
             }
                 return Task.FromResult(true);
         }
    
         private bool ValidateTags(NotificationRegistration registration)
         {
             // Create a regex to search for disallowed tags.
             System.Text.RegularExpressions.Regex searchTerm =
             new System.Text.RegularExpressions.Regex(@"facebook:|google:|twitter:|microsoftaccount:",
                 System.Text.RegularExpressions.RegexOptions.IgnoreCase);
    
             foreach (string tag in registration.Tags)
             {
                 if (searchTerm.IsMatch(tag))
                 {
                     return false;
                 }
             }
             return true;
         }
    
         public Task Unregister(ApiServices services, HttpRequestContext context, 
             string deviceId)
         {
             // This is where you can hook into registration deletion.
             return Task.FromResult(true);
         }
     }

 登録中に **Register** メソッドが呼び出されます。 これによって、ログインしているユーザーの ID であるタグを登録に追加できます。 ユーザーが別のユーザーの IDで登録するのを防ぐために、指定したタグが検証されます。 このユーザーに通知が送信されると、このデバイスと、ユーザーが登録した他のすべてのデバイスで受信されます。

6. Controllers フォルダーを展開し、TodoItemController.cs プロジェクト ファイルを開き、**PostTodoItem** メソッドの場所を特定して、**SendAsync** を呼び出すコード行を次のコードに置き換えます。

     // Get the logged-in user.
     var currentUser = this.User as ServiceUser;
    
     // Use a tag to only send the notification to the logged-in user.
     var result = await Services.Push.SendAsync(message, currentUser.Id);

7. モバイル サービス プロジェクトを再発行します。

これで、サービスはユーザー ID タグを使用してプッシュ通知 (挿入された項目のテキスト) を、ログイン ユーザーによって作成されるすべての登録に送信するようになります。





