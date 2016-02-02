既定では、モバイル アプリ バックエンドの API は匿名で呼び出すことができます。 次に、認証されたクライアントのみにアクセスを制限する必要があります。

+ **Node.js バックエンド (ポータル経由)** :

    モバイル アプリの **[設定]** で、**[簡単テーブル]** をクリックし、目的のテーブルを選択します。 **[アクセス許可の変更]** をクリックし、すべてのアクセス許可に対して **[認証済みアクセスのみ]** を選択し、**[保存]** をクリックします。

+ **.NET バックエンド (C#)**:

    サーバー プロジェクトで、**[Controllers]**、**[TodoItemController.cs]** の順に移動します。 追加、 `[Authorize]` 属性を **TodoItemController** クラスに、次のようにします。 アクセスを特定のメソッドのみに制限するには、この属性を、クラスではなく、そのメソッドのみに適用するだけです。 サーバー プロジェクトを発行します。

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Node.js バックエンド (Node.js コード経由)** :

    テーブルへのアクセスに対して認証を要求するには、Node.js サーバー スクリプトに次の行を追加します。

        table.access = 'authenticated';

    詳細についてを参照してください [方法: テーブルへのアクセスに対して認証を要求](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth)します。 自分のサイトからのクイック スタート コード プロジェクトをダウンロードする方法については、次を参照してください。 [方法: Node.js バックエンド クイック スタート コードを Git を使用してプロジェクトのダウンロード](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)します。






