

<properties 
    pageTitle="個人のデバイスを組織に参加させる | Microsoft Azure" 
    description="ユーザーが個人用の Windows 10 コンピューターを企業ネットワークに登録する方法と、BYOD シナリオのデプロイメント手順について説明します。" 
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

# 個人のデバイスを組織に参加させる

Windows 10 デバイスを組織に参加させるには
--------------------------------------------------------------------------------------------
1.   **開始** メニューの [ **設定**します。 
2.  選択 **アカウント**, 、] をクリックし、 **アカウント**します。
3.  クリックして **追加職場または学校のアカウント**, 、し組織のアカウントを入力します。
4.  組織のサインイン ページが表示されます。 ユーザー名とパスワードを入力し、クリックして **OK**します。
5.  次に、多要素認証で必要な情報の入力を求められます。 これは IT によって構成されています。
6.  次に、Azure AD によって、このユーザー/デバイスをモバイル デバイス管理 (MDM) に登録する必要があるかどうかがチェックされます。 
7.  次に、Windows によって、デバイスが Azure AD の組織のディレクトリに登録され、MDM に登録されます。
8.  これが実行されると、ユーザーが管理対象ユーザーである場合は、Windows によってセットアップ処理がラップされ、ユーザーは自動サインイン経由でデスクトップに移動します。
9.  フェデレーション ユーザーの場合は、Windows サインイン画面が表示され、資格情報を入力する必要があります。

## 追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport 経由でのパスワードを使用しない ID の認証](active-directory-azureadjoin-passport.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)

