<properties
    pageTitle="Azure AD とアプリケーション: 開発者ガイド | Microsoft Azure"
    description="IT プロフェッショナル向けに記述されたこの記事では、Azure アプリケーションを Active Directory を統合するためのガイドラインを示します。"
    services="active-directory"
    documentationCenter=""
    authors="IHenkel"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/09/2015"
    ms.author="inhenk"/>

# Azure AD とアプリケーション: 開発者ガイド

## 概要

このガイドでは、Azure Active Directory 用の基幹業務 (LoB) アプリケーションの開発の概要について説明します。特に Active Directory/Office 365 のグローバル管理者向けに記述されています。

Azure AD と統合されるアプリケーションを構築すると、組織内のユーザーは、Office 365 でシングル サインオンできます。 アプリケーションを Azure AD と統合すると、アプリケーションに対して設定する認証ポリシーを管理できます。 詳細については、条件付きアクセスおよび多要素認証 (MFA) アプリを保護する方法を参照してください、次のドキュメント: [アクセス規則を構成します。](active-directory-conditional-access-azuread-connected-apps.md)

Azure Active Directory を使用するためには、アプリケーションを登録する必要があります。 アプリケーションを登録することで、組織の開発者は、Azure AD を使用して、電子メール、予定表、ドキュメントなどのユーザー リソースへのアクセスを要求する組織のメンバーを認証できます。

(ゲストではありません)、ディレクトリのすべてのメンバーとも呼ばれる、アプリケーションを登録できます *アプリケーション オブジェクトを作成する*です。

アプリケーションを登録すると、ユーザーは次の操作を実行できます。

- Azure AD が認識するアプリケーションの ID を取得する
- アプリケーションが自身を AD に対して認証するために使用する 1 つまたは複数のシークレット/キーを取得する
- Azure ポータルで、名前やロゴなどを使用してアプリケーションをブランド化する
- アプリ用の Azure AD 承認機能を活用する
  - アプリケーションのロール ベースのアクセス制御 (RBAC)
  - OAuth 承認サーバーとしての Azure Active Directory (アプリケーションによって公開される API をセキュリティで保護する)

- アプリケーションが期待どおりに機能するために必要なアクセス許可を宣言する。 以下が含まれます。
      アプリのアクセス許可 (グローバル管理者のみ)。 次に例を示します。
        のもう 1 つ Azure AD アプリケーションまたはロール メンバーシップ Azure リソース、リソース グループまたはサブスクリプションに関連ロールのメンバーシップ
      アクセス許可を委任 (任意のユーザー)。 次に例を示します。
        -(AAD) でのサインインと読み取りプロファイル
        -(Exchange) は、メールを送信する、メールを読む
        -(SharePoint) の読み取り

> [AZURE.NOTE]既定では、すべてのメンバーは、アプリケーションを登録できます。 アプリケーションを登録するためのアクセス許可を特定のメンバーに制限する方法については、アプリケーションを Azure AD に追加する方法に関するドキュメントを参照してください。

開発者によるアプリケーションの作成をサポートするために、グローバル管理者として次の操作を行う必要があります。

- アクセス規則を構成する (アクセス ポリシー/MFA)
- ユーザー割り当てを必要とするようにアプリを構成し、ユーザーを割り当てる
- 既定のユーザー同意エクスペリエンスを抑制する

## アクセス規則を構成する

既に説明したように、次の記事で、任意のアプリケーションのアクセス規則を構成する方法の詳細を参照してください。

[アクセス規則を構成する](active-directory-conditional-access-azuread-connected-apps.md)です。

## ユーザー割り当てを必要とするようにアプリを構成し、ユーザーを割り当てる

既定では、ユーザーがアプリケーションにアクセスするためのユーザー割り当ては必要ありません。 ただし、アプリケーションがロールを公開している場合、またはアプリケーションをユーザーのアクセス パネルに表示する場合は、ユーザー割り当てが必要であり、ユーザーまたはグループを割り当てる必要があります。

[ユーザー割り当ての要求](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Azure AD Premium または Enterprise Mobility Suite (EMS) のサブスクライバーの場合は、グループを活用することを強くお勧めします。 グループをアプリケーションに割り当てると、グループの所有者に継続的にアクセスの管理を委任できます。 自分でグループを作成するか、グループ管理機能を使用して組織の責任者にグループの作成を依頼できます。

- [アプリケーションへのユーザーの割り当て](active-directory-applications-guiding-developers-assigning-users.md)
- [アプリケーションへのグループの割り当て](active-directory-applications-guiding-developers-assigning-groups.md)

## ユーザーの同意の抑制

既定では、ユーザーは、サインインするために必要なアクセス許可に同意する必要があります。 アプリケーションへのアクセス許可を付与することを求める同意エクスペリエンスは、そのような決定に慣れていないユーザーの混乱を招く可能性があります。

信頼できるアプリケーションの場合は、組織内のすべてのユーザーに代わってアプリケーションに同意できます。

詳細については、ユーザーの同意し、同意が Azure で発生するを参照してください [と Azure Active Directory のアプリケーションの統合。](active-directory-integrating-applications.md)

