<properties
   pageTitle="Azure Active Directory Graph API | Microsoft Azure"
   description="REST API エンドポイントを介して Azure AD にプログラムでアクセスできる Graph API の概要およびクイック スタート ガイドです。"
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/17/2015"
   ms.author="mbaldwin" />


# Azure Active Directory Graph API

> [AZURE.IMPORTANT] Azure AD Graph API の機能がを介して利用も [Graph](https://graph.microsoft.io/), 、Outlook、OneDrive、OneNote、プランナー、および 1 つのエンドポイントと 1 つのアクセス トークンを使用してアクセス可能な Office グラフなどの他の Microsoft サービスからの Api を含んだ API の統合します。

Azure Active Directory Graph API を使用すると、REST API エンドポイントを介して Azure AD にプログラムによってアクセスできます。 アプリケーションでは、Graph API を使用して、ディレクトリのデータとオブジェクトに対して、作成、読み取り、更新、および削除 (CRUD) の各操作を実行できます。 たとえば、Graph API では、ユーザー オブジェクトへの次のような一般的な操作がサポートされています。

- ディレクトリに新しいユーザーを作成する

- ユーザーの詳細なプロパティ (所属するグループなど) を取得する

- ユーザーのプロパティ (場所や電話番号など) を更新するか、またはユーザーのパスワードを変更する

- ロールベースのアクセスに対応しているかどうか、ユーザーのグループ メンバーシップを確認する

- ユーザーのアカウントを無効にするか、または完全に削除する

ユーザー オブジェクトに加えて、グループやアプリケーションなどのその他のオブジェクトに対しても同様の操作を実行できます。 ディレクトリ上で Graph API を呼び出すには、アプリケーションを Azure AD に登録し、ディレクトリへのアクセスを許可するように構成する必要があります。 通常、これは、ユーザーまたは管理者の同意フローによって実現されます。

Azure Active Directory Graph API を使用するには、次を参照してください。、 [Graph API のクイック スタート ガイド](active-directory-graph-api-quickstart.md), 、したり表示したり、 [対話型の Graph API リファレンス ドキュメント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)します。


## 特徴

Graph API には、次の機能が用意されています。

- **REST API エンドポイント**: Graph API は、標準の HTTP 要求を使用してアクセスされるエンドポイントで構成された REST ベースのサービスです。 Graph API では、要求と応答のコンテンツの種類として XML または Javascript Object Notation (JSON) をサポートしています。 詳細については、次を参照してください。 [Azure AD Graph REST API リファレンス](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)します。

- **Azure AD による認証**: Graph API に対するすべての要求は、その要求の Authorization ヘッダーに JSON Web トークン (JWT) を追加することによって認証する必要があります。 このトークンを取得するには、Azure AD のトークン エンドポイントに対して要求を送信し、有効な資格情報を提供します。 OAuth 2.0 クライアント資格情報フローまたは承認コード付与フローを使用して、Graph を呼び出すためのトークンを取得することができます。 詳細については、 [Azure AD での OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)します。

- **ロールベースの承認 (RBAC)**: セキュリティ グループは、Graph API 内で RBAC を実行する際に使用されます。 たとえば、アプリケーションを呼び出して、ユーザーが特定のリソースへのアクセス権を持っているかどうかを決定する場合、 [グループ メンバーシップの確認 (推移的)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) 操作で、true または false を返します。

- **差分クエリ**: Graph API に対して頻繁にクエリを実行することなく 2 つの期間の間でディレクトリに変更があるかどうかを確認する場合は、差分クエリ要求を実行することができます。 この種の要求は、前回の差分クエリ要求と現在の要求の間で行われた変更のみを返します。 詳細については、次を参照してください。 [Azure AD Graph API 差分クエリ](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)します。

- **ディレクトリ拡張機能**: ディレクトリ オブジェクトの一意のプロパティの読み取りまたは書き込みを行う必要があるアプリケーションを開発している場合は、Graph API を使用して、拡張機能の値を登録して使用できます。 たとえば、アプリケーションがユーザーごとに Skype ID プロパティを必要とする場合は、ディレクトリに新しいプロパティを登録することができ、そのプロパティはすべてのユーザー オブジェクトで使用できるようになります。 詳細については、次を参照してください。 [Azure AD Graph API ディレクトリ スキーマ拡張機能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)します。

## シナリオ

Graph API により、多くのアプリケーション シナリオを実現できます。 最も一般的なシナリオを次に示します。

- **基幹業務 (シングル テナント) アプリケーション**: このシナリオでは、エンタープライズ開発者は、Office 365 サブスクリプションを持つ組織に勤めています。 開発者は、Azure AD を操作してユーザーへのライセンスの割り当てなどのタスクを実行する Web アプリケーションを作成します。 このタスクでは、Graph API へのアクセスが必要となるため、開発者は、Azure AD にこのシングル テナント アプリケーションを登録し、Graph API の読み取りおよび書き込みのアクセス許可を構成します。 その後、アプリケーション独自の資格情報または現在サインインしているユーザーの資格情報を使用して、Graph API を呼び出すトークンを取得するように、アプリケーションは構成されます。

- **サービス アプリケーション (マルチテナント) としてのソフトウェア**: このシナリオでは、独立系ソフトウェア ベンダー (ISV) が、Azure AD を使用する他の組織にユーザー管理機能を提供するホスト型のマルチテナント Web アプリケーションを開発しています。 これらの機能にはディレクトリ オブジェクトへのアクセスが必要になるため、アプリケーションは Graph API を呼び出す必要があります。 開発者は、Azure AD にアプリケーションを登録し、Graph API に対する読み取りと書き込みのアクセス許可を必要とするように構成して、他の組織がディレクトリ内のアプリケーションを使用することに同意できるように外部アクセスを有効にします。 別の組織のユーザーが初めてアプリケーションに対する認証を行うとき、アプリケーションが要求しているアクセス許可に関する同意ダイアログが表示されます。 同意すると、アプリケーションには、ユーザーのディレクトリでの Graph API に対する要求されたアクセス許可が付与されます。 同意フレームワークの詳細については、次を参照してください。 [同意フレームワークの概要](active-directory-integrating-applications.md)します。

## 関連項目

[Azure AD Graph API のクイック スタート ガイド](active-directory-graph-api-quickstart.md)

[AD Graph REST に関するドキュメント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)





