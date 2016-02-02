<properties 
    pageTitle="Visual Studio 接続済みサービスを使用して Mobile Services を追加した後の JavaScript の使用開始 | Microsoft Azure " 
    description="Visual Studio の JavaScript プロジェクトで Mobile Services を使用する方法" 
    services="mobile-services" 
    documentationCenter="" 
    authors="TomArcher" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="JavaScript" 
    ms.topic="article" 
    ms.date="09/17/2015" 
    ms.author="tarcher"/>


# Visual Studio 接続済みサービスを使用して Azure Mobile Services を追加した後の JavaScript の使用開始

> [AZURE.SELECTOR]
> - [作業の開始](vs-mobile-services-javascript-getting-started.md)
> - [変更内容](vs-mobile-services-javascript-what-happened.md)

これらの例で使用されているコードを実行するために行う必要がある最初のステップは、接続しているモバイル サービスの種類によります。

 - JavaScript バックエンド モバイル サービスの場合は、TodoItem と呼ばれるテーブルを作成します。 テーブルを作成するには、サーバー エクスプ ローラーで、Azure ノードでモバイル サービスをコンテキスト メニューを開き、モバイル サービスのノードを右クリックして **Create Table**します。 テーブル名として「TodoItem」と入力します。

 - .NET バックエンド モバイル サービスの場合は、TodoItem テーブルは Visual Studio によって既にデフォルトのプロジェクト テンプレート内に作成されていますが、これを Azure に発行する必要があります。 発行するには、ソリューション エクスプローラーでモバイル サービス プロジェクトのコンテキスト メニューを開き、[**Publish Web (Web の発行)**] を選択します。 既定値を受け入れ、**[Publish (発行)]** を選択します。

## テーブルへの参照を取得する

クライアント オブジェクトは既にプロジェクトに追加されています。 このオブジェクトは、モバイル サービスの名前に "Client" を追加した名前になります。 次のコードは、TodoItem のデータを含むテーブルへの参照を取得します。この後でデータ テーブルの読み取りと更新の操作を実行する際に、TodoItem のデータを使用できます。

    var todoTable = yourMobileServiceClient.getTable('TodoItem');

## エントリを追加する

新しい項目をデータ テーブルに挿入します。 ID (文字列型の GUID) が新しい行のプライマリ キーとして自動的に作成されます。 Mobile Services のインフラストラクチャが ID 列を使用するため、ID 列の型を変更しないでください。

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

## テーブルを読み取る/照会する

次のコードは、テーブルのすべての項目を照会し、ローカル コレクションを更新して、その結果を UI 要素の listItems にバインドします。

        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });

where メソッドを使用してクエリを変更できます。 次の例では、完了した項目を除外します。

    todoTable.where(function () {
        return (this.complete === false && this.createdAt !== null);
    })
    .read()
    .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
    });

使用するクエリの例については、次を参照してください。 [クエリ オブジェクト](http://msdn.microsoft.com/library/azure/jj613353.aspx)します。

## エントリを更新する

データ テーブルの行を更新します。 この例では、*todoItem* は更新済みの項目です。また、*item* はモバイル サービスから返されるものと同じ項目です。 使用して、ローカルの todoItems リスト内の項目が更新は、モバイル サービスが応答すると、 [splice](http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx) メソッドです。 呼び出す、 **実行** メソッドで返された [Promise](https://msdn.microsoft.com/library/dn802826.aspx) オブジェクトを挿入されたオブジェクトのコピーを取得し、エラーがあれば処理します。

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

##### エントリを削除する

データ テーブルの行を削除します。 呼び出す、 [実行]() メソッドで返された [Promise](https://msdn.microsoft.com/library/dn802826.aspx) オブジェクトを挿入されたオブジェクトのコピーを取得し、エラーがあれば処理します。

    todoTable.del(todoItem).done(function (item) {
        todoItems.splice(todoItems.indexOf(item), 1);
    }

[モバイル サービスの詳細についてください。](http://azure.microsoft.com/documentation/services/mobile-services/)





