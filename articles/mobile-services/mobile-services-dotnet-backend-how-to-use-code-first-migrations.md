<properties
    pageTitle="データ モデルの変更を .NET バックエンド モバイル サービスに加える方法"
    description="このトピックでは、データ モデル初期化子と、データ モデルの変更を .NET バックエンド モバイル サービスに加える方法について説明します。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    writer="glenga"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="NA"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="glenga"/>


# データ モデルの変更を .NET バックエンド モバイル サービスに加える方法

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


このトピックでは Entity Framework の Code First Migrations を使用して、既存のデータを失うことなく、既存の Azure SQL Database にデータ モデルの変更を加える方法を説明します。 この手順では、.NET バックエンド プロジェクトを既に Azure に発行したこと、データベース内に既存のデータがあること、およびリモートとローカルのデータ モデルが同期状態にあることを想定します。 また、Azure Mobile Services によって実装され、開発時に使用するデフォルトの Code First 初期化子についても説明します。 こうした初期化子により、既存データを維持する必要がない場合に Code First Migrations を使用せずに簡単にスキーマを変更できます。
>[AZURE.NOTE]SQL Database でテーブルのプレフィックスとして使用されるスキーマ名は、web.config ファイルの MS_MobileServiceName アプリ設定で定義されています。 ポータルからスターター プロジェクトをダウンロードするとき、この値はモバイル サービス名に既に設定されています。 スキーマ名がモバイル サービスと一致すると、複数のモバイル サービスで同じデータベース インスタンスを安全に共有できます。

.NET バックエンド プロジェクトでは自動移行がサポートされていないことに注意してください。

## データ モデルの更新

.NET バックエンド モバイル サービスに機能を追加するときは、新しいコントローラーを追加して API で新しいエンドポイントを公開します。カスタム コントローラーまたはテーブル コントローラーとして、新しい API を作成します。A [TableController<TEntity>] [EntityData] から継承するデータ型を公開します。データベースに永続化されるデータを有効にするには、このデータ型もに追加されて、新しいデータ モデル [DbSet<T>] [DbContext] にします。Entity Framework の Code First の詳細については、次を参照してください。 [Code First によるモデルを作成する](https://msdn.microsoft.com/data/ee712907#codefirst)します。

Visual Studio を使用すれば、クライアント アプリケーションに新しいデータ型を公開するためのテーブル コント ローラーを容易に作成できます。 詳細については、次を参照してください。 [コント ローラーを使用して、モバイル サービスのデータにアクセスする方法](https://msdn.microsoft.com/library/windows/apps/xaml/dn614132.aspx)します。

## データ モデル初期化子

Mobile Services では、.NET バックエンド モバイル サービス プロジェクトにおいて 2 つのデータ モデル初期化子ベースのクラスを用意しています。 両方の初期化子は、削除し、Entity Framework では、[DbContext] でデータ モデルの変更が検出した場合は、データベース内のテーブルを再作成します。 初期化子は、モバイル サービスがローカル コンピューターで実行している場合と、Azure でホストされている場合の両方で作動するように作られています。
>[AZURE.NOTE].NET バックエンド モバイル サービスを発行すると、データ アクセス操作が実行されるまで初期化子は実行されません。 つまり、新たに発行されたサービスでは、クエリなどのデータ アクセス操作がクライアントによってリクエストされるまで、ストレージに使用されるデータ テーブルは作成されません。
>
>データ アクセス操作は、スタート ページの **[試してみる]** からアクセスできる組み込みの API ヘルプ機能を使用して実行することもできます。 モバイル サービスをテストするページ、API の使用に関する詳細については、ローカルでモバイル サービス プロジェクトをテストする」を参照してください。 [既存のアプリケーションへの Mobile Services の追加](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md#test-the-service-locally)します。

初期化子は両方とも、モバイル サービスで使用されるスキーマのすべてのテーブル、ビュー、関数、手順をデータベースから削除します。

+ **ClearDatabaseSchemaIfModelChanges** <br/> Code First データ モデルの変更を検出する場合にのみ、スキーマ オブジェクトは削除されます。 [Azure クラシック ポータル] からダウンロードされた .NET バックエンド プロジェクト デフォルトの初期化子は、この基本クラスから継承します。

+ **ClearDatabaseSchemaAlways**: <br/> スキーマ オブジェクトがデータ モデルがアクセスされるたびに削除されます。 このベース クラスはデータ モデルを変更せずにデータベースをリセットするために使用します。

ローカル コンピューターで実行する場合は、その他の Code First データ モデル初期化子を使用できます。 ただし、データベースの削除を試みる初期化子は Azure で失敗します。これはユーザーはデータベースの削除が許可されていないためです。

モバイル サービスをローカル開発している間は、初期化子を継続して使用でき、.NET バックエンドに関するチュートリアルでは、初期化子を使用していることを想定します。 ただし、データ モデルに変更を加え、データベース内で既存のデータを保持しようとする場合は、Code First Migrations を使用する必要があります。
>[AZURE.IMPORTANT]Azure ライブ サービスに対してモバイル サービス プロジェクトを開発し、テストをする場合は、必ずテスト専用のモバイル サービス インスタンスを使用する必要があります。 現時点で運用されているモバイル サービスや、クライアント アプリケーションによって使用されているモバイル サービスに対して、開発またはテストを決して実施しないでください。

ダウンロードしたクイックスタート プロジェクトでは、Code First 初期化子は WebApiConfig.cs ファイルで定義されます。 **Seed** メソッドをオーバーライドしてデータの最初の行を新しいテーブルに追加します。 データのシードの例については、[移行データのシード処理] を参照してください。

## <a name="migrations"></a>Code First Migrations を有効にします。

Code First Migrations は、実行されたときにスナップショットの手法を使用して、データベースに対してスキーマの変更を加えるコードを生成します。 Migrations を使用する場合は、データ モデルに対して増分の変更を加え、データベース内で既存のデータを保持することができます。
>[AZURE.NOTE].NET バックエンド モバイル サービス プロジェクトを既に Azure に発行し、SQL Database のテーブル スキーマが、プロジェクトの現在のデータ モデルと一致していない場合は、初期化子を使用してテーブルを削除するか、Code First Migrations を使用して発行を試みる前にスキーマとデータ モデルを手動で同期させる必要があります。

次の手順では Migrations を有効にし、プロジェクト、ローカル データベース、Azure のそれぞれに対してデータ モデルの変更を適用します。

1. Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. **[ツール]** メニューで **[NuGet パッケージ マネージャー]** を展開して、**[パッケージ マネージャー コンソール]** をクリックします。

    この結果、パッケージ マネージャー コンソールが表示され、このコンソールを使用して Code First Migrations を管理することができます。

3. パッケージ マネージャー コンソールで、次のコマンドを実行します。

        PM> Enable-Migrations

    この結果、プロジェクトに対して Code First Migrations が有効になります。

4. コンソールで、次のコマンドを実行します。

        PM> Add-Migration Initial

    この結果、*Initial* という名前の新しい移行が作成されます。 移行用コードは、Migrations プロジェクト フォルダーに格納されます。

5. [App_Start] フォルダーを展開し、WebApiConfig.cs プロジェクト ファイルを開き、次の **using** ステートメントを追加します。

        using System.Data.Entity.Migrations;
        using todolistService.Migrations;

    上記のコードで置き換える必要があります、 _todolistService_ 、ダウンロードしたクイック スタート プロジェクトをプロジェクトの名前空間を持つ文字列 <em>mobile & #95; service & #95; 名前</em>サービスです。

6. この同じコード ファイル内で、**Database.SetInitializer** メソッドの呼び出しをコメント アウトし、その後に次のコードを追加します。

        var migrator = new DbMigrator(new Configuration());
        migrator.Update();

    この結果、データベースの削除と再作成を実行する Code First database の既定の初期化子が無効になり、最新の移行を適用する明示的な要求に置換されます。 この時点で、データに対する移行がまだ作成されていない場合は、データにアクセスしたときに、InvalidOperationException 内でデータ モデルに対するすべての変更が発生する結果になります。 この後、サービスで Code First Migrations を使用して、データ モデルに対する変更をデータベースに移行する必要があります。

7.  F5 キーを押して、ローカル コンピューターでモバイル サービス プロジェクトを開始します。

    この時点で、データベースは、データ モデルと同期した状態にあります。 登録されたデータを指定した場合は、**[Try it out]**、**[GET tables/todoitem]**、**[Try this out]**、**[Send]** の順にクリックして、このことを確認できます。 詳細については、[時のデータの移行] を参照してください。

8.   ここで、TodoItem の種類に対して新しい UserId プロパティを追加するなど、データ モデルに変更を加え、プロジェクトをリビルドし、パッケージ マネージャーで次のコマンドを実行します。

        PM > Add-migration NewUserId

    This creates a new migration named *NewUserId*. A new code file, which implements this change, is added in the Migrations folder

9.  F5 キーを押して、ローカル コンピューターでモバイル サービス プロジェクトを再起動します。

    この移行がデータベースに適用され、データベースはもう一度データ モデルに同期されます。 登録されたデータを指定した場合は、**[Try it out]**、**[GET tables/todoitem]**、**[Try this out]**、**[Send]** の順にクリックして、このことを確認できます。 詳細については、[時のデータの移行] を参照してください。

10. Azure に対してモバイル サービスを再発行してから、クライアント アプリケーションを実行してデータにアクセスし、データが読み込まれて何もエラーが発生していないことを確認します。

13. (省略可能)[Azure クラシック ポータル]、[モバイル サービス] で、をクリックして **構成** > **SQL Database**します。 この結果、モバイル サービスのデータベースに対応する SQL Database ページに移動します。

14. (省略可能) **[管理]** をクリックし、SQL Database サーバーにログインしてから、**[設計]** をクリックして、Azure にスキーマの変更が加えられたことを確認します。


## 初期化子のない Code First Migrations の使用

Code First Migrations を .NET バックエンド プロジェクトで使用する前に、データ モデル初期化子を実行する必要があります。 初期化子を使用しないと、移行の適用時にエラーが発生する可能性があります。 定義済みのデータ モデル初期化子を使用しないことを選択した場合は、Migrations\Configuration.cs ファイルで EntityTableSqlGenerator を SqlGenerator として使用するように移行が構成されていることを確認します。次の例を参照してください。

    public Configuration()
    {
        AutomaticMigrationsEnabled = false;
        SetSqlGenerator("System.Data.SqlClient", new EntityTableSqlGenerator());
    }

## <a name="seeding"></a>移行データのシード処理

移行を実行するときに、Migrations が登録データをデータベースに追加するように設定することもできます。 **Configuration** クラスには、登録を行うための **Seed** メソッドがあり、このメソッドを使用してデータの挿入または更新をオーバーライドすることができます。 Migrations を有効にするときに、Configuration.cs コード ファイルが Migrations フォルダーに追加されます。 これらの例は、[シード] メソッドに登録されたデータをオーバーライドする方法を示して、 **TodoItems** テーブルです。 [シード] メソッドは、最新バージョンに移行した後に呼び出されます。

### 新しいテーブルの登録

次のコードでは、新しいデータ行を使用して **TodoItems** テーブルへの登録を実行します。

        List<TodoItem> todoItems = new List<TodoItem>
        {
            new TodoItem { Id = "1", Text = "First item", Complete = false },
            new TodoItem { Id = "2", Text = "Second item", Complete = false },
        };
    
        foreach (TodoItem todoItem in todoItems)
        {
            context.Set<TodoItem>().Add(todoItem);
        }
        base.Seed(context);

### テーブル内での新しい列の登録

次のコードでは、UserId 列のみを登録します。

        context.TodoItems.AddOrUpdate(
            t => t.UserId,
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 2 }
            );
        base.Seed(context);

このコードは、登録されたデータを新しい UserId 列に追加するには、[AddOrUpdate] ヘルパー拡張メソッドを呼び出します。 [AddOrUpdate] を使用すると、重複する行は作成されません。





[migrations]: #migrations 
[seeding data in migrations]: #seeding 
[0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png 
[1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png 
[2]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png 
[dropcreatedatabaseifmodelchanges]: http://msdn.microsoft.com/library/gg679604(v=vs.113).aspx 
[seed]: http://msdn.microsoft.com/library/hh829453(v=vs.113).aspx 
[azure classic portal]: https://manage.windowsazure.com/ 
[dbcontext]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=vs.113).aspx 
[addorupdate]: http://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx 
[tablecontroller<tentity>]: https://msdn.microsoft.com/library/azure/dn643359.aspx 
[entitydata]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.entitydata.aspx 
[dbset<t>]: https://msdn.microsoft.com/library/azure/gg696460.aspx 

