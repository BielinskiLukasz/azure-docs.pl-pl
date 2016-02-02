<properties
    pageTitle="SQL Database チュートリアル: SQL データベースの作成 | Microsoft Azure"
    description="サンプル データと Microsoft のリレーショナル データベース管理システム (RDBMS) を使用して、Azure ポータルで最初の SQL Database を分単位で作成することができます。"
    keywords="sql database tutorial,create a sql database"  
    services="sql-database"
    documentationCenter=""
    authors="jeffgoll"
    manager="jeffreyg"
    editor="cgronlun"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/01/2015"
    ms.author="jeffreyg"/>


# SQL Database チュートリアル: サンプル データと Azure ポータルを使用して分単位で SQL Database を作成する

**1 つのデータベース**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


この SQL Database チュートリアルでは、Azure ポータルでサンプル データを使用して、わずか数分で最初の SQL Database を作成する方法について説明します。 学習内容は次のとおりです。

- 作成したデータベースをホストするサーバーを作成し、そのファイアウォール規則を設定します。
- 操作可能なデータが格納された AdventureWorks サンプルから SQL Database を作成します。

開始する前に、Azure アカウントとサブスクリプションが必要です。 持っていない場合は、サインアップするため、 [無料評価版](http://azure.microsoft.com/pricing/free-trial/)します。
> [AZURE.NOTE] この SQL Database チュートリアルでは、マイクロソフトのリレーショナル データベース管理サービスである Azure SQL Database を使用して、クラウドでデータベースを設定する方法について説明します。 別のオプションとして、Azure Virtual Machine で SQL Server を実行することもできます。 参照してください [理解する Azure SQL Database と Azure Vm における SQL Server](data-management-azure-sql-database-and-sql-server-iaas.md) 簡単に比較するかがわかります [SQL server 仮想マシンのプロビジョニング](virtual-machines-provision-sql-server.md) 仮想マシンを使用します。

## 手順 1. サインインして SQL データベースの設定を開始する

1. サインイン、 [Azure ポータル](http://portal.azure.com/)します。
2. **[新規]**、**[データ + ストレージ]**、**[SQL Database]** の順にクリックします。

    ![SQL Database チュートリアル: 新しい SQL Database を作成する](./media/sql-database-get-started/create-db.png)

    **[SQL Database]** 設定ブレードが表示されます。ここで、サーバーとデータベースの詳細を設定します。

    ![SQL データベースのデータベースおよびサーバー設定](./media/sql-database-get-started/get-started-dbandserversettings.png)

## 手順 2. サーバーの設定を選択する

Azure の SQL データベースは、データベース サーバーに存在します。 1 つのサーバーで複数のデータベースをホストできます。 データベースをセットアップするときに、そのデータベースをホストするサーバーを作成してセットアップすることもできます。また、以前に作成されたサーバーを使用することもできます。 ここでは、新しいサーバーをセットアップします。

1. データベースの**名前**を入力します (ここでは「**AdventureWorks**」を使用します)。 他のデータベース設定については、後でもう一度説明します。
2. **[サーバー]** で **[必要な設定の構成]**、**[新しいサーバーを作成する]** の順にクリックします。

    ![データベースに名前を付ける](./media/sql-database-get-started/name-and-newserver.png)

3. **[新しいサーバー]** ブレードで、**[サーバー名]** に、Azure 全体で一意の覚えやすい名前を入力します。 この名前は、後でデータベースに接続して操作するときに必要になります。
4. **[サーバー管理者ログイン]** に覚えやすいログインを入力します (ここでは「**AdventureAdmin**」を使用します)。 次に、**[パスワード]** にセキュリティで保護されたパスワードを入力し、**[パスワードの確認]** にもう一度パスワードを入力します。

    ![新しいデータベース サーバー設定](./media/sql-database-get-started/get-started-serversettings.png)

     最新の機能を使用するために、**[V12 サーバー (最新の更新プログラム) の作成]** は **[はい]** に設定したままにします。 **[場所]** では、サーバーが作成されるデータ センターのリージョンが示されます。
    >[AZURE.TIP] データベースを使用するアプリケーションに近い場所にデータベース サーバーを作成します。 場所を変更する場合は、**[場所]** をクリックし、別の名前を選択して、**[OK]** をクリックするだけです。

5. **[OK]** をクリックして、**[SQL Database]** ブレードに戻ります。

データベースとサーバーはまだ作成されていません。 これらは次の手順で作成されます。次の手順では、AdventureWorks サンプルからデータベースを作成して設定を確認します。

## 手順 3. SQL Database を設定して作成する

1. **[SQL Database]** ブレードで **[ソースの選択]**、**[サンプル]** の順にクリックします。

    ![サンプルから SQL Database を作成する](./media/sql-database-get-started/new-sample-db.png)

2. **[SQL Database]** ブレードに戻ると、**[サンプルの選択]** に **"AdventureWorks LT [V12]"** が表示されます。 **[作成]** をクリックし、サーバーとデータベースの作成を開始します。

    ![サンプル SQL Database を作成する](./media/sql-database-get-started/adworks_create.png)
    >[AZURE.NOTE] この簡易な方法では、**[価格レベル]**、**[照合順序]**、**[リソース グループ]** の設定は変更していません。 データベースの価格レベルの変更およびスケールアップとスケールダウンは、いつでもダウンタイムなしで実行できます。 参照してください [SQL Database の料金](http://azure.microsoft.com/pricing/details/sql-database/) と [SQL データベースの価格レベル](sql-database-service-tiers.md) の詳細。 データベースの照合順序は、ここで設定すると変更できません。 参照してください [照合順序と Unicode のサポート](https://msdn.microsoft.com/library/ms143726.aspx) の照合順序の詳細。 参照してください [Azure リソース マネージャーの概要](resource-group-overview.md) の詳細については Azure リソース グループ。

Azure のスタート画面に戻ると、データベースが作成されてオンラインになるまでの進行状況がタイルに表示されます。 また、**[すべて参照]**、**[SQL Database]** の順にクリックして、データベースがオンラインであることを確認することもできます。

ご利用ありがとうございます。 これで SQL Database はクラウドで稼働するようになりました。 あともう少しで終了ですが、 重要な手順が 1 つ残っています。 データベースに接続できるように、データベース サーバーで規則を作成する必要があります。

## 手順 4. ファイアウォールを構成する

データベースを操作できるように、クライアント コンピューターの IP アドレスからの接続を許可するファイアウォール規則をサーバーで設定する必要があります。 この規則は、接続を確立するのに役立つだけでなく、Azure の SQL サーバーに関するその他の詳細情報を入手できる領域を表示する優れた方法でもあります。

1. **[すべて参照]** をクリックし、下へスクロールして **[SQL Server]** をクリックし、**[SQL Server]** の一覧で、先ほど作成したサーバーの名前をクリックします。

    ![データベース サーバーの選択](./media/sql-database-get-started/browse_dbservers.png)

3. 右側に表示されるデータベース プロパティのブレードで、**[設定]** をクリックし、一覧から **[ファイアウォール]** をクリックします。

    ![ファイアウォール設定を開く](./media/sql-database-get-started/db_settings.png)

    **[ファイアウォールの設定]** に、現在の**クライアント IP アドレス**が表示されます。

    ![現在の IP アドレス](./media/sql-database-get-started/firewall_config_client_ip.png)

4. **[クライアント IP の追加]** をクリックすると、Azure でその IP アドレスの規則が作成されます。次に **[保存]** をクリックします。

    ![IP アドレスの追加](./media/sql-database-get-started/firewall_config_new_rule.png)
    >[AZURE.IMPORTANT] クライアント IP アドレスは不定期で変更される可能性があるため、新しいファイアウォール規則を作成するまでサーバーにアクセスできなくなる場合があります。 使用して IP アドレスをチェックする [Bing](http://www.bing.com/search?q=my%20ip%20address), 、して単一の IP アドレスまたは範囲の IP アドレスを追加します。 参照してください [ファイアウォール設定を構成する方法](sql-database-configure-firewall-settings.md) 詳細です。

## 次のステップ

ここでは、この SQL Database チュートリアルを完了し、いくつかのサンプル データを使用してデータベースを作成しました。次はお気に入りのツールを使用して調査します。

- TRANSACT-SQL と SQL Server Management Studio を使い慣れてなら、学習方法 [SSMS でデータベース接続とクエリ、SQL](sql-database-connect-query-ssms.md)します。

- Excel がわかっている場合は、学習方法 [Excel を使用して SQL データベースへの接続](sql-database-connect-excel.md)します。

- コーディングを開始する準備ができたら、表示 [c# を使用して SQL database の接続とクエリ](sql-database-connect-query.md) と [from .NET (c#) を使用して SQL データベース](sql-database-develop-dotnet-simple.md)します。 参照してください、 [クイック スタート コード サンプル SQL データベースに](sql-database-develop-quick-start-client-code-samples.md) Node.js、Python、Ruby、Java、PHP、および C++ のサンプルについては、どのように-だけでなく c# のです。

- 内部設置型 SQL Server データベースを Azure に移動するを参照して [Azure SQL Database にデータベースを移行する](sql-database-cloud-migrate.md) の詳細。







