<properties 
    pageTitle="チュートリアル: Azure Active Directory と Panorama9 の統合 | Microsoft Azure" 
    description="Azure Active Directory で Panorama9 を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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


# チュートリアル: Azure Active Directory と Panorama9 の統合

このチュートリアルでは、Azure と Panorama9 の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Panorama9 でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Panorama9 に割り当てた Azure AD ユーザーは、Panorama9 会社サイト (サービス プロバイダーが開始したサインオン) で、アプリケーションにシングル サインオンまたはできるを使用して、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Panorama9 のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-panorama9-tutorial/IC790016.png "Scenario")
## Panorama9 のアプリケーション統合の有効化

このセクションでは、Panorama9 のアプリケーション統合を有効にする方法を説明します。

### Panorama9 のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-panorama9-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-panorama9-tutorial/IC700994.png "Applications")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-panorama9-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-panorama9-tutorial/IC749322.png "Add an application from gallerry")

6.  **検索ボックス**に、「**Panorama9**」と入力します

    ![アプリケーション ギャラリー](./media/active-directory-saas-panorama9-tutorial/IC790017.png "Application Gallery")

7.  結果ウィンドウで **[Panorama9]** を選び、**[完了]** をクリックしてアプリケーションを追加します。

    ![Panorama9](./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
## シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD のアカウントで Panorama9 に対する認証を行えるようにする方法を説明します。  
Panorama9 のシングル サインオンを構成するには、証明書から拇印の値を取得する必要があります。  
この手順に慣れていない場合は、次を参照してください。 [証明書の拇印の値を取得する方法](http://youtu.be/YKQF266SAxI)します。

### シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルでの **Panorama9** アプリケーション統合ページで、] をクリックして **でのシングル サインオンを構成する** を開くには、 ** [シングル サインオン ** ダイアログ。

    ![Configure Single Sign-On](./media/active-directory-saas-panorama9-tutorial/IC790019.png "Configure Single Sign-On")

2.  **どのようなユーザーが Panorama9 への** ] ページで、[ **Microsoft Azure AD シングル サインオン**, 、] をクリックし、 **次**します。

    ![Configure Single Sign-On](./media/active-directory-saas-panorama9-tutorial/IC790020.png "Configure Single Sign-On")

3.  **アプリケーション URL の構成** ] ページの [、 **Panorama9 サインオン URL** ] ボックスに、型に使用する URL、ユーザーが Panorama9 へのサインイン (例::"*https://dashboard.panorama9.com/saml/access/3262*")、順にクリック **次**します。

    ![アプリケーション URL の構成](./media/active-directory-saas-panorama9-tutorial/IC790021.png "Configure App URL")

4.  **Panorama9 でのシングル サインオンの構成** ] ページで、証明書をダウンロードする] をクリックして **証明書のダウンロード**, をローカル コンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-panorama9-tutorial/IC790022.png "Configure Single Sign-On")

5.  別の Web ブラウザーのウィンドウで、Panorama9 企業サイトに管理者としてログインします。

6.  上部のツールバーで、**[管理]**、**[拡張機能]** の順にクリックします。

    ![拡張機能](./media/active-directory-saas-panorama9-tutorial/IC790023.png "Extensions")

7.  **[拡張機能]** ダイアログで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン](./media/active-directory-saas-panorama9-tutorial/IC790024.png "Single Sign-On")

8.  **[設定]** セクションで、次の手順に従います。

    ![設定](./media/active-directory-saas-panorama9-tutorial/IC790025.png "Settings")

    1.  Azure ポータルの **[Panorama9 でのシングル サインオンの構成]** ダイアログ ページで、**シングル サインオン サービス URL** の値をコピーし、**[ID プロバイダー]** テキストボックスに貼り付けます。
    2.  エクスポートした証明書から **[拇印]** の値をコピーし、**[証明書の指紋]** ボックスに貼り付けます。
        >[AZURE.TIP]詳細については、「 [証明書拇印値を取得する方法](http://youtu.be/YKQF266SAxI)

    3.  **[保存]** をクリックします。

9.  Azure AD ポータルでのシングル サインオンの構成情報を選択し、 **完了** を閉じる、 **[シングル サインオン** ダイアログ。

    ![Configure Single Sign-On](./media/active-directory-saas-panorama9-tutorial/IC790026.png "Configure Single Sign-On")
## ユーザー プロビジョニングの構成

Azure AD ユーザーが Panorama9 にログインできるようにするには、ユーザーを Panorama9 にプロビジョニングする必要があります。  
Panorama9 の場合、プロビジョニングは手動で行います。

### ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **Panorama9** 企業サイトに管理者としてログインします。

2.  上部のメニューで、**[管理]**、**[ユーザー]** の順にクリックします。

    ![ユーザー](./media/active-directory-saas-panorama9-tutorial/IC790027.png "Users")

3.  「**+**」をクリックします。

4.  [ユーザー データ] セクションで、次の手順に従います。

    ![ユーザー](./media/active-directory-saas-panorama9-tutorial/IC790028.png "Users")

    1.  **[電子メール]** テキストボックスに、プロビジョニングする有効な Azure Active Directory ユーザーの電子メール アドレスを入力します。
    2.  **[保存]** をクリックします。

>[AZURE.NOTE]Panorama9 から提供されている他の Panorama9 ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

## ユーザーを割り当てる

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを Panorama9 に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Panorama9** アプリケーション統合ページで、をクリックして **ユーザーの割り当て**します。

    ![ユーザーの割り当て](./media/active-directory-saas-panorama9-tutorial/IC790029.png "Assign Users")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-panorama9-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、次を参照してください。 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。




