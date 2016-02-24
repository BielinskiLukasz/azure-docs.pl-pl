<properties
   pageTitle="Azure AD B2B コラボレーション プレビュー: 動作のしくみ | Microsoft Azure"
   description="Azure Active Directory B2B コラボレーションが、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートする方法を説明します"
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Azure Active Directory (Azure AD) B2B コラボレーションのプレビュー: しくみ
Azure AD B2B コラボレーションは、招待/利用モデルに基づいています。 共同作業対象者の電子メール アドレスと、使用を許可するアプリケーションを指定します。 Azure AD は、対象者にリンクを含む電子メール招待状を送信します。 パートナーのユーザーがリンクをクリックすると、Azure AD アカウントを使用したサインイン、または新しい Azure AD アカウントへのサインアップを求められます。

1. お客様の管理者では、パートナーのユーザーを招待をアップロードして [構造化の .csv ファイル](active-directory-b2b-references-csv-file-format.md) Azure ポータルを使用します。
2. ポータルはパートナーのユーザーに電子メールで招待状を送ります。
3. パートナーのユーザーが電子メールのリンクをクリックすると、資格情報を使用してサインインするように (Azure AD が既にある場合)、または Azure AD B2B コラボレーションのユーザーとしてサインアップするように求められます。
4. パートナーのユーザーは、アクセスできるようになった招待されたアプリケーションにリダイレクトされます。

## ディレクトリの操作
パートナーのユーザーは、外部ユーザーとして Azure AD に存在します。 つまり、管理者は、社内のユーザーの場合と同じように Azure ポータルまたは Azure PowerShell を使用して、ライセンスをプロビジョニングし、グループのメンバーシップを割り当て、会社のアプリへのアクセスを許可できます。

有料の Azure AD サブスクリプション (Basic または Premium) がなくても Azure AD B2B を使用できますが、有料の Azure AD サブスクリプション (Basic または Premium) を持っているとさらに次のようなメリットがあります。

 - 管理者はアプリにグループを割り当てて、招待されたユーザーのアクセスをさらに簡単に管理できます。
 - 管理テナントのブランド化を使用して、招待電子メールと使用エクスペリエンスをブランド化し、招待されたパートナーのユーザーにいっそう詳細なコンテキストを提供できます。

## 関連記事:
 Azure B2B コラボレーションに関する他の記事を参照してください。

 - [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [詳細なチュートリアル](active-directory-b2b-detailed-walkthrough.md)
 - [CSV ファイル形式リファレンス](active-directory-b2b-references-csv-file-format.md)
 - [外部ユーザー トークンの形式](active-directory-b2b-references-external-user-token-format.md)
 - [外部ユーザー オブジェクト属性の変更](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [現在のプレビューの制限事項](active-directory-b2b-current-preview-limitations.md)

