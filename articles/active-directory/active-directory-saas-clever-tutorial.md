<properties 
    pageTitle="チュートリアル: Azure Active Directory と Clever の統合 | Microsoft Azure" 
    description="Azure Active Directory で Clever を使用して、シングル サインオン、自動化されたプロビジョニング、および詳細を有効にする方法を説明します。" 
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


# チュートリアル: Azure Active Directory と Clever の統合

このチュートリアルでは、Azure と Clever の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Clever テナント

このチュートリアルを完了すると、Clever に割り当てた Azure AD ユーザーは、巧妙な会社サイト (サービス プロバイダーが開始したサインオン) で、アプリケーションにシングル サインオンまたはできるを使用して、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Clever のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-clever-tutorial/IC798977.png "Scenario")
## Clever のアプリケーション統合の有効化

このセクションでは、Clever のアプリケーション統合を有効にする方法について説明します。

### Clever のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-clever-tutorial/IC700994.png "Applications")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-clever-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-clever-tutorial/IC749322.png "Add an application from gallerry")

6.  **検索ボックス**に、「**Clever**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-clever-tutorial/IC798978.png "Application Gallery")

7.  結果ウィンドウで **[Clever]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
## シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Clever に対する認証を行えるようにする方法を説明します。  
Clever アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを **SAML トークン属性**の構成に追加する必要があります。  
次のスクリーンショットはその例です。

![属性](./media/active-directory-saas-clever-tutorial/IC798980.png "Attributes")

### シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルでの **Clever** アプリケーション統合ページで、] をクリックして **でのシングル サインオンを構成する** を開くには、 ** [シングル サインオン ** ダイアログ。

    ![Configure Single Sign-On](./media/active-directory-saas-clever-tutorial/IC784682.png "Configure Single Sign-On")

2.  **どのような Clever にサインオン** ] ページで、[ **Microsoft Azure AD シングル サインオン**, 、順にクリック **次**します。

    ![Configure Single Sign-On](./media/active-directory-saas-clever-tutorial/IC798981.png "Configure Single Sign-On")

3.  **アプリケーション URL の構成** ] ページの [、 **巧妙なサインオン URL** ユーザーが巧みなアプリケーションにサインオンする URL を使用] ボックスに、型 (例:: *https://clever.com/in/azsandbox*)、順にクリック **次**します。

    ![アプリケーション URL の構成](./media/active-directory-saas-clever-tutorial/IC798982.png "Configure App URL")

4.  **Clever でのシングル サインオンの構成** ] ページで、メタデータをダウンロードする] をクリックして **メタデータのダウンロード**, 、コンピューターのローカルにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-clever-tutorial/IC798983.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、Clever 企業サイトに管理者としてログインします。

6.  ツールバーで、**[インスタント ログイン]** をクリックします。

    ![インスタント ログイン](./media/active-directory-saas-clever-tutorial/IC798984.png "Instant Login")

7.  **[インスタント ログイン]** ページで、次の手順を実行します。

    ![インスタント ログイン](./media/active-directory-saas-clever-tutorial/IC798985.png "Instant Login")

    1.  **ログイン URL** を入力します。
        >[AZURE.NOTE] **ログイン URL** はカスタム値です。
         実際の値は、SClever サポート チームから取得できます。

    2.  **[ID システム]** として、**[ADFS]** を選択します。
    3.  **[保存]** をクリックします。

8.  Azure AD ポータルでのシングル サインオンの構成情報を選択し、 **完了** を閉じる、 **[シングル サインオン** ダイアログ。

    ![Configure Single Sign-On](./media/active-directory-saas-clever-tutorial/IC798986.png "Configure Single Sign-On")

9.  上部にあるメニュー [ **属性** を開くには、 **SAML トークン属性** ダイアログ。

    ![属性](./media/active-directory-saas-clever-tutorial/IC795920.png "Attributes")

10. 必要な属性のマッピングを追加するには、次の手順を実行します。

    ![SAML トークンの属性](./media/active-directory-saas-clever-tutorial/IC795921.png "saml token attributes")

    | 属性名| 属性値|
    |---|---|
    | clever.student.credentials.district\_username| User.userprincipalname|

    1.  上記の表の各データ行で、**[ユーザー属性の追加]** をクリックします。
    2.  **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
    3.  **[属性値]** ボックスで、その行に対して表示される属性値を選択します。
    4.  **[完了]** をクリックします。

11. **[変更の適用]** をクリックします。

## ユーザー プロビジョニングの構成

Azure AD ユーザーが Clever にログインできるようにするには、そのユーザーを Clever にプロビジョニングする必要があります。  
Clever の場合、プロビジョニングは Clever サポート チームが実行する必要のある手動のタスクです。
>[AZURE.NOTE] Clever から提供されている他の Clever ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

## ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを Clever に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Clever * * アプリケーション統合ページで、をクリックして * * ユーザーの割り当て**します。

    ![ユーザーの割り当て](./media/active-directory-saas-clever-tutorial/IC798987.png "Assign Users")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-clever-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、次を参照してください。 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。





