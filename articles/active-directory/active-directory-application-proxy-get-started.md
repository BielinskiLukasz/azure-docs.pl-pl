<properties
    pageTitle="オンプレミス アプリへの安全なリモート アクセスを実現する方法"
    description="Azure AD アプリケーション プロキシを使用して、オンプレミス アプリへの安全なリモート アクセスを実現する方法について取り上げます。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/02/2015"
    ms.author="kgremban"/>


# オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法

リモートからアクセスするユーザーが使っているデバイスは多岐にわたります。タブレット、スマートフォン、ノート PC が、自分の管理下にあるものもあれば、自分の管理下にないデバイスが使われることもあるでしょう。 おびただしい数のリソースが存在する中で、それらに対するアクセスのセキュリティを確保することは容易ではありません。 近年、リモート アクセスの安全性を確保する方法としてリバース プロキシが広く用いられてきました。しかし、リバース プロキシは、ファイアウォールの背後に設置する必要があります。ファイアウォールのセキュリティを確保したり、可用性を高めることが難しいという問題がありました。

## クラウドでの安全なリモート アクセス

最新のクラウド環境では、Microsoft Azure Active Directory (AD) のアプリケーション プロキシが、リモート アクセスを一段高いレベルに引き上げます。 アプリケーション プロキシは Azure AD の機能のひとつであり、サービスとして提供されるため、デプロイしやすく、使いやすいのが特徴です。 Office 365 でも使われている ID プラットフォーム、Azure AD と連携します。

## Azure Active Directory アプリケーション プロキシとは何か

アプリケーション プロキシは、SharePoint サイトや Outlook Web Access などのオンプレミスでホストされている Web アプリケーションのシングル サインオンと安全なリモート アクセスを実現します。 SaaS アプリと同じように Azure Active Directory でオンプレミスの Web アプリケーションを発行することができます。VPN を導入したりネットワーク インフラストラクチャに変更を加えたりする必要はありません。 アプリケーション プロキシを使用してアプリケーションを発行することが可能です。 従業員は、自宅から自分のデバイスでアプリケーションにログインし、このクラウド ベースのプロキシを使用して認証を行うことができます。

## それはどのように機能しますか?

### アクセスの実現

アプリケーション プロキシは、社内ネットワークに "コネクタ" と呼ばれる軽量の Windows Server サービスをインストールすることによって機能します。 コネクタを導入するにあたって、受信ポートは一切開放する必要がありません。また DMZ に何かを設置する作業も不要です。 アプリへのトラフィック量が多い場合は、コネクタを増設すれば、このサービスによって負荷分散が行われます。 コネクタはステートレスで、すべての情報が必要に応じてクラウドから取得されます。
リモート ユーザーが任意のデバイスからアプリケーションにアクセスすると、そのユーザーは、Azure Active Directory によって認証されて、アプリケーションにアクセスできるようになります。

### シングル サインオン

Azure AD アプリケーション プロキシは、統合ウィンドウズ認証 (IWA) や要求に対応するアプリケーションへのシングル サインオン (SSO) を提供します。 発行対象のアプリケーションで IWA が使用されている場合、アプリケーション プロキシが Kerberos の制約付き委任を使い、見かけ上ユーザーとして振る舞うことで、SSO を実現します。 Azure Active Directory を信頼している要求に対応するアプリケーションでは、Azure AD によってユーザーが認証済みであるために SSO が成立します。

## ファースト ステップ

Azure AD Basic または Azure AD Premium サブスクリプションに加え、自分が全体管理者となっている Azure AD ディレクトリが必要です。 ディレクトリ管理者、およびアプリにアクセスするユーザーについても、Azure AD Basic または Azure AD Premium のライセンスが必要となります。 見て [ここ](active-directory-editions.md) Azure AD のエディションの詳細についてです。

### オンプレミス アプリケーションへのリモート アクセスを可能にするための第一歩

アプリケーション プロキシの設定は、次の 2 つの手順で行います。

1. [アプリケーション プロキシを有効にして、コネクタを構成します。](active-directory-application-proxy-enable.md)<br>
2. [アプリケーションを公開する](active-directory-application-proxy-publish.md) -迅速かつ簡単なウィザードを使って、内部設置型アプリケーション発行され、アクセス可能なリモートからです。

## 次の手順

アプリケーション プロキシを使ってできることは他にもたくさんあります。


- [ドメイン名を使用してアプリケーションを公開します。](active-directory-application-proxy-custom-domains.md)
- [シングル サインオンの有効化にします。](active-directory-application-proxy-sso-using-kcd.md)
- [クレーム対応アプリケーションの使用](active-directory-application-proxy-claims-aware-apps.md)
- [条件付きアクセスを有効にします。](active-directory-application-proxy-conditional-access.md)


### アプリケーション プロキシに関するその他の情報

- [こちらをご覧オンライン ヘルプ](active-directory-application-proxy-enable.md)
- [アプリケーション プロキシに関するブログします。](http://blogs.technet.com/b/applicationproxyblog/)
- [Channel 9 のビデオを見る。](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## その他のリソース

* [組織として Azure にサインアップします。](../sign-up-organization.md)
* [Azure Id](../fundamentals-identity.md)





