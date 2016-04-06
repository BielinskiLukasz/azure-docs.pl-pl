<properties
    pageTitle="Azure PowerShell を使用したロールベースのアクセス制御 (RBAC) の管理 |Microsoft Azure"
    description="Azure PowerShell を使用して、ロールの表示、ロールの割り当て、ロールの割り当ての削除などを行って RBAC を管理する方法。"
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
    ms.date="12/04/2015"
    ms.author="inhenk"/>

# Azure PowerShell を使用したロールベースのアクセス制御 (RBAC) の管理
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## RBAC のロールの表示
### 使用可能なすべてのロールの表示
割り当てることができる RBAC のロールを表示したり、アクセス権が付与されている操作を調べたりするには、次のコマンドを使用します。

        Get-AzureRmRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### ロールのアクションの表示
特定のロールのアクションを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## アクセス権の表示
### 選択したサブスクリプションのすべてのロールの割り当ての表示
指定したサブスクリプション、リソース、またはリソース グループで有効な RBAC のアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment

### リソース グループに対して有効なロールの割り当ての表示
リソース グループのアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### ユーザーへのロールの割り当て (ユーザー グループに割り当てられているロールを含む) の表示
指定したユーザーと、そのユーザーが属するグループへのアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### 従来のサービス管理者ロールと共同管理者ロールの割り当ての表示
従来のサブスクリプション管理者と共同管理者のアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## アクセスの許可
### オブジェクト ID の検索
以下のコマンド シーケンスを使用するには、オブジェクト ID を検索しておく必要があります。  既にわかっているサブスクリプション id を使用して、それ以外の場合を参照するくださいと見なされます [Get-azuresubscription](https://msdn.microsoft.com/library/dn495302.aspx) MSDN にします。

#### Azure AD グループのオブジェクト ID の検索
Azure AD グループのオブジェクト ID を取得するには、次のコマンドを使用します。

    Get-AzureRmADGroup -SearchString <group name in quotes>

#### Azure AD サービス プリンシパルのオブジェクト ID の検索
使用して、Azure AD サービス プリンシパルのオブジェクト id を取得します。
    Get AzureRmADServicePrincipal-SearchString <service name in quotes>

### サブスクリプションのスコープでのグループへのロールの割り当て
サブスクリプションのスコープでグループにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### サブスクリプションのスコープでのアプリケーションへのロールの割り当て
サブスクリプションのスコープでアプリケーションにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### リソース グループのスコープでのユーザーへのロールの割り当て
リソース グループのスコープでユーザーにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### リソースのスコープでのグループへのロールの割り当て
リソースのスコープでグループにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## アクセス権の削除
ユーザー、グループ、アプリケーションのアクセス権を削除するには、次のコマンドを使用します。

    Remove-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## カスタム ロールの作成
カスタム ロールを作成するには、`New-AzureRmRoleDefinition` コマンドを使用します。

次の例と呼ばれるカスタム ロールを作成する *仮想マシンの演算子* のすべての読み取り操作へのアクセスを許可する *Microsoft.Compute*, 、*Microsoft.Storage*, と *Microsoft.Network* を再起動し、仮想マシンを監視を開始するには、リソース プロバイダー、およびアクセス権を付与します。 カスタム ロールは 2 つのサブスクリプションで使用できます。

![](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## カスタム ロールの修正
カスタム ロールを修正するには、最初に `Get-AzureRmRoleDefinition` コマンドを使用してロール定義を取得します。 必要に応じてロール定義を変更します。 最後に、`Set-AzureRmRoleDefinition` コマンドを使用して変更したロール定義を保存します。

次の例では追加、 `Microsoft.Insights/diagnosticSettings/*` する操作、 *仮想マシン演算子* カスタム ロールです。

![](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

次の例では、Azure サブスクリプションが Virtual Machine Operator カスタム ロールの割り当て可能なスコープに追加されます。

![](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## カスタム ロールの削除

カスタム ロールを削除するには、`Remove-AzureRmRoleDefinition` コマンドを使用します。

次の例では、削除、 *仮想マシン演算子* カスタム ロールです。

![](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## カスタム ロールの一覧表示
特定のスコープで割り当て可能なロールを一覧表示するには、`Get-AzureRmRoleDefinition` コマンドを使用します。

次の例では、選択したサブスクリプションで割り当て可能なすべてのロールが一覧表示されます。

![](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

次の例では、 *仮想マシン演算子* カスタム ロールでは使用できません、 *Production4* サブスクリプションにそのサブスクリプションがないため、 **AssignableScopes** ロールのです。

![](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## RBAC のトピック
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]


