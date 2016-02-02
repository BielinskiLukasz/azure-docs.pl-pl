<properties
    pageTitle="Azure Active Directory の AD ロールベースのアクセス制御 | Microsoft Azure"
    description="この記事では、Azure のロールベースのアクセス制御について説明します。"
    services="active-directory"
    documentationCenter=""
    authors="IHenkel"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="12/14/2015"
    ms.author="inhenk"/>


# Azure のロールベースのアクセス制御

## ロールベースのアクセス制御

Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。 RBAC を使用して、開発チーム内で職務を分離し、職務に必要なアクセス権のみをユーザーに付与します。

### Azure でのアクセス管理の基礎

各 Azure サブスクリプションは Azure Active Directory にあります。 そのディレクトリからのユーザー、グループ、およびアプリケーションのみが、Azure ポータル、Azure コマンドライン ツール、および Azure 管理 API を使用して、Azure のサブスクリプションでリソースを管理するためのアクセス権を付与されます。

正しいスコープで、ユーザー、グループ、およびアプリケーションに適切な RBAC ロールを割り当てることで、アクセス権が付与されます。 サブスクリプション全体にアクセス権を付与するには、サブスクリプションのスコープでロールを割り当てます。 サブスクリプション内の特定のリソース グループへのアクセス権を付与するには、リソース グループのスコープでロールを割り当てます。 Web サイト、仮想マシン、およびサブネットのように、リソースのみにアクセス権を付与するよう、特定のリソースでロールを割り当てる場合もあります。

![](./media/role-based-access-control-configure/overview.png)

ユーザー、グループ、およびアプリケーションに割り当てる RBAC ロールでは、そのスコープ内でユーザー (またはアプリケーション) が管理できるリソースを決定します。

### Azure RBAC の組み込みのロール

Azure RBAC には、すべてのリソースの種類に適用される 3 つの基本的なロール(所有者、作成協力者、および閲覧者) があります。 所有者は、他のユーザーへアクセス権を委任する権限を含め、すべてのリソースへのフル アクセス権を持ちます。 作成協力者は、Azure リソースのすべてのタイプを作成および管理できますが、他のユーザーへアクセス権を付与することはできません。 閲覧者は、既存の Azure リソースを表示できるのみです。 残りの Azure RBAC ロールでは、特定の Azure リソースの管理が許可されます。 たとえば、仮想マシンの作成協力者ロールでは、仮想マシンの作成と管理は許可されますが、仮想マシンが接続する仮想ネットワークまたはサブネットの管理は許可されません。

[RBAC の組み込みの役割の](role-based-access-built-in-roles.md) Azure で使用できる組み込みの RBAC ロールを一覧表示します。 各ロールに対しては、組み込みのロールがアクセス権を付与する操作を指定します。

### Azure のリソース階層とアクセス権の継承

Azure の各サブスクリプションは、1 つのディレクトリのみに属しており、各リソース グループは 1 つのサブスクリプションのみに属します。さらに、各リソースは、1 つのリソース グループにのみ属しています。 親スコープで付与されたアクセス権は、子スコープに継承されます。 閲覧者のロールをサブスクリプションのスコープで、Azure AD グループに付与すると、そのグループのメンバーは、サブスクリプション内のすべてのリソース グループとすべてのリソースを表示することができるようになります。 リソース グループのスコープで、アプリケーションに作成協力者のロールを付与すると、そのアプリケーションは、そのリソース グループのすべてのタイプのリソースを管理することができるようになりますが、サブスクリプション内の他のリソース グループは管理できません。

### Azure RBAC と従来のサブスクリプションの管理者と共同管理者の比較

従来のサブスクリプションの管理者と共同管理者は、Azure サブスクリプションへのフル アクセス権を持ちます。 Azure クラシック ポータル (https://manage.windowsazure.com) と Azure サービス マネージャー Api だけでなく Azure ポータル (https://portal.azure.com) と新しい Azure リソース マネージャー Api を使用してリソースを管理できるようにします。 RBAC モデルで、従来の管理者は、サブスクリプション スコープで所有者ロールを割り当てられます。

承認の細かいモデル (Azure RBAC) は、Azure ポータル (https://portal.azure.com) と Azure リソース マネージャー Api でのみサポートされます。 ユーザーおよび (サブスクリプション/リソース/リソース グループのスコープ) での RBAC ロールを割り当てられているアプリケーションは、従来の管理ポータル (http://manage.windowsazure.com) と Azure サービス管理 Api を使用できません。

### 管理用の承認とデータ操作

Azure の詳細な承認モデル (Azure RBAC) は、Azure ポータルと Azure リソース マネージャー API における Azure リソースの管理操作専用です。 RBAC による Azure リソースのデータ レベルの操作の中には、許可されていないものもあります。 たとえば、ストレージ アカウントの作成/読み取り/更新/削除は RBAC によって制御できますが、ストレージ アカウント内の BLOB やテーブルの作成/読み取り/更新/削除は現時点では RBAC によって制御できません。 同様に、SQL DB の作成/読み取り/更新/削除は RBAC によって制御できますが、DB 内の SQL テーブルの作成/読み取り/更新/削除は現時点では RBAC によって制御できません。

## Azure ポータルを使用したアクセス権の管理

### アクセス許可の表示

リソース グループ ブレードの [essentials] セクションでは、[access settings] を選択します。 **[ユーザー]** ブレードには、リソース グループへのアクセス権が付与されたすべてのユーザー、グループ、およびアプリケーションが一覧表示されます。 アクセス権は、リソース グループに割り当てられているか、親のサブスクリプションへの割り当てから継承されています。

![](./media/role-based-access-control-configure/view-access.png)
> [AZURE.NOTE] 従来のサブスクリプションの管理者と共同管理者は、新しい RBAC モデルのサブスクリプションの有効な所有者です。

### アクセス権の追加

1. クリックして、 **追加** 上のアイコン、 **ユーザー** ブレードです。 ![](./media/role-based-access-control-configure/grant-access1.png)
2. 割り当てるロールを選択します。
3. アクセス権を付与するユーザー、グループ、またはアプリケーションを検索して、選択します。
4. ユーザー、グループ、および表示名、電子メール アドレス、およびオブジェクト識別子を使用してアプリケーションのディレクトリを検索します。![](./media/role-based-access-control-configure/grant-access2.png)

### アクセス権の削除

1. **[ユーザー]** ブレードで、削除するロールの割り当てを選択します。
2. [割り当ての詳細] ブレードで、**[削除]** アイコンをクリックします。
3. **[はい]** をクリックして削除を確定します。

![](./media/role-based-access-control-configure/remove-access1.png)

> [AZURE.NOTE] 継承された割り当ては、子スコープから削除できません。 親スコープへ移動し、割り当てを削除します。


![](./media/role-based-access-control-configure/remove-access2.png)

## Azure PowerShell を使用したアクセス権の管理

アクセス権は、Azure PowerShell ツールで Azure RBAC コマンドを使用することで管理できます。

-   使用 `Get AzureRmRoleDefinition` ] ボックスの一覧の RBAC ロールとアクセスを許可する操作を検査する割り当てのために使用できます。

-   使用 `Get AzureRmRoleAssignment` リスト RBAC アクセスの割り当てが指定されたサブスクリプションまたはリソース グループまたはリソースに有効にします。 使用して、 `ExpandPrincipalGroups` パラメーターを指定したユーザーも、ユーザーがメンバーになっているグループにリストのアクセスの割り当て。 使用して、 `IncludeClassicAdministrators` パラメーターも従来のサブスクリプションの管理者と共同管理者の一覧を表示します。

-   使用 `新規 AzureRmRoleAssignment` ユーザー、グループ、アプリケーションにアクセスを許可します。

-   使用 `削除 AzureRmRoleAssignment` のアクセスを削除します。

参照してください [Azure PowerShell を使用してアクセスを管理](role-based-access-control-manage-access-powershell.md) より詳細な Azure PowerShell を使用したアクセスの管理の例を示します。

## Azure コマンドライン インターフェイスを使用したアクセス権の管理

アクセス権は、Azure コマンドライン インターフェイスで、Azure RBAC コマンドを使用することで管理できます。

-   使用 `azure ロールの一覧` ] ボックスの一覧の RBAC ロールを割り当て可能にします。 アクセス権を付与する操作を調査するには、[azure role show] を使用します。

-   使用 `azure ロールの割り当て一覧` リスト RBAC アクセスの割り当てが指定されたサブスクリプションまたはリソース グループまたはリソースに有効にします。 使用して、 `expandPrincipalGroups` オプションを指定したユーザーも、ユーザーがメンバーになっているグループにリストのアクセスの割り当て。 使用して、  `includeClassicAdministrators` パラメーターも従来のサブスクリプションの管理者と共同管理者の一覧を表示します。

-   使用 `azure ロールの割り当てを作成` ユーザー、グループ、アプリケーションにアクセスを許可します。

-   使用 `azure ロールの割り当てを削除` のアクセスを削除します。

参照してください [Azure CLI を使用してアクセスを管理](role-based-access-control-manage-access-azure-cli.md) より詳細な Azure CLI を使用してアクセスの管理の例を示します。

## アクセス変更履歴レポートの使用

Azure サブスクリプションで発生したすべてのアクセス変更は、Azure イベントに記録されます。

### Azure PowerShell を使用したレポートの作成

Azure サブスクリプションで、誰が、どのような種類のアクセス権を、どこから、どこへ、どのスコープで付与または破棄したかといったレポートを作成するには、以下の PowerShell コマンドを使用します。

    `Get-AzureAuthorizationChangeLog`

### Azure CLI を使用したレポートの作成

Azure サブスクリプションで、誰が、どのような種類のアクセス権を、どこから、どこへ、どのスコープで付与または破棄したかといったレポートを作成するには、以下の Azure コマンドライン インターフェイス (CLI) コマンドを使用します。

    `azure authorization changelog`

> [AZURE.NOTE] アクセス変更は、過去 90 日間に対し、(15 日分を) 照会することができます。

次の例では、サブスクリプションで過去 7 日間のアクセス変更をすべて一覧表示します。

![](./media/role-based-access-control-configure/access-change-history.png)

### スプレッドシートへのアクセス変更のエクスポート

確認のためにスプレッドシートへアクセス変更の内容をエクスポートすると便利です。

![](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## Azure RBAC のカスタム ロール

アクセス権に関して実際の要件を満たす組み込みのロールが存在しない場合は、Azure RBAC でカスタム ロールを作成します。 カスタム ロールは、Azure PowerShell の RBAC のコマンドライン ツールで作成できるほか、Azure コマンドライン インターフェイスで作成することができます。 組み込みのロールと同様、カスタム ロールは、ユーザーやグループ、アプリケーションに対し、サブスクリプション スコープ、リソース グループ スコープ、リソース スコープで割り当てることができます。

以下に示したのは、仮想マシンの監視と再起動を許可するカスタム ロール定義の例です。

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
### アクション

カスタム ロールでアクセス権を付与する Azure の操作は、そのロールの actions プロパティで指定します。 このプロパティに文字列で指定された一連の操作によって、Azure リソース プロバイダーのセキュリティ保護可能な操作が識別されます。 ワイルドカードを含む文字列を操作 (\ *) のアクセス許可は、操作の文字列に一致するすべての操作を参照してください。 次に例を示します。

-   `*/read` 読み取り操作のすべての Azure リソース プロバイダーのすべてのリソースの種類へのアクセスを許可します。
-   `Microsoft.Network/*/read` Microsoft.Network リソース プロバイダー内のすべてのリソース型 Azure のための操作の読み取りアクセス許可を付与します。
-   `Microsoft.Compute/virtualMachines/*` リソースの種類の仮想マシンとその子のすべての操作へのアクセスを許可します。
-   `Microsoft.Web/sites/restart/Action` web サイトを再起動するアクセス権を付与します。

使用 `Get AzureRmProviderOperation` または `azure プロバイダー操作ショー` Azure リソース プロバイダーのリスト操作するためのコマンドです。 これらのコマンドを使って、操作文字列が有効であるかどうかを確認したり、操作文字列のワイルドカードを展開した結果を表示したりすることもできます。

![](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

![](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

### not actions

許可の対象となる一連の操作を指定する際、特定の操作を除外した方が、その操作を除くすべての操作を指定するよりも容易である場合は、カスタム ロールの **NotActions** プロパティを使用してください。 カスタム ロールによって実際に付与されるアクセス権は、Actions の操作から **NotActions** の操作を除外することによって計算されます。

**NotActions** で特定の操作を除外したロールをユーザーに割り当てたうえで、同じユーザーにその操作へのアクセスを許可する別のロールを割り当てた場合、その操作の実行がユーザーに許可されることに注意してください。 **NotActions** は拒否ルールとは異なり、特定の操作を除外する必要があるときに、許可の対象となる一連の操作を指定しやすくすることを目的としたものに過ぎません。

### AssignableScopes

カスタム ロールの **AssignableScopes** プロパティは、ユーザー、グループ、アプリケーションへの割り当てにカスタム ロールを利用できるスコープ (サブスクリプション、リソース グループ、リソースのいずれか) を指定します。 **AssignableScopes** を使用すると、必要なサブスクリプションまたはリソース グループにカスタム ロールの割り当てを限定して、それ以外のサブスクリプションやリソース グループについては元のユーザー エクスペリエンスを保ち、不要な混乱を避けることができます。 カスタム ロールの表示、更新、削除をだれに許可するかという点も **AssignableScopes** の制御対象となります。 有効な AssignableScopes の例を次に示します。

-   “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”, “/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624”: 対象となるロールの割り当てを 2 つのサブスクリプションに許可します。
-   “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”: 対象となるロールの割り当てを 1 つのサブスクリプションに許可します。
-   “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network”: 対象となるロールの割り当てを Network リソース グループにのみ許可します。

### カスタム ロールのアクセス制御

カスタム ロールの **AssignableScopes** プロパティは、そのロールをだれが表示、変更、削除できるかという点に影響を及ぼします。

**カスタム ロールを作成できるユーザー:**
カスタム ロールを正常に作成できるのは、そのロールを作成するユーザーに、指定されたすべての **AssignableScopes** についてカスタム ロールの作成が許可されている場合だけです。 ロールを作成するユーザーが実行できる場合にのみ、カスタム ロールの作成が成功した `Microsoft.Authorization/roleDefinition/write 操作` すべてで、 **AssignableScopes** ロールのです。 したがって、サブスクリプション、リソース グループ、リソースの Owners (および User Access Administrators) は、それぞれのスコープで使用するカスタム ロールを作成することができます。

**カスタム ロールに変更を加えることができるユーザー:**
カスタム ロールの更新が、ロールのすべての **AssignableScopes** について許可されているユーザーは、そのカスタム ロールに変更を加えることができます。 実行できるように `Microsoft.Authorization/roleDefinition/write` すべての操作、 **AssignableScopes** カスタム ロールのカスタム ロールを変更できます。 たとえば、カスタム ロールの割り当てが 2 つの Azure サブスクリプションに許可されている場合 (そのロールの **AssignableScopes** プロパティに 2 つのサブスクリプションが存在する場合)、ユーザーがカスタム ロールに変更を加えるためには、その両方のサブスクリプションの Owner (または User Access Administrators) である必要があります。

**特定のスコープで割り当て可能なカスタム ロールを表示できるユーザー:**
実行できるユーザー、 `Microsoft.Authorization/roleDefinition/read` スコープでの操作は、そのスコープに割り当て可能な RBAC 役割を表示できます。 Azure RBAC の組み込みロールについてはいずれも、割り当て可能なロールの表示対象として許可されています。





