<properties 
    pageTitle="チュートリアル: Azure Active Directory と Huddle の統合 | Microsoft Azure" 
    description="Azure Active Directory と Huddle を使用して、シングル サインオン、自動化されたプロビジョニング、および詳細を有効にする方法を説明します。" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />


# チュートリアル: Azure Active Directory と Huddle の統合

このチュートリアルでは、Azure と Huddle の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Huddle でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Huddle に割り当てた Azure AD ユーザーは、Huddle 会社サイト (サービス プロバイダーが開始したサインオン) で、アプリケーションにシングル サインオンまたはできるを使用して、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Huddle のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![Configure Single Sign-On](./media/active-directory-saas-huddle-tutorial/IC787830.png "Configure Single Sign-On")
## Huddle のアプリケーション統合の有効化

このセクションでは、Huddle のアプリケーション統合を有効にする方法について説明します。

### Huddle のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-huddle-tutorial/IC700994.png "Applications")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-huddle-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-huddle-tutorial/IC749322.png "Add an application from gallerry")

6.  **検索ボックス**に「**Huddle**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-huddle-tutorial/IC787831.png "Application Gallery")

7.  結果ウィンドウで **[Huddle]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Huddle](./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
## シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Huddle に対する認証を行うことができるようにする方法を説明します。

### シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルでの **Huddle** アプリケーション統合ページで、] をクリックして **でのシングル サインオンを構成する** を開くには、 ** [シングル サインオン ** ダイアログ。

    ![Configure Single Sign-On](./media/active-directory-saas-huddle-tutorial/IC787833.png "Configure Single Sign-On")

2.  **どのようなユーザーが Huddle へのサインオン** ] ページで、[ **Microsoft Azure AD シングル サインオン**, 、順にクリック **次**します。

    ![Configure Single Sign-On](./media/active-directory-saas-huddle-tutorial/IC787834.png "Configure Single Sign-On")

3.  **アプリケーション URL の構成** ] ページで、 **Huddle サインオン URL** ] ボックスに、次のパターンを使用して Huddle の URL のテナントの種類"*http://company.huddle.com*"、順にクリック **次**します。

    ![アプリケーション URL の構成](./media/active-directory-saas-huddle-tutorial/IC787835.png "Configure App URL")

4.  **Huddle でのシングル サインオンの構成** ] ページで、次の手順を実行します。

    ![Configure Single Sign-On](./media/active-directory-saas-huddle-tutorial/IC787836.png "Configure Single Sign-On")

    1.  クリックして **証明書のダウンロード**, 、お使いのコンピューターに証明書ファイルを保存します。
    2.  **[発行者の URL]** の値、**[SAML SSO URL]** の値、およびダウンロードした証明書をコピーし、それらを Huddle サポート チームに送信します。

    >[AZURE.NOTE] Huddle サポート チームがシングル サインオンを有効にする必要があります。
    構成が完了すると、通知が届きます。

5.  Azure AD ポータルでのシングル サインオンの構成情報を選択し、 **完了** を閉じる、 **[シングル サインオン** ダイアログ。

    ![Configure Single Sign-On](./media/active-directory-saas-huddle-tutorial/IC787837.png "Configure Single Sign-On")
## ユーザー プロビジョニングの構成

Azure AD ユーザーが Huddle にログインできるようにするには、そのユーザーを Huddle にプロビジョニングする必要があります。  
Huddle の場合、プロビジョニングは手動で行います。

### ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **Huddle** 企業サイトに管理者としてログインします。

2.  **[ワークスペース]** をクリックします。

3.  クリックして **人 \ > 招待**します。

    ![ユーザー](./media/active-directory-saas-huddle-tutorial/IC787838.png "People")

4.  **[新しい招待の作成]** セクションで、次の手順を実行します。

    ![新しい招待](./media/active-directory-saas-huddle-tutorial/IC787839.png "New Invitation")

    1.  **[ユーザーを招待して参加させるチームの選択]** の一覧で **[チーム]** を選択します。
    2.  プロビジョニングする有効な AAD アカウントの**電子メール アドレス**を、関連するテキスト ボックスに入力します。
    3.  **[招待]** をクリックします。

    >[AZURE.NOTE] アカウントがアクティブになる前に、Azure AD アカウント所有者に、アカウント確認用のリンクを含む電子メールが送信されます。

>[AZURE.NOTE] Huddle から提供されている他の Huddle ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

## ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを Huddle に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Huddle * * アプリケーション統合ページで、をクリックして * * ユーザーの割り当て**します。

    ![ユーザーの割り当て](./media/active-directory-saas-huddle-tutorial/IC787840.png "Assign Users")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-huddle-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、次を参照してください。 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。




