<properties 
    pageTitle="Windows 10 デバイスを [設定] メニューから Azure AD に設定する | Microsoft Azure" 
    description="ユーザーが [設定] メニューで Azure AD に参加する方法について説明します。" 
    services="active-directory" 
    documentationCenter="" 
    authors="femila" 
    manager="stevenpo" 
    editor=""
    tags="azure-classic-portal"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="femila"/>


# Windows 10 デバイスを [設定] メニューから Azure AD に設定する

Windows 7 または 8 を使用しているコンピューターを既に Windows 10 にアップグレードしている場合は、[設定] メニューから Azure AD に参加できます。

[設定] メニューから Azure AD に参加するには
----------------------------

1. [スタート] メニューで、[設定] チャームをクリックします。
2. [設定]、**[システム]**、**[バージョン情報]**、**[Azure AD に参加]** の順に選択します。
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. [Azure AD に参加] メッセージ ウィンドウの **[続行]** をクリックします。
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. サインイン資格情報を入力します。 このサインイン エクスペリエンスには、認証を完了するために必要なすべての手順が含まれます。 フェデレーション テナントの一部である場合は、組織でホストされているフェデレーション エクスペリエンスが管理者によって提供されます。
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. 組織が Azure AD に参加するために多要素認証を使用するように構成されている場合は、続行する前に 2 つ目の要素を提供する必要があります。
6. をクリックして **Accept** 上、** このデバイスを管理できるように** 画面です。
7. 「これで、お客様のデバイスは Azure AD の組織に参加しました。」というメッセージが表示されます。


## 追加情報

* [Azure AD 参加の使用シナリオについてください。](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [ドメインに参加しているデバイスを Windows 10 エクスペリエンス用に Azure AD に接続します。](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 参加の設定します。](active-directory-azureadjoin-setup.md)
* [Microsoft Passport を使用してパスワードしない id の認証](active-directory-azureadjoin-passport.md)
* [Azure AD 参加の使用シナリオについてください。](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [ドメインに参加しているデバイスを Windows 10 エクスペリエンス用に Azure AD に接続します。](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 参加の設定します。](active-directory-azureadjoin-setup.md)






