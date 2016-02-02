<properties
    pageTitle="チュートリアル: Azure Active Directory と Evidence.com の統合 | Microsoft Azure"
    description="Azure Active Directory と Evidence.com の間でシングル サインオンを構成する方法について説明します。"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="asmalser"/>



# チュートリアル: Azure Active Directory と Evidence.com の統合

このチュートリアルでは、Azure Active Directory (AAD) と Syncplicity の間でのシングル サインオンを設定する方法を説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Microsoft Azure サブスクリプション
* シングル サインオン、Evidence.com サブスクリプション (電子メール earlyaccess@evidence.com で SAML ベースのシングル サインオンが有効になっていない場合) を有効になっています。

このチュートリアルを完了すると、Evidence.com に割り当てられている AAD ユーザーは、AAD アクセス パネルを使用してアプリケーションへのシングル サインオンが可能になります。

## 目的のディレクトリへの Evidence.com の追加

このセクションでは、Evidence.com を統合アプリケーションとして Azure Active Directory に追加する方法を説明します。

**Evidence のアプリケーション統合を有効にするには**

1.  [Azure クラシック ポータル](https://manage.windowsazure.com), 、左側のナビゲーション ウィンドウで、次のようにクリックします。 **Active Directory**します。

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

4.  アプリケーション ギャラリーを開くには、**[追加]**、**[ギャラリーからアプリケーションを追加します]** の順にクリックします。

5.  検索ボックスに、「**Evidence.com**」と入力します。

6.  結果ウィンドウで **[Evidence.com]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。


## シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、Evidence.com で Azure Active Directory のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1.  Azure クラシック ポータルで Evidence.com を追加したら、**[シングル サインオンの構成]** をクリックします。

2.  次の画面で、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

3.  [アプリケーション URL の構成] 画面で、URL を入力、Evidence.com テナントの URL にユーザーがサインインする場所 (例: https://yourtenant.evidence.com をクリック **次**します。

4.  **[証明書のダウンロード]** リンクをクリックし、証明書をローカル ドライブに保存します。 この証明書やメタデータの Url (エンティティの ID、SSO サインイン URL とサインアウト URL) は Evidence.com サイトに SSO を設定するために使用されます。

5.  別の Web ブラウザー ウィンドウで、Evidence.com テナントに管理者としてログインし、**[Admin] (管理)** タブに移動します。

6.  **[Agency Single Sign On] (代理店のシングル サインオン)** をクリックします。

7.  **[SAML Based Single Sign On] (SAML ベースのシングル サインオン)** を選択します。

8.  Azure クラシック ポータルに表示されている **[発行者の URL]**、**[シングル サインオン]**、**[シングル サインアウト]** の値をコピーして、Evidence.com の対応するフィールドに貼り付けます。

9.  手順 4 でダウンロードした証明書を Notepad.exe などのテキスト エディターで開き、その内容をコピーして **[Security Certificate] (セキュリティ証明書)** ボックスに貼り付けます。

10. Evidence.com の構成を保存します。

11. Azure クラシック ポータルで、**[説明どおりにシングル サインオンを構成したことを確認してください]** チェックボックスをオンにします。 これをオンにすると、このアプリケーションのチェック ボックスの機能を、現在の証明書で開始できるようになります。

12. [シングル サインオンの確認] ページで **[完了]** をクリックします。


## Evidence.com テスト ユーザーの作成

Azure AD ユーザーがサインインできるようにするには、ユーザーを Evidence.com アプリケーションにプロビジョニングする必要があります。 このセクションでは、Evidence.com で Azure AD ユーザー アカウントを作成する方法について説明します。

**Evidence.com でユーザー アカウントをプロビジョニングするには**

1.  Web ブラウザー ウィンドウで、Evidence.com 企業サイトに管理者としてログインします。

2.  **[Admin] (管理)** タブをクリックします。

3.  **[Add User] (ユーザーの追加)** をクリックします。

4.  **[Add] (追加)** ボタンをクリックします。

5.  追加したユーザーの **[Email Address] (電子メール アドレス)** が、アクセス権を付与する Azure AD 内のユーザーのユーザー名と一致する必要があります。 組織内でユーザー名と電子メール アドレスが同じ値ではない場合は、Azure クラシック ポータルで、**[Evidence.com]、[属性]、[シングル サインオン]** セクションの順に進み、Evidence.com に送信される nameidenitifer を電子メール アドレスに変更することができます。


## Evidence.com へのユーザーの割り当て

プロビジョニングされた AAD ユーザーがアクセス パネルに Evidence.com を表示できるようにするには、Azure クラシック ポータル内でアクセス権を割り当てる必要があります。

**Evidence.com にユーザーを割り当てるには**

1.  Azure クラシック ポータルの Evidence.com 用のクイック スタート ページで、**[ユーザーを Evidence.com に割り当てる]** をクリックします。

2.  **[表示]** メニューで、Evidence.com にユーザーとグループのどちらを割り当てるかを選択し、チェックマーク ボタンをクリックします。

3.  **[ユーザー]** 一覧で、Evidence.com を割り当てるユーザーまたはグループを選択します。

4.  ページ フッターにある **[割り当て]** をクリックします。






