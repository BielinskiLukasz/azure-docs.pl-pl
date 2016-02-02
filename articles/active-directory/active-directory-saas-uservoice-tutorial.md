<properties 
    pageTitle="チュートリアル: Azure Active Directory と UserVoice の統合 | Microsoft Azure" 
    description="Azure Active Directory で UserVoice を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法を説明します。" 
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


# チュートリアル: Azure Active Directory と UserVoice の統合

このチュートリアルでは、Azure と UserVoice の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   UserVoice のテナント

このチュートリアルを完了すると、UserVoice に割り当てた Azure AD ユーザーは、UserVoice 会社サイト (サービス プロバイダーが開始したサインオン) で、アプリケーションにシングル サインオンまたはできるを使用して、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  UserVoice のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-uservoice-tutorial/IC777514.png "Scenario")

## UserVoice のアプリケーション統合の有効化

このセクションでは、UserVoice のアプリケーション統合を有効にする方法を説明します。

### UserVoice のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-uservoice-tutorial/IC700994.png "Applications")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-uservoice-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-uservoice-tutorial/IC749322.png "Add an application from gallerry")

6.  **検索ボックス**に「**UserVoice**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-uservoice-tutorial/IC777513.png "Application gallery")

7.  結果ウィンドウで **[UserVoice]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![UserVoice](./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

## シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで UserVoice に対する認証を行うことができるようにする方法を説明します。  
UserVoice のシングル サインオンを構成するには、証明書からサムプリント値を取得する必要があります。  
この手順に慣れていない場合は、次を参照してください。 [証明書の拇印の値を取得する方法](http://youtu.be/YKQF266SAxI)します。

### シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルでの **UserVoice** アプリケーション統合ページで、] をクリックして **でのシングル サインオンを構成する** を開くには、 ** [シングル サインオン ** ダイアログ。

    ![シングル サインオンの構成](./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configure single sign-on")

2.  **どのようなユーザーの UserVoice に** ] ページで、[ **Microsoft Azure AD シングル サインオン**, 、順にクリック **次**します。

    ![シングル サインオンの構成](./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configure single sign-on")

3.  **アプリケーション URL の構成** ] ページで、 **UserVoice サインイン URL** ] ボックスに、次のパターンを使用して URL を入力"* https://\<tenant-name\>します。UserVoice.com*"、クリックして **次**します。

    ![アプリケーション URL の構成](./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configure App URL")

4.  **UserVoice でのシングル サインオンの構成** ] ページで、証明書をダウンロードする] をクリックして **証明書のダウンロード**, 、としては、証明書ファイルをローカルに保存および **c:\\UserVoice.cer**します。

    ![シングル サインオンの構成](./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configure single sign-on")

5.  別の Web ブラウザーのウィンドウで、UserVoice 企業サイトに管理者としてログインします。

6.  上部のツール バーの [設定] をクリックし、[Web ポータル] を選択します。

    ![設定](./media/active-directory-saas-uservoice-tutorial/IC777519.png "Settings")

7.  **[Web ポータル]** タブの **[ユーザー認証]** セクションで、**[編集]** をクリックして **[ユーザー認証の編集]** ダイアログ ページを開きます。

    ![Web ポータル](./media/active-directory-saas-uservoice-tutorial/IC777520.png "Web portal")

8.  **[ユーザー認証の編集]** ダイアログ ページで、次の手順に従います。

    ![ユーザー認証の編集](./media/active-directory-saas-uservoice-tutorial/IC777521.png "Edit user authentication")

    1.  **[シングル サインオン]** をクリックします。
    2.  Azure ポータルで、**[UserVoice でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[SSO リモート サインイン]** ボックスに貼り付けます。
    3.  Azure ポータルで、**[UserVoice でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[SSO リモート サインアウト]** ボックスに貼り付けます。
    4.  エクスポートした証明書から **[サムプリント]** の値をコピーし、**[現在の証明書 SHA1 フィンガープリント]** ボックスに貼り付けます。
        >[AZURE.TIP] 詳細については、「 [証明書拇印値を取得する方法](http://youtu.be/YKQF266SAxI)

    5.  **[認証設定の保存]** をクリックします。

9.  Azure AD ポータルでのシングル サインオンの構成情報を選択し、 **完了** を閉じる、 **[シングル サインオン** ダイアログ。

    ![シングル サインオンの構成](./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configure single sign-on")

## ユーザー プロビジョニングの構成

Azure AD ユーザーが UserVoice にログインできるようにするには、そのユーザーを UserVoice にプロビジョニングする必要があります。  
UserVoice の場合、プロビジョニングは手動で行います。

### ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  **UserVoice** テナントにログインします。

2.  **[設定]** に移動します。

    ![設定](./media/active-directory-saas-uservoice-tutorial/IC777811.png "Settings")

3.  **[全般]** をクリックします。

4.  **[エージェントとアクセス許可]** をクリックします。

    ![エージェントとアクセス許可](./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agents and permissions")

5.  **[管理者の追加]** をクリックします。

    ![管理者の追加](./media/active-directory-saas-uservoice-tutorial/IC777813.png "Add admins")

6.  **[管理者の招待]** ダイアログで、次の手順を実行します。

    ![管理者の招待](./media/active-directory-saas-uservoice-tutorial/IC777814.png "Invite admins")

    1.  [電子メール] ボックスに、プロビジョニングするアカウントの電子メール アドレスを入力し、**[追加]** をクリックします。
    2.  **[招待]** をクリックします。

>[AZURE.NOTE] UserVoice から提供されている他の UserVoice ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

## ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを UserVoice に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **UserVoice * * アプリケーション統合ページで、をクリックして * * ユーザーの割り当て**します。

    ![ユーザーの割り当て](./media/active-directory-saas-uservoice-tutorial/IC777523.png "Assign users")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-uservoice-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、次を参照してください。 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。




