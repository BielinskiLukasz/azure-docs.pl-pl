<properties
    pageTitle="Azure Conditional Access Preview for SaaS Apps | Microsoft Azure"
    description="Azure AD の条件付きアクセスを使用すると、アプリケーションごとの Multi-Factor Authentication のアクセス規則と、信頼されたネットワークにないユーザーのアクセスをブロックする機能を構成できます。 "
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/30/2015"
    ms.author="femila"/>


# Azure Conditional Access Preview for SaaS Apps

Azure Conditional Access Preview for SaaS Apps は、パブリック プレビューで利用できます。 プレビューでは、アプリケーションごとの Multi-Factor Authentication のアクセス規則と、信頼されたネットワークにないユーザーのアクセスをブロックする機能を構成できます。

Multi-Factor Authentication の規則は、アプリケーションに割り当てられているすべてのユーザーに適用することも、指定したセキュリティ グループのユーザーにのみ適用することもできます。 ユーザーが組織のネットワーク内の IP アドレスからアプリケーションにアクセスしている場合は、そのユーザーを Multi-Factor Authentication の要件から除外できます。
これらの機能は、Azure Active Directory Premium ライセンスを購入したお客様に提供されます。

## シナリオの前提条件

* Azure Active Directory Premium のサブスクリプション

* フェデレーションまたは管理対象 Azure Active Directory テナント

* フェデレーション テナントでは、Multi-Factor Authentication (MFA) を有効にする必要があります。

## このプレビュー リリースの既知の問題

このプレビューは、事前に統合されたフェデレーション SaaS アプリケーション、パスワード シングル サインオンを使用するアプリケーション、登録済みの開発された基幹業務アプリケーション、および Azure AD アプリケーション プロキシに適用されます。 一部の他のアプリケーションがまだ有効になっています。

## アプリケーションごとのアクセス規則の構成

ここでは、アプリケーションごとのアクセス規則を構成する方法について説明します。

1. Microsoft Azure ポータルに管理者としてサインインします。
2. 左ウィンドウで、**[Active Directory]** を選択します。
3. [ディレクトリ] タブで、ディレクトリを選択します。
4. **[アプリケーション]** タブを選択します。
5. 規則を設定するアプリケーションを選択します。
6. **[構成]** タブをクリックします。
7. 下へスクロールして、アクセス規則のセクションを表示します。 目的のアクセス規則を選択します。
8. 規則を適用するユーザーを指定します。
9. **[有効] チェック ボックスをオン**にして、ポリシーを有効にします。

## アクセス規則について

ここでは、Azure Conditional Application Access プレビューでサポートされているアクセス規則について詳細に説明します。
### アクセス規則を適用するユーザーの指定

既定では、このポリシーは、アプリケーションへのアクセスを持つすべてのユーザーに適用されます。 ただし、指定したセキュリティ グループのメンバーであるユーザーのみにポリシーを制限することもできます。 **[グループの追加]** を使用すると、グループ選択のダイアログ ボックスから、アクセス規則を適用するグループを 1 つまたは複数選択できます。 このダイアログ ボックスを使用して、選択したグループを削除することもできます。 グループに適用する規則を選択すると、アクセス規則は、指定したセキュリティ グループに属するユーザーにのみ課せられます。

[除外] をオンにして、1 つまたは複数のグループを指定することで、セキュリティ グループをポリシーから明示的に除外することもできます。 除外リストのグループ メンバーであるユーザーは、アクセス規則が適用されるグループのメンバーである場合でも、Multi-Factor Authentication の要件が適用されません。
以下に示すアクセス規則では、マネージャー グループのすべてのユーザーが、アプリケーションにアクセスするときに Multi-Factor Authentication を使用する必要があります。

![MFA による条件付きアクセス規則の設定](./media/active-directory-conditional-access/conditionalaccess-saas-apps.png)

## MFA による条件付きアクセス規則

ユーザーごとの Multi-Factor Authentication 機能を使用してユーザーが構成されている場合は、そのユーザーのこの設定がアプリの Multi-Factor Authentication 規則に優先します。 これは、たとえ、それらのユーザーがアプリケーションの Multi-Factor Authentication 規則から除外されていたとしても、Multi-Factor Authentication を実行するには、ユーザーごとの Multi-Factor Authentication で構成されたユーザーが必要になることを意味します。 Multi-Factor Authentication とユーザーごとの設定に関する詳細を参照してください。

### アクセス規則のオプション

現在のプレビューでは、次のオプションがサポートされています。

* **多要素認証が必要です**: このオプションを使用した場合、アクセス規則が適用されるユーザーは、ポリシーが適用されるアプリケーションにアクセスする前に Multi-Factor Authentication を完了する必要があります。

* **作業中でない場合、多要素認証が必要です**: このオプションを使用した場合、信頼できる IP からのユーザーは、Multi-Factor Authentication を実行する必要はありません。 Multi-Factor Authentication の設定ページでは、信頼できる IP の範囲を構成できます。

* **作業中でない場合、アクセスをブロック**: このオプションを使用した場合、信頼できる IP 以外からのユーザーはブロックされます。 Multi-Factor Authentication の設定ページでは、信頼できる IP の範囲を構成できます。

### 規則の状態の設定

アクセス規則の状態により、規則を有効または無効にすることができます。 アクセス規則が無効の場合は、Multi-Factor Authentication の要件は適用されません。

### アクセス規則の評価

アクセス規則は、ユーザーが OAuth 2.0、OpenID Connect、SAML、または WS-Federation を使用するフェデレーション アプリケーションにアクセスした場合に評価されます。 さらに、アクセス規則は、OAuth 2.0 と OpenID Connect が使用された場合、アクセス トークンの取得に更新トークンが使用された場合に評価されます。 更新トークンの使用時にポリシーの評価が失敗すると、エラー invalid_grant が返されます。これは、ユーザーがクライアントに対して再認証する必要があることを示します。
Multi-Factor Authentication を実現するためのフェデレーション サービスの構成

フェデレーション テナントでは、Multi-Factor Authentication (MFA) が、Azure Active Directory またはオンプレミスの AD FS サーバーによって実行される場合があります。

既定では、MFA は Azure Active Directory によってホストされているページで実行されます。 オンプレミスの MFA を構成するには、Windows PowerShell の Azure AD モジュールを使用して、Azure Active Directory で –SupportsMFA プロパティを true に設定する必要があります。

次の例を使用してオンプレミス MFA を有効にする方法を示しています、 [Set-msoldomainfederationsettings コマンドレット](https://msdn.microsoft.com/library/azure/dn194088.aspx) contoso.com テナントで。

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

このフラグの設定に加えて、Multi-Factor Authentication が実行されるように、フェデレーション テナントの AD FS インスタンスを構成する必要があります。 オンプレミスの Azure Multi-Factor Authentication のデプロイの手順に従ってください。






