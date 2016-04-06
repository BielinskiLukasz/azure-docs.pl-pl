<properties
    pageTitle="Azure Mobile Services での HTML クライアントの使用方法 | Microsoft Azure"
    description="Azure Mobile Services 向け HTML クライアントを使用する方法について説明します。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/23/2015"
    ms.author="glenga"/>

# Azure Mobile Services 向け HTML/JavaScript クライアントを使用する方法

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

##概要

このガイドでは、Azure Mobile Services 向け HTML/JavaScript クライアントを使用して一般的なシナリオを実行する方法について説明します。Windows ストア JavaScript アプリと PhoneGap/Cordova アプリが含まれます。 紹介するシナリオは、データの照会、挿入、更新、および削除、ユーザーの認証、エラー処理などです。 モバイル サービスに慣れていない場合は、」を完了するを考慮する必要があります、 [モバイル サービス クイック スタート](mobile-services-html-get-started.md)します。 このクイック スタート チュートリアルでは、アカウントを構成し、初めてのモバイル サービスを作成します。

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>方法: Mobile Services クライアントを作成する

Mobile Services クライアントに参照を追加する方法は、アプリ プラットフォームに依存し、次のものがあります。

- Web ベースのアプリケーションの場合、HTML ファイルを開き、次のコードをページのスクリプト参照に追加します。

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js"></script>

- JAVASCRIPT/HTML で記述された Windows ストア アプリ、追加、 **WindowsAzure.MobileServices.WinJS** NuGet パッケージをプロジェクトにします。

- PhoneGap または Cordova アプリの追加、 [Mobile Services プラグイン](https://github.com/Azure/azure-mobile-services-cordova) をプロジェクトにします。 このプラグインをサポートしています [プッシュ通知](#push-notifications)します。

エディターで、JavaScript ファイルを開くか作成し、`MobileServiceClient` 変数を定義する次のコードを追加します。さらに、モバイル サービスからのアプリケーション URL とアプリケーション キーを `MobileServiceClient` コンストラクターに順に指定します。

    var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

プレース ホルダーを置き換える必要があります `AppUrl` 、モバイル サービスのアプリケーションの URL を使用し、 `AppKey` から取得するアプリケーション キーを持つ、 [Azure クラシック ポータル](http://manage.windowsazure.com/)します。

>[AZURE.IMPORTANT]アプリケーション キーの目的は、モバイル サービスに対するランダムな要求をフィルターで除外して、アプリケーションと共に配布されます。 このキーは暗号化されないため、セキュリティで保護されていると見なすことはできません。 モバイル サービスのデータへのアクセスを完全に保護するには、アクセスを許可する前にユーザーを認証する必要があります。 詳細については、次を参照してください。 [方法: ユーザーを認証する](#authentication)です。

##<a name="querying"></a>方法: モバイル サービスのデータを照会する

SQL Database テーブルのデータにアクセスまたは変更するすべてのコードは、`MobileServiceTable` オブジェクトに対して関数を呼び出します。 `MobileServiceClient` のインスタンスに対して `getTable()` 関数を呼び出して、テーブルへの参照を取得します。

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>方法: 返されるデータをフィルター処理する

次のコードは、クエリに `where` 句を含めることによってデータをフィルター処理する方法を示しています。 すべての項目を返す `todoItemTable` 、complete フィールドに等しい `false`します。 `todoItemTable` 以前に作成したモバイル サービス テーブルへの参照です。 where 関数は、行のフィルタリング述語をテーブルに対するクエリに適用します。 この関数は、引数として JSON オブジェクトを受け取るかまたは行フィルターを定義する関数を受け取り、詳細に構成可能なクエリを返します。

    var query = todoItemTable.where({
        complete: false
    }).read().done(function (results) {
        alert(JSON.stringify(results));
    }, function (err) {
        alert("Error: " + err);
    });

呼び出して `where` クエリ オブジェクトおよび、オブジェクトをパラメーターとして渡すことで、Mobile Services を行のみを返すように指示していますが `complete` 列が含まれる、 `false` 値。 また、次の要求 URI を確認し、クエリ文字列自体変更を加えていることを確認します。

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

ブラウザー開発者ツールや Fiddler などのメッセージ検査ソフトウェアを使用して、モバイル サービスに送信された要求の URI を表示できます。

通常、この要求は、サーバー側で次のような SQL クエリに変換されます。

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0

`where` メソッドに渡されるオブジェクトは任意の数のパラメーターを持つことができます。これらのパラメーターは、クエリに対してすべて AND 句として解釈されます。 たとえば、次のコードがあるとします。

    query.where({
       complete: false,
       assignee: "david",
       difficulty: "medium"
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

このコードは、ほぼ次のように変換できます (前と同じ要求)。

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND assignee = 'david'
          AND difficulty = 'medium'

上記の `where` ステートメントと上記の SQL クエリでは、"david" に割り当てられた、難易度 (difficulty) が "medium" である未完了の項目が検索されます。

ただし、別の方法で同じクエリを作成することもできます。 Query オブジェクトの `.where` 呼び出しは `WHERE` 句に `AND` 式を追加します。そこで、これを 3 行で記述することもできます。

    query.where({
       complete: false
    });
    query.where({
       assignee: "david"
    });
    query.where({
       difficulty: "medium"
    });

または、次のように fluent API を使用することもできます。

    query.where({
       complete: false
    })
       .where({
       assignee: "david"
    })
       .where({
       difficulty: "medium"
    });

この 2 つの方法は等価であり、区別しないで使用できます。 これまでのすべての `where` 呼び出しは、いくつかのパラメーターを持つオブジェクトを受け取り、データベースからのデータに対して同等性を調べるために比較されました。 しかし、オブジェクトの代わりに関数を受け取る、クエリ メソッドの別のオーバーロードがあります。 この関数では、非同等性やその他の関係演算のための演算子を使用して、より複雑な式を書くことができます。 これらの関数では、`this` キーワードがサーバー オブジェクトにバインドされます。

関数の本文は、クエリ文字列パラメーターに渡される Open Data Protocol (OData) のブール式に変換されます。 次に示すように、パラメーターを受け取らない関数を渡すこともできます。

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


パラメーターを持つ関数を渡した場合、`where` 句以降のすべての引数は指定された順に関数パラメーターに適用されます。 関数スコープ外からのすべてのオブジェクトは、パラメーターとして渡す必要があります (関数は、外部変数をキャプチャできません)。 次の 2 つの例では、引数 "david" が `name` パラメーターにバインドされています。最初の例では、引数 "medium" も `level` パラメーターにバインドされています。 さらに、関数は、次に示すように、サポートされている式を含む単一の `return` ステートメントで構成されている必要があります。

     query.where(function (name, level) {
        return this.assignee == name && this.difficulty == level;
     }, "david", "medium").read().done(function (results) {
        alert(JSON.stringify(results));
     }, function (err) {
        alert("Error: " + err);
     });

このように、規則に従っている限り、より複雑なフィルターをデータベース クエリに追加できます。

    query.where(function (name) {
       return this.assignee == name &&
          (this.difficulty == "medium" || this.difficulty == "low");
    }, "david").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

`where` と `orderBy`、`take`、`skip` を組み合わせることができます。 詳細については、次のセクションを参照してください。

### <a name="sorting"></a>方法: 返されるデータを並べ替える

次のコードは、クエリに `orderBy` または `orderByDescending` 関数を含めることによってデータを並べ替える方法を示しています。 次のコードは、`todoItemTable` から、`text` フィールドの値に基づいて昇順に並べ替えられた項目を返します。 既定では、サーバーは最初の 50 個の要素のみを返します。

> [AZURE.NOTE] サーバー側で設定されたページ サイズをすべての要素が返されることを防ぐために既定で使用されます。 これにより、大きなデータ セットの既定の要求がサービスに悪影響を与えないようにします。
次のセクションで説明する `take` を呼び出すことにより、返される項目の数を増やすことができます。 `todoItemTable` 以前に作成したモバイル サービス テーブルへの参照です。

    var ascendingSortedTable = todoItemTable.orderBy("text").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

    var descendingSortedTable = todoItemTable.orderByDescending("text").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

    var descendingSortedTable = todoItemTable.orderBy("text").orderByDescending("text").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

### <a name="paging"></a>方法: ページにデータを返す

クライアントが応答でさらに多くのデータ数を明示的に指定していない場合、Mobile Services は既定で特定の要求に対して 50 行だけ返します。 次のコードでは、クエリで `take` 句と `skip` 句を使用して、返されたデータにページングを実装する方法を示しています。  次のクエリを実行すると、テーブルの最初の上位 3 つの項目が返されます。

    var query = todoItemTable.take(3).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

クエリの URI では、`take(3)` メソッドがクエリ オプション `$top=3` に変換されていることに注目してください。

次の変更されたクエリは、最初の 3 つの結果をスキップし、その後の 3 つを返します。 ページ サイズが 3 つの項目である場合、これは実質的にデータの 2 番目の "ページ" になります。

    var query = todoItemTable.skip(3).take(3).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

繰り返しになりますが、モバイル サービスに送信された要求の URI を表示できます。 クエリの URI では、`skip(3)` メソッドがクエリ オプション `$skip=3` に変換されていることに注目してください。

これは、ハードコーディングされたページング値を `take` および `skip` 関数に渡す、簡略化したシナリオです。 実際のアプリケーションでは、ユーザーが前後のページに移動できるように、ページャー コントロールまたは同等の UI と共に上記と同様のクエリを使用することができます。

### <a name="selecting"></a>方法: 特定の列を選択する

クエリに `select` 句を追加し、結果に含める一連のプロパティを指定できます。 たとえば、次のコードは `todoItemTable` の各行から `id`、`complete`、`text` プロパティを返します。

    var query = todoItemTable.select("id", "complete", "text").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    })

select 関数のパラメーターは、取得するテーブルの列の名です。


これまでに説明した関数はいずれも付加的なものであるため、この後も呼び出すようにし、クエリに活用することにします。 次の例も参照してください。

    query.where({
       complete: false
    })
       .select('id', 'assignee')
       .orderBy('assignee')
       .take(10)
       .read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);

### <a name="lookingup"></a>方法: ID でデータを検索する

`lookup` 関数は、`id` 値のみを受け取り、データベースからその ID を持つオブジェクトを返します。 整数型または文字列の `id` 列を持つデータベース テーブルが作成されます。 既定では文字列の `id` 列になります。

    todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
       alert(JSON.stringify(result));
    }, function (err) {
       alert("Error: " + err);
    })

##<a name="odata-query"></a>OData クエリ操作を実行する

Mobile Services では、REST クエリの作成と実行に OData クエリ URI 慣例を使用します。  組み込まれているクエリ関数ですべての OData クエリ (特にプロパティないのサブストリング検索などの複雑なフィルター操作) が作成できるわけではありません。 複雑なクエリには、次のように有効な OData クエリ オプション文字列を `read` 関数に渡すことができます。

    function refreshTodoItems() {
        todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
            var itemElements = $.map(items, createUiForTodoItem);
            $("#todo-items").empty().append(itemElements);
            $("#no-items").toggle(items.length === 0);
        }, handleError);
    }

>[AZURE.NOTE]未処理の OData クエリ オプション文字列を指定すると、 `read` 関数の場合、同じクエリでクエリ ビルダー メソッドを使用することもできません。 この場合、クエリ全体を OData クエリ文字列として作成する必要があります。 OData システム クエリ オプションの詳細については、次を参照してください。、 [OData system query options reference]します。

##<a name="inserting"></a>方法: モバイル サービスにデータを挿入する

次のコードは、テーブルに新しい行を挿入する方法を示しています。 クライアントは、モバイル サービスに POST 要求を送信して、データ行を挿入することを要求します。 要求の本文には、挿入するデータが JSON オブジェクトとして含まれます。

    todoItemTable.insert({
       text: "New Item",
       complete: false
    })

このコードは、指定された JSON オブジェクトからのデータをテーブルに挿入します。 挿入操作が完了したときに呼び出されるコールバック関数を指定することもできます。

    todoItemTable.insert({
       text: "New Item",
       complete: false
    }).done(function (result) {
       alert(JSON.stringify(result));
    }, function (err) {
       alert("Error: " + err);
    });

###ID 値の操作

モバイル サービス テーブルの一意のカスタム文字列値をサポートしている **id** 列です。 このため、アプリケーションは、ID にメール アドレスやユーザー名などのカスタム値を使用できます。 たとえば、次のコードは JSON オブジェクトとして新しい項目を挿入します。一意の ID は電子メール アドレスです。

    todoItemTable.insert({
       id: "myemail@domain.com",
       text: "New Item",
       complete: false
    });

文字列 ID には、次のような利点があります。

+ データベースと往復することなく ID が生成されます。
+ 他のテーブルやデータベースのレコードをより簡単にマージできます。
+ ID 値をより適切にアプリケーションのロジックに統合できます。

挿入されたレコードで文字列 ID 値がまだ設定されない場合は、Mobile Services によって ID 用の一意の値が生成されます。 クライアントまたは .NET バックエンドで独自 ID 値を生成する方法の詳細については「 [方法: 一意の ID 値を生成](mobile-services-how-to-use-server-scripts.md#generate-guids)します。

また、テーブルに整数 ID を使用することもできます。 整数 ID を使用するには、`mobile table create` コマンドで `--integerId` オプションを使用してテーブルを作成する必要があります。 このコマンドは、Azure のコマンド ライン インターフェイス (CLI) で使用されます。 CLI の使用の詳細については、次を参照してください。 [モバイル サービス テーブルの管理用コマンド](../virtual-machines-command-line-tools.md#Mobile_Tables)します。

##<a name="modifying"></a>方法: モバイル サービスのデータを変更する

次のコードは、テーブルのデータを更新する方法を示しています。 クライアントは、モバイル サービスに PATCH 要求を送信して、データ行を更新することを要求します。 要求の本文には、更新する特定のフィールドが JSON オブジェクトとして含まれます。 このコードは、テーブル `todoItemTable` 内の既存の項目を更新します。

    todoItemTable.update({
       id: idToUpdate,
       text: newText
    })

1 つ目のパラメーターは、テーブル内の更新するインスタンスを ID で指定します。

更新操作が完了したときに呼び出されるコールバック関数を指定することもできます。

    todoItemTable.update({
       id: idToUpdate,
       text: newText
    }).done(function (result) {
       alert(JSON.stringify(result));
    }, function (err) {
       alert("Error: " + err);
    });

##<a name="deleting"></a>方法: モバイル サービスのデータを削除する

次のコードは、テーブルからデータを削除する方法を示しています。 クライアントは、モバイル サービスに DELETE 要求を送信して、データ行を削除することを要求します。 このコードは、テーブル todoItemTable 内の既存の項目を削除します。

    todoItemTable.del({
       id: idToDelete
    })

1 つ目のパラメーターは、テーブル内の削除するインスタンスを ID で指定します。

削除操作が完了したときに呼び出されるコールバック関数を指定することもできます。

    todoItemTable.del({
       id: idToDelete
    }).done(function () {
       /* Do something */
    }, function (err) {
       alert("Error: " + err);
    });

##<a name="binding"></a>方法: ユーザー インターフェイスにデータを表示する

このセクションでは、返されたデータ オブジェクトを UI 要素を使用して表示する方法について説明します。 次のサンプル コードを実行すると、`todoItemTable` 内の項目を照会し、それを単純なリストに表示できます。 ここでは、どのような種類の選択、フィルター処理、または並べ替えも行われません。

    var query = todoItemTable;

    query.read().then(function (todoItems) {
       // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
       var listOfItems = document.getElementById('placeToInsert');
       for (var i = 0; i < todoItems.length; i++) {
          var li = document.createElement('li');
          var div = document.createElement('div');
          div.innerText = todoItems[i].text;
          li.appendChild(div);
          listOfItems.appendChild(li);
       }
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

Windows ストア アプリで、クエリの結果を使用してデータ ソースとしてバインドできる [WinJS.Binding.List] オブジェクトを作成する、 [ListView] オブジェクトです。 詳細については、次を参照してください。 [Data binding (Windows Store apps using JavaScript and HTML)]します。

##<a name="custom-api"></a>方法: カスタム API の呼び出し

カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。 カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。 モバイル サービスでカスタム API を作成する方法の例は、次を参照してください。 [方法: カスタム API エンドポイントを定義する](mobile-services-dotnet-backend-define-custom-api.md)です。

カスタム API を呼び出すことによって、クライアントから呼び出す、 [invokeApi](https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L337) メソッドを **MobileServiceClient**します。 たとえば、次のコード行がへの POST 要求を送信、 **completeAll** のモバイル サービスの API。

    client.invokeApi("completeall", {
        body: null,
        method: "post"
    }).done(function (results) {
        var message = results.result.count + " item(s) marked as complete.";
        alert(message);
        refreshTodoItems();
    }, function(error) {
        alert(error.message);
    });


現実的な例と、複数の詳細については **invokeApi**, を参照してください [Azure Mobile Services クライアント Sdk のカスタム API](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx)します。

##<a name="authentication"></a>方法: ユーザーを認証する

Mobile Services は、Facebook、Google、Microsoft アカウント、Twitter などのさまざまな外部 ID プロバイダーを使用したアプリケーション ユーザーの認証と承認をサポートします。 テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。 さらに、認証されたユーザーの ID を使用することにより、サーバー スクリプトで承認ルールを実装することもできます。 詳細については、チュートリアル [「モバイル サービスでの認証の使用」] を参照してください。

>[AZURE.NOTE] PhoneGap または Cordova アプリで認証を使用する場合は、プロジェクトに、次のプラグインを追加することも必要があります。
>
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git


2 つの認証フローがサポート: _サーバー フロー_ と _クライアント フロー_します。 サーバー フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。 クライアント フローでは、プロバイダー固有およびデバイス固有の SDK を利用することから、シングル サインオンなどのデバイス固有の機能との統合がさらに進みます。

###サーバー フロー
Mobile Services によって Windows ストア アプリまたは HTML5 アプリの認証プロセスが管理されるようにするには、
アプリケーションを ID プロバイダーに登録する必要があります。 その後、モバイル サービス内で、プロバイダーから提供されたアプリケーション ID とシークレットを構成する必要があります。 詳細については、このチュートリアルを参照してください。 [アプリへの認証の追加](mobile-services-html-get-started-users.md)します。

ID プロバイダーを登録した後は、単純にプロバイダーの [MobileServiceAuthenticationProvider] 値で [LoginAsync メソッド] を呼び出すだけです。 たとえば、Facebook にログインするには、次のコードを使用します。

    client.login("facebook").done(function (results) {
         alert("You are now logged in as: " + results.userId);
    }, function (err) {
         alert("Error: " + err);
    });

Facebook 以外の ID プロバイダーを使用している場合は、上の `login` メソッドに渡す値を `microsoftaccount`、`facebook`、`twitter`、`google`、`windowsazureactivedirectory` のいずれかに変更します。

この場合、Mobile Services は、選択されたプロバイダーのログイン ページを表示し、ID プロバイダーでのログインが成功した後で Mobile Services 認証トークンを生成することで、OAuth 2.0 認証フローを管理します。  [login] 関数に完了するとは、JSON オブジェクトを返します (**ユーザー**)、ユーザー ID と Mobile Services 認証トークンの両方を公開する、 **userId** と **authenticationToken** フィールドに、それぞれします。 このトークンはキャッシュして、期限が切れるまで再利用することができます。 詳細については、「認証トークンをキャッシュする」を参照してください。

###クライアント フロー
アプリケーションは個別に ID プロバイダーにアクセスして、返されたトークンを認証のために Mobile Services に提供することもできます。 このクライアント フローでは、ユーザーにシングル サインイン エクスペリエンスを提供したり、ID プロバイダーから追加のユーザー データを取得したりすることができます。

####Facebook/Google SDK の基本的な例

この例では、認証用の Facebook クライアント SDK を使用します。

    client.login(
         "facebook",
         {"access_token": token})
    .done(function (results) {
         alert("You are now logged in as: " + results.userId);
    }, function (err) {
         alert("Error: " + err);
    });

この例では、それぞれのプロバイダー SDK で提供されるトークンが変数 `token` に格納されるとします。
現時点では、クライアント認証に Twitter を使用することはできません。

####Microsoft アカウントの基本的な例
次の例では、Windows ストア アプリケーションのシングル サインオンを Microsoft アカウントによってサポートしている Live SDK を使用します。

    WL.login({ scope: "wl.basic"}).then(function (result) {
          client.login(
                "microsoftaccount",
                {"authenticationToken": result.session.authentication_token})
          .done(function(results){
                alert("You are now logged in as: " + results.userId);
          },
          function(error){
                alert("Error: " + err);
          });
    });

この単純な例は、Live Connect を呼び出すことによって、モバイル サービスに渡されますからトークンを取得、 [login] 関数です。


####Microsoft アカウントの完全な例

次の例では、Live SDK と WinJS API を使用して、拡張されたシングル サインオン エクスペリエンスを提供する方法を示します。

    // Set the mobileClient variable to client variable generated by the tooling.
    var mobileClient = <yourClient>;

    var session = null;
    var login = function () {
        return new WinJS.Promise(function (complete) {
            WL.login({ scope: "wl.basic" }).then(function (result) {
                session = result.session;

                WinJS.Promise.join([
                    WL.api({ path: "me", method: "GET" }),
                    mobileClient.login(result.session.authentication_token)
                ]).done(function (results) {
                    // Build the welcome message from the Microsoft account info.
                    var profile = results[0];
                    var title = "Welcome " + profile.first_name + "!";
                    var message = "You are now logged in as: "
                        + mobileClient.currentUser.userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message, title);
                    dialog.showAsync().then(function () {
                        // Reload items from the mobile service.
                        refreshTodoItems();
                    }).done(complete);

                }, function (error) {

                });
            }, function (error) {
                session = null;
                var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
                dialog.showAsync().done(complete);
            });
        });
    }

    var authenticate = function () {
        // Block until sign-in is successful.
        login().then(function () {
            if (session === null) {
                // Authentication failed, try again.
                authenticate();
            }
        });
    }

    // Initialize the Live client.
    WL.init({
        redirect_uri: mobileClient.applicationUrl
    });

    // Start the sign-in process.
    authenticate();

これにより、Live Connect クライアントが初期化され、新しいログイン要求が Microsoft アカウントに送信され、返された認証トークンが Mobile Services に送信され、サインイン済みのユーザーに関する情報が表示されます。 アプリは、認証が成功するまで起動しません。
<!--- //this guidance may be bad from an XSS vulnerability standpoint. We need to find better guidance for this
###Caching the authentication token
In some cases, the call to the login method can be avoided after the first time the user authenticates. We can use [sessionStorage] or [localStorage] to cache the current user identity the first time they log in and every subsequent time we check whether we already have the user identity in our cache. If the cache is empty or calls fail (meaning the current login session has expired), we still need to go through the login process.

    // After logging in
    sessionStorage.loggedInUser = JSON.stringify(client.currentUser);

    // Log in
    if (sessionStorage.loggedInUser) {
       client.currentUser = JSON.parse(sessionStorage.loggedInUser);
    } else {
       // Regular login flow
   }

     // Log out
    client.logout();
    sessionStorage.loggedInUser = null;
-->

##<a name="push-notifications"></a>方法: プッシュ通知に登録する

アプリが PhoneGap または Apache Cordova HTML/JavaScript アプリのときは、ネイティブ モバイル プラットフォームによってデバイスでプッシュ通知を受け取ることができます。  [Azure Mobile Services 用 Apache Cordova プラグイン](https://github.com/Azure/azure-mobile-services-cordova) Azure Notification Hubs によるプッシュ通知に登録することができます。 使用する特定の通知サービスは、コードが実行されるネイティブ デバイス プラットフォームに依存します。 これを行う方法の例は、このサンプルを参照してください。 [Cordova アプリにプッシュ通知を使用して Microsoft Azure](https://github.com/Azure/mobile-services-samples/tree/master/CordovaNotificationsArticle)します。

>[AZURE.NOTE]このプラグインのみサポートされて iOS および Android デバイスいます。 Windows デバイスも含むソリューションでは、記事を参照してください。 [通知ハブの統合を使用する PhoneGap アプリへのプッシュ通知](http://blogs.msdn.com/b/azuremobile/archive/2014/06/17/push-notifications-to-phonegap-apps-using-notification-hubs-integration.aspx)します。

##<a name="errors"></a>方法: エラーを処理する

Mobile Services には、エラーの検出、検証、回避のためのさまざまな方法があります。

たとえば、サーバー スクリプトは、モバイル サービスに登録され、挿入や更新が行われるデータでの広範な操作 (検証やデータの修正を含む) の実行に使用できます。 次のようにデータを検証および変更するサーバー スクリプトを定義し、登録するとします。

    function insert(item, user, request) {
       if (item.text.length > 10) {
          request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
       } else {
          request.execute();
       }
    }

サーバー側スクリプトは、モバイル サービスに送信された文字列データの長さを検証し、長すぎる文字列 (この場合は 10 文字を超える) を拒否します。

モバイル サービスはデータを検証してエラー応答をサーバー側に送信するため、検証からのエラー応答を処理できるように HTML アプリケーションを更新することができます。

    todoItemTable.insert({
       text: itemText,
       complete: false
    })
       .then(function (results) {
       alert(JSON.stringify(results));
    }, function (error) {
       alert(JSON.parse(error.request.responseText).error);
    });


これをさらに詳しく見ると、データ アクセスを実行するたびに 2 つ目の引数としてエラー ハンドラーを渡します。

    function handleError(message) {
       if (window.console && window.console.error) {
          window.console.error(message);
       }
    }

    client.getTable("tablename").read()
        .then(function (data) { /* do something */ }, handleError);

##<a name="promises"></a>方法: promise を使用する

promise は、まだ計算されていない値の操作を完了するスケジュールを設定するためのメカニズムを提供します。 promise は、非同期 API を使用して相互作用を管理するための抽象化です。

`done` promise は、指定された関数が正常に完了した時点またはエラーが発生した時点で、すぐに実行されます。 `then` promise とは異なり、関数内で処理されないエラーをスローすることが保証されます。この関数は、ハンドラーの実行が完了した後、then の場合であれば返されたすべてのエラーをエラー状態の promise としてスローします。 詳細については、次を参照してください。 [done]します。

    promise.done(onComplete, onError);

次に例を示します。

    var query = todoItemTable;
    query.read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

`then` promise は `done` promise と同じです。ただし、`done` promise では、`then` promise とは異なり、関数内で処理されないエラーがスローされることが保証されます。 `then` にエラー ハンドラーを提供しなかった場合、操作でエラーが発生すると、例外はスローされず、代わりにエラー状態の promise が返されます。 詳細については、次を参照してください。 [then]します。

    promise.then(onComplete, onError).done( /* Your success and error handlers */ );

次に例を示します。

    var query = todoItemTable;
    query.read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

promise はいくつかの異なる方法で使用することができます。 前の `then` 関数から返される promise で `then` または `done` を呼び出すことにより、promise 操作を連結できます。 `then` は操作の中間ステージ (たとえば、`.then().then()`) に使用し、`done` は操作の最終ステージ (たとえば、`.then().then().done()`) に使用します。  `then` は promise を返すため、複数の `then` 関数を連結できます。 `done` メソッドは undefined を返すため、このメソッドを複数個連結することはできません。 [Learn more about the  differences between then and done].

    todoItemTable.insert({
       text: "foo"
    }).then(function (inserted) {
       inserted.newField = 123;
       return todoItemTable.update(inserted);
    }).done(function (insertedAndUpdated) {
       alert(JSON.stringify(insertedAndUpdated));
    })

##<a name="customizing"></a>方法: クライアント要求ヘッダーをカスタマイズする

カスタムの要求ヘッダーを送信できます。そのためには、`withFilter` 関数を使用し、フィルター内で送信される要求の任意のプロパティの読み取りと書き込みを行います。 このようなカスタム HTTP ヘッダーを追加するケースとしては、サーバー側スクリプトで必要とされる場合や、カスタム HTTP ヘッダーによってサーバー側スクリプトを拡張できる場合があります。

    var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
       .withFilter(function (request, next, callback) {
       request.headers.MyCustomHttpHeader = "Some value";
       next(request, callback);
    });

フィルターを使用する機会は要求ヘッダーをカスタマイズする機会よりもたくさんあります。 確認または変更要求、確認または応答を変更、ネットワー キング呼び出しのバイパス、複数の呼び出しなどを送信する、使用できます。

##<a name="hostnames"></a>方法: クロス オリジン リソース共有を使用する

モバイル サービスとの対話やモバイル サービスへの要求の送信を許可する Web サイトを制御するには、ホストする Web サイトのホスト名をクロス オリジン リソース共有 (CORS) ホワイトリストに追加してください。 JavaScript バックエンド モバイル サービスの [構成] タブでホワイト リストを構成することができます、 [Azure クラシック ポータル](https://manage.windowsazure.com)します。 必要に応じて、ワイルドカードを使用できます。 既定では、新しいモバイル サービスは `localhost` からのアクセスのみを許可するようブラウザーに指示します。クロス オリジン リソース共有 (CORS) により、外部ホスト名のブラウザーで実行されている JavaScript コードがモバイル サービスと対話できるようになります。  この構成は、WinJS アプリケーションには必要ありません。

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[Look up data by ID]: #lookingup
[How to: Display data in the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Use promises]: #promises
[How to: Customize request headers]: #customizing
[How to: Use cross-origin resource sharing]: #hostnames
[Next steps]: #nextsteps
[Execute an OData query operation]: #odata-query



<!-- URLs. -->
[then]: http://msdn.microsoft.com/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/library/windows/apps/hh701079.aspx
[Learn more about the  differences between then and done]: http://msdn.microsoft.com/library/windows/apps/hh700334.aspx
[how to handle errors in promises]: http://msdn.microsoft.com/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/library/windows/apps/br211837.aspx
[Data binding (Windows Store apps using JavaScript and HTML)]: http://msdn.microsoft.com/library/windows/apps/hh758311.aspx
[login]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L301
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[OData system query options reference]: http://go.microsoft.com/fwlink/p/?LinkId=444502


