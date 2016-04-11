<properties
    pageTitle="Visual Studio .NET モバイル サービス プロジェクトの概要 (接続済みサービス) | Microsoft Azure"
    description="Visual Studio .NET プロジェクトで Azure Mobile Services を使用する方法"
    services="mobile-services"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/17/2015"
    ms.author="tarcher"/>

# Mobile Services の使用 (.NET プロジェクト)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
> - [Getting Started (概要)](vs-mobile-services-dotnet-getting-started.md)
> - [変更内容](vs-mobile-services-dotnet-what-happened.md)

これらの例で使用されているコードを実行するために行う必要がある最初のステップは、接続しているモバイル サービスの種類によります。

- JavaScript バックエンド モバイル サービスの場合は、TodoItem と呼ばれるテーブルを作成します。  テーブルを作成するには、サーバー エクスプ ローラーで、Azure ノードでモバイル サービスをコンテキスト メニューを開き、モバイル サービスのノードを右クリックして **Create Table**します。 テーブル名として「TodoItem」と入力します。

- .NET バックエンド モバイル サービスの場合は、TodoItem テーブルは Visual Studio によって既にデフォルトのプロジェクト テンプレート内に作成されていますが、これを Azure に発行する必要があります。 発行するには、ソリューション エクスプ ローラーで、モバイル サービス プロジェクトのショートカット メニューを表示および選択 **Web の発行**します。 既定値をそのまま使用して、 **発行** ] ボタンをクリックします。

#####テーブルへの参照を取得する

次のコードによって、TodoItem のデータを含むテーブル (`todoTable`) への参照が作成されます。このデータは、後でデータ テーブルの読み取りと更新の操作を実行する際に使用できます。 TodoItem クラスに属性を設定して、モバイル サービスがクエリへの応答として送信する JSON を解釈できるようにする必要があります。

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

    IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

このコードは、テーブル設定されたアクセス許可がある場合は動作 **アプリケーション キーを持つユーザー**します。 モバイル サービスの安全を確保するためにアクセス許可を変更する場合、ユーザー認証サポートを追加する必要があります。 参照してください [認証を使ってみる](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md)します。

#####テーブル項目を追加する

新しい項目をデータ テーブルに挿入します。

    TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
    await todoTable.InsertAsync(todoItem);

#####テーブルの読み取りまたはクエリを実行する

次のコードは、テーブルのすべての項目をクエリします。 テーブルは、データの最初のページ (既定で 50 項目) しか返さないことに注意してください。 オプション パラメーターを使用して、必要なページ サイズを渡すことができます。

    List<TodoItem> items;
    try
    {
        // Query that returns all items.
        items = await todoTable.ToListAsync();
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }


#####テーブル項目を更新する

データ テーブルの行を更新します。 パラメーター項目は、更新対象の TodoItem オブジェクトです。

    await todoTable.UpdateAsync(item);

#####テーブル項目を削除する

データベースの行を削除します。 パラメーター項目は、削除対象の TodoItem オブジェクトです。

    await todoTable.DeleteAsync(item);


[モバイル サービスの詳細を確認する](http://azure.microsoft.com/documentation/services/mobile-services/)

