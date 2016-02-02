<properties
   pageTitle="Azure Active Directory 認証を使用して SQL Database に接続する | Microsoft Azure"
   description="Azure Active Directory 認証を使用して SQL Database に接続する方法について説明します。"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/08/2015"
   ms.author="rick.byham@microsoft.com"/>


# Azure Active Directory 認証を使用して SQL Database に接続する

Azure Active Directory 認証は、Azure Active Directory (Azure AD) の ID を使用して Microsoft Azure SQL Database に接続するメカニズムです。 Azure Active Directory 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。 ID の一元管理では、1 か所で SQL Database ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。 次のような利点があります。

- SQL Server 認証の代替方法が用意されています。
- データベース サーバー全体でユーザー ID が急増するのを防ぎます。
- 1 か所でのパスワードのローテーションを許可します。
- 顧客は外部の (AAD) グループを使用してデータベースのアクセス許可を管理できます。
- 統合 Windows 認証や、Azure Active Directory でサポートされる他の認証形式を有効にすることで、パスワードが保存されないようにすることができます。
- Azure Active Directory 認証では、包含データベース ユーザーを使用して、データベース レベルで ID を認証します。

> [AZURE.IMPORTANT] Azure Active Directory 認証プレビュー機能でありプレビューの使用条件のほか、必要に応じてライセンス契約 (例: Enterprise Agreement、Microsoft Azure 契約、またはマイクロソフト オンライン サブスクリプション契約) で [Microsoft Azure プレビューの使用の補足条項](http://azure.microsoft.com/support/legal/preview-supplemental-terms/)します。

構成の手順には、Azure Active Directory 認証を構成して使用する次の手順が含まれます。

1. Azure Active Directory を作成して設定する
2. データベースが Azure SQL Database V12 にあることを確認する
3. 省略可能: Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更する
4. Azure SQL Server 用に Azure Active Directory 管理者を作成する
5. クライアント コンピューターを構成する
6. Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成する
7. Azure AD の ID を使用してデータベースに接続する


## 信頼のアーキテクチャ

次の図では、Azure SQL Database で Azure AD 認証を使用する場合のソリューション アーキテクチャの概要を示しています。 矢印は通信経路を示します。

![aad auth diagram][1]

次の図では、フェデレーション、信頼、ホスティングの関係を示します。これらの関係により、クライアントは、Azure AD によって認証された、データベースが信頼するトークンを送信することで、データベースに接続できます。 重要なのは、Azure AD 認証を使用してデータベースにアクセスするには、ホストしているサブスクリプションを Azure Active Directory に関連付ける必要があることを理解することです。

![サブスクリプションの関係性][2]

## 管理者の構造

Azure AD 認証を使用すると、SQL Database サーバーの管理者アカウントは、元の SQL Server 管理者と Azure AD 管理者の 2 つになります。 ユーザー データベースに最初の Azure AD 包含データベース ユーザーを作成できるのは、Azure AD アカウントに基づく管理者のみです。 Azure AD の管理者ログインには、Azure AD ユーザーまたは Azure AD グループを使用できます。 管理者がグループ アカウントの場合は、SQL Server インスタンスに複数の Azure AD 管理者を有効にすることで、すべてのグループ メンバーがそのアカウントを使用できます。 グループ アカウントを管理者として使用すると、SQL Database でユーザーまたはアクセス許可を変更することなく Azure AD でグループ メンバーを一元的に追加および削除できるため、より管理しやすくなります。 いつでも構成できる Azure AD 管理者 (ユーザーまたはグループ) は 1 つだけです。

![admin structure][3]

## アクセス許可

新しいユーザーを作成するには、データベースにおける **ALTER ANY USER** アクセス許可が必要です。 **ALTER ANY USER** アクセス許可は、任意のデータベース ユーザーに付与できます。 また、**ALTER ANY USER** アクセス許可は、サーバーの管理者アカウント、そのデータベースの **CONTROL ON DATABASE** または **ALTER ON DATABASE** アクセス許可を持つデータベース ユーザー、**db_owner** データベース ロールのメンバーも保持しています。

Azure SQL Database に包含データベース ユーザーを作成するには、Azure AD の ID を使用してデータベースに接続する必要があります。 最初の包含データベース ユーザーを作成するには、(データベースの所有者である) Azure AD 管理者を使用してデータベースに接続する必要があります。 これについては、以下の手順 4. ～ 5. で説明します。

## Azure AD の機能と制限事項

Azure Active Directory の次のメンバーは、Azure SQL サーバーをプロビジョニングできます。
- ネイティブ メンバー: 顧客のドメインまたは管理されたドメインでは、Azure AD で作成したメンバー。 詳細については、次を参照してください。 [を Azure AD ドメイン名を追加](active-directory-add-domain.md)します。
- フェデレーション ドメインのメンバー: フェデレーション ドメインを使用して Azure AD で作成されたメンバーです。 詳細については、次を参照してください。 [Microsoft Azure では、Windows Server Active Directory とのフェデレーションをサポート](http://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)します。
- ネイティブまたはフェデレーション ドメインのメンバーであるその他の Azure Active ディレクトリからインポートされたメンバー。
- Active Directory グループがセキュリティ グループとして作成します。

Microsoft アカウント (outlook.com、hotmail.com、live.com など) またはその他のゲスト アカウント (gmail.com、yahoo.com など) はサポートされていません。 ログインする場合は、 [https://login.live.com](https://login.live.com) Azure SQL Database 用 Azure AD 認証のサポートされていない Microsoft アカウントを使用しているアカウントとパスワードを使用します。

### 追加の考慮事項

- さらに管理しやすくするには、管理者として専用の Azure Active Directory グループをプロビジョニングすることをお勧めします。
- Azure SQL Server 用にいつでも構成できる Azure AD 管理者 (ユーザーまたはグループ) は 1 つだけです。
- Azure Active Directory アカウントを使用して最初に Azure SQL Server に接続できるのは、Azure Active Directory 管理者のみです。 Active Directory 管理者は、それ以降の Azure Active Directory のデータベース ユーザーを構成できます。
- 接続のタイムアウトを 30 秒に設定することをお勧めします。
- BI や Excel など、一部のツールはサポートされていません。
- Azure Active Directory 認証では、**.NET Framework Data Provider for SqlServer** (.NET Framework 4.6 以降のバージョン) のみがサポートされています。 そのため、Management Studio (SQL Server 2016 で使用可能) とデータ層アプリケーション (DAC と .bacpac) は接続できますが、**sqlcmd.exe** は接続できません。**sqlcmd** では ODBC プロバイダーが使用されているためです。
- 2 要素認証や他の形式の対話型認証はサポートされていません。


## 1.Azure Active Directory を作成して設定する

Azure Active Directory を作成し、ユーザーとグループを設定します。 次のトピックがあります。

- 初期ドメインとして Azure AD の管理対象ドメインを作成します。
- オンプレミスの Active Directory ドメイン サービスを Azure Active Directory とフェデレーションします。

詳細については、次を参照してください。 [を Azure AD ドメイン名を追加](active-directory-add-domain.md), 、[Microsoft Azure では、Windows Server Active Directory とのフェデレーションをサポート](http://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), 、[Azure AD ディレクトリの管理](https://msdn.microsoft.com/library/azure/hh967611.aspx), 、および [管理 Windows PowerShell を使用して Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)します。

## 2.データベースが Azure SQL Database V12 にあることを確認する

Azure Active Directory 認証は、最新の SQL Database V12 でサポートされています。 SQL Database V12 に関するおよびお住まいの地域で利用可能なことはかどうかについて紹介については、次を参照してください。 [最新の SQL Database Update V12 の新機能](sql-database-v12-whats-new.md)します。

既存のデータベースがある場合は、(たとえば、SQL Server Management Studio を使用して) データベースに接続することで、SQL Database V12 でホストされていることを確認して実行する `SELECT @@VERSION;` SQL Database V12 でデータベースの想定される出力が少なくとも **Microsoft SQL Azure (RTM) - 12.0**します。

SQL Database V12 ので、データベースがホストされていない場合は、次を参照してください。 [を計画し、SQL Database V12 にアップグレードする準備](sql-database-v12-plan-prepare-upgrade.md), 、し、データベースを SQL Database V12 に移行する従来の Azure ポータルにアクセスします。

次に示されている手順に従って、SQL Database V12 の新しいデータベースを作成する代わりに、 [最初の Azure SQL database を作成する](sql-database-get-started.md)します。 **ヒント**: 新しいデータベースのサブスクリプションを選択する前に、次の手順をお読みください。

## 3.省略可能: Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更する

データベースを組織の Azure AD ディレクトリに関連付けるには、そのディレクトリを、データベースをホストする Azure サブスクリプションの信頼できるディレクトリにします。 詳細については、次を参照してください。 [方法 Azure サブスクリプションを Azure AD に関連付ける](https://msdn.microsoft.com/library/azure/dn629581.aspx)します。

**追加情報:** すべての Azure サブスクリプションには、Azure AD インスタンスとの間に信頼関係があります。 つまり、ディレクトリを信頼してユーザー、サービス、デバイスを認証します。 複数のサブスクリプションが同じディレクトリを信頼できますが、1 つのサブスクリプションは 1 つのディレクトリだけを信頼します。 ディレクトリが信頼して、サブスクリプションの下を参照してください、 **設定** タブで [https://manage.windowsazure.com/](https://manage.windowsazure.com/)します。 このサブスクリプションとディレクトリの間の信頼関係は、サブスクリプションと Azure 内の他のすべてのリソース (Web サイト、データベースなど) の間の関係と異なります。後者は、サブスクリプションの子リソースにより近いものです。 サブスクリプションの有効期限が切れた場合、サブスクリプションに関連付けられたこれらの他のリソースへのアクセスも停止します。 一方、ディレクトリは Azure 内に残っており、別のサブスクリプションをそのディレクトリと関連付けて、ディレクトリ ユーザーの管理を継続できます。 リソースの詳細については、次を参照してください。 [で、Azure リソースへのアクセスを理解する](https://msdn.microsoft.com/library/azure/dn584083.aspx)します。

次の手順では、特定のサブスクリプションの関連付けられたディレクトリを変更する方法について詳しく説明します。

1. 接続、 [Azure Classic Portal](https://manage.windowsazure.com/) 管理者は Azure サブスクリプションを使用しています。
2. 左側のバナーで **[設定]** を選択します。
3. [設定] 画面にサブスクリプションが表示されます。 目的のサブスクリプションが表示されない場合は、上部にある **[サブスクリプション]** をクリックし、**[ディレクトリでフィルター]** ボックスの一覧で、サブスクリプションを含むディレクトリを選択して、**[適用]** をクリックします。

    ![サブスクリプションを選択する][4]
4. **設定** 領域では、サブスクリプションをクリックし、クリックして  **ディレクトリの編集** ページの下部にあります。

    ![ad-settings-portal][5]
5. **[ディレクトリの編集]** ボックスで、SQL Server に関連付けられている Azure Active Directory を選択し、矢印をクリックして次に進みます。

    ![edit-directory-select][6]
6. **[ディレクトリ マッピングの確認]** ダイアログ ボックスで、"**共同管理者は全員削除されます。**" と表示されていることを確認します。

    ![edit-directory-confirm][7]
7. チェック マークをクリックしてポータルを再度読み込みます。

> [AZURE.NOTE] ディレクトリを変更すると、共同管理者、Azure AD のユーザーとグループ、ディレクトリでサポートされているリソース ユーザーすべてへのアクセスが削除され、それらはこのサブスクリプションまたはそのリソースにアクセスできなくなります。 サービス管理者の場合のみ、新しいディレクトリに基づくプリンシパルのアクセスを構成できます。 この変更は、すべてのリソースに反映されるまで相当な時間がかかる場合があります。 ディレクトリを変更すると、SQL Database の Azure AD 管理者も変更されるため、既存のすべての Azure AD ユーザーは SQL Database にアクセスできなくなります。 Azure AD の管理者を再設定し (後で説明)、新しい Azure AD ユーザーを作成する必要があります。

## 4.Azure SQL Server 用に Azure Active Directory 管理者を作成する

各 Azure SQL Server の開始には、Azure SQL Server 全体の管理者である 1 つのサーバー管理者アカウントを使用します。 Azure AD アカウントであるもう 1 つのサーバー管理者を作成する必要があります。 このプリンシパルは、master データベースの包含データベース ユーザーとして作成されます。 管理者の場合、サーバー管理者アカウントは、すべてのユーザー データベースの **db_owner** ロールのメンバーであり、**dbo** ユーザーとして各ユーザー データベースに入ります。 サーバーの管理者アカウントの詳細については、次を参照してください。 [Azure SQL Database におけるデータベースの管理とログイン](sql-database-manage-logins.md) と **ログインとユーザー** の [Azure SQL Database のセキュリティ ガイドラインと制限事項](sql-database-security-guidelines.md)です。
> [AZURE.NOTE] Azure AD アカウント (Azure SQL Server の管理者アカウントを含む) に基づいていないユーザーは、Azure AD に基づくユーザーを作成できません。これは、Azure AD で提示されるデータベース ユーザーを検証するアクセス許可がないためです。

### Azure クラシック ポータルを使用して、Azure SQL Server の Azure Active Directory 管理者をプロビジョニングします。

1. [Azure Classic Portal](https://portal.azure.com/), 、右上隅にあるボックスの考えられるアクティブなディレクトリの一覧への接続をクリックします。 既定の Azure AD として適切な Active Directory を選択します。 この手順では、Active Directory とサブスクリプションの関連付けを Azure SQL Database とリンクすることで、Azure AD と SQL Server の両方に同じサブスクリプションが使用されるようにします。

    ![choose-ad][8]
2. 左側のバナーで **[SQL Server]** を選択し、使用する **SQL Server** を選択した後、**[SQL Server]** ブレードの上部にある **[設定]** をクリックします。

    ![ad settings][9]
3. **[設定]** ブレードで **[Active Directory 管理者 (プレビュー)]** をクリックして、プレビューの句をそのまま使用します。
4. **[Active Directory 管理者 (プレビュー)]** ブレードで、クリックしてプレビューの条件を確認したら、**[OK]** をクリックしてその条件に同意します。
5. **[Active Directory 管理者 (プレビュー)]** ブレードで **[Active Directory 管理者]** をクリックし、上部にある **[管理者の設定]** をクリックします。
6. **[管理者の追加]** ブレードで、ユーザーを検索し、管理者にするユーザーまたはグループを選択したら、**[選択]** をクリックします。 [Active Directory 管理者] ブレードには、Active Directory のメンバーとグループがすべて表示されます。 淡色表示されているユーザーまたはグループは、Azure AD 管理者としてサポートされていないため選択できません (サポートされている管理者の一覧については、上記の「**Azure AD の機能と制限事項**」を参照してください)。 ロール ベースのアクセス制御 (RBAC) はポータルにのみ適用され、SQL Server には反映されません。
7. 上部にある、 **Active Directory 管理者** ブレードで、をクリックして **保存**します。 
    ![choose admin][10]

    管理者を変更する処理には数分かかる場合があります。 処理が完了すると、**[Active Directory 管理者]** ボックスに新しい管理者が表示されます。

> [AZURE.NOTE] Azure AD 管理者をセットアップする場合、新しい管理者名 (ユーザーまたはグループ) が SQL Server 認証のログインとして、マスター データベース内に存在していてはなりません。 存在する場合、Azure AD 管理者のセットアップは失敗します。その作成をロールバックされ、そのような管理者 (名前) が既に存在していることが示されます。 前述 SQL Server 認証ログインは、Azure AD には属していないため、Azure AD 認証を使用してサーバーに接続する操作は失敗します。

後で管理者を削除するには、**[Active Directory 管理者]** ブレードの上部にある **[管理者の削除]** をクリックします。

### PowerShell を使用して Azure SQL Server の Azure AD 管理者をプロビジョニングする

PowerShell コマンドレットを実行するには、Azure PowerShell をインストールし、実行している必要があります。 詳細については、次を参照してください。 [をインストールして、Azure PowerShell を構成する方法](../powershell-install-configure.md)します。

Azure AD 管理者をプロビジョニングするには、次のような Azure PowerShell コマンドを実行する必要があります。

- Add-AzureRmAccount
- Select-AzureRmSubscription


Azure AD 管理者のプロビジョニングと管理に使用するコマンドレットは、次のとおりです。

| コマンドレット名| 説明|
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [セット AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx)| Azure SQL Server の Azure Active Directory 管理者をプロビジョニングします(現在のサブスクリプションから実行する必要があります)。|
| [削除 AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx)| Azure SQL Server の Azure Active Directory 管理者を削除します。|
| [Get AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx)| 現在 Azure SQL Server 用に構成されている Azure Active Directory 管理者に関する情報を返します。|

たとえば、これらのコマンドの各詳細を表示する PowerShell コマンドのヘルプを表示を使用して `get-help セット AzureRmSqlServerActiveDirectoryAdministrator`します。

次のスクリプトを Azure AD の管理者グループの名前を規定 **DBA_Group** (オブジェクト id `40b79501-b343-44ed-9ce7-da4c8cc7353f`) の **demo_server** という名前のリソース グループ内のサーバー **グループ 23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" 
–ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 入力パラメーターには、Azure AD の表示名またはユーザー プリンシパル名を使用できます。 たとえば `DisplayName ="John Smith"` と `DisplayName ="johns@contoso.com"`します。 Azure AD グループの場合は、Azure AD の表示名のみがサポートされています。
> [AZURE.NOTE] Azure PowerShell コマンド `セット AzureRmSqlServerActiveDirectoryAdministrator` からサポートされていないユーザー用の Azure AD 管理者をプロビジョニングするできなくなります。 サポートされていないユーザーのプロビジョニングは可能ですが、このようなユーザーはデータベースに接続できません (サポートされている管理者の一覧については、上記の「**Azure AD の機能と制限事項**」を参照してください)。

次の例では、オプションとして **ObjectID** を使用します。

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" 
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] **DisplayName** が一意でない場合は、Azure AD の **ObjectID** が必要です。 **ObjectID** と **DisplayName** の値を取得するには、Azure クラシック ポータルの [Active Directory] セクションを使用して、ユーザーまたはグループのプロパティを表示します。

次の例では、Azure SQL Server の現在の Azure AD 管理者に関する情報が返されます。

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

次の例は、Azure AD 管理者を削除します。
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

## 5.クライアント コンピューターを構成する

Azure AD の ID を使用して Azure SQL Database に接続するアプリケーションまたはユーザーが存在するすべてのクライアント コンピューターには、次のソフトウェアをインストールする必要があります。

- .NET framework 4.6 以降から [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)します。
- SQL Server 用の azure Active Directory 認証ライブラリ (**ADALSQL します。DLL**) は複数の言語で使用できます (x86、amd64)、ダウンロード センターから [Microsoft Active Directory の認証ライブラリの Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742)します。

### ツール

- いずれかをインストールする [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) または [SQL Server Data Tools for Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) が .NET Framework 4.6 の要件を満たしています。
- SSMS の場合、x86 バージョンの **ADALSQL.DLL** がインストールされます。 (現時点では、SSMS は、インストール後に再起動が必要であることを示すメッセージを表示しません。 これは、将来の CTP で修正される予定です。)
- SSDT の場合、amd64 バージョンの **ADALSQL.DLL** がインストールされます。 SSDT では、Azure AD 認証が部分的にしかサポートされていません。
- 最新の Visual Studio から [Visual Studio のダウンロード](https://www.visualstudio.com/downloads/download-visual-studio-vs) 、.NET Framework 4.6 の要件を満たしますが、の必要な amd64 バージョンをインストールしない **ADALSQL します。DLL**.

## 6.Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成する

### 包含データベース ユーザーについて

Azure Active Directory 認証では、データベース ユーザーを包含データベース ユーザーとして作成することが必要です。 Azure AD の ID に基づく包含データベース ユーザーは、master データベースにログインを持たないデータベース ユーザーで、そのデータベースに関連付けられている Azure AD ディレクトリの ID にマップされています。 Azure AD の ID には、個々のユーザー アカウントにもグループ アカウントにもなります。 包含データベース ユーザーの詳細については、次を参照してください。 [Your のデータベース ポータブルの包含データベース ユーザーを作成する](https://msdn.microsoft.com/library/ff929188.aspx)します。 データベース ユーザー (管理者と想定) はポータルを使用して作成することはできず、RBAC の役割は SQL Server には反映されません。

### SQL Server Management Studio を使用してユーザー データベースに接続する

Azure AD 管理者が正しく設定されていることを確認するには、Azure AD の管理者アカウントを使用して **master** データベースに接続します。
Azure AD ベースの包含データベース ユーザー (データベースを所有しているサーバー管理者以外) をプロビジョニングするには、そのデータベースへのアクセス権を持つ Azure AD の ID を使用してデータベースに接続します。
> [AZURE.IMPORTANT] Azure Active Directory 認証のサポートはで利用できる [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)します。

#### Active Directory 統合認証を使用して接続する

フェデレーション ドメインから Azure Active Directory の資格情報を使用して Windows にログオンしている場合は、この方法を使用します。

1. Management Studio を起動して、**[データベース エンジンへの接続]** (または **[サーバーへの接続]**) ダイアログ ボックスの **[認証]** ボックスで、**[Active Directory 統合認証]** を選択します。 接続用の既存の資格情報が表示されるため、パスワードは必要ないか、入力できません。
2. **[オプション]** ボタンをクリックし、**[接続プロパティ]** ページの **[データベースへの接続]** ボックスに、接続先となるユーザー データベースの名前を入力します。

#### Active Directory パスワード認証を使用して接続する

Azure AD の管理対象ドメインを使用して Azure AD のプリンシパル名で接続する場合は、この方法を使用します。 また、リモートで作業する場合など、ドメインにアクセスできないフェデレーション アカウントにもこの方法を使用できます。

この方法を使用するのは、Azure とフェデレーションされていないドメインの資格情報を使用して Windows にログオンしている場合や、初期ドメインまたはクライアント ドメインに基づく Azure AD で Azure AD 認証を使用する場合です。

1. Management Studio を起動して、**[データベース エンジンへの接続]** (または **[サーバーへの接続]**) ダイアログ ボックスの **[認証]** ボックスで、**[Active Directory パスワード認証]** を選択します。
2. **ユーザー名** 形式で、Azure Active Directory のユーザー名を入力ボックスで、 **username@domain.com**します。 これは、Azure Active Directory のアカウントか、Azure Active Directory とフェデレーションするドメインのアカウントである必要があります。
3. **[パスワード]** ボックスに、Azure Active Directory アカウントまたはフェデレーション ドメイン アカウントのユーザー パスワードを入力します。
4. **[オプション]** ボタンをクリックし、**[接続プロパティ]** ページの **[データベースへの接続]** ボックスに、接続先となるユーザー データベースの名前を入力します。


### ユーザー データベースに Azure AD の包含データベース ユーザーを作成する

Azure AD ベースの包含データベース ユーザー (データベースを所有するサーバー管理者以外) を作成するには、少なくとも **ALTER ANY USER** アクセス許可を持つユーザーとして、(前の手順で説明したように) Azure AD の ID でデータベースに接続します。 その後、次の Transact-SQL 構文を使用します。

    CREATE USER Azure_AD_principal_name 
    FROM EXTERNAL PROVIDER;

*Azure_AD_principal_name* には、Azure AD ユーザーのユーザー プリンシパル名または Azure AD のグループの表示名を指定できます。

**例:**
包含データベースを作成するには、Azure AD を表すユーザー フェデレーション ユーザーまたはドメイン ユーザーを管理します。

    CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
    CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Azure AD またはフェデレーション ドメインのグループを表す包含データベース ユーザーを作成するには、次のようにします。

    CREATE USER [Nurses] FROM EXTERNAL PROVIDER;

含まれる作成の詳細については、Azure Active Directory id に基づくユーザーのデータベースを参照してください [CREATE USER (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)します。

データベース ユーザーを作成すると、そのユーザーは **CONNECT** アクセス許可が付与されるため、**PUBLIC** ロールのメンバーとしてそのデータベースに接続できます。 最初にこのユーザーが利用できるアクセス許可は、**PUBLIC** ロールに付与されているアクセス許可か、またはそのユーザーが属している Windows グループに付与されているアクセス許可のみです。 Azure AD ベースの包含データベース ユーザーをプロビジョニングすると、他の種類のユーザーにアクセス許可を付与する場合と同様に、そのユーザーに追加のアクセス許可を付与できます。 通常は、データベース ロールにアクセス許可を付与し、そのロールにユーザーを追加します。 詳細については、次を参照してください。 [データベース エンジンのアクセス許可の基本](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)します。 特別な SQL データベース ロールの詳細については、次を参照してください。 [Azure SQL Database におけるデータベースの管理とログイン](sql-database-manage-logins.md)します。
管理対象ドメインにインポートされるフェデレーション ドメイン ユーザーは、管理対象ドメインの ID を使用する必要があります。
> [AZURE.NOTE] データベースのメタデータでは、Azure AD ユーザーはタイプ E (EXTERNAL_USER) 、グループはタイプ X (EXTERNAL_GROUPS) でマークされます。 詳細については、次を参照してください。 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)します。


## 7.Azure Active Directory の ID を使用してデータベースに接続する

Azure Active Directory 認証では、Azure AD の ID を使用してデータベースに接続する次の方法がサポートされています。

- 統合 Windows 認証を使用する
- Azure AD のプリンシパル名とパスワードを使用する

### 7.1.統合 (Windows) 認証を使用して接続する

統合 Windows 認証を使用するには、ドメインの Active Directory を Azure Active Directory とフェデレーションする必要があります。また、データベースに接続しているクライアント アプリケーション (またはサービス) を、ユーザーのドメイン資格情報でドメインに参加しているコンピューター上で実行している必要があります。

統合認証と Azure AD の ID を使用してデータベースに接続するには、データベース接続文字列内の Authentication キーワードを "Active Directory Integrated" に設定する必要があります。 次の C# のコード サンプルでは、ADO .NET を使用します。

    string ConnectionString = 
    @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated;";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

メモ接続文字列のキーワード `Integrated Security = True` は、Azure SQL Database に接続するためにサポートされていません。

### 7.2.Azure AD のプリンシパル名とパスワードを使用して接続する

統合認証と Azure AD の ID を使用してデータベースに接続するには、Authentication キーワードを "Active Directory Password" に設定し、接続文字列にユーザー ID (UID) とパスワード (PWD) のキーワードと値を含める必要があります。 次の C# のコード サンプルでは、ADO .NET を使用します。

    string ConnectionString =
      @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

特定のコードに関連する Azure AD 認証の例を参照してください、 [SQL Server セキュリティ ブログ](http://blogs.msdn.com/b/sqlsecurity/) MSDN にします。

## 関連項目

[Azure SQL データベースにおけるデータベースとログインの管理](sql-database-manage-logins.md)

[包含データベース ユーザー](https://msdn.microsoft.com/library/ff929071.aspx)

[CREATE USER (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)




[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png 
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png 
[3]: ./media/sql-database-aad-authentication/3admin-structure.png 
[4]: ./media/sql-database-aad-authentication/4select-subscription.png 
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png 
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png 
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png 
[8]: ./media/sql-database-aad-authentication/8choose-ad.png 
[9]: ./media/sql-database-aad-authentication/9ad-settings.png 
[10]: ./media/sql-database-aad-authentication/10choose-admin.png 

