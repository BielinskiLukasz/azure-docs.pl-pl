<properties
   pageTitle="Azure での Office 365 サブスクリプションのディレクトリの管理 | Microsoft Azure"
   description="Azure Active Directory と Azure クラシック ポータルを使用して Office 365 サブスクリプション アカウントのディレクトリを管理する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/01/2015"
   ms.author="curtand"/>


# Azure での Office 365 サブスクリプションのディレクトリの管理

この記事では、Office 365 サブスクリプションに対して作成されたディレクトリを Azure クラシック ポータル内で管理する方法について説明します。 実際の手順は Azure のサブスクリプションを既に持っているかどうかによって異なります。 Azure クラシック ポータルにサインインするには、Azure サブスクリプションのサービス管理者または共同管理者である必要があります。

Azure サブスクリプションをまだ持っていない場合は、Office 365 へのサインインに使用している会社または学校のアカウントでサインアップする必要があります。

![](./media/active-directory-manage-o365-subscription/AAD_O365_01.png)

対応する Azure サブスクリプションは見つかりませんが、**[Azure へのサインアップ]** をクリックすると、Office 365 アカウントからの関連情報がサインアップ フォームに事前入力されます。 同じアカウントがサービス管理者ロールに既定で割り当てられます。

![](./media/active-directory-manage-o365-subscription/AAD_O365_02.png)

Azure サブスクリプションの手続きが完了したら、Azure クラシック ポータルにサインインして、Azure のサービスにアクセスできます。 Office 365 ユーザーを認証する同じディレクトリを管理するには、Active Directory 拡張機能をクリックします。

Azure サブスクリプションを既に持っている場合、追加のディレクトリを管理するプロセスも単純です。 プロセスを確認するには次の図が役立ちます。

この例で、Michael Smith さんは Contoso.com の Office 365 サブスクリプションを持っています。 彼はサインアップして自分の Microsoft アカウントを使用して、Azure のサブスクリプションを持ち、msmith@hotmail.com です。 この場合、彼は 2 つのディレクトリを管理します。

| [サブスクリプション]| Office 365| Azure|
|  -------------- | ------------- | ------------------------------- |
| 表示名| Contoso| 既定のディレクトリ|
| ドメイン名| contoso.com| msmithhotmail.onmicrosoft.com|

彼は、自分の Microsoft アカウントを使って Azure にサインインしているときに Contoso ディレクトリ内のユーザー ID を管理したいと考えています。そこで、彼は、多要素認証などの Azure AD の機能を有効にすることができます。

![](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

この場合、2 つのディレクトリは相互に独立しています。

## 2 つの独立したディレクトリを管理するには

Michael Smith は、msmith@hotmail.com として Azure にサインインしたときに、両方のディレクトリを管理するためには、次の手順を実行する彼は必要があります。
> [AZURE.NOTE]
> この手順は、ユーザーが Microsoft アカウントでサインインしているときにのみ実行できます。 ユーザーが会社または学校のアカウントを使用してサインインしている場合、**[既存のディレクトリの使用]** オプションは使用できません。会社または学校のアカウントはそのホーム ディレクトリ (つまり、会社または学校のアカウントが保存され、会社または学校が所有しているディレクトリ) でのみ認証できるためです。

1.  Msmith@hotmail.com として Azure 旧ポータルにサインインします。
2.  **[新規]**、**[アプリケーション サービス]**、**[Active Directory]**、**[ディレクトリ]**、**[カスタム作成]** の順にクリックします。
3.  [既存のディレクトリの使用] をクリックし、**[サインアウトする準備ができました]** チェック ボックスをオンにします。
4.  Contoso.onmicrosoft.com (msmith@contoso.com など) のグローバル管理者として Azure 旧ポータルにサインインします。
5.  "**Azure で Contoso ディレクトリを使用しますか?**" という確認を求められたら、**[続行]** をクリックします。
6.  **[今すぐサインアウト]** をクリックします。
7.  Msmith@hotmail.com として Azure 旧ポータルにサインインします。 Contoso ディレクトリと既定のディレクトリが Active Directory 拡張機能に表示されます。

次の手順を完了すると、msmith@hotmail.com は Contoso ディレクトリ内のグローバル管理者です。

## グローバル管理者としてリソースを管理するには

今すぐ John doe さんが Azure クラシック ポータルにサインインし、msmith@hotmail.com の Azure サブスクリプションに関連付けられている web サイトとデータベース リソースを管理する必要があるとしましょう。 このために、Michael Smith さんは、以下の追加手順を実行する必要があります。

1.  Azure サブスクリプションのサービス管理者アカウントを使用して Azure 旧ポータルにサインイン (この例では msmith@hotmail.com)。
2.  サブスクリプションを Contoso ディレクトリに移行します。**[設定]**、**[サブスクリプション]** の順にクリックし、サブスクリプションを選択します。**[ディレクトリの編集]** をクリックし、**[Contoso (Contoso.com)]** を選択します。 移行の一環として、サブスクリプションの共同管理者である会社または学校のアカウントはすべて削除されます。
3.  John doe さんをサブスクリプションの共同管理者として追加します。] をクリック **設定** > **管理者** > サブスクリプションを選択 > **追加** > 型 **JohnDoe@Contoso.com**します。

## 次のステップ

サブスクリプションとディレクトリの関係の詳細については、次を参照してください。 [サブスクリプションは、ディレクトリに関連付ける方法](active-directory-how-subscriptions-associated-directory.md)します。





