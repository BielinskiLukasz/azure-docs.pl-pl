<properties 
    pageTitle="テーブル ストレージを使用した Web アプリケーション (Node.js) | Microsoft Azure" 
    description="Express を使用する Web アプリケーションのチュートリアルを基に、Azure Storage サービスと Azure モジュールを追加するチュートリアル。" 
    services="cloud-services, storage" 
    documentationCenter="nodejs" 
    authors="TomArcher" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="09/01/2015" 
    ms.author="tarcher"/>







# ストレージを使用する Node.js Web アプリケーション

## 概要

このチュートリアルで作成したアプリケーションを拡張します、
Microsoft を使用して、[Express を使用する Node.js Web アプリケーション] のチュートリアル
データ管理サービスを使用する Node.js 用 azure クライアント ライブラリ。 クラスターの
web ベースのタスク一覧アプリケーションを作成するアプリケーションを拡張します。
Azure にデプロイすることができます。 タスク一覧では、ユーザー
タスクの取得、新しいタスクを追加し、タスクを完了としてマークします。

タスク項目は Azure ストレージに格納されます。 Azure
ストレージは、フォールト トレランスに優れた非構造化データ ストレージが提供し、
高可用性です。 Azure ストレージには、いくつかのデータ構造が含まれています。
格納して、データへのアクセスで記憶域を利用して
Node.js 用 Azure SDK に含まれる Api からのサービスまたは
REST api です。 詳細については、[保存して Azure でのデータへのアクセス] を参照してください。

このチュートリアルは、[Node.js Web を完了するいると想定しています。
アプリケーションでは、 [express を使用して Express][node.js web application using express] チュートリアルです。

学習内容:

-   Jade テンプレート エンジンを操作する方法
-   Azure データ管理サービスを操作する方法

完成したアプリケーションのスクリーンショットは次のようになります。

![Internet Explorer で表示された完成した Web ページ](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Web.Config のストレージ資格情報の設定

Azure ストレージにアクセスするには、ストレージに合格する必要があります。
資格情報。 そのためには、web.config アプリケーション設定を使用します。
これらの設定は環境変数としてノードにします。
Azure SDK によって読み取られます。
> [AZURE.NOTE] ストレージの資格情報は、アプリケーションを
Azure に展開するときにのみ使用されます。 エミュレーターで実行している場合、アプリケーションは
ストレージ エミュレーターを使用します。

ストレージ アカウントの資格情報を取得するには、次の手順を実行します。
web.config 設定に追加します。

1.  Azure PowerShell をまだ開いていない場合は起動します。**[スタート]** メニューで **[すべてのプログラム]、[Azure]** の順に展開し、**[Azure PowerShell]** を右クリックして、**[管理者として実行]** を選択します。

2.  アプリケーションが含まれているフォルダーに移動します。 たとえば、C:\\node\\tasklist\\WebRole1 です。

3.  Azure Powershell ウィンドウで次のコマンドレットを入力して、ストレージ アカウント情報を取得します。

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    これにより、ホストされるサービスに関連付けられたストレージ アカウントとアカウント キーのリストが取得されます。
    > [AZURE.NOTE] Azure SDK では、サービスを展開するときにストレージ アカウントが作成されるので、以前のガイドでアプリケーションを展開したときから、ストレージ アカウントは既に存在しています。

4.  **ServiceDefinition.csdef** ファイルを開きます。このファイルには、アプリケーションを Azure にデプロイするときに使用される環境設定が含まれます。

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  次のブロックを **Environment** 要素の下に挿入し、{STORAGE ACCOUNT} と {STORAGE ACCESS KEY} を、デプロイメントに使用するストレージ アカウントのアカウント名とプライマリ キーに置き換えます。

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

    ![web.cloud.config ファイルの内容](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  ファイルを保存して、メモ帳を閉じます。

### 追加モジュールのインストール

2. 次のコマンドを入力して、[azure]、[node-uuid]、[nconf]、および [async] モジュールをローカルにインストールし、これらのモジュールのエントリを **package.json** ファイルに保存します。

     PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

 このコマンドの出力は次のように表示されます。

     node-uuid@1.4.1 node_modules\node-uuid
    
     nconf@0.6.9 node_modules\nconf
     ├── ini@1.1.0
     ├── async@0.2.9
     └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)
    
     azure-storage@0.1.0 node_modules\azure-storage
     ├── extend@1.2.1
     ├── xmlbuilder@0.4.3
     ├── mime@1.2.11
     ├── underscore@1.4.4
     ├── validator@3.1.0
     ├── node-uuid@1.4.1
     ├── xml2js@0.2.7 (sax@0.5.2)
     └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)


## ノード アプリケーションでのテーブル サービスの使用

ここでは、**express** コマンドで作成された基本的なアプリケーションを、タスクのモデルを格納する **task.js** ファイルを追加することによって拡張します。 また、既存の **app.js** を変更し、このモデルを使用する新しい **tasklist.js** ファイルを作成します。

### モデルの作成

1. **WebRole1** ディレクトリ内に、**models** という名前の新しいディレクトリを作成します。

2. **models** ディレクトリ内に、**task.js** という名前の新しいファイルを作成します。 このファイルには、アプリケーションで作成されるタスクのモデルが格納されます。

3. **task.js** ファイルの先頭に、必要なライブラリを参照する次のコードを追加します。

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4. 次に、Task オブジェクトを定義およびエクスポートするコードを追加します。 このオブジェクトは、テーブルへの接続を処理します。

     module.exports = Task;
    
     function Task(storageClient, tableName, partitionKey) {
       this.storageClient = storageClient;
       this.tableName = tableName;
       this.partitionKey = partitionKey;
       this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
         if(error) {
           throw error;
         }
       });
     };

5. 次に、Task オブジェクトの追加のメソッドを定義する次のコードを追加します。このメソッドによって、テーブルに格納されたデータを操作できます。

     Task.prototype = {
       find: function(query, callback) {
         self = this;
         self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
           if(error) {
             callback(error);
           } else {
             callback(null, result.entries);
           }
         });
       },
    
       addItem: function(item, callback) {
         self = this;
         // use entityGenerator to set types
         // NOTE: RowKey must be a string type, even though
         // it contains a GUID in this example.
         var itemDescriptor = {
           PartitionKey: entityGen.String(self.partitionKey),
           RowKey: entityGen.String(uuid()),
           name: entityGen.String(item.name),
           category: entityGen.String(item.category),
           completed: entityGen.Boolean(false)
         };
    
         self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
           if(error){  
             callback(error);
           }
           callback(null);
         });
       },
    
       updateItem: function(rKey, callback) {
         self = this;
         self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
           if(error) {
             callback(error);
           }
           entity.completed._ = true;
           self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
             if(error) {
               callback(error);
             }
             callback(null);
           });
         });
       }
     }

6. **task.js** ファイルを保存して閉じます。

### コントローラーの作成

1. **WebRole1/routes** ディレクトリに **tasklist.js** という名前の新しいファイルを作成し、テキスト エディターで開きます。

2. 次のコードを **tasklist.js** に追加します。 これによって、**tasklist.js** で使用される azure モジュールと async モジュールが読み込まれます。 また、**TaskList** 関数が定義されます。先ほど定義した **Task** オブジェクトのインスタンスがこの関数に渡されます。

     var azure = require('azure-storage');
     var async = require('async');
    
     module.exports = TaskList;
    
     function TaskList(task) {
       this.task = task;
     }

2. **tasklist.js** ファイルへの内容の追加を続行し、**showTasks**、**addTask**、および **completeTasks** の各メソッドを追加します。

     TaskList.prototype = {
       showTasks: function(req, res) {
         self = this;
         var query = azure.TableQuery()
           .where('completed eq ?', false);
         self.task.find(query, function itemsFound(error, items) {
           res.render('index',{title: 'My ToDo List ', tasks: items});
         });
       },
    
       addTask: function(req,res) {
         var self = this      
         var item = req.body.item;
         self.task.addItem(item, function itemAdded(error) {
           if(error) {
             throw error;
           }
           res.redirect('/');
         });
       },
    
       completeTask: function(req,res) {
         var self = this;
         var completedTasks = Object.keys(req.body);
         async.forEach(completedTasks, function taskIterator(completedTask, callback) {
           self.task.updateItem(completedTask, function itemsUpdated(error) {
             if(error){
               callback(error);
             } else {
               callback(null);
             }
           });
         }, function goHome(error){
           if(error) {
             throw error;
           } else {
            res.redirect('/');
           }
         });
       }
     }

3. **tasklist.js** ファイルを保存します。

### app.js の変更

1. **WebRole1** ディレクトリ内の **app.js** ファイルを、テキスト エディターで開きます。

2. ファイルの先頭に次のコードを追加します。このコードは、azure モジュールを読み込み、テーブル名とパーティション キーを設定します。

        var azure = require('azure-storage');
        var tableName = 'tasks';
        var partitionKey = 'hometasks';

3. app.js ファイル内で、次の行が表示されるまで下へスクロールします。

     app.use('/', routes);
     app.use('/users', users);

 これらの行を下のコードに置き換えます。 インスタンスが初期化はこの <strong>タスク</strong> 、ストレージ アカウントへの接続を使用します。 これは、 <strong>TaskList</strong>, 、Table サービスと通信するために使用します。

     var TaskList = require('./routes/tasklist');
     var Task = require('./models/task');
     var task = new Task(azure.createTableService(), tableName, partitionKey);
     var taskList = new TaskList(task);
    
     app.get('/', taskList.showTasks.bind(taskList));
     app.post('/addtask', taskList.addTask.bind(taskList));
     app.post('/completetask', taskList.completeTask.bind(taskList));

4. **app.js** ファイルを保存します。

### index ビューの変更

1. **views** ディレクトリに移動し、テキスト エディターで **index.jade** ファイルを開きます。

2. **index.jade** ファイルの内容を次のコードに置き換えます。 これにより、既存のタスクを表示するビューと、新しいタスクの追加とタスクの完了済みのマーク付けを実行するためのフォームを定義します。

     extends layout
    
     block content
       h1= title
       br
    
       form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
           tr
             td Name
             td Category
             td Date
             td Complete
           if tasks != []
             tr
               td 
           else
             each task in tasks
               tr
                 td #{task.name._}
                 td #{task.category._}
                 - var day   = task.Timestamp._.getDate();
                 - var month = task.Timestamp._.getMonth() + 1;
                 - var year  = task.Timestamp._.getFullYear();
                 td #{month + "/" + day + "/" + year}
                 td
                   input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
         button.btn(type="submit") Update tasks
       hr
       form.well(action="/addtask", method="post")
         label Item Name: 
         input(name="item[name]", type="textbox")
         label Item Category: 
         input(name="item[category]", type="textbox")
         br
         button.btn(type="submit") Add item

3. **index.jade** ファイルを保存して閉じます。

### グローバル レイアウトの変更

**views** ディレクトリ内の **layout.jade** ファイルは、他の **.jade** ファイルのグローバル テンプレートとして使用されます。 この手順で使用するように変更が [Twitter Bootstrap](https://github.com/twbs/bootstrap), 、見栄えの良い web サイトをデザインしやすくツールキットです。

1. ファイルをダウンロードして展開 [Twitter Bootstrap](http://getbootstrap.com/)します。 コピー、 **bootstrap.min.css** ファイルから、 **bootstrap \\dist\\css** フォルダーを **public \\stylesheets** tasklist アプリケーションのディレクトリ。

2. **views** フォルダーから、テキスト エディターで **layout.jade** を開き、内容を次の内容に置き換えます。

        doctype html
        html
          head
            title= title
            link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
            link(rel='stylesheet', href='/stylesheets/style.css')
          body.app
            nav.navbar.navbar-default
              div.navbar-header
                a.navbar-brand(href='/') My Tasks
            block content

3. **layout.jade** ファイルを保存します。

### エミュレーターでのアプリケーションの実行

エミュレーターでアプリケーションを開始するには、次のコマンドを実行します。

    PS C:\node\tasklist\WebRole1> start-azureemulator -launch

ブラウザーが開き、次のページが表示されます。

![My Task List というタイトルの Web ページと新しいタスクを追加するためのタスクとフィールドを含むテーブル](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

フォームを使用してアイテムを追加するか、完了済みのマークを付けることにより既存のアイテムを削除します。

## Azure にアプリケーションをデプロイする

Windows PowerShell ウィンドウで、次のコマンドレットを呼び出して、ホストされるサービスを Azure に再デプロイします。

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

**myuniquename** を、このアプリケーションの一意の名前に置き換えます。 **datacentername** を、Azure データ センターの名前で置き換えます (たとえば、**West US**)。

デプロイが完了すると、次のような応答が表示されます。

    PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
    WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
    WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
    WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
    WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
    WARNING: 2:19:01 PM - Connecting...
    WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
    WARNING: 2:19:40 PM - Upgrading...
    WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
    WARNING: 2:22:48 PM - Initializing...
    WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
    WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

前と同様に **–launch** オプションを指定してあるので、発行が完了するとブラウザーが開かれ、Azure で実行されているアプリケーションが表示されます。

![My Task List ページを表示するブラウザー ウィンドウ。 URL を示しますページは、Azure でホストされているようになりました。](. media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png/)

## アプリケーションの停止と削除

アプリケーションを配置するには、後にすることも無効にすることができます。
コストを回避またはビルドし、無料の試用期間内の他のアプリケーションの展開
までの時間。

Azure では、消費されたサーバー時間の 1 時間単位の料金が Web ロール インスタンスに課金されます。
アプリケーションを展開すると、サーバー時間が消費される場合でも、
インスタンスが実行されていないと停止の状態にします。

次の手順では、アプリケーションの停止と削除の方法を示します。

1.  Windows PowerShell ウィンドウで、サービスのデプロイを停止します
    次のコマンドレットは、前のセクションで作成されます。

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    サービスの停止には、数分間かかる場合があります。 サービスが停止すると、停止したことを知らせるメッセージが表示されます。

3.  サービスを削除するには、次のコマンドレットを呼び出します。

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    確認を求めるメッセージが表示されたら、「**Y**」と入力して、サービスを削除します。

    サービスの削除には、数分間かかる場合があります。 サービスが削除されると、削除されたことを知らせるメッセージが表示されます。


[node.js web application using express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/ 
[storing and accessing data in azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx 
[node.js web application]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/ 

