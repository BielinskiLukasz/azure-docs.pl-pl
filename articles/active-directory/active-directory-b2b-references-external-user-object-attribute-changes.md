<properties
   pageTitle="Azure Active Directory B2B コラボレーション プレビュー用の外部ユーザー オブジェクト属性の変更 |Microsoft Azure"
   description="Azure Active Directory B2B は、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで、会社間のリレーションシップをサポートします"
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
   ms.workload="na"
   ms.date="10/27/2015"
   ms.author="viviali"/>


# Azure Active Directory (Azure AD) B2B コラボレーション プレビューの外部ユーザー オブジェクト属性の変更

Azure AD ディレクトリ内の各ユーザーは、ユーザー オブジェクトで表されます。 Azure AD のユーザー オブジェクトは、B2B コラボレーションの招待承諾フローのさまざまな段階で属性が変更されます。 ディレクトリ内のパートナー ユーザーを表すユーザー オブジェクトには、承諾時 (パートナー ユーザーが招待メール内のリンクをクリックしたとき) に変更される属性があります。 具体的には次の処理が行われます。

- **SignInName** 属性と **AltSecId** 属性が設定されます。
- **DisplayName** 属性は、ユーザー プリンシパル名 (user_fabrikam.com#EXT#@contoso.com) からサインイン名 (user@fabrikam.com) に変更

Azure AD でこれらの属性を追跡すると、パートナー ユーザーが B2B コラボレーションへの招待を承諾したかどうかに関するトラブルシューティングに役立ちます。

## 関連記事:

Azure B2B コラボレーションに関する他の記事を参照してください。

- [Azure AD B2B コラボレーションとは何ですか。](active-directory-b2b-what-is-azure-ad-b2b.md)
- [そのしくみ](active-directory-b2b-how-it-works.md)
- [詳細なチュートリアル](active-directory-b2b-detailed-walkthrough.md)
- [CSV ファイル形式のリファレンス](active-directory-b2b-references-csv-file-format.md)
- [外部ユーザー トークンの形式](active-directory-b2b-references-external-user-token-format.md)
- [現在のプレビューの制限事項](active-directory-b2b-current-preview-limitations.md)





