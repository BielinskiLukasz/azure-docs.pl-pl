<properties
   pageTitle="Azure Active Directory の B2B コラボレーション | Microsoft Azure"
   description="Azure Active Directory の B2B コラボレーションは、ビジネス パートナーが会社のアプリケーションにアクセスできるようにします。パートナーの各ユーザーは、1 つの  Azure AD アカウントによって表されます。"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="msStevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="curtand"/>

# Azure Active Directory (Azure AD) B2B コラボレーション

Azure AD の B2B コラボレーションは、パートナーによって管理されるユーザーが会社のアプリケーションにアクセスできるようにします。 パートナー企業のユーザーを招待し、リソースにアクセスできるように認証することで、会社間の関係を作成できます。 いったん各企業と Azure Active Directory をフェデレーションすると各ユーザーが 1 つの Azure AD アカウントで表されるようになるため、複雑さが軽減されます。 パートナーのユーザーが組織を離れるとアクセス権が取り消されるほか、内部ディレクトリ内のメンバーシップを使用した意図しないアクセスが防止されるため、高いレベルのセキュリティが確保されます。 B2B コラボレーションには、Azure AD をまだ保有していないビジネス パートナー向けに、Azure AD のアカウントをビジネス パートナーに提供するための合理化されたサインアップ エクスペリエンスが用意されています。

-   ビジネス パートナーはサインインに自分の資格情報を使用するため、あなたが外部パートナーのディレクトリを管理する必要はありません。またユーザーがパートナー組織から離れたときにアクセス権を削除する必要もありません。

-   アプリへのアクセスは、ビジネス パートナーのアカウントのライフサイクルとは無関係に管理します。 つまり、ビジネス パートナーの IT 部門に処理を依頼する必要なくアクセスを取り消すことができます。

## 機能

B2B コラボレーションは、管理を簡略化し、SaaS アプリ (Office 365、Salesforce、Azure Services、およびすべてのモバイル、クラウド、およびオンプレミスの要求対応アプリケーション) を含む会社のリソースへのパートナーによるアクセスのセキュリティを向上させます。 B2B コラボレーションでは、パートナーが独自のアカウントを管理する一方で、企業がパートナーのアクセスにセキュリティ ポリシーを適用することができます。

Azure Active Directory B2B コラボレーションは、構成が簡単で、あらゆる規模のパートナーが、電子メール検証プロセスを使用する独自の Azure Active Directory を持たない場合でも、簡単にサインアップできます。 さらに、外部のディレクトリやパートナーごとのフェデレーション構成が必要ないため、容易に管理できます。

プロセス:

1. Azure AD B2B コラボレーションを利用する会社の管理者が、外部ユーザーを招待および承認するために、2,000 行以下のコンマ区切り値 (CSV) ファイルを B2B コラボレーション ポータルにアップロードします。

  ![CSV [ファイルのアップロード] ダイアログ](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. ポータルを介して、これらの外部ユーザーに電子メール招待状が送信されます。

3. 招待されたユーザーは、(Azure AD で管理される) Microsoft の既存の職場アカウントにサインインするか、または Azure AD で新しい職場アカウントを取得します。

4. サインインすると、ユーザーは、共有されているアプリにリダイレクトされます。

コンシューマーへの招待の電子メール アドレス (Gmail などまたは [*comcast.net*](http://comcast.net/)) は現在サポートされていません。

B2B コラボレーションの機能の詳細は、チェック アウト [このビデオ](http://aka.ms/aadshowb2b)します。

## CSV ファイルの形式

CSV ファイルの形式は次のとおりです。 1 つまたは複数のオプションを指定しない場合でも、必要なすべてのコンマを追加してください。

**電子メール:** 招待されたユーザーのアドレスを電子メールで送信します。<br/>
**DisplayName:** 招待されたユーザー (通常は、姓と名) の表示名。<br/>
**InviteAppID:**  の電子メールへの招待と同意のページのブランドを使用するアプリケーションの ID。<br/>
**InviteReplyURL:** への招待の承認後に招待されたユーザーに指示する URL。 会社固有の URL があります (たとえば [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/))。 招待の会社のアクセス パネルの URL が生成されたこの省略可能なフィールドが指定されていない場合 (この URL は、フォームの  `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`)。<br/>
**InviteAppResources:** Appid アプリケーションがユーザーを割り当てることができます。 Appid を呼び出すことによって取得することは `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/>
**InviteGroupResources:** にユーザーを追加するグループの Objectid。 Objectid を呼び出すことによって取得することは `Get-MsolGroup | fl DisplayName, ObjectId`<br/>
**InviteContactUsUrl:** 、招待されたユーザーが組織にお問い合わせする場合に、電子メールの招待に含める URL 問い合わせ先]。<br/>

## サンプル CSV ファイル
サンプル CSV ファイルを次に示します。このサンプル CSV は、用途に合わせて変更できます。 任意の名前を付けてファイルに保存します。このとき、ファイル名拡張子には、必ず ".csv" を使用してください。

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```
## 次のステップ
Azure B2B コラボレーションに関する他の記事を参照してください。

- [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
- [動作のしくみ](active-directory-b2b-how-it-works.md)
- [詳細なチュートリアル](active-directory-b2b-detailed-walkthrough.md)
- [CSV ファイル形式リファレンス](active-directory-b2b-references-csv-file-format.md)
- [外部ユーザー トークンの形式](active-directory-b2b-references-external-user-token-format.md)
- [外部ユーザー オブジェクト属性の変更](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [現在のプレビューの制限事項](active-directory-b2b-current-preview-limitations.md)

