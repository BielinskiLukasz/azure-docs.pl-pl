<properties 
    pageTitle="セットアップ中に新しいデバイスを Azure AD に設定する | Microsoft Azure" 
    description="最初の実行エクスペリエンス中に Azure AD への参加を設定する方法について説明するトピックです。" 
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

# セットアップ中に新しいデバイスを Azure AD に設定する

Windows 10 では、エンドユーザーは、最初の実行エクスペリエンス (FRX) 中に自分のデバイスを Azure AD に参加させることができます。 これによって、組織は、市販のデバイスを従業員や学生に配布するか、ユーザーに自分のデバイスを選択させる (CYOD) ことができます。
Windows 10 の Professional SKU または Enterprise SKU をインストールしている場合、既定のエクスペリエンスは、会社所有のデバイスのセットアップになります。

デバイスを Azure AD に参加させるには
-----------------------------------------------------------------------

1. 後に、 **準備** ステージでは、セットアップ ウィザードが表示されます。
2. リージョンと言語をカスタマイズすることから開始し、使用許諾契約書に同意し、オンラインにします。
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png) </center> 
3. インターネットに接続するネットワークを選択します。
4. デバイスが個人所有であるか会社所有であるかを選択します。
5. クリックして **このデバイスは私の組織に属している**します。 これで、Azure AD 参加エクスペリエンスが開始されます。 次に示すのは、Windows 10 Professional SKU で表示される画面です。 
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png) </center>

6.  組織によって提供された資格情報を入力します。
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center> 
7.  ユーザー名を入力すると、Azure AD で一致するテナントが検索されます。 フェデレーション ドメインに属している場合は、オンプレミスのセキュリティ トークン サービス (STS) サーバー (たとえば AD FS) にリダイレクトされます。 フェデレーション ドメインのユーザーでない場合は、Azure AD でホストされるページに資格情報を直接入力する必要があります。 会社のブランド化が構成されている場合は、組織のロゴとサポート テキストも表示されます。
8.  次に、多要素認証で必要な情報の入力を求められます。 これは IT によって構成されています。
9.  次に、Azure AD によって、このユーザー/デバイスをモバイル デバイス管理 (MDM) に登録する必要があるかどうかがチェックされます。 
10. 次に、Windows によって、デバイスが Azure AD の組織のディレクトリに登録され、MDM に登録されます。
11. これが実行されると、ユーザーが管理対象ユーザーである場合は、Windows によってセットアップ処理がラップされ、ユーザーは自動ログイン経由でデスクトップに移動します。
12. フェデレーション ユーザーの場合は、Windows ログイン画面が表示され、ログインするための資格情報を入力する必要があります。

> [AZURE.NOTE] Windows の内部設置型 Active Directory ドメインに参加する既定のエクスペリエンスはサポートされません。 このため、PC をドメインに参加させる予定の場合は、[代わりにローカル アカウントで Windows をセットアップしてください] リンクを選択する必要があります。 その後で、前に実行したように、PC の [設定] メニューからドメインに参加できます。

## 追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport 経由でのパスワードを使用しない ID の認証](active-directory-azureadjoin-passport.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)


