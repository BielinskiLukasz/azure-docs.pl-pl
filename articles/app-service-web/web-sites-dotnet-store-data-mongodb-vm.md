<properties 
    pageTitle="仮想マシンで実行される MongoDB に接続する Web アプリを Azure に作成する" 
    description="Azure 仮想マシン上の MongoDB に接続された Azure App Service に、Git を使用して ASP.NET アプリをデプロイする方法を示すチュートリアル。"
    tags="azure-portal" 
    services="app-service\web, virtual-machines" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="12/11/2015" 
    ms.author="cephalin"/>



# 仮想マシンで実行される MongoDB に接続する Web アプリを Azure に作成する

Git を使用すると、ASP.NET アプリケーションを Azure App Service Web Apps にデプロイできます。 このチュートリアルでは、Azure の仮想マシンで実行されている MongoDB データベースに接続する、ASP.NET MVC の単純なフロントエンド タスク一覧アプリケーションをビルドします。 [MongoDB ][mongodb] は一般的なオープン ソースのパフォーマンスの高い NoSQL データベースです。 開発用コンピューターで ASP.NET アプリケーションを実行してテストした後、Git を使用してアプリケーションを App Service Web Apps にアップロードします。
>[AZURE.NOTE] 場合は、Azure アカウントがサインアップする前に Azure App Service の使用を開始するには、 [App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751), 、App Service で有効期間の短いスターター web アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。


## 背景知識

このチュートリアルでは次の事項に関する知識があると楽ですが、必須ではありません。

* MongoDB の C# ドライバー。 Mongodb c# アプリケーションの開発に関する詳細については、MongoDB を参照してください。 [CSharp Language Center ][mongoc#langcenter]します。
* ASP .NET Web アプリケーション フレームワーク。 すべてについては、 [ASP.net website][asp.net]します。
* ASP .NET MVC Web アプリケーション フレームワーク。 すべてについては、 [ASP.NET MVC web サイト ][mvcwebsite]します。
* Azure。 初めての詳細については参照 [Azure ][windowsazure]します。

## 前提条件

- [Visual Studio Express 2013 for Web ][vseweb] または [Visual Studio 2013 ][vsult]
- [Azure SDK for .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- アクティブな Microsoft Azure サブスクリプション

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a>
## 仮想マシンを作成して MongoDB をインストールする

このチュートリアルは、Azure に仮想マシンが作成済みであることを前提としています。 仮想マシンの作成後、仮想マシンに MongoDB をインストールする必要があります。

* Windows 仮想マシンを作成して MongoDB をインストールする、次を参照してください。 [Azure ][installmongoonwindowsvm]します。
* または、Linux 仮想マシンを作成して MongoDB をインストールする、次のように表示します。 [[installmongooncentoslinuxvm] Azure の CentOS Linux を実行する仮想マシン上の MongoDB のインストール][installmongooncentoslinuxvm]します。

Azure に仮想マシンを作成して MongoDB をインストールしたら、仮想マシンの DNS 名 (たとえば "testlinuxvm.cloudapp.net") とエンドポイントで指定した MongoDB 用の外部ポートを忘れずに記録してください。 この情報は後で必要になります。

<a id="createapp"></a>
## アプリケーションを作成する

このセクションでは、Visual Studio を使用して "My Task List" という ASP.NET アプリケーションを作成し、Azure App Service Web Apps への最初のデプロイを実行します。 このアプリケーションはローカルで実行されますが、Azure 上の仮想マシンに接続し、そこに作成した MongoDB インスタンスを使用します。

1. Visual Studio で、**[新しいプロジェクト]** をクリックします。

    ![[開始] ページの ][startpagenewproject]

1. **[新しいプロジェクト]** ウィンドウの左側のウィンドウで、**[Visual C#]** を選択し、**[Web]** を選択します。 中央のウィンドウで [ **ASP.NET Web アプリケーション**します。 ウィンドウの下部で、プロジェクトに「MyTaskListApp」という名前を付け、**[OK]** をクリックします。

    ![[新しいプロジェクト] ダイアログ][newprojectmytasklistapp]

1. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[MVC]** を選択し、**[OK]** をクリックします。

    ![MVC テンプレートの選択][vs2013selectmvctemplate]

1. Microsoft Azure にサインインしていない場合は、サインインするように求められます。 画面の指示に従って Azure にサインインします。
2. サインインしたら、App Service の Web アプリの構成を開始できます。 **[Web アプリ名]**、**[App Service プラン]**、**[リソース グループ]**、および **[リージョン]** を指定し、**[OK]** をクリックします。

    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. プロジェクトの作成が完了したら、**[Azure App Service の利用状況]** ウィンドウに示されているとおりに、Azure App Service で Web アプリが作成されるのを待ちます。 次に、**[MyTaskListApp をこの Web アプリケーションに今すぐ発行する]** をクリックします。

1. **[発行]** をクリックします。

    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

    既定の ASP.NET アプリケーションが Azure App Service Web Apps に発行されると、そのアプリケーションはブラウザーで起動します。

## MongoDB C# ドライバーをインストールする

MongoDB では、ドライバーを通じてクライアント側の C# アプリケーションをサポートしています。このドライバーをローカルの開発用コンピューターにインストールする必要があります。 C# ドライバーは NuGet から入手できます。

MongoDB C# ドライバーをインストールするには、以下を実行します。

1. **ソリューション エクスプローラー**で、**MyTaskListApp** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

    ![NuGet パッケージの管理][vs2013managenugetpackages]

2. **[NuGet パッケージの管理]** ウィンドウの左側のウィンドウで、**[オンライン]** をクリックします。 右側の **[オンラインで検索]** ボックスに「mongodb.driver」と入力します。 **[インストール]** をクリックして、ドライバーをインストールします。

    ![MongoDB C# ドライバーの検索][searchformongodbcsharpdriver]

3. **[同意する]** をクリックして、10gen, Inc. のライセンス条項に同意します。

4. クリックして **閉じる** ドライバーがインストールした後です。
    ![インストールされた MongoDB C# ドライバー][mongodbcsharpdriverinstalled]


これで MongoDB C# ドライバーがインストールされました。 参照、 **MongoDB.Bson**, 、**MongoDB.Driver**, 、および **MongoDB.Driver.Core**  ライブラリがプロジェクトに追加されました。

![MongoDB C# ドライバーの参照][mongodbcsharpdriverreferences]

## モデルを追加する

**ソリューション エクスプローラー**で、*[Models]* フォルダーを右クリックし、**[追加]**、新しい **[クラス]** の順にクリックし、名前を「*TaskModel.cs*」にします。 *TaskModel.cs* で、既存のコードを次のコードに置き換えます。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MongoDB.Bson.Serialization.Attributes;
    using MongoDB.Bson.Serialization.IdGenerators;
    using MongoDB.Bson;
    
    namespace MyTaskListApp.Models
    {
        public class MyTask
        {
            [BsonId(IdGenerator = typeof(CombGuidGenerator))]
            public Guid Id { get; set; }
    
            [BsonElement("Name")]
            public string Name { get; set; }
    
            [BsonElement("Category")]
            public string Category { get; set; }
    
            [BsonElement("Date")]
            public DateTime Date { get; set; }
    
            [BsonElement("CreatedDate")]
            public DateTime CreatedDate { get; set; }
    
        }
    }

## データ アクセス レイヤーを追加する

**ソリューション エクスプローラー**で、*[MyTaskListApp]* プロジェクトを右クリックし、**[追加]**、**[新しいフォルダー]** の順にクリックし、名前を「*DAL*」にします。 ソリューション エクスプローラーで、*[DAL]* フォルダーを右クリックし、**[追加]**、**[クラス]** の順にクリックします。 クラス ファイルに「*Dal.cs*」という名前を付けます。 *Dal.cs* で、既存のコードを次のコードに置き換えます。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    
    
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            private MongoServer mongoServer = null;
            private bool disposed = false;
    
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net"
            private string connectionString = "mongodb://mongodbsrv20151211.cloudapp.net";
    
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
    
            // Default constructor.        
            public Dal()
            {
            }
    
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
    
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
    
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
    
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
    
            # region IDisposable
    
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
    
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        if (mongoServer != null)
                        {
                            this.mongoServer.Disconnect();
                        }
                    }
                }
    
                this.disposed = true;
            }
    
            # endregion
        }
    }

## コントローラーを追加する

*ソリューション エクスプローラー*で **Controllers\HomeController.cs** ファイルを開いて、既存のコードを次のコードに置き換えます。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.Mvc;
    using MyTaskListApp.Models;
    using System.Configuration;
    
    namespace MyTaskListApp.Controllers
    {
        public class HomeController : Controller, IDisposable
        {
            private Dal dal = new Dal();
            private bool disposed = false;
            //
            // GET: /MyTask/
    
            public ActionResult Index()
            {
                return View(dal.GetAllTasks());
            }
    
            //
            // GET: /MyTask/Create
    
            public ActionResult Create()
            {
                return View();
            }
    
            //
            // POST: /MyTask/Create
    
            [HttpPost]
            public ActionResult Create(MyTask task)
            {
                try
                {
                    dal.CreateTask(task);
                    return RedirectToAction("Index");
                }
                catch
                {
                    return View();
                }
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            # region IDisposable
    
            new protected void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
    
            new protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        this.dal.Dispose();
                    }
                }
    
                this.disposed = true;
            }
    
            # endregion
    
        }
    }

## スタイルを設定する

ページの上部にタイトルを変更するには、開く、 *Views\Shared\\_Layout.cshtml* ファイルで **ソリューション エクスプ ローラー** と次のように見えるようには、"My Task List Application"で、ナビゲーション ヘッダーの"Application name"を置換します。

    @Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

タスク一覧メニューを設定するために、*\Views\Home\Index.cshtml* ファイルを開き、既存のコードを次のコードに置き換えます。

    @model IEnumerable<MyTaskListApp.Models.MyTask>
    
    @{
        ViewBag.Title = "My Task List";
    }
    
    <h2>My Task List</h2>
    
    <table border="1">
        <tr>
            <th>Task</th>
            <th>Category</th>
            <th>Date</th>
    
        </tr>
    
    @foreach (var item in Model) {
        <tr>
            <td>
                @Html.DisplayFor(modelItem => item.Name)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Category)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Date)
            </td>
    
        </tr>
    }
    
    </table>
    <div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>

新しいタスクを作成する機能を追加するには、右クリックし、 *\home\\* フォルダーと **追加** 、 **ビュー**します。 ビューの名前を「*Create*」にします。 コードを次のコードに置き換えます。

    @model MyTaskListApp.Models.MyTask
    
    <script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
    
    @using (Html.BeginForm("Create", "Home")) {
        @Html.ValidationSummary(true)
        <fieldset>
            <legend>New Task</legend>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Name)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Name)
                @Html.ValidationMessageFor(model => model.Name)
            </div>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Category)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Category)
                @Html.ValidationMessageFor(model => model.Category)
            </div>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Date)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Date)
                @Html.ValidationMessageFor(model => model.Date)
            </div>
    
            <p>
                <input type="submit" value="Create" />
            </p>
        </fieldset>
    }

**ソリューション エクスプローラー**の表示は次のようになります。

![ソリューション エクスプローラー][solutionexplorermytasklistapp]

## MongoDB 接続文字列を設定する

**ソリューション エクスプローラー**で、*DAL/Dal.cs* ファイルを開きます。 次のコード行を見つけます。

    private string connectionString = "mongodb://<vm-dns-name>";

置換 `< vm dns 名 >` で作成した MongoDB を実行する仮想マシンの DNS 名を持つ、 [仮想マシンを作成して MongoDB をインストール][] このチュートリアルの手順です。仮想マシンの DNS 名を確認するには、Azure ポータルで **[仮想マシン]** をクリックし、**[DNS 名]** の値を確認します。

仮想マシンの DNS 名が "testlinuxvm.cloudapp.net" で、MongoDB が既定のポート 27017 をリッスンしている場合、その接続文字列のコード行は次のようになります。

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

仮想マシンのエンドポイントで MongoDB 用に別の外部ポートが指定されている場合は、接続文字列で次のようにしてポートを指定できます。

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

MongoDB の接続文字列の詳細については、次を参照してください。 [接続 ][mongoconnectionstrings]します。

## ローカル デプロイをテストする

開発用コンピューターでアプリケーションを実行するには、**[デバッグ]** メニューの **[デバッグ開始]** をクリックするか、**F5** キーを押します。 IIS Express が起動し、ブラウザーが開いて、アプリケーションのホーム ページが表示されます。 新しいタスクを追加でき、そのデータは Azure の仮想マシンで実行されている MongoDB データベースに追加されます。

![My Task List アプリケーション][tasklistappblank]

## Azure App Service Web Apps に発行する

このセクションでは、変更を Azure App Service Web Apps に発行します。

1. ソリューション エクスプローラーで、もう一度 **MyTaskListApp** を右クリックし、**[発行]** をクリックします。
2. **[発行]** をクリックします。

    Web アプリが Azure App Service で実行され、Azure Virtual Machines の MongoDB データベースにアクセスできるようになります。

## 概要

これで、Azure App Service Web Apps に ASP .NET アプリケーションをデプロイできました。 Web アプリを表示するには、次の操作を行います。

1. Azure ポータルにログインします。
2. **[Web Apps]** をクリックします。
3. **[Web Apps]** ボックスの一覧から Web アプリを選択します。

Mongodb c# アプリケーションの開発に関する詳細については、次を参照してください。 [CSharp Language Center ][mongoc#langcenter]します。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]









[azureportal]: http://manage.windowsazure.com 
[windowsazure]: http://www.windowsazure.com 
[mongoc#langcenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/ 
[mvcwebsite]: http://www.asp.net/mvc 
[asp.net]: http://www.asp.net/ 
[mongoconnectionstrings]: http://www.mongodb.org/display/DOCS/Connections 
[mongodb]: http://www.mongodb.org 
[installmongooncentoslinuxvm]: /manage/linux/common-tasks/mongodb-on-a-linux-vm/ 
[installmongoonwindowsvm]: /manage/windows/common-tasks/install-mongodb/ 
[vseweb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express 
[vsult]: http://www.microsoft.com/visualstudio/eng/2013-downloads 
[startpagenewproject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png 
[newprojectmytasklistapp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png 
[vs2013selectmvctemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png 
[vs2013defaultmvcapplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png 
[vs2013managenugetpackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png 
[searchformongodbcsharpdriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png 
[mongodbcsharpdriverinstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png 
[mongodbcsharpdriverreferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png 
[solutionexplorermytasklistapp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png 
[tasklistappblank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png 
[wawscreatewebsite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png 
[wawsdashboardmytasklistapp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png 
[image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png 
[image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png 
[image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png 
[create a virtual machine and install mongodb]: #virtualmachine 
[create and run the my task list asp.net application on your development computer]: #createapp 
[create an azure web site]: #createwebsite 
[deploy the asp.net application to the web site using git]: #deployapp 

