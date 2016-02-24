<properties
   pageTitle="SQL Data Warehouse でのデータベースの保護 | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse でのデータベース保護に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/15/2015"
   ms.author="sahajs"/>

# SQL Data Warehouse でのデータベース保護

この記事では、Azure SQL Data Warehouse データベースの保護に関する基本事項を説明します。 特にこの記事では、アクセスの制限、データの保護、およびデータベースでのアクティビティの監視を行うためのリソースの概要を説明します。

## 接続のセキュリティ

接続のセキュリティとは、ファイアウォール ルールと接続の暗号化を使用して、データベースへの接続を制限し、保護する方法のことです。

ファイアウォール ルールはサーバーとデータベースの両方で使用され、明示的にホワイト リストに登録されていない IP アドレスからの接続試行を拒否します。 新しいデータベースへの接続を試行するために、アプリケーションまたはクライアント コンピューターのパブリック IP アドレスを許可するには、まず Microsoft Azure クラシック ポータル、REST API、または PowerShell を使用して、サーバーレベルのファイアウォール ルールを作成する必要があります。 ベスト プラクティスとして、可能な限りサーバーのファイアウォールにより許可される IP アドレスの範囲を制限する必要があります。 詳細については、[Azure SQL Database ファイアウォール] を参照してください。


## 認証

認証とは、データベースへの接続時に ID を証明する方法のことです。 SQL Data Warehouse では現在、ユーザー名とパスワードを使用した SQL 認証がサポートされています。

データベースの論理サーバーを作成したときに、ユーザー名とパスワードによる "サーバー管理" ログインを指定したとします。 これらの資格情報を使用すると、データベース所有者、つまり "dbo" として、そのサーバーにある任意のデータベースを認証できます。

ただし、ベスト プラクティスとして、組織のユーザーは別のアカウントを使用して認証する必要があります。 この方法により、アプリケーションに付与されるアクセス許可を制限でき、アプリケーション コードが SQL インジェクション攻撃に対して脆弱な場合に、悪意のあるアクティビティのリスクを軽減できます。 サーバー ログインに基づいてデータベース ユーザーを作成するには:

最初に、サーバーのマスター データベースにサーバー管理ログインを使用して接続し、新しいサーバー ログインを作成します。 

```
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'strong_password';

```

次に、SQL Data Warhouse データベースにサーバー管理ログインを使用して接続し、作成したばかりのサーバー ログインに基づいてデータベース ユーザーを作成します。

```

-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;

```

SQL Database の認証の詳細については、[Azure SQL Database におけるデータベースとログインの管理] を参照してください。


## Authorization

承認とは、Azure SQL Data Warehouse データベース内で実行できる事柄を指し、これはアカウントのロール メンバーシップと権限によって制御されます。 ベスト プラクティスとして、必要最低限の特権をユーザーに付与することをお勧めします。 Azure SQL Data Warehouse により、T-SQL 内のロールで簡単に管理できるようになります。

```
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

接続しているサーバー管理者のアカウントは db_owner のメンバーであり、データベース内ですべての操作を実行する権限を持ちます。 スキーマのアップグレードやその他の管理操作をデプロイするために、このアカウントを保存します。 アクセス許可が限定された "ApplicationUser" アカウントを使用して、アプリケーションで必要な最小限の特権により、アプリケーションをデータベースに接続します。

ユーザーが Azure SQL Database で実行できる操作をさらに制限する方法がいくつかあります。

- [データベース ロール]db_datareader と db_datawriter 以外のより強力なアプリケーション ユーザー アカウント、またはより権限の小さな管理アカウントの作成に使用できます。
- 詳細な [アクセス許可] では、個々 の列、テーブル、ビュー、プロシージャ、およびデータベース内の他のオブジェクトで行うことができます操作を制御できます。
- [ストアド プロシージャ]は、データベースに対して実行できるアクションを制限できます。

Azure クラシック ポータルまたは Azure リソース マネージャー API を使用したデータベースと論理サーバーの管理は、ポータル ユーザー アカウントのロールの割り当てによって制御されます。 このトピックの詳細については、[Azure ポータルでロール ベースのアクセス制御] を参照してください。



## 暗号化

Azure SQL Data Warehouse では、"静止"状態にあるときに、データを暗号化することによって、データを保護できます。 またはデータベース ファイルおよび [透過的なデータ暗号化] を使用して、バックアップに保存します。 データベースを暗号化するには、サーバーの master データベースに接続して以下を実行します。


```

ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;

```

また、[Azure クラシック ポータル] でデータベースの設定から透過的なデータ暗号化を有効にすることができます。



## 監査

データベースの監査イベントと追跡イベントは、規制遵守の維持や、疑わしいアクティビティの特定に役立ちます。 SQL Data Warehouse の監査により、Azure Storage アカウントの監査ログにデータベースのイベントを記録できます。 また SQL Data Warehouse の監査を Microsoft Power BI と統合することにより、詳細なレポートと分析が容易になります。 詳細については、次を参照してください。 [SQL Database 監査の開始を取得] []。



## 次のステップ
他の開発のヒントについては、[開発の概要に関するページを参照してください。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md


<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx 
[Transparent Data Encryption]: http://go.microsoft.com/fwlink/?LinkId=526242
[Get started with SQL Database Auditing]: sql-database-auditing-get-started.md
[Azure Classic Portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure Portal]: http://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-configure.aspx

