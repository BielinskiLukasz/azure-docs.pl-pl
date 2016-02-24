<properties 
    pageTitle="Azure AD 参加の使用シナリオとデプロイに関する考慮事項 | Microsoft Azure" 
    description="管理者がエンド ユーザー (従業員、学生、他のユーザー) のために Azure AD 参加を設定する方法を説明します。 また、Azure AD 参加を使用するさまざまな実際のシナリオについても説明します。" 
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

# Azure AD 参加の使用シナリオとデプロイに関する考慮事項 

## Azure AD 参加の使用シナリオ
シナリオ 1: 業務の多くでクラウドを利用している企業
--------------------------------------------------------
現在、業務や ID 管理の多くでクラウドを利用している企業や、近いうちにクラウドに移行しようとしている企業は、Azure AD 参加の恩恵を受けることができます。 Azure AD に作成したアカウントを使用して Windows 10 にサインインすることができます。 いずれかで [最初の実行エクスペリエンス (FRX) プロセス]((active-directory-azureadjoin-user-frx.md)) または Azure AD に参加する [設定エクスペリエンス](active-directory-azureadjoin-user-upgrade.md), 、ユーザーは各自のマシンを Azure AD に参加できます。  ユーザーは、ブラウザーまたは Office アプリケーション内で、Office 365 のようなクラウド リソースへの SSO アクセスを行うことができます。 

シナリオ 2: 教育機関 
----------------------------------------------------------------------------------
教育機関は通常 2 つのユーザー型を持つ: 教員と学生です。 教員メンバーは、組織に長期に所属するメンバーと見なされるため、このようなメンバーにはオンプレミスのアカウントを作成することが必要になります。 一方、学生の場合は組織に所属する期間が短いため、Azure AD で管理することにより、ディレクトリ スケールをオンプレミスではなくクラウドにプッシュできます。 このような学生は、Azure AD のアカウントを使用して Windows にサインインし、Office アプリケーションで Office 365 リソースにアクセスできるようになります。 

シナリオ 3: 小売業
---------------------------------------------------------------------------------------
小売業には、季節従業員と長期雇用の従業員がいます。 長期雇用のフルタイム従業員に対してはオンプレミスのアカウントを作成できます。このような従業員は、通常、ドメインに参加しているコンピューターを使用します。 一方、季節従業員については、組織への所属期間が短いため、ユーザー ライセンスをより簡単に移動できる方法で管理することが必要になります。 Office 365 のライセンスを持つユーザーをクラウドに作成することで、これらのユーザーが Azure AD アカウントを使用して Windows と Office アプリケーションにサインインできるようになると共に、これらのユーザーが退職した後のライセンスの移動性を保つことができます。 

シナリオ 4: その他のシナリオ
------------------------------------------------------------------------------------------
上記のシナリオ以外、ユーザーが自分のデバイスが自動 MDM 登録、およびシングル サインオンによる Azure AD に簡略化された参加エクスペリエンスの Azure AD でデバイス管理のために Azure AD に参加することにメリットでき、内部設置型リソースです。  


##Azure AD 参加のデプロイに関する考慮事項

ユーザーが会社所有のデバイスを直接 Azure AD に参加させることができるようにする
-----------------------------------------------------------------------------------------

企業では、パートナーの企業や組織にクラウドのみのアカウントを提供できます。 このようなパートナーは、会社のアプリとリソースへの簡単なアクセスとシングル サインオンを利用できるようになります。 このシナリオは、主にクラウドのリソース (Azure AD を認証に利用している SaaS アプリや Office 365 アプリなど) へのアクセスに自分のデバイスを使用しているユーザーに適用されます。

### 前提条件
**企業レベル (管理者)**

*   Azure サブスクリプションと Azure Active Directory  

**ユーザー レベル**

*   Windows 10 (Professional および Enterprise SKU)

### 管理者の作業
* [デバイスの登録の設定](active-directory-azureadjoin-setup.md)

### ユーザーの作業
* [設定中に新しい Windows 10 デバイスを Azure AD 用に設定する](active-directory-azureadjoin-user-frx.md)
* [Windows 10 デバイスを [設定] メニューから Azure AD に設定する](active-directory-azureadjoin-user-upgrade.md)
* [個人の Windows 10 デバイスを組織に参加させる](active-directory-azureadjoin-personal-device.md)
  


## Windows 10 について、組織での BYOD を有効にする
会社のユーザーと従業員が個人の Windows デバイスを使用して会社のアプリとリソースにアクセスできるように設定できます。 ユーザーは、Azure AD のアカウント (仕事用アカウント) を個人の Windows デバイスに追加することで、安全かつポリシーに準拠する方法で、会社リソースにアクセスできます。 

### 前提条件
**企業レベル (管理者)**

*   Azure AD サブスクリプション

**ユーザー レベル**

*   Windows 10 (Professional および Enterprise SKU)


### 管理者の作業

* [デバイスの登録の設定](active-directory-azureadjoin-setup.md)

### ユーザーの作業
* [個人の Windows 10 デバイスを組織に参加させる](active-directory-azureadjoin-personal-device.md)


## 追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport 経由でのパスワードを使用しない ID の認証](active-directory-azureadjoin-passport.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)

