<properties
    pageTitle="チュートリアル: Entity Framework と行レベル セキュリティによるマルチテナント データベースを持つ Web アプリの構築"
    description="Entity Framework と行レベル セキュリティを使用して、マルチテナント SQL データベース バックエンドを持つ ASP.NET MVC 5 Web アプリを開発する方法について説明します。"
  metaKeywords="azure asp.net mvc entity framework multi tenant row level security rls sql database"
    services="app-service\web"
    documentationCenter=".net"
    manager="jeffreyg"
  authors="tmullaney"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/30/2015"
    ms.author="thmullan"/>


# チュートリアル: Entity Framework と行レベル セキュリティによるマルチテナント データベースを持つ Web アプリの構築

このチュートリアルではマルチ テナント web アプリケーションの構築方法、"[共有データベース、共有スキーマ](https://msdn.microsoft.com/library/aa479086.aspx)"Entity Framework を使用して、テナント モデルおよび [行レベルのセキュリティ](https://msdn.microsoft.com/library/dn765131.aspx)します。 このモデルでは、1 つのデータベースに多くのテナントのデータが含まれていて、各テーブルの各行は "テナント ID" に関連付けられています。 Azure SQL Database の新しい機能である行レベル セキュリティ (RLS) は、各テナントが他のテナントのデータにアクセスできないようにするために使用されます。 そうするには、アプリケーションに 1 つの小さな変更を行うだけで済みます。 データベース内のテナント アクセス ロジックを一元化することで、RLS はアプリケーション コードを簡略化し、テナント間での偶発的なデータ漏洩のリスクを軽減します。

単純な Contact Manager アプリケーションから始めましょう [認証および SQL DB と ASP.NET の MVP アプリの作成し、Azure App Service にデプロイ](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)します。 この状態では、アプリケーションはすべてのユーザー (テナント) にすべての連絡先の表示を許可します。

![Contact Manager application before enabling RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

これを少し変更するだけで、マルチテナントのサポートを追加し、ユーザーが自分に属している連絡先だけを表示できるようにすることができます。

## 手順 1: アプリケーションにインターセプター クラスを追加し、SESSION_CONTEXT を設定する

アプリケーションに 1 つの変更を行う必要があります。 アプリケーションのすべてのユーザーが同じ接続文字列 (つまり、同じ SQL ログイン) を使用してデータベースに接続するため、現在、RLS ポリシーには、どのユーザーをフィルター処理する必要があるかを判断する方法がありません。 この方法は、効率的な接続プーリングができるため、Web アプリケーションでは非常に一般的です。しかし、データベース内の現在のアプリケーション ユーザーを識別するための別の方法が必要になります。 アプリケーションの現在のユーザー Id のキー/値ペアのセットに解決するには、 [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806) クエリを実行する前にします。 SESSION_CONTEXT はセッション スコープのキー/値ストアであり、これに格納されている UserId を RLS ポリシーが使用して、現在のユーザーを識別します。 *注: SESSION_CONTEXT は、現在、Azure SQL Database のプレビュー機能です。*

追加、 [インターセプター](https://msdn.microsoft.com/data/dn469464.aspx), 、新機能で Entity Framework (EF) 6、EF は、各クエリを実行する前に、T-SQL ステートメントを付加することによって、SESSION_CONTEXT で現在のユーザー Id を自動的に設定します。

1.  Visual Studio で ContactManager プロジェクトを開きます。
2.  ソリューション エクスプローラーで Models フォルダーを右クリックし、[追加]、[クラス] の順に選択します。
3.  新しいクラスの名前を "SessionContextInterceptor.cs" にして、[追加] をクリックします。
4.  SessionContextInterceptor.cs の内容を次のコードに置き換えます。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.SqlClient;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbCommandInterceptor
    {
        private void SetSessionContext(DbCommand command)
        {
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    // Set SESSION_CONTEXT to current UserId before executing queries
                    var sql = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId;";

                    command.CommandText = sql + command.CommandText;
                    command.Parameters.Insert(0, new SqlParameter("@UserId", userId));
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }
        public void NonQueryExecuting(DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
        {
            this.SetSessionContext(command);
        }
        public void NonQueryExecuted(DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
        {

        }
        public void ReaderExecuting(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
        {
            this.SetSessionContext(command);
        }
        public void ReaderExecuted(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
        {

        }
        public void ScalarExecuting(DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
        {
            this.SetSessionContext(command);
        }
        public void ScalarExecuted(DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
        {

        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

必要なアプリケーションの変更は、これだけです。 アプリケーションをビルドして発行します。

## 手順 2: データベース スキーマに UserId 列を追加する

次に、各行をユーザー (テナント) に関連付けるために、Contacts テーブルに UserId 列を追加する必要があります。 データベースで直接スキーマを変更するため、このフィールドを EF データ モデルに含める必要はありません。

SQL Server Management Studio または Visual Studio を使用してデータベースに直接接続し、以下の T-SQL を実行します。

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

これによって、Contacts テーブルに UserId 列が追加されます。 AspNetUsers テーブルに格納されている UserIds と一致するように、nvarchar(128) データ型が用されます。また、DEFAULT 制約が作成され、新たに挿入された行の UserId に、現在 SESSION_CONTEXT に格納されている UserId が自動的に設定されるようにします。

これで、テーブルは次のようになります。

![SSMS Contacts table](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

新しい連絡先が作成されると、自動的に正しい UserId が割り当てられます。 しかし、ここではデモのために、既存のいくつかの連絡先を既存のユーザーに割り当てておきましょう。

アプリケーションで既にいくつかのユーザーを作成してある場合 (たとえば、ローカル アカウント、Google アカウント、または Facebook アカウントを使用して)、それらは AspNetUsers テーブル内にあります。 次のスクリーンショットでは、ユーザーは 1 人だけです。

![SSMS AspNetUsers table](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

、User1@contoso.com の Id をコピーし、次の T-SQL ステートメントを貼り付けます。 ステートメントを実行して、この UserId に 3 つの連絡先を関連付けます。

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## 手順 3: データベースに行レベル セキュリティ ポリシーを作成する

最後の手順は、SESSION_CONTEXT の UserId を使用して、クエリから返される結果を自動的にフィルター処理するセキュリティ ポリシーを作成することです。

まだデータベースに接続している場合は、次の T-SQL を実行します。

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS accessResult
    WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
    ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
    ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go
```

このコードでは、3 つの処理が行われます。 まず、RLS オブジェクトへのアクセスを一元化して制限するためのベスト プラクティスとして、新しいスキーマを作成します。 次に、行の UserId が SESSION_CONTEXT の UserId と一致した場合に '1' を返す述語関数を作成します。 最後に、Contacts テーブルに対するフィルター述語とブロック述語の両方としてこの関数を追加するセキュリティ ポリシーを作成します。 フィルター述語は、現在のユーザーに属する行だけがクエリから返されるようにします。ブロック述語は、アプリケーションが誤って正しくないユーザーのために行を挿入しないようにするための安全装置として機能します。 *注: ブロック述語は、現在、Azure SQL Database のプレビュー機能です。*

ここで、アプリケーションを実行し、user1@contoso.com としてサインインします。 このユーザーには、前にこの UserId に割り当てた連絡先だけが表示されるようになりました。

![Contact Manager application before enabling RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

このことをさらに検証するために、新しいユーザーを登録してみます。 新しいユーザーには連絡先が割り当てられていないため、連絡先が表示されません。 ユーザーが新しい連絡先を作成すると、それがユーザーに割り当てられ、そのユーザーだけに表示されるようになります。

## 次のステップ

これで完了です。 単純な Contact Manager Web アプリが、各ユーザーが独自の連絡先リストを持つマルチテナントのアプリに変わりました。 行レベル セキュリティを使用することで、アプリケーション コードにテナント アクセス ロジックを強制するような複雑な処理を避けることができました。 この透過性により、アプリケーションは実際のビジネスの問題に集中することができ、アプリケーションのコードベースの拡大に伴ってデータを誤って漏えいするリスクも軽減できます。

このチュートリアルでは、RLS でできることのほんの一部に触れただけです。 たとえば、より洗練されたきめ細かいアクセス ロジックを利用したり、SESSION_CONTEXT に現在の UserId 以外のものを格納したりすることもできます。 ことも [エラスティック データベース ツールのクライアント ライブラリで RLS を統合](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md) スケール アウト データ層のマルチ テナントのシャードをサポートするためにします。

これらの可能性の他にも、RLS をさらに良いものにするための努力が続けられています。 ご質問、ご意見、ご要望などありましたら、お知らせください。 フィードバックをお待ちしています。





