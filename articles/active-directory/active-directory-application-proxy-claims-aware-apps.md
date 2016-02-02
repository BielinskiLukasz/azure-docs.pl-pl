<properties
    pageTitle="アプリケーション プロキシで要求に対応するアプリケーションを利用する"
    description="Azure AD アプリケーション プロキシをセットアップする方法について説明します。"
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
    ms.date="12/08/2015"
    ms.author="kgremban"/>




# アプリケーション プロキシで要求に対応するアプリケーションを利用する

要求に対応するアプリケーションは、Security Token Service (STS) へのリダイレクトを行います。STS は、ユーザーをアプリケーションにリダイレクトする前に、ユーザーにトークンの代わりの資格情報を要求します。 これらのリダイレクトで機能するようにアプリケーション プロキシを有効にするには、次の手順を実行する必要があります。
> [AZURE.IMPORTANT] アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。 詳細については、次を参照してください。 [Azure Active Directory のエディション](active-directory-editions.md)します。


## 前提条件

この手順を実行する前に、要求に対応するアプリのリダイレクト先の STS が、オンプレミス ネットワークの外部でも使用できるようになっていることを確認してください。


### Azure クラシック ポータルの構成

1. 説明に従って、アプリケーションを発行 [アプリケーション プロキシを使用してアプリケーションを公開](active-directory-application-proxy-publish.md)します。
2. アプリケーションの一覧で、要求に対応するアプリケーションを選択し、**[構成]** をクリックします。
3. **[事前認証方法]** で **[パススルー]** を選択した場合は、**[外部 URL]** スキームとして **[HTTPS]** を選択してください。
4. **[事前認証方法]** として **Azure Active Directory** を選択した場合は、**[内部認証方法]** として **[なし]** を選択してください。


### ADFS 構成

1. [ADFS 管理] を開きます。
2. **[証明書利用者信頼]** に移動し、アプリケーション プロキシで発行しているアプリを右クリックし、**[プロパティ]** を選択します。

![[証明書利用者信頼] でアプリ名を右クリックしているスクリーンショット](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)

3. **[エンドポイント]** タブの **[エンドポイントの種類]** で、**[WS-Federation]** を選択します。
4. **[信頼された URL]** で、アプリケーション プロキシの **[外部 URL]** で入力した URL を入力し、**[OK]** をクリックします。

![エンドポイントを追加し、信頼された URL 値を設定しているスクリーン ショット](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)





