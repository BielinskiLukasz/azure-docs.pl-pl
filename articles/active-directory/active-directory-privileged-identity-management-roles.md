<properties
   pageTitle="Azure Privileged Identity Management: ロール"
   description="Azure Privileged Identity Management 拡張機能で特権 ID に使用されるロールについて説明します。"
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>


# Azure Privileged Identity Management: ロール

## Azure Active Directory、Office 365、その他のソースからのロール

Azure PIM は、既定の管理者ロールとして次のロールを使用します。

- グローバル管理者
- 課金管理者
- サービス管理者
- ユーザー管理者
- パスワード管理者

Office 365 からの役割に関する詳細については、Exchange Online、Sharepoint Online および Skype for Business ここをクリックします。[Office 365 の管理ロールの割り当て](https://support.office.com/en-us/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504?ui=en-US&rs=en-US&ad=US)






## ユーザーのロールとログイン

> [AZURE.NOTE]ユーザーが Azure PIM にログインできるためには、Azure のライセンスが必要です。

## Azure AD でのユーザーへのライセンスの割り当て

> [AZURE.NOTE] ライセンス オプションは、そのサブスクリプションに対してライセンスが実際に存在する場合にのみ表示されます。

1. グローバル管理者アカウントまたは共同管理者アカウントで、[http://manage.windowsazure.com] (http://manage.windowsazure.com) にログインします。
2. メイン メニューの **[すべての項目]** をクリックします。
3. ライセンスが関連付けられていて使用するディレクトリを選択します。
4. **[ライセンス]** をクリックします。 利用可能なライセンスの一覧が表示されます。
5. 配布するライセンスが含まれているライセンス プランをクリックします。
6. **[ユーザーの割り当て]** をクリックします。
7. ライセンスを割り当てるユーザーを選択します。
8. **[割り当て]** ボタンをクリックします。 ユーザーは Azure にログインできるようになります。


## 次のステップ

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]





