<properties
    pageTitle="Azure Active Directory B2C プレビュー: FAQ | Microsoft Azure"
    description="Azure Active Directory B2C についてよく寄せられる質問"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2015"
    ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: FAQ

このページには、Azure Active Directory (AD) B2C プレビューに関してよく寄せられる質問への回答が記載されています。 常に最新情報をチェックしてください。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### 既存の従業員ベースの Azure AD テナントで Azure AD B2C を使用できますか。

現在のところ、既存の Azure AD テナントで Azure AD B2C 機能を有効にすることはできません。 Azure AD B2C 機能を使用するための別個のテナントを作成し、お客様を管理することをお勧めします。

### Azure AD B2C を使用してソーシャル ログイン (Facebook および Google+) を Office 365 に提供することはできますか。

Azure AD B2C は Office 365 と共に使用することはできません。 一般に、SaaS アプリ (Salesforce、Workday など) に認証を提供するために使用することはできません。 これは、消費者向けの Web およびモバイル アプリケーションの ID およびアクセス管理を提供するものであり、従業員やパートナー向けではありません。

### Azure AD B2C の "ローカル アカウント" とは何ですか。 それらは、Azure AD の "職場または学校のアカウント" とはどのような点が異なるのですか。

Azure AD テナントでは、テナント内のすべてのユーザー (既存の Microsoft アカウントを持つユーザーを除く) は、`<xyz>@<tenant domain>` という形式の電子メール アドレスを使用してサインインします。`<tenant domain>` はテナント内のいずれかの有効なドメイン、または初期 `<...>.onmicrosoft.com` ドメインです。 このタイプのアカウントは "職場または学校のアカウント" であり、"組織アカウント" とも呼ばれます。

Azure AD B2C テナントは、ほとんどのアプリケーションは、任意の任意の電子メール アドレス (例、joe@comcast.net、bob@gmail.com、sarah@contoso.com または jim@live.com) を使用してサインインするユーザーをします。 このタイプのアカウントは "ローカル アカウント" です。 現在、ローカル アカウントとして任意のユーザー名 (単なるプレーン文字列) もサポートしています (joe、bob、sarah、jim など)。 Azure AD B2C サービスでは、これらの 2 つの "タイプ" のローカル アカウントからいずれかを選択できます。

### 現在サポートされているソーシャル ID プロバイダーはどれですか。 将来サポートする予定のプロバイダーはどれですか。

現在、Facebook、Google+、LinkedIn、および Amazon をサポートしています。 お客様のご要望に基づいて、Microsoft アカウントやその他の人気のあるソーシャル ID プロバイダーのサポートを追加する予定です。

### さまざまなソーシャル ID プロバイダーからお客様に関する情報をさらに収集するために "スコープ" を構成できますか。

いいえ。ただし、この機能は検討中です。 サポートされている一連のソーシャル ID プロバイダーに使用されている、既定のスコープは次のとおりです。

- Facebook: 電子メール
- Google +: 電子メール
- Amazon: プロファイル
- LinkedIn: r_emailaddress r_basicprofile

### 使用しているアプリケーションを Azure AD B2C で機能させるには、Azure で実行する必要がありますか。

いいえ。アプリケーションは任意の場所でホストできます (クラウドまたはオンプレミス)。 Azure AD B2C とやり取りするために必要なのは、パブリックにアクセス可能なエンドポイントで HTTP 要求を送受信する機能のみです。

### 複数の Azure AD B2C ディレクトリがあります。 Azure プレビュー ポータルでそれらを管理するにはどのようにすればいいですか。

Azure プレビュー ポータルには、各 Azure AD B2C テナント独自の B2C 機能ブレードがあります。 読み取り [ここ](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) Azure プレビュー ポータルの特定のテナントの B2C 機能ブレードに移動する方法にします。 Azure プレビュー ポータルで Azure AD B2C ディレクトリを切り替えると、大部分のブラウザでは B2C 機能ブレードが開いたままになりません。

### Azure AD B2C によって送信される検証電子メールをカスタマイズするにはどうすればいいですか (コンテンツ、および送信者フィールド ("From:" フィールド))。

読み取り [UI のカスタマイズでこの記事](active-directory-b2c-reference-ui-customization.md) 詳細です。

### 既存のユーザー名、パスワード、およびプロファイルを自分のデータベースから Azure AD B2C に移行するにはどのようにすればいいですか。

Azure AD Graph API を使用することができます (この例を参照してください [ここ](active-directory-b2c-devquickstarts-graph-dotnet.md)) 移行ツールを記述します。 将来、さまざまな移行オプションとツールをすぐに使用できる状態で提供する予定です。

### Azure AD Connect を使用して、自分のオンプレミス Active Directory に保存されているお客様の ID を Azure AD B2C に移行できますか。

いいえ。Azure AD Connect は Azure AD B2C と連携するようには設計されていません。 将来、さまざまな移行オプションとツールをすぐに使用できる状態で提供する予定です。

### Azure AD B2C は Microsoft Dynamics のような CRM システムと連携しますか。

現時点では連携しません。 これらのシステムとの統合を検討中です。

### Azure AD B2C は SharePoint On-Premises 2016 以前と連携しますか。

現時点では連携しません。 Azure AD B2C では、ポータル/E コマース アプリケーションが SharePoint On-Premises のニーズに基づいて構築した SAML 1.1 トークンはサポートされません。 Azure AD B2C をシナリオを共有する Sharepoint の外部パートナー用ありませんに注意してください。参照してください [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) 代わりにします。

### Azure AD B2C ではどのようなレポート機能と監査機能が提供されますか。 それは Azure AD Premium の機能と同じですか。

いいえ。Azure AD B2C では Azure AD Premium と同じレポート セットはサポートされていません。 Azure AD B2C では、基本レポート API および監査 API が間もなくリリースされる予定です。

### Azure AD B2C で提供されているページの UI をローカライズできますか。 どの言語がサポートされていますか。

現在、Azure AD B2C は英語のみに最適化されています。 可能な限り早急にローカライズ機能を展開する予定です。

### Azure AD B2C によって提供されているサインアップおよびサインイン ページで独自の URL を使用できますか。 たとえば、URL を login.microsoftonline.com から login.contoso.com に変更できますか。

現時点では変更できません。 この機能は検討中です。 ドメインを「確認」にも注意してください、 **ドメイン** Azure ポータルで、テナントのタブはこれが実行されません。

### Enterprise Mobility Suite (EMS) の一部として Azure AD B2C を取得できますか。

いいえ。Azure AD B2C は従量課金の Azure サービスであり、EMS には含まれていません。

### Azure AD B2C に関する問題を報告するにはどのようにすればいいですか。

チェック アウト [サポートここ](active-directory-b2c-support.md) Azure AD B2C にします。

### Azure AD B2C が一般に使用できるようになるのはいつですか。

現時点では、一般に使用可能になる日付に関する情報を提供することはできません。

## 詳細情報

またたい現在 [プレビューの制限事項、制限および制約](active-directory-b2c-limitations.md)します。

